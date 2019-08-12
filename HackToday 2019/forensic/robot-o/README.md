# HackToday 2019
## robot-o

## Informasi Soal
| Kategori | Poin |
| -------- | ---- |
| Forensic | 500 |
### Deskripsi
>Robot-o, robot buatan Friska, masih dalam tahap pengembangan sehingga terdapat corrupt pada audio yang dikeluarkan robot-o.
>Bisakah kamu memperbaikinya?
>
>format flag: hacktoday{flag}
>
>author: deomkicer

## Cara Penyelesaian
Diberikan sebuah file dengan ekstensi '.voice', namun ketika penulis mencoba check didalam terminal, hasilnya adalah
> ↪ file robot-o.voice 
> robot-o.voice: RIFF (little-endian) data, WAVE audio

Hasil tersebut menunjukan bahwa file tersebut ternyata merupakan file audio dengan ekstensi '.wav' yang corrupt sesuai dengan apa yang deskripsi soal ceritakan.
Karena sekarang kita sudah mengetahui jenis file tersebut, maka langkah selanjutnya penulis mencari cara untuk repair wav file. Tentu saja disini kita sangat memerlukan google, untuk explore lebih jauh untuk menemukan caranya.

Tanpa harus menunggu lama, penulis menggunakan keyword 'repair wav file' pada google, hasil yang dibutuhkan langsung muncul,
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/forensic/robot-o/screenshot/1.jpg)

Ternyata repairing wav file dapat dilakukan dengan audio editor (disini penulis menggunakan `Adobe Audition`), hanya dengan open file raw pada audio editor, lalu pilih file wav yang rusak tersebut, dan export menjadi file wav. 
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/forensic/robot-o/screenshot/2.jpg)

Setelah open file, sound wave yang dihasilkan dari file tersebut seperti berikut,
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/forensic/robot-o/screenshot/3.jpg)

Terlihat bahwa sound wave tersebut seperti bar yang di gabung menjadi satu buah audio, yang biasanya sound wave seperti diatas menunjukan sebuah morse code. Ketika audio di play ternyata benar! file wav tersebut berisi morse code dengan durasi play 33 detik.

Disini penulis memerlukan tools automated yang dapat mentranslasikan morse code panjang yang berdurasi 33 detik. Setelah dilakukan googling, ternyata ada website https://morsecode.scphillips.com/labs/decoder/ yang dapat mentranslate secara otomatis, penulis hanya perlu upload, play, dan menunggu proses translasi selama 33 detik.

Hasil akhir,
![image](https://raw.githubusercontent.com/redspiracy/write-ups/master/HackToday%202019/forensic/robot-o/screenshot/4.jpg)

## Flag
THE FLAG IS 8AE8CC93E223D5F957CE8B078D2020E7
> hacktoday{8AE8CC93E223D5F957CE8B078D2020E7}
