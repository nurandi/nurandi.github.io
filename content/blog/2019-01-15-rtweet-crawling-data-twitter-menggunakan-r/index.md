---
title: 'rtweet: Crawling Data Twitter Menggunakan R'
slug: rtweet-crawling-data-twitter-menggunakan-r
author: "Nur Andi Setiabudi"
date: "2019-01-15"
layout: post
published: yes
share-img: image/twitter-techgladscom.png
status: publish
tags:
- r
- twitter
- rtweet
excerpt: rtweet adalah R package yang berguna untuk menambang atau crawling data Twitter, baik berupa data historis (REST API) ataupun data realtime (stream API). Beberapa fungsi yang tersedia diantaranya search_tweets, get_timelines, get_followers dan get_friends.
---

Sekitar empat tahun lalu, saya menulis artikel dengan judul yang hampir sama persis: [twitteR: Crawling Data Twitter Menggunakan R](https://www.nurandi.id/blog/crawling-data-twitter-menggunakan-r/). Pada saat itu, cara paling mudah untuk mendapatkan *(crawling)* data dari Twitter dengan R adalah menggunakan *package* [**twitteR**](https://cran.r-project.org/web/packages/twitteR/). Sayangnya pada pertengahan 2016, [Jeff Gentry](https://github.com/geoffjentry/twitteR), sang pengembang, menghentikan pengembangan dan *update/maintenance* terhadap *package* tersebut. Meskipun *package* **twitteR** masih bisa digunakan (setidaknya sampai saat ini), Mr. Jeff merekomendasikan untuk beralih menggunakan *package* lain yang tidak kalah kerennya, yaitu [**rtweet**](https://cran.r-project.org/web/packages/rtweet/index.html). 

{% include image url="image/twitter-techgladscom.png" alt="twitter-techgladscom.png" caption="Twitter. Kredit: techglads.com" %}

[**rtweet**](https://rtweet.info) dikembangkan oleh [Michael W. Kearney](https://github.com/mkearney). Versi pertama dirilis pada Agustus 2018, atau hampir bersamaan dengan pengumuman penghentian pengembangan **twitteR**. Sesuai dengan deskripsinya, **rtweet** berfungsi untuk mengakses API Twitter melalui R. Jika **twitteR** hanya mampu digunakan untuk berinteraksi dengan REST API (untuk mencari "data" historis atau yang sudah lampau), **rtweet** ini bisa juga digunakan untuk mengakses stream API *(live/realtime)*. Pada artikel ini kita bahas untuk REST API dulu ya.

# Instalasi

Ada dua cara, yaitu install dari [CRAN](https://cran.r-project.org/web/packages/rtweet/index.html) (untuk versi rilis terkini) dan dari [Github](https://github.com/mkearney/rtweet) (untuk versi *development*/pengembangan).

### Opsi 1: dari CRAN


```r
install.packages("rtweet")
```

### Opsi 2: dari Github

Note: install dari Github memerlukan *package* **devtools**


```r
install.packages("devtools")
library(devtools)
```


```r
install_github("mkearney/rtweet")
```

Setelah terinstal, *load package* **rtweet**


```r
library(rtweet)
```

Pelajari lebih lengkap tentang instalasi *package* R melalui artikel [Menginstal R Package](https://www.nurandi.id/blog/menginstal-r-package/).


# API Authorization

Untuk mengakses API Twitter diperlukan akun Twitter, aplikasi *(application)* dan token akses *(access token)*. Silakan pelajari langkah-langkah membuat aplikasi dan mendapatkan token akses pada artikel [Twitter Authentication dengan R](https://www.nurandi.id/blog/twitter-authentication-dengan-r/). 

{% include image url="image/ilustrasi-app-token.png" alt="ilustrasi-app-token.png" caption="Ilustrasi Twitter App yang menampilkan keys dan tokens" %}

**Note:** Pada Juli 2018, Twitter [mengubah kebijakan](https://blog.twitter.com/developer/en_us/topics/tools/2018/new-developer-requirements-to-protect-our-platform.html) terkait registrasi aplikasi baru. Kini, untuk membuat aplikasi harus menggunakan *developer account*. Saat melakukan registrasi, Twitter meminta kita untuk menjelaskan secara lebih detail hal-hal terkait dengan aplikasi. Selanjutnya Twitter akan meninjau, apakah aplikasi disetujui atau tidak. Kabar baiknya, aplikasi-aplikai yang didaftarkan sebelum diterapkannya kebijakan baru ini masih bisa digunakan. *Setidaknya sampai hari ini*. Info selengkapnya [di sini](https://blog.twitter.com/developer/en_us/topics/tools/2018/new-developer-requirements-to-protect-our-platform.html).

Setelah membuat aplikasi, proses selanjutnya adalah otentikasi *(authentication)* agar R melalui **rtweet** bisa berinteraksi dengan API Twitter. Proses ini ada dua opsi, yaitu (1) melalui browser dan (2) dengan *access token*. Keduanya dengan menggunakan fungsi `create_token()`. Silakan pilih salah satu.

### Opsi 1: Otentikasi melalui browser

Opsi ini membutuhkan:

* *Consumer API key*
* *Consumer API **secret** key*


```r
token <- create_token(
  consumer_key = "XXXXXk4gFgjuI6hx05zwaGh0Q",
  consumer_secret = "XXXXXCPPWEBJhYS4Z7rDVGZt3y1dkVehTnSe7wsM0NKVWBDLoV")
```

R otomatis akan membuka *browser*, meminta *login* ke Twitter dan menampilkan pesan:

```
Authentication complete. Please close this page and return to R.
```

Ini berarti otentikasi berhasil.


### Opsi 2: Otentikasi dengan *access token/secret*

Selain *consumer API dan API secret key*, opsi ini juga membutuhkan:

* *Access token*
* *Access token secret*


```r
token <- create_token(
  consumer_key = "XXXXXk4gFgjuI6hx05zwaGh0Q",
  consumer_secret = "XXXXXCPPWEBJhYS4Z7rDVGZt3y1dkVehTnSe7wsM0NKVWBDLoV",
  access_token = "XXXXX937-XXXXXu6x7wR70mUAWc1BCc7gJIOdfugps6iFgU6GO",
  access_secret = "XXXXXkUqEsfnfsy31GSLIEyaiDLB0UC2rw8EP1gNaLTc7")
```

Jika tidak muncul *error*, berarti proses otentikasi sukses.


**Note:** *Consumer API key*, *consumer API secret key*, *access token* dan *access token secret* di atas hanya contoh, silakan ganti dengan nilai yang sesuai.


# Crawling Data Twitter

Setelah proses otentikasi berhasil, kita siap untuk "menambang" data *(crawling)* dari Twitter. Untuk kepeluan ini, **rtweet** mengemas berbagai fungsi yang cukup lengkap, di antaranya:

* `search_tweets()` : mencari *tweet* dengan kata kunci tertentu
* `lookup_users()` : menampilkan data detail dari satu atau lebih *user(s)* 
* `get_timelines()` : menampilkan *status/tweet* yang pernah diposting oleh user tertentu aka *timeline*
* `get_followers()` : menampilkan *list followers* dari user tertentu
* `get_friends()` : menampilkan *list fiends/followings* atau yang di-follow user tentu
* dan masih banyak yang lain seperti untuk menampilkan *retweet*, siapa yang me-*retweet*, mendapatkan *list favorite*, menampilkan *trending topics*, *cleansing* *tweet*, dan juga ekspor data ke *file csv*.

### `search_tweets()`

Mencari *tweet* yang memuat kata kunci tertentu. Secara *default*, dalam sekali dijalankan akan memberikan **MAKSIMAL** 18.000 *tweets*, yang merupakan sampel dari historis **tujuh hari terakhir**. Bagaimana kalau ingin lebih dari 18 ribu atau lebih dari tujuh hari? Salah satu caranya dengan membuat akun *premium* atau *entreprise*. Lebih lengkap tentang ketentuan ini bisa dibaca di [sini](https://developer.twitter.com/en/docs/tweets/search/overview).

{% include image url="image/search-tweet.png" alt="search-tweet.png" caption="Ilustrasi search pada Twitter" %}

**Contoh**: mencari (hingga) 1.000 *tweets* yang memuat kata kunci [**"bogor"**](https://twitter.com/search?l=&q=bogor&src=typd&lang=en)


```r
tweet <- search_tweets(q = "kota bogor", n = 1000)
```



Objek **tweet** merupakan *data frame/tibble* dengan lebih dari 80 variabel/kolom. Silakan tampilkan dengan perintah:


```r
colnames(tweet)
```

Kita lihat tiga kolom di antaranya:


```r
tweet[,c("created_at", "screen_name", "text")]
```



```
## # A tibble: 994 x 3
##    created_at          screen_name   text                                  
##    <dttm>              <chr>         <chr>                                 
##  1 2019-01-16 12:35:57 hasnahahahah~ @CommuterLine kereta terakhir dr jkt ~
##  2 2019-01-16 12:35:20 iffa_retno69  "#dadakan #padangsidempuan #sidikalan~
##  3 2019-01-13 19:09:55 iffa_retno69  "#padangsidempuan #sidikalang #tebing~
##  4 2019-01-16 12:11:59 terrotouli    Kekurangan dari pembangunan infrastru~
##  5 2019-01-16 12:10:53 aininur56     5. Sekretaris PCNU Kota Bogor ini men~
##  6 2019-01-16 12:07:08 CommuterLine  @imbecillic Selamat malam, untuk pemb~
##  7 2019-01-13 14:36:10 CommuterLine  #InfoLintas : KA 1460 (Jakarta Kota-B~
##  8 2019-01-13 12:46:36 CommuterLine  @itsmeanggii Cikarang-Jakarta Kota te~
##  9 2019-01-15 13:34:21 CommuterLine  @ditakswn Selamat malam. Kami informa~
## 10 2019-01-15 16:41:12 CommuterLine  @CahyoWarih Selamat malam. Dapat kami~
## # ... with 984 more rows
```

*Keyword/query* dapat berupa satu atau beberapa kata, namun hindari *keyword* yang kompleks. Menurut Twitter, query sebaiknya tidak lebih dari 10 kata. Berikut contoh cara penulisan *keyword*:

| Query              | Untuk mencari tweet yang mengandung:                        |
|--------------------|-------------------------------------------------------------|
| `"bogor"`          | kata "bogor"                                                |
| `"kota bogor"`     | kata "kota" dan kata "bogor" (tidak memperhatikan   urutan) |
| `"\"kota bogor\""` | frase "kota bogor"                                          |
| `"kota OR bogor"`  | kata "kota" atau kata "bogor" atau keduanya                 |
| `"kota -bogor"`    | kata "kota" tapi tidak memuat kata "bogor"                  |
| `"#bogor"`         | *hashtag* "bogor"                                           |
| `"@bogor"`         | *mention* "bogor"                                           |


### `get_timelines()`

Menampilkan *timeline* atau *status/tweet* **terbaru** yang pernah di-*posting* oleh satu atau beberapa *user(s)*. Setiap *user* akan ditampilkan hingga maksimal 3.200 *tweet*.

**Contoh**: Menapilkan *timeline* dari [**"nurandi"**](https://twitter.com/nurandi)


```r
timeline <- get_timeline("nurandi")
```

Objek yang dihasilkan berupa *data frame/tibble* dengan lebih dari 80 kolom. 


```r
timeline[,c("created_at", "source", "text")]
```



```
## # A tibble: 100 x 3
##    created_at          source        text                                  
##    <dttm>              <chr>         <chr>                                 
##  1 2019-01-16 03:23:37 Twitter Web ~ https://t.co/b5KGxX56L5               
##  2 2019-01-07 04:51:17 IFTTT         New blog article: Apa Itu Jekyll dan ~
##  3 2018-12-13 23:53:31 Twitter for ~ "Semoga kita bukan termasuk orang yan~
##  4 2018-09-18 12:47:30 Twitter for ~ aku kalo ke ATM lebih takut keliatan ~
##  5 2018-08-17 01:37:24 Twitter Web ~ "@Telkomsel Hi, saya berlangganan pak~
##  6 2018-07-05 01:55:25 Twitter for ~ @IndonesiaGaruda halo, GA-824 CGK-SIN~
##  7 2018-06-02 16:51:35 Twitter for ~ "English: i know i supposed to meet y~
##  8 2018-04-23 04:54:58 Twitter for ~ @imigrasi_jakbar Min, saya sudah daft~
##  9 2018-04-04 08:37:53 Twitter for ~ @TokopediaCare verifikasi pembayatan ~
## 10 2018-03-13 04:42:15 Twitter for ~ @BPJSTKinfo Terima kasih infonya. BPJ~
## # ... with 90 more rows
```


### `lookup_users()`

Menampilkan data detail dari satu atau lebih akun/*user(s)*.

**Contoh**: Menampilkan detail dari [**"nurandi"**](https://twitter.com/nurandi)



```r
user <- lookup_users("nurandi")
```

Objek yang dihasilkan juga merupakan *data frame/tibble* dengan lebih dari 80 kolom.


```r
user[,c("created_at", "screen_name", "name", "location", "description")]
```



```
## # A tibble: 1 x 5
##   created_at          screen_name name        location     description     
##   <dttm>              <chr>       <chr>       <chr>        <chr>           
## 1 2019-01-16 03:23:37 nurandi     Nur Andi S~ Jakarta/Bog~ Aspiring Data S~
```

Untuk *lookup* beberapa *user(s)*, input harus berupa *vector*. Misal:


```r
users <- lookup_users(c("nurandi", "jokowi", "billgates", "mosalah"))
```


```r
users[,c("created_at", "screen_name", "name", "location", "description")]
```



```
## # A tibble: 4 x 5
##   created_at          screen_name name     location   description          
##   <dttm>              <chr>       <chr>    <chr>      <chr>                
## 1 2019-01-16 03:23:37 nurandi     Nur And~ Jakarta/B~ Aspiring Data Scient~
## 2 2019-01-16 10:10:08 jokowi      Joko Wi~ Jakarta    "Akun Twitter resmi ~
## 3 2019-01-15 17:23:00 BillGates   Bill Ga~ Seattle, ~ Sharing things I'm l~
## 4 2019-01-09 08:16:38 MoSalah     Mohamed~ Liverpool~ Footballer for Liver~
```


### `get_followers()` dan `get_friends()`

Menampilkan *list* dari *followers* (yang mem-follow) dan *list* dari *followings/friends* (yang di-follow) oleh akun/user tertentu.

**Contoh**: Menampilkan *list followers* dari [**"nurandi"**](https://twitter.com/nurandi)


```r
followers <- get_followers("nurandi")
followings <- get_friends("nurandi")
```

Output yang dihasilkan berupa *tibble* dari *user_id*.


```r
followers
```



```
## # A tibble: 255 x 1
##    user_id            
##    <chr>              
##  1 743414321514897408 
##  2 1042477396547395585
##  3 1049626193857732611
##  4 49285223           
##  5 1018277822542929920
##  6 1039338205743538176
##  7 1027183219244847110
##  8 73331490           
##  9 58994539           
## 10 986940727077699585 
## # ... with 245 more rows
```


```r
followings
```



```
## # A tibble: 325 x 2
##    user    user_id           
##    <chr>   <chr>             
##  1 nurandi 59106338          
##  2 nurandi 862600351         
##  3 nurandi 2160025940        
##  4 nurandi 80126180          
##  5 nurandi 3272463157        
##  6 nurandi 176519485         
##  7 nurandi 1200006384        
##  8 nurandi 775449094739197953
##  9 nurandi 68746721          
## 10 nurandi 1408142352        
## # ... with 315 more rows
```

Selanjutnya, untuk mendapatkan detail dari *list user ID followers/friends*, kita dapat menggunakan fungsi `lookup_users()`. Misal:


```r
detail_followings <- lookup_users(followings$user_id)
```


```r
detail_followings[,c("created_at", "screen_name", "location", "description")]
```



```
## # A tibble: 325 x 4
##    created_at          screen_name  location     description               
##    <dttm>              <chr>        <chr>        <chr>                     
##  1 2019-01-07 20:13:53 perrystephe~ Sydney       I tweet (rarely) about #r~
##  2 2019-01-16 12:03:30 kitabisacom  Indonesia    Situs galang dana dan don~
##  3 2019-01-11 20:56:56 KJRIToronto  Toronto, Ka~ Akun twitter Konsulat Jen~
##  4 2018-10-24 15:30:47 kitchenerma~ Kitchener, ~ 1017 Victoria St N. (519)~
##  5 2019-01-16 01:23:53 imigrasi_ja~ Kota Tua, J~ "Kantor Imigrasi Kelas I ~
##  6 2019-01-16 12:19:11 flerlagekr   Williamspor~ Tableau Zen Master <U+25CF> Anal~
##  7 2019-01-16 12:52:46 INABadminton Cipayung, J~ "Akun Twitter Resmi Humas~
##  8 2019-01-15 20:20:40 goodfellow_~ San Francis~ Google Brain research sci~
##  9 2019-01-16 01:42:39 fchollet     Mountain Vi~ Deep learning @google. Cr~
## 10 2019-01-13 22:19:13 math_rachel  San Francis~ co-founder https://t.co/Z~
## # ... with 315 more rows
```

Fungsi-fungsi yang kita bahas tadi hanya sebagian dari fitur yang tersedia pada *package* **rtweet**. Juga masih banyak parameter yang bisa ditambahkan agar pencarian yang kita lakukan sesuai kebutuhan. Untuk mengetahui fungsi-fungsi tersebut secara detail termasuk parameter yang bisa digunakan, ketik `?` diikuti nama fungsi. Misal `?search_tweets`. Atau, bisa juga pelajari referensi pada laman [rtweet.info](https://rtweet.info/reference/flatten.html). 

Selamat menambang data :)




