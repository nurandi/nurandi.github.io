---
layout: post
slug: crawling-data-twitter-menggunakan-r
title: 'twitteR: Crawling Data Twitter Menggunakan R'
date: 2015-01-31 01:58:34.000000000 +07:00
published: true
status: publish
excerpt: Dalam artikel ini kita akan berdiskusi tentang bagaimana mengambil
  atau crawling data Twitter menggunakan R memnafaatkan API yang disediakan oleh Twitter.
tags:
- R
- twitter
redirect_from:
- /sosmed/crawling-data-twitter-menggunakan-r/
- /2015/01/31/crawling-data-twitter-menggunakan-r/
---
Analisis terhadap media/jejaring sosial (*social media analytics*)
adalah alat yang ampuh untuk memahami sikap, preferensi dan opini publik
di berbagai sumber online. Bagi sebuah organisasi atau perusahaan,
analisis media sosial dapat memberikan keunggulan atas pesaing mereka
melalui pengetahuan menyeluruh tentang bagaimana produk dan layanan
mereka dirasakan oleh pelanggan atau calon pelanggan potensial. Analisis
media sosial memungkinkan organisasi dan perusahan untuk membuat
keputusan yang cerdas mengenai kebutuhan, sikap, pendapat, tren terbaru
dan berbagai faktor yang mempengaruhi pelanggan (dari
[socialmediadata.com](http://socialmediadata.com/the-importance-understanding-social-media-analytics/)).

<figure>
  <img src="image/twitter-background.jpg">
  <figcaption>Credit: thedesk.matthewkeys.net</figcaption>
</figure> 

   
Kabar baiknya, data dari berbagai media sosial telah tersedia, walaupun
masih berada di "awan". Media sosial terbesar di dunia,
[Twitter](https://dev.twitter.com/overview/api) dan
[Facebook](https://developers.facebook.com/docs/graph-api), misalnya,
menyediakan *API/application program interface* yang memungkinkan kita
mendapatkan data mereka. Begitu juga dengan
[Google+](https://developers.google.com/+/api/),
[Instagram](http://instagram.com/developer/) dan
[Path](https://path.com/developers/docs). Yang jadi pertanyaan,
bagaimana kita bisa dapatkan data tersebut? Untuk menjawab pertanyaan
tersebut lah artikel ini saya tulis.

Dalam artikel ini kita akan berdiskusi tentang bagaimana mengambil atau
*crawling* data Twitter menggunakan R dengan memanfaatkan
[API](https://dev.twitter.com/rest/public). Untuk media lainnya
(Facebook, Instagram, dan lain-lain), *Insya Allah* akan kita bahas pada
kesempatan lain.

## Instal *package* `twitteR`

**`twitteR`** (ditulis oleh
[geoffjentry](https://github.com/geoffjentry/twitteR)) adalah *R
package* yang menyediakan akses ke API Twitter sehingga memungkinkan
kita melakukan *crawling* data Twitter menggunakan R. Instal versi
terbaru `twitteR` dari GitHub menggunakan *package* `devtools` (jika
belum).

```r
library(devtools)
install_github("twitteR", username="geoffjentry")
library(twitteR)
```

Lebih lengkap tentang bagaimana menginstall *package* `twitteR` dapat
dilihat pada [artikel
ini](http://nurandi.id/blog/twitter-authentication-dengan-r/).

## Twitter authentication

Agar R dapat digunakan untuk mengekstrak data Twitter, terlebih dahulu
kita harus mengirim sebuah *secure authorized requests* ke Twitter API.
Proses ini dapat dilakukan dengan fungsi berikut:

```r
api_key = "isi dengan API key"
api_secret = "isi dengan API secret"
access_token = "isi dengan Access token"
access_token_secret = "isi dengan Access token secret"

setup_twitter_oauth(api_key, api_secret, access_token, access_token_secret)
```

Kita harus mempunyai Twitter *application* untuk mendapatkan *API key,
API secret, access token* dan *access token secret*. Bagaimana membuat
Twitter *application* dan mendapatkan token bisa dilihat pada [artikel
ini](https://www.nurandi.id/blog/twitter-authentication-dengan-r/).

Ketika fungsi dijalankan `setup_twitter_oauth()`, *R console* akan
menanyakan:
`Use a local file to cache OAuth access credentials between R sessions?`.
Biasanya saya jawab `2`.

Oke, pada tahapan ini kita sudah mendapatkan *secure connection* ke
Twitter API dan R siap untuk mengambil data.

## *Search Twitter*

Untuk mendapatkan tweet yang berisi kata (*keyword*) tertentu, misalnya
"bogor", dapat dilakukan dengan fungsi berikut:

```r
tw.bogor = searchTwitter("bogor")
```

Secara *default*, fungsi tersebut akan mengambil 25 tweet yang berisi
kata "bogor". Berikut adalah lima tweet pertama pada objek `tw.bogor`:

    [[1]]
    [1] "JoinTupperware: Visit http://t.co/TlhtJHHlDG untuk cek detail tentang Tupperware Bogor, Info & Order [PIN:29369518 - WA: 081297139078] #TupperwareBogor"

    [[2]]
    [1] "PolresBogorKota: Jadwal SIM Keliling Kota Bogor, Sabtu 31/1/2015, Lokasi Grha Pena Radar Bogor Taman Yasmin, Pkl. 09:00-12:00 WIB @RTMC_PoldaJabar"

    [[3]]
    [1] "rezadzulfikar: "SECOND CHANCE" - EXPO2015 - (at MAN 2 Bogor) [pic] — https://t.co/T8hEzpf4nL"

    [[4]]
    [1] "Tupperware_Info: Mau beli Produk Tupperware di Bogor secara Retail? hubungi [PIN:29369518 - WA: 081297139078] #TupperwareBogor"

    [[5]]
    [1] "KiteIPB: RT @Fithriyyah_27: KPMKB adlh organisasi mahasiswa KalBar yg berdomisili di Bogor @KiteIPB #MakeAChoice"

## *Get Users*

Untuk lihat profil satu akun twitter, misalnya nama, deskripsi, jumlah
*follower* dan *following* dan jumlah tweet, gunakan fungsi `getUser()`:

```r
user = getUser("nurandi")
```

"nurandi" bisa diganti dengan akun twitter atau twitter ID.

Misalnya, saya ingin melihat siapa saja *followers* dari "nurandi" :

```r
user$getFollowers()
```

Outputnya (lima pertama):

    $`3004628621`
    [1] "ayusri884"

    $`2994157963`
    [1] "putra_tasal"

    $`2941365294`
    [1] "Sneak_Pro"

    $`980172871`
    [1] "Imatumi1"

    $`2733762661`
    [1] "LaluMahsar"

`getUser()` hanya dapat digunakan untuk melihat profil *satu* akun. Jika
banyak akun, gunakan fungsi `lookupUsers()`. Contohnya:

```r
users = lookupUsers(c("nurandi","prabowo08","jokowi_do2"))
```

## *Timeline*

Kita juga bisa meng-*crawl* *timeline* atau status twitter dari satu
atau beberapa akun. Caranya :

```r
user.tl = userTimeline("nurandi")
```

Lima baris pertama objek `user.tl` adalah sebagai berikut:

    [[1]]
    [1] "nurandi: Membuat Word Cloud dengan R http://t.co/mSfd9QBjKi"

    [[2]]
    [1] "nurandi: Mengelola R Package http://t.co/JBA0K84bnk"

    [[3]]
    [1] "nurandi: @cygyt @lenimarlena waktu lu ke bali ga ngajak2 :D"

    [[4]]
    [1] "nurandi: Rabuan... @cygyt , @lenimarlena http://t.co/2c2bgeJCGs"

    [[5]]
    [1] "nurandi: @HimalkomIPB ada yg bisa jadi mentor programing with Python? Saya ingin belajar dan butuh mentor. Thanks"

Fungsi-fungsi yang kita diskusikan tadi merupakan fungsi dasar dan
sebenarnya masih banyak parameter yang bisa ditambahkan. Untuk
mengetahui fungsi-fungsi tersebut secara detail termasuk parameter yang
bisa digunakan, ketik `?` diikuti nama fungsi pada *R console*.

> **UPDATE** (16 Jan 2019): Pada pertengahan 2016, [Jeff Gentry](https://github.com/geoffjentry/twitteR), sang pengembang, menghentikan pengembangan dan *update/maintenance* *package* **twitteR**. Meskipun masih bisa digunakan (setidaknya sampai saat ini), Mr. Jeff merekomendasikan untuk beralih menggunakan *package* lain yang tidak kalah kerennya, yaitu [**rtweet**](https://cran.r-project.org/web/packages/rtweet/index.html). Bagaimana menggunakan *package* **rtweet** untuk *crawling* data Twitter sudah saya jabarkan pada artikel [**rtweet: Crawling Data Twitter Menggunakan R**](https://www.nurandi.id/blog/rtweet-crawling-data-twitter-menggunakan-r/)."



Selamat, kita sudah berhasil mendapatkan tweet yang berisi kata-kata
tertentu, informasi/profil akun twitter dan timeline/status. Mudah
bukan?

Lalu bagaimana selanjutnya? Apakah bisa kita mendapatkan informasi
detail seperti kapan tweet tersebut di *posting*, ada berapa jumlah
*follower* dari akun yang mem-*posting* tweet tertentu? Atau, bisa tidak
kita simpan hasil *crawling* tersebut ke file .csv misalnya? Tentu saja
bisa. Yang harus kita lakukan adalah melakukan sedikit manipulasi data.
Silakan simak artikel [berikut](https://www.nurandi.id/blog/twitter-mengolah-data-twitter-hasil-crawling/) :)


