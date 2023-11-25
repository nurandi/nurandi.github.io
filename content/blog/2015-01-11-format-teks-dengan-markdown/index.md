---
layout: post
slug: format-teks-dengan-markdown
title: Format Teks dengan Markdown
date: 2015-01-11 10:32:30.000000000 +07:00
published: true
status: publish
tags:
- markdown
excerpt: Markdown merupakan alternatif untuk menulis konten web tanpa tag HTML,
  yaitu dengan hanya menuliskan teks biasa ditambah simbol-simbol yang umum digunakan.
redirect_from:
- /blogging/format-teks-dengan-markdown/
- /2015/01/11/format-teks-dengan-markdown/
---
Markdown adalah tools konversi *text-to-HTML*. Dengan Markdown, kita
dapat menulis sebuah teks dengan mudah dan mengkonversinya menjadi
HTML/XHTML. Markdown dibuat oleh [John
Gruber](http://daringfireball.net/projects/markdown/) sejak tahun 2004
dan terus dikembangkan hingga sekarang.

Markdown merupakan alternatif untuk menulis konten untuk web maupun blog
tanpa menuliskan tag HTML, yaitu dengan hanya menuliskan teks biasa
ditambah simbol-simbol yang umum digunakan seperti *asterisks* (`*`),
*undescore* (`_`), tanda lebih kecil/besar (`< >`), dan lain-lain.
Dengan demikian selain lebih sederhana, Markdown sangat mudah ditulis
dan juga dibaca. Untuk menulis teks dalam format *italic* (cetak
miring), misalnya, kita hanya perlu menggunakan *asterisks* (`*`), tidak
lagi menggunakan tag HTML `<i>`.

![markdown](image/markdown.png)


Karena sangat mudah digunakan, Markdown menjadi salah satu standar dalam
penulisan dokumen untuk akademik, *scientist* dan lainnya. Website
seperti [GitHub](https://github.com/), [reddit](http://www.reddit.com/),
[stackoverflow](http://stackoverflow.com/) dan
[SourceForge](http://sourceforge.net/) telah menggunakan Markdown untuk
*formatting* konten maupun komentar.

Markdown dapat digunakan pada [Wordpress](https://wordpress.org) dengan
terlebih dahulu menginstal dan mengaktifkan plugin
[WP-Markdown](https://wordpress.org/plugins/wp-markdown/) atau
[JetPack](https://wordpress.org/plugins/jetpack/).

## Bagaimana menggunakan Markdown?

Berikut adalah contoh bagaimana menata format teks dengan Markdown.

Header
==========

    # Header 1
    ## Header 2
    ...
    ###### Header 6

    Atau
    Alternatif Header 1
    ===================
    Alternatif Header 2
    -------------------

Output:

Header 1
========

Header 2
--------

...

###### Header 6

Atau

Alternatif Header 1
===================

Alternatif Header 2
-------------------

# Emphasis

    Miring atau italic : *asterisks* atau _underscores_.
    Tebal atau bold : double **asterisks** atau __underscores__.
    Miring dan tebal : **asterisks dan _underscores_**.

Output:

Miring atau italic : *asterisks* atau *underscores*.<br/>
Tebal atau bold : double **asterisks** atau **underscores**.<br/>
Miring dan tebal : ***asterisks* dan *underscores***.<br/>

# Code

Untuk code dalam teks (*inline*), gunakan *single-backtick* ```,
misalnya:

    Ini huruf `monospace`

Output:

Ini huruf `monospace`

Sedangkan jika terdiri dari beberapa baris, gunakan *triple-backticks*, misalnya:

    ```
    install.packages("twitteR")
    library(twitteR)
    ```

Output:

    install.packages("twitteR")
    library(twitteR)
    
Atau beri empat spasi sebelum kode:

```
    install.packages("twitteR")
    library(twitteR)
```

# List

    1. Pertama
      1. Sub-item
      2. Sub-item lagi.
    2. Kedua
        * Bisa dengan asteriks.
        - Bisa juga dengan minus.
        + Atau dengan plus.
    3. Ketiga

Output:

1.  Pertama
    1.  Sub-item
    2.  Sub-item lagi.
2.  Kedua
    -   Bisa dengan asteriks.
    -   Bisa juga dengan minus.
    -   Atau dengan plus.
3.  Ketiga

# Quote

    > "Dan sebaik-baik manusia adalah orang yang paling bermanfaat bagi manusia." *(HR. Thabrani dan Daruquthni)*

Output:

> Dan sebaik-baik manusia adalah orang yang paling bermanfaat bagi
> manusia. *(HR. Thabrani dan Daruquthni)*

# Link

    <https://www.google.com>
    [Google](https://www.google.com)
    [Google](https://www.google.com "Google's Homepage")

Output:

<https://www.google.com><br/>
[Google](https://www.google.com)<br/>
[Google](https://www.google.com "Google's Homepage")

# Image

    ![Logo R](www.r-project.org/Rlogo.jpg)

Output:

![Logo R](//www.r-project.org/Rlogo.jpg)

Sintaks lengkapnya bisa diperoleh di
[daringfireball.net](http://daringfireball.net/projects/markdown/syntax).
Contoh penggunaan Markdown bisa dilihat pada halaman GitHub
[ini](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
dan [ini](https://help.github.com/articles/markdown-basics/), atau
[Googling](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=markdown).
Selamat mencoba.

------------------------------------------------------------------------

## R Markdown — Dynamic Documents for R

[R Markdown](http://rmarkdown.rstudio.com/) adalah paket R yang
mengkombinasikan sintaks Markdown dengan kode R untuk membuat dokumen,
presentasi dan laporan dengan R.

![markdown-chunk]({{ site.url }}{{ site.baseurl }}image/markdownChunk.png)

Tunggu tutorial bagimana menggunakan R Markdown. Karena, *honestly*,
saya juga belum pernah menggunakannya. :)
