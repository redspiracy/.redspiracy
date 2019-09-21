# SlashRoot CTF 4.0
## _Android_

## Informasi Soal
| Kategori | Poin |
| -------- | ---- |
| Reverse Engineering | 150 |
### Deskripsi
> (Pada saat writeup dibuat, website sudah inactive, sehingga kehilangan descripsi soal, namun tidak terdapat info maupun clue pada description).

## Cara Penyelesaian
iberikan sebuah file ‘CrackMe102.apk‘. Namun untuk mengetahui Algoritma dari apk tersebut, ada 2 cara, yaitu extract dengan menggunakan Apktool atau dengan JADX (http://www.javadecompilers.com/apk).

Pada kasus ini, saya memilih untuk mengextract apk tersebut menggunakan JADX, agar source code yang dipakai untuk membuat apk tersebut berkemungkinan dapat dibaca. Berbeda ketika kita menggunakan Apktool, maka extract yang yang dihasilkan banyak berbentuk ‘.smali’ yang sulit untuk dibaca.

Setelah saya mengextract  file apk tersebut, terdapat 2 buah file yaitu sources dan resources,
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/SlashRootCTF%204.0/reverse-engineering/Android/Screenshot/1.jpg)

Secara default biasanya resource hanya mengandung asset-asset yang dibutuhkan dalam apk tersebut, sedangkan sources merupakan isi dari code apa yang dijalankan, maka ktia bisa mencari source codenya pada folder source. Setelah mencari kurang lebih 5 menit, ternyata source code terdapat pada folder ini:

![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/SlashRootCTF%204.0/reverse-engineering/Android/Screenshot/2.jpg)

Terdapat 6 file java, yang dijalankan oleh apk. Namun seperti halnya coding, kita bisa memulai dari analisa apa isi dari ‘MainActivity.java’ karena code tersebut seharusnya merupakan function main() dalam apk tersebut.

Setelah dilihat, ternyata benar bahwa ada code yang dijalankan sebelum print flag, seperti berikut:

![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/SlashRootCTF%204.0/reverse-engineering/Android/Screenshot/3.jpg)

Dapat diartikan pada fungsi if yang tertera, yaitu mengambil value string dari input form, lalu di lempar kedalam fungsi ‘CheckAnswer()’. Lalu, jika fungsi tersebut mereturn nilai true, maka flag akan tercetak. Untuk mengetahui lebih lanjut kita dapat melihat file ‘CheckAnswer.java’ yang tertera dalam folder yang sama. Berikut isi dari file tersebut,

![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/SlashRootCTF%204.0/reverse-engineering/Android/Screenshot/4.jpg)

Ternyata proses check tersebut menggunakan constraint yang cukup banyak, sehingga kita akan kesulitan untuk memcahkan constraint-constraint tersebut. Pada kasus ini, saya mencoba membuat z3-solver script untuk menyelesaikan constraint tersebut.

Namun ada sedikit kendala dimana kita tidak menemukan panjang flag/char yang dimaksud. Disana tertulis bahwa “this.text.trim().length() != Integer.valueOf(this.context.getString(C0264R.string.num_char)).intValue()“. Dari tulisan tersebut, kita dapat mengetahui bahwa panjang string terdapat pada variable num_char pada file ‘C0264R.java’, namun ketika dibuka, num_char tersebut tidak memberikan informasi panjang.

![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/SlashRootCTF%204.0/reverse-engineering/Android/Screenshot/7.jpg)

Karena mustahil bahwa panjangnya akan 2131492896, maka kita harus mencari file string, seperti yang tertulis pada “/* renamed from: id.slashrootctf.crackme102.R$string */“, dimana yang berarti ada file string dalam folder crackme102 tersebut.

Selanjutnya kita harus mencari file string tersebut, dan yang paling memungkinkan adalah di resource, karena string yang disimpan pada source hanya merupakan id dari variable tersebut, untuk memanggil variable tersebut di resource. Kurang lebih 30 menit melakukan pencarian secara manual. Akhirnya file string ditemukan,

![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/SlashRootCTF%204.0/reverse-engineering/Android/Screenshot/6.jpg)

Sehingga kita dapat melihat panjang char yang sebenarnya,

![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/SlashRootCTF%204.0/reverse-engineering/Android/Screenshot/5.jpg)

