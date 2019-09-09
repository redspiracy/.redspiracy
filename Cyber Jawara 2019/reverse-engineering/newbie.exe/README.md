# Cyber Jawara 2019
## _newbie.exe_

## Informasi Soal
| Kategori | Poin |
| -------- | ---- |
| Reverse Engineering | 500 |
### Deskripsi
> Mari belajar reverse engineering dengan mencoba memecahkan program dengan kode yang sederhana.
>
> Problem setter: farisv


## Cara Penyelesaian
Diberikan sebuah file exe, yang ketika dibuka oleh IDA, hanya berisi sedikit code simple seperti dibawah ini:
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/Cyber%20Jawara%202019/reverse-engineering/newbie.exe/screenshots/1.jpg)

Dalam code tersebut didapatkan flow bahwa program meminta sebuah input yang kemudian disimpan dalam variable s, lalu dilakukan iterasi sebanyak panjang variabel s, dan dikalikan dengan value 8. Setelah itu di cocokan dengan value dalam array num.

Dalam case ini, tentu saja harus dilakukan reverse flow program, dengan cara membagi nilai array num dengan value 8, agar isi variable s tersebut dapat diketahui. Array num sendiri berisikan: 
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/Cyber%20Jawara%202019/reverse-engineering/newbie.exe/screenshots/2.jpg)
Terdapat sedikit modifikasi pada isi array num, yakni pada bagian 2 dup(x), yang berarti duplicate x sebanyak 2 kali.

Selanjutnya penulis merubah value dari array num tersebut kedalam array di python untuk dilakukan operasi pembagian dengan code seperti berikut:
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/Cyber%20Jawara%202019/reverse-engineering/newbie.exe/screenshots/3.jpg)

Ketika code tersebut dijalankan, maka akan mendapat flag.
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/Cyber%20Jawara%202019/reverse-engineering/newbie.exe/screenshots/4.jpg)
 
 

## Flag
> CJ2019{17db80b6de7d266f20fc855919b1ab61c27b60ae}
