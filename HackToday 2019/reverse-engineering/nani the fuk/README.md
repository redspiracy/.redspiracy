# HackToday 2019
## _nani the fuk_

## Informasi Soal
| Kategori | Poin |
| -------- | ---- |
| Revese Engineering | 500 |
### Deskripsi
> aku dimana?
>
>unrelated: https://www.youtube.com/watch?v=1AhDtMxwnvI
>
>author: circleous

## Cara Penyelesaian
Diberikan sebuah soal berbentuk binary (binary terlampir didalam folder nani the fuk)
Jika binary dieksekusi maka binary akan meminta input, setelah mencoba inputan, ternyata program hanya menerima input 1 karakter, seperti berikut,
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/reverse-engineering/nani%20the%20fuk/screenshot/1.jpg)

Ketika input yang dimasukan salah maka program akan exit. Program tersebut terlihat seperti akan meminta input 1 karakter sebanyak 20 kali seperti yang terlihat pada gambar. Untuk mengetahui lebih lanjut, maka kita harus menganalisa asemmblynya, apa yang terjadi jika input benar dan salah.
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/reverse-engineering/nani%20the%20fuk/screenshot/4.jpg)

Setelah dianalisa, program menyimpan input yang kita masukan ke dalam ‘qword_4068’, dan setelah itu input kita akan di proses dengan shl dan xor seperti berikut ini,
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/reverse-engineering/nani%20the%20fuk/screenshot/3.jpg)
 
Namun ketika diteliti lebih lanjut, return yang dikeluarkan program akan selalu sama, sampai kita memasukan inputan sebanyak 20 kali. Dibuktikan dengan informasi berikut,
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/reverse-engineering/nani%20the%20fuk/screenshot/2.jpg)

Pada pertama kali program dimulai, program melakukan syscall pada **‘a0120’**, setelah inputan diproses dan benar, maka akan dilanjutkan pada **‘a0220’**.dst. Karena output yang diberikan program akan selalu sama sampai inputan ke-20, maka kita dapat melakukan bruteforce dengan output program sebagai acuan true or false, dengan code berikut:

```
from subprocess import Popen, PIPE
import string

charset = string.letters + string.digits + '_'
param = '01'
flag = ''
x = 0

while x < len(charset):
    proc = Popen('./nani-the-fuk', stdin=PIPE, stdout=PIPE)
    char = flag + '%s\n' %charset[x]
    result = proc.communicate(input=char)[0]
    current = result[len(result)-13:len(result)-11]
    print charset[x]
    if current > param:
        flag += charset[x] + '\n'
        param = current
        x = 0
        print 'Flag : hacktoday{' + flag.replace('\n', '') + '}'
        continue
    x += 1
```

Dengan code tersebut kita membruteforce program dengan module subprocess untuk open binary tersebut dan string untuk membuat charset yang digunakan untuk melakukan input char per char yang diambil dari variable charset \[a-z,A-Z,0-9,\_] dan yang merupakan karakter yang printable dan biasanya terkandung dalam flag. Lalu dilakukan bruteforce dengan looping sebanyak charset dengan membandingkan angka output program sebagai acuannya, jika bertambah / lebih besar dari variable param sebelumnya, berarti char yang diinput adalah benar dan disimpan kedalam variable flag, dan update variable param sebagai acuan next brute, serta set variable x menjadi 0, agar mengulang kembali dari charset ‘a’, hingga variable x == len(charset) yang berarti bruteforce telah selesai.

Kita jalan kan code berikut, dan hasilnya:
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/reverse-engineering/nani%20the%20fuk/screenshot/5.jpg)
Flag berhasil didapatkan!

## Flag
> hacktoday{s1g_c0ntorl_fl0w_xD_}
