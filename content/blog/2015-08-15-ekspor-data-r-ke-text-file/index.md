---
layout: post
slug: ekspor-data-r-ke-text-file
title: Ekspor Data R ke Text File
date: 2015-08-15 12:42:35.000000000 +07:00
published: true
status: publish
tags:
- R
redirect_from:
- /r/ekspor-data-r-ke-text-file/
- /2015/08/15/ekspor-data-r-ke-text-file/
excerpt: Beberapa waktu lalu, di kolom komentar dalam blog ini, ada yang nanya bagaimana caranya ekspor data dari R ke file CSV (comma separated values). Oke, di sini akan saya jelaskan.
---
Beberapa waktu lalu, di kolom
[komentar](https://nurandi.id/blog/twitter-mengolah-data-twitter-hasil-crawling/#comment1)
dalam blog ini, ada yang nanya bagaimana caranya ekspor data dari R ke
file CSV *(comma separated values)*. Oke, di sini akan saya jelaskan.

Ekspor data dari R ke file teks (seperti CSV, *tab separated value*, dan
lain-lain) sangatlah mudah. Ada beberapa fungsi yang biasa saya gunakan,
di antaranya `write.csv()` dan `write.table`. Keduanya ada pada
*package* ~~base~~ `utils` yang merupakan *package base* (bawaan)
sehingga kita tidak perlu repot-repot menginstal *package* tambahan.
Mari kita lihat satu per satu.

## Fungsi `write.csv()`

Digunakan untuk ekspor (menulis) *data frame* atau *matrix* ke file teks
dengan format *comma separated value*. Maksudnya, setiap kolom dalam
file ini dipisahkan oleh koma. Misalnya, kita akan mengekspor *data
frame* `mydf` ke file `myfile.csv`, maka kita bisa gunakan fungsi
sebagai berikut:

```r
write.csv(mydf, file="myfile.csv")
```

Secara *default*, `write.csv` akan menambahkan `row.names` pada kolom
pertama. Jika tidak diperlukan, kita dapat tambahkan parameter
`row.names=FALSE` ke dalam fungsi di atas, sehingga menjadi:

```r
write.csv(mydf, file="myfile.csv", row.names=FALSE)
```

Ada banyak parameter yang bisa digunakan. Silakan jalankan `?write.csv`
pada *R console*. :)

## Fungsi `write.table()`

Dalam hal kegunaan dan cara menggunakannya, fungsi ini hampir sama
seperti `write.csv()`. Hanya saja pemisah kolom yang digunakan pada
`write.table()` secara *default* adalah ~~tab~~ spasi. Dibandingkan
dengan `write.csv()`, fungsi `write.table()` mempunyai banyak parameter.
Saya biasanya menggunakan fungsi ini saat akan menambahkan baris-baris
data baru ke file yang sudah ada *(append)*, dengan menambahkan
parameter `append=TRUE`.

Bagaimana, mudah bukan ...?

Jika ada yang kesulitan, mari kita diskusikan bersama :)