Pada file string tertulis bahwa num_char memiliki nilai 29, yang berarti, panjang flag/char tersebut adalah 29 karakter. Kita sekarang sudah dapat membuat code z3nya dengan constraint-constraint yang ada, berikut code yang saya buat:
```
from z3 import *

def is_valid(x):
    return Or(
        (x == ord('A')),
        (x == ord('B')),
        (x == ord('C')),
        (x == ord('D')),
        (x == ord('E')),
        (x == ord('F')),
        (x == ord('G')),
        (x == ord('H')),
        (x == ord('I')),
        (x == ord('J')),
        (x == ord('K')),
        (x == ord('L')),
        (x == ord('M')),
        (x == ord('N')),
        (x == ord('O')),
        (x == ord('P')),
        (x == ord('Q')),
        (x == ord('R')),
        (x == ord('S')),
        (x == ord('T')),
        (x == ord('U')),
        (x == ord('V')),
        (x == ord('W')),
        (x == ord('X')),
        (x == ord('Y')),
        (x == ord('Z')),
        (x == ord('0')),
        (x == ord('1')),
        (x == ord('2')),
        (x == ord('3')),
        (x == ord('4')),
        (x == ord('5')),
        (x == ord('6')),
        (x == ord('7')),
        (x == ord('8')),
        (x == ord('9')),
        (x == ord('-'))
    )


s = Solver()
vec = ""

for i in xrange(0, 29):
    vec += "m[{}] ".format(i)

m = BitVecs(vec, 32)


s.add(m[5] == ord('-'))
s.add(m[11] == ord('-'))
s.add(m[17] == ord('-'))
s.add(m[23] == ord('-'))
s.add(m[24] == ord('D'))
s.add(m[16] == ord('Z'))
s.add(m[2] == ord('8'))
s.add(m[3] == ord('1'))

s.add(m[9] == m[10])
s.add(m[18] == ((m[2] - m[3]) + 50))
s.add(m[21] == (((m[1] + m[26]) + 7) / 2))
s.add(m[15] == ((m[18] - m[3]) + 40))
s.add(m[19] * 2 == (m[24] + m[25] + 10))
s.add(m[26] == (m[20] - m[1]) * 25)
s.add(m[2] == ((m[18] - m[3]) + 48))
s.add(m[0] * 2 == ((m[14] * 3) - m[18]))
s.add(m[20] == m[21] + 2 - 3 + 1)
s.add(m[16] == (m[22] + 1))
s.add(m[12] == m[19])
s.add(((m[26] * 14) - m[25]) % m[16] == 0)
s.add(m[6] * 2 == m[9] + ord('S'))
s.add(m[24] == m[10] + 1)
s.add(m[24] + 2 == m[25])
s.add(m[27] == m[25] + (m[25] % 10) + 6)
s.add(m[28] == m[25])
s.add(m[1] == m[2] - 3)
s.add(m[14] == (m[6] - 1) + 1)
s.add(m[16] / 9 == m[22] - m[7])
s.add(m[16] - 1 == m[4])
s.add(m[22] == m[4])
s.add(m[8] == (((m[25] / 10) + 1) * 11))
s.add(m[16] - (m[16] / 5) == m[13])

for i in xrange(0, 29):
    s.add(is_valid(m[i]))

while s.check() == z3.sat:
    model = s.model()
    flag = ""
    nope = []
    for i in m:
        if str(i) and model[i] is not None:
            flag += chr(long(str(model[i])))
        nope.append(i != model[i])
    s.add((nope[:-1]))

    print flag
```

Kita tinggal menjalankan code tersebut untuk memenuhi semua aturan dari constraint tersebut.
Hasil terminal
```
redspiracy@revrsengineer:~/Desktop/CTF/SlashRootCTF 4.0$ 
 ↪ python z3_apk.py
T581Y-KOXCC-JHK0Z-9J77Y-DF2LF
```

ternyata string yang dibutuhkan adalah T581Y-KOXCC-JHK0Z-9J77Y-DF2LF.

Untuk memastikan benar/salahnya, kita dapat menginstall apk tersebut pada smartphone kita masing-masing, lalu masukan serial tersebut. Hasilnya:

![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/SlashRootCTF%204.0/reverse-engineering/Android/Screenshot/8.jpg)

## Flag
> SlashRootCTF{T581Y-KOXCC-JHK0Z-9J77Y-DF2LF}
