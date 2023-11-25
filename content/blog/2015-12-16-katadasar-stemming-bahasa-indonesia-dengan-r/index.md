---
layout: post
slug: katadasar-stemming-bahasa-indonesia-dengan-r
title: 'katadasaR : Stemming Bahasa Indonesia dengan R'
date: 2015-12-16 23:35:52.000000000 +07:00
published: true
status: publish
tags:
- R
excerpt: katadasaR adalah package untuk stemming bahasa indonesia dengan R menggunakan
  algoritma Nazief dan Andriani yaitu dengan menghapus imbuhan
redirect_from:
  - /r/katadasar-stemming-bahasa-indonesia-dengan-r/
  - /2015/12/06/katadasar-stemming-bahasa-indonesia-dengan-r/
---
*Stemming* merupakan proses menemukan kata dasar *(root word)* dari kata
berimbuhan *(affixed word)* dengan cara menghilangkan semua imbuhan
*(affix)* yang terdiri dari awalan *(prefix)*, sisipan *(infix)*,
akhiran *(suffix)* dan kombinasi awalan dan akhiran *(confix)*. Detail
kata berimbuhan dalam bahasa Indonesia dan proses pembentukannya bisa
dilihat pada [artikel ini](http://indodic.com/affixeng.html). Dalam
*text analytics*, *stemming* merupakan salah satu proses penting yang
sangat mempengaruhi kualitas hasil analisis. Ada banyak algoritma yang
digunakan untuk melakukan proses *stemming*, diantaranya algoritma
Nazief dan Andriani dan algoritma Porter.

<figure>
  <img src="image/root words.jpg">
  <figcaption>Illustration from k5learning.com</figcaption>
</figure>
  
Di internet, banyak sekali informasi terkait *stemming* bahasa Indonesia
bahkan source code-nya pun tersedia. Silakan
[Googling](https://www.google.co.id/search?q=stemming+bahasa+indonesia)
untuk infomasi lebih lengkap :) Namun, tidak demikian dengan R. Saya
kesulitan untuk untuk menemukan R *package* untuk keperluan *stemming*
bahasa Indonesia. Karena itulah saya membuat *package* `katadasaR`.

`katadasaR` adalah *package* yang berisi fungsi untuk *stemming* bahasa
Indonesia dengan R menggunakan algoritma Nazief dan Andriani. Ini
[**bukanlah karya saya**]{.highlight} sepenuhnya sebab saya hanya
menulis ulang kode *C-Sharp* dari blog
[csharp-indonesia.com](http://www.csharp-indonesia.com/2014/07/algoritma-stemming-pencarian-kata-dasar.html)
ke dalam bahasa R.

Fitur
-----

`katadasaR` melakukan *stemming* kata-per-kata dengan mengacu pada kamus
kata dasar yang secara *default* sudah tersedia pada *package*.

`katadasaR` dilengkapi fungsi untuk menghapus imbuhan berikut:

-   Awalan, seperti *bertemu*, *dimakan*
-   Akhiran, seperti *makanan*, *miliknya*
-   Kombinasi awalan dan akhiran, seperti *pertemuan, mempermainkan*

Namun, untuk saat ini `katadasaR` belum bisa digunakan untuk:

-   Menghapus sisipan, misalnya *er* dari *seruling*, *el* dari
    *selidik*
-   Menghapus sisipan kata serapan, misalnya *isme* dari *nasionali sme*
-   Menghapus imbuhan dari pengulanan kata, misalnya *bermain-main,
    bermaaf-maafan*
-   Menambahkan kamus atau list kata dasar (dalam pengembangan)
-   Dan lain-lain (infokan berbagai *bugs* di
    [Github](https://github.com/nurandi/katadasaR))

Instalasi
---------

Saat ini masih dalam tahap pengembangan dan hanya tersedia di [repo
Github](https://github.com/nurandi/katadasaR). Untuk instal *package*
`katadasaR`, silakan gunakan fungsi `devtools::install_github()`
berikut:

```r
# install_packages("devtools")
library(devtools)
install_github("nurandi/katadasaR")
```

Penggunaan
----------

`katadasaR` hanya mampu melakukan *stemming* terhadap satu kata, bukan
frase atau kalimat. Untuk *stemming* kalimat, silakan split kalimat
menjadi kata-kata lalu gunakan fungsi `katadasaR` dalam loop. Contoh:

```r
library(katadasaR)

katadasaR("makanan")

# output:
# [1] "makan"

words <- c("jakarta", "seminar", "penggunaan", "menggurui", "pelajaran", "dimana")
sapply(words, katadasaR)

# output
#    jakarta    seminar penggunaan  menggurui  pelajaran     dimana 
#  "jakarta"  "seminar"     "guna"     "guru"     "ajar"     "mana" 
```

### Acknowledgement

[csharp-indonesia.com](http://www.csharp-indonesia.com/2014/07/algoritma-stemming-pencarian-kata-dasar.html)

------------------------------------------------------------------------

***Semoga bermanfaat*** :)
