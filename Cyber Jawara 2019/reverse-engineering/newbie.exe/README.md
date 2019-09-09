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

Pada code tersebut didapatkan bahwa terdapat sebuah input yang disimpan dalam variable s dan dilakukan iterasi sepanjang char s, dikalikan dengan value 8. Setelah itu di cocokan dengan value dalam array num. Array num sendiri berisikan: 
 
 
Selanjutnya kita dapat merubah bentuk tersebut kedalam array, dan dilakukan sedikit code seperti berikut: 
Ketika code tersebut dijalankan, maka akan mendapat flag. 
 
 

## Flag
> Flag{ditulis_disini}
