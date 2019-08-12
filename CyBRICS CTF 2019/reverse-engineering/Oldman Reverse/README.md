# CyBRICS CTF 2019
## _Oldman Reverse_

## Informasi Soal
| Kategori | Poin |
| -------- | ---- |
| Reverse Engineering | 10 |
### Deskripsi
> Author: George Zaytsev (groke)
>
> I've found this file in my grandfather garage. Help me understand what it does

## Cara Penyelesaian
Pada CyBRICS CTF 2019 terdapat soal bernama ‘Oldman Reverse’, lalu disertakan file attachment dalam bentuk assembly code. Berikut merupakan assembly yang diberikan,
```
.MCALL  .TTYOUT,.EXIT
START:
    mov  #MSG r1 
    mov  #0d r2
    mov  #32d r3
loop:       
    mov  #MSG r1 
    add  r2 r1
    movb (r1) r0
    .TTYOUT
    sub #1d r3
    cmp #0 r3
    beq  DONE
    add #33d r2
    swab r2
    clrb r2
    swab r2    
    br loop
DONE: 
    .EXIT

MSG:
    .ascii "cp33AI9~p78f8h1UcspOtKMQbxSKdq~^0yANxbnN)d}k&6eUNr66UK7Hsk_uFSb5#9b&PjV5_
8phe7C#CLc#<QSr0sb6\{%NC8G|ra!YJyaG_~RfV3sw_&SW~}((_1>rh0dMzi><i6)wPgxiCzJJVd8CsGkT^p
>_KXGxv1cIs1q(QwpnONOU9PtP35JJ5<hlsThB{uCs4knEJxGgzpI&u)1d{4<098KpXrLko{Tn{gY<
|EjH_ez{z)j)_3t(|13Y}"
.end START
```
Setelah saya melakukan analisa pada code tersebut, ternyata assembly tersebut merupakan code Assembly PDP11 yang terlihat dari syntax yang dipakai pada code tersebut (seperti BEQ, SWAB, BR.dll). Setelah dilakukan analisa lebih lanjut, ternyata alur program ini berjalan seperti berikut:
```
.MCALL  .TTYOUT,.EXIT
START:
    mov  #MSG r1 -------------> Memindahkan isi string MSG kedalam r1
    mov  #0d r2 --------------> Inisialisasi r2 = 0
    mov  #32d r3 -------------> Inisialisasi r3 = 32
loop:       ------------------> Looping function
    mov  #MSG r1 -------------> Memindahkan isi string MSG kedalam r1 (lagi)
    add  r2 r1 ---------------> Menambahkan nilai r2 ke index r1
    movb (r1) r0 -------------> Memindahkan deferred address dari r1, ke r0
    .TTYOUT
    sub #1d r3 ---------------> Melakukan pengurangan pada r3 (r3 = r3 - 1)
    cmp #0 r3 ----------------> Pembandingan value r3 dengan 0
    beq  DONE ----------------> Jika return TRUE, maka lompat ke function DONE
    add #33d r2 --------------> Menambahkan r2 dengan value 33 (r2 = r2 + 33)
    swab r2 ------------------> Rotate 8 bit pada address r2
    clrb r2 ------------------> Clear byte pada r2 yang telah di rotate (kosong)
    swab r2 ------------------> Rotate 8 bit kembali ke address r2 semula
    br loop
DONE: ------------------------> Exit function
    .EXIT

MSG: -------------------------> Variable MSG yang menyimpan string.
    .ascii "cp33AI9~p78f8h1UcspOtKMQbxSKdq~^0yANxbnN)d}k&6eUNr66UK7Hsk_uFSb5#9b&PjV5_
8phe7C#CLc#<QSr0sb6\{%NC8G|ra!YJyaG_~RfV3sw_&SW~}((_1>rh0dMzi><i6)wPgxiCzJJVd8CsGkT^p
>_KXGxv1cIs1q(QwpnONOU9PtP35JJ5<hlsThB{uCs4knEJxGgzpI&u)1d{4<098KpXrLko{Tn{gY<
|EjH_ez{z)j)_3t(|13Y}"
.end START
```
Didapatkan gambaran flow program assembly tersebut, lalu saya mencoba melakukan coding python untuk mendapatkan index flag yang terdapat dalam buffer MSG. Berikut code pertama yang dicoba,
```
r1 = 'cp33AI9~p78f8h1UcspOtKMQbxSKdq~^0yANxbnN)d}k&6eUNr66UK7Hsk_uFSb5#9b&PjV5_8p
he7C#CLc#<QSr0sb6\{%NC8G|ra!YJyaG_~RfV3sw_&SW~}((_1>rh0dMzi><i6)wPgxiCzJJVd8CsGk
T^p>_KXGxv1cIs1q(QwpnONOU9PtP35JJ5<hlsThB{uCs4knEJxGgzpI&u)1d{4<098KpXrLko{T
n{gY<|EjH_ez{z)j)_3t(|13Y}'

r2 = 0
r3 = 32
flag = ''

while r3 > 0:
    flag += r1[r2]
    r2 += 33
    r3 -= 1
    print flag
```
Lalu saya mencoba run code tersebut untuk mengetahui hasilnya, dan didapatkan seperti ini
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/CyBRICS%20CTF%202019/reverse-engineering/Oldman%20Reverse/screenshot/1.jpg)

