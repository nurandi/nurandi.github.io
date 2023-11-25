---
layout: post
slug: mengelola-r-package
title: Mengelola R Package
date: 2015-01-24 03:18:14.000000000 +07:00
published: true
status: publish
tags:
- R
excerpt: Bagaimana mengelola R package, misalnya melihat package apa saja yang sudah
  diinstal, memeriksa apakan suatu package sudah terinstal ?
redirect_from:
- /r/mengelola-r-package/
- /2015/01/24/mengelola-r-package/
---
Pada artikel [sebelumnya](http://nurandi.id/blog/menginstal-r-package/)
kita telah berdiskusi tentang fungsi-fungsi yang dapat digunakan untuk
instal R *package*, baik instal dari dari file lokal menggunakan
*wizard*, instal otomatis dari repositori CRAN dengan fungsi
`install.packages()` maupun instal dari GitHub menggunakan fungsi
`install_github()` dari *package* `devtools`.

Setelah *package* terinstal, biasanya kita berkeinginan untuk mengelola
R *packages* tersebut, misalnya untuk melihat *package* apa saja yang
sudah diinstal, memeriksa apakan suatu *package* sudah terinstal,
meng-*uninstall* *package* dan sebagainya. Berikut ini adalah beberapa
fungsi untuk mengelola R package yang saya kutip dari [R FAQ
ini](http://www.ats.ucla.edu/stat/r/faq/packages.htm).

## Memeriksa package apa saja yang sudah terinstal

Untuk melihat daftar *package* yang sudah terinstal gunakan fungsi
`installed.packages()`. Fungsi tersebut menghasilkan sebuah matriks yang
merinci *package* apa saja yang sudah terinstal pada R. Informasi yang
terdapat pada matriks itu antara lain:

-   **Package**, nama *package* yang terinstal
-   **LibPath**, *library*/folder di mana *package* terinstal
-   **Version**, versi dari *package*
-   **Depends**, *dependencies*

Gunakan fungsi `colnames()` untuk menampilkan semua nama kolom dan
`nrow()` untuk mengetahui jumlah *package* yang terinstal.

```r
x = installed.packages()
colnames(x)
nrow(x)
```

## Memeriksa apakah suatu package telah terinstal

Terkadang kita ingin mengetahui apakah suatu *package*, misalnya
`twitteR`, telah terinstal atau belum. Fungsi `is.element()` dapat
digunakan untuk tujuan tersebut.

```r
is.element("twitteR", x[,1])
```

di mana `x` adalah output dari fungsi `installed.packages()`. Jika sudah
terinstal, R *console* akan menjawab `TRUE`.

## Mengetahui package apa saja yang tersedia

Fungsi `available.packages()` menampilkan *package* apa saja yang
tersedia. Fungsi tersebut menghasilkan sebuah matriks yang merinci semua
*package* yang dapat kita instal dari repositori CRAN, dilengkapi dengan
informasi pendukung seperti *version*, *depends*, dan lain-lain. Sesaat
setelah saya jalankan fungsi berikut

```r
nrow(available.packages())
```

R console menampilkan angka **6147**. Artinya, tersedia lebih dari 6
ribu *package* di repositori CRAN yang dapat kita download/instal.
**Fantastis**.

## Menghapus package dari *library*

Berkebalikan dengan `install.packages()`, fungsi `remove.packages()`
berguna untuk menghapus (meng-*uninstall*) *package* dari *library*.

## Menampilkan informasi tentang suatu package

Dokumentasi tentang sebuah *package* yang **sudah terinstal** dapat
ditampilkan dengan fungsi `help()`. Misalnya:

```r
help(package="twitteR")
```

akan menampilan deskripsi serta fungsi dan data yang tersedia pada
*package* `twitteR`.

------------------------------------------------------------------------

Ada fungsi lain yang terlewatkan? Yuk, kita diskusikan :)
