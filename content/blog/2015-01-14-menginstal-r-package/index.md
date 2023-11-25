---
layout: post
slug: menginstal-r-package
title: Menginstal R Package
date: 2015-01-14 00:29:02.000000000 +07:00
published: true
status: publish
tags:
- r
excerpt: 'Sebagian besar R package belum terinstal secara default. Untuk dapat menggunakannya,
  terlebih dahulu kita harus menginstal R package.'
redirect_from:
- /r/menginstal-r-package/
- /2015/01/14/menginstal-r-package/
---
Salah satu kenapa R menjadi sangat popular adalah ketersediaan
paket/*packages* (kumpulan fungsi, data dan kode) R. Hingga hari ini,
ada lebih dari [6 ribu paket R ada di *repository*
CRAN](http://cran.r-project.org/). Belum termasuk paket yang disediakan
sumber lain. Jumlah paket tersebut terus tumbuh dari hari-ke-hari secara
[eksponensial](http://blog.revolutionanalytics.com/2010/09/what-can-other-languages-learn-from-r.html).

Pada saat instal R untuk pertama kali, hanya beberapa paket yang ikut
terinstal secara otomatis. Sementara paket lainnya harus kita instal
secara manual untuk bisa digunakan.

## Menginstal R package dari lokal

-   Dari halaman [CRAN](http://cran.r-project.org/), pilih paket yang
    diinginkan, misalnya paket
    [car](http://cran.r-project.org/web/packages/car/index.html)
-   Download *package source* (file *.zip*). Dalam contoh ini adalah
    file "car\_2.0-22.zip".
-   Pada RGUI, Klik menu `Packages`, lalu pilih
    `Install package(s) from local zip files...`
-   Pilih file *.zip* yang telah kita *download*
-   Jika berhasil, *R console* akan memberi pesan
    `package 'car' successfully unpacked and MD5 sums checked`.

## Menginstal R package langsung dari CRAN *repository*

Ketika sedang terhubung dengan internet, menginstal paket dapat langsung
dilakukan dari CRAN *repository*. Caranya sangat sederhanya, yaitu
dengan menggunakan fungsi :

```r
install.packages(pkgs)
```

`pkgs` adalah nama paket yang akan diinstal, bisa satu atau lebih (nama
paket bersifat *case-sensitive*). Misalnya untuk menginstal paket `car`,
jalankan perintah berikut pada *R console* :

```r
install.packages("car")
```

Lalu pilih *CRAN mirror*, misalnya "Indonesia (Jakarta)". Sama seperti
menginstal paket dari file lokal, akan muncul pesan
`package 'car' successfully unpacked and MD5 sums checked` pada *R
console*.

Untuk menginstal dua atau lebih paket dalam satu perintah, misalnya
paket `car` dan `ggplot2`, modifikasi fungsi `install.packages()`
menjadi :

```r
install.packages(c("car", "ggplot2"))
```

Kadang kala, paket yang kita instal mempunyai ketergantungan (misalnya
memanggil fungsi) terhadap paket lain. Tentu saja paket lain tersebut
harus kita instal. Dengan menambahkan opsi `dependencies = TRUE` pada
fungsi `install.packages()` maka R akan menginstal semua paket yang
dibutuhkan. (**KOREKSI** dari [Mas Suharto pada comment di
bawah](http://nurandi.id/blog/menginstal-r-package/#comment1), bahwa
*"Secara default, tanpa* `dependencies=TRUE`*,* `install.packages` *juga
meng-install paket lain yang dibutuhkan. Namun, paket yang termasuk
“Suggests” (artinya paket itu digunakan, tetapi tanpa paket itu pun bisa
jalan) tidak ikut di-install"*).

```r
install.packages(c("car", "ggplot2"), dependencies=TRUE)
```

Pada *R console* akan muncul info :
`also installing the dependencies ‘effects’, ‘minqa’, ‘nloptr’, ....`.

Setelah proses instalasi selesai dilakukan, *load* (muat) paket ke dalam
*R session* dengan fungsi `library()`, misalnya :

```r
library(car)
```

Dan .... paket `car` siap digunakan. Sangat mudah bukan ?

## Menginstal R package dari [GitHub](https://github.com/)

Selain dari CRAN, kita juga bisa menginstal paket (pada umumnya versi
*developer*) dari [GitHub](https://github.com/) menggunakan fungsi
`install_github()`. Fungsi tersebut ada pada paket `devtools`. Berikut
tahapan instal paket dari GitHub:

-   Instal paket `devtools` dari CRAN (jika belum terinstal), dan *load*
    ke dalam *R session*

    ```r
    install.packages("devtools")
    library(devtools)
    ```

-   Jalankan perintah `install_github(package, username)` atau
    `install_github(username/package)` di mana `package` adalah nama
    paket dan `username` adalah user name. Misalnya :

    ```r
    install_github("wch/ggplot2")
    ```

    Atau

    ```r
    install_github("ggplot2", "wch")
    ```

    Atau, untuk instal banyak paket sekaligus

    ```
    install_github(c("geoffjentry/twitteR", "wch/ggplot2"))
    ```

Selamat mencoba ... :)