Potongan template flag ‘cybrics{‘ didapatkan dan error ‘Index out of range’ didapatkan yang berarti nilai dari r2 terus bertambah tetapi nilai r2 menjadi lebih besar dari index MSG.

Ketika dilakukan analisa ulang pada code assembly dan code python, rupanya kita dapat melakukan modulus pada nilai r2 dengan total panjang dari MSG, sehingga r2 dapat terus bertambah, namun return valuenya pasti akan selalu kurang dari total panjang  MSG. Sehingga kita merubah sedikit code menjadi,
```
r1 = 'cp33AI9~p78f8h1UcspOtKMQbxSKdq~^0yANxbnN)d}k&6eUNr66UK7Hsk_u
FSb5#9b&PjV5_8phe7C#CLc#<QSr0sb6\{%NC8G|ra!YJyaG_~RfV3sw_&SW~}((_1>rh0dM
zi><i6)wPgxiCzJJVd8CsGkT^p>_KXGxv1cIs1q(QwpnONOU9PtP35JJ5<hlsThB{uCs4k
nEJxGgzpI&u)1d{4<098KpXrLko{Tn{gY<|EjH_ez{z)j)_3t(|13Y}'

r2 = 0
r3 = 32
flag = ''

while r3 > 0:
    flag += r1[r2 % len(r1)]
    r2 += 33
    r3 -= 1
    print flag
```

Hasilnya adalah:
```
D:\>python oldmansolve.py
c
cy
cyb
cybr
cybri
cybric
cybrics
cybrics{
cybrics{p
cybrics{pd
cybrics{pdp
cybrics{pdp_
cybrics{pdp_g
cybrics{pdp_gp
cybrics{pdp_gpg
cybrics{pdp_gpg_
cybrics{pdp_gpg_c
cybrics{pdp_gpg_cr
cybrics{pdp_gpg_crc
cybrics{pdp_gpg_crc_
cybrics{pdp_gpg_crc_d
cybrics{pdp_gpg_crc_dt
cybrics{pdp_gpg_crc_dtd
cybrics{pdp_gpg_crc_dtd_
cybrics{pdp_gpg_crc_dtd_b
cybrics{pdp_gpg_crc_dtd_bk
cybrics{pdp_gpg_crc_dtd_bkb
cybrics{pdp_gpg_crc_dtd_bkb_
cybrics{pdp_gpg_crc_dtd_bkb_p
cybrics{pdp_gpg_crc_dtd_bkb_ph
cybrics{pdp_gpg_crc_dtd_bkb_php
cybrics{pdp_gpg_crc_dtd_bkb_php}
```

## Flag
> cybrics{pdp_gpg_crc_dtd_bkb_php}
