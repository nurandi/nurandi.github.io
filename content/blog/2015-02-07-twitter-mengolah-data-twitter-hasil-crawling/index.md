---
layout: post
slug: twitter-mengolah-data-twitter-hasil-crawling
title: 'twitteR: Mengolah Data Twitter Hasil Crawling'
date: 2015-02-07 01:56:51.000000000 +07:00
published: true
status: publish
excerpt: Kali ini mari kita bahas bagaimana mengolah data hasil crawling
  dari Twitter. Raw-data yang diperoleh sebagian besar berupa list.
tags:
- r
- twitter
redirect_from:
- /sosmed/mengolah-data-twitter-hasil-crawling/
- /2015/02/07/mengolah-data-twitter-hasil-crawling/
---
Menyambung diskusi kita tentang bagaimana [*crawling* data Twitter
menggunakan package `twitteR` pada
R](https://nurandi.id/blog/crawling-data-twitter-menggunakan-r/), kali
ini mari kita bahas bagaimana mengolah data tersebut. *Raw-data* yang
diperoleh dari hasil *crawling* sebagian besar berupa *list*.
`searchTwitter()` dan `userTimeline()` menghasilkan *list* dari objek
*status*. Setiap elemen pada *list* tersebut berisi detail tweet yang
sesuai dengan kriteria pencarian yang ditentukan. Output `getUser()`
berupa objek *user*, sementara `lookupUsers()` berupa *list* dari objek
*user*, di mana setiap elemennya berisi detail dari user. Untuk mengolah
data twitter hasil crawling, kita harus mengenal objek-objek tersebut.
Berikut penjelasan lebih detail mengenai objek *status* dan objek
*user*:

## Objek *status*

Dihasilkan oleh fungsi `searchTwitter()` dan `userTimeline()`, misalnya:

```r
tw.bogor = searchTwitter("bogor", n=500)
user.tl = userTimeline("nurandi", n=500)
```

Untuk mengetahui tipe data tersebut, gunakan fungsi `mode(tw.bogor)` dan
`mode(user.tl)`. Keduanya adalah `list`. Sedangkan struktur datanya bisa
dilihat dengan fungsi `str()`. Setiap elemen pada *list* mempunyai
struktur yang sama, sehingga untuk mengetahui struktur data, kita cukup
lihat satu elemen saja, misalnya: `str(tw.bogor[1])` atau
`str(user.tl[1])`. Outputnya adalah sebagai berikut:

     $ :Reference class 'status' [package "twitteR"] with 17 fields
      ..$ text         : chr "Testimoni MASKER SPIRULINA http://t.co/s96cHQKKim SMS: 089676926626 | HERBAL, BPOM #Cisarua #Bogor"
      ..$ favorited    : logi FALSE
      ..$ favoriteCount: num 0
      ..$ replyToSN    : chr(0) 
      ..$ created      : POSIXct[1:1], format: "2015-02-07 00:29:38"
      ..$ truncated    : logi FALSE
      ..$ replyToSID   : chr(0) 
      ..$ id           : chr "563857078658154496"
      ..$ replyToUID   : chr(0) 
      ..$ statusSource : chr "<a href="http://twittbot.net/" rel="nofollow">twittbot.net</a>"
      ..$ screenName   : chr "TiensCisarua"
      ..$ retweetCount : num 0
      ..$ isRetweet    : logi FALSE
      ..$ retweeted    : logi FALSE
      ..$ longitude    : chr(0) 
      ..$ latitude     : chr(0) 
      ..$ urls         :'data.frame':   0 obs. of  4 variables:
      .. ..$ url         : chr(0) 
      .. ..$ expanded_url: chr(0) 
      .. ..$ dispaly_url : chr(0) 
      .. ..$ indices     : num(0) 
      ..and 51 methods, of which 39 are possibly relevant:
      ..  getCreated, getFavoriteCount, getFavorited, getId, getIsRetweet, getLatitude,
      ..  getLongitude, getReplyToSID, getReplyToSN, getReplyToUID, getRetweetCount,
      ..  getRetweeted, getRetweeters, getRetweets, getScreenName, getStatusSource, getText,
      ..  getTruncated, getUrls, initialize, setCreated, setFavoriteCount, setFavorited,
      ..  setId, setIsRetweet, setLatitude, setLongitude, setReplyToSID, setReplyToSN,
      ..  setReplyToUID, setRetweetCount, setRetweeted, setScreenName, setStatusSource,
      ..  setText, setTruncated, setUrls, toDataFrame, toDataFrame#twitterObj

Dari output di atas kita dapat lihat bahwa objek *status* terdiri dari
17 variabel (*text, favorited, ..., urls*). Gunakan perintah `?status`
pada *R console* untuk mengetahui keterangan tiap-tiap variabel.

## Objek *user*

Dihasilkan oleh fungsi `getUser()` dan `lookupUsers()`, misalnya:

```r
user = getUser("nurandi")
users = lookupUsers(c("nurandi","prabowo08","jokowi_do2"))
```

`user` adalah objek *S4*, sedangkan `users` adalah *list*. Setiap elemen
pada objek `users` mempunyai struktur yang sama dengan objek `user`.
Berikut adalah keluaran dari fungsi `str(user)`.

    Reference class 'user' [package "twitteR"] with 18 fields
     $ description      : chr "Oh God ! This is so sexy ...."
     $ statusesCount    : num 255
     $ followersCount   : num 196
     $ favoritesCount   : num 10
     $ friendsCount     : num 229
     $ url              : chr "http://t.co/2zFucAuWdD"
     $ name             : chr "Nur Andi Setiabudi"
     $ created          : POSIXct[1:1], format: "2009-07-22 12:13:06"
     $ protected        : logi FALSE
     $ verified         : logi FALSE
     $ screenName       : chr "nurandi"
     $ location         : chr "Jakarta - ID"
     $ lang             : chr "en"
     $ id               : chr "59108937"
     $ lastStatus       :Reference class 'status' [package "twitteR"] with 17 fields
      ..$ text         : chr "RT @kdnuggets: Data scientist memes - the ‘hottest profession’: it's not the data size, it's how you use it! http://t.co/AJDi3q"| __truncated__
      ..$ favorited    : logi FALSE
      ..$ favoriteCount: num 0
      ..$ replyToSN    : chr(0) 
      ..$ created      : POSIXct[1:1], format: "2015-02-05 11:24:56"
      ..$ truncated    : logi FALSE
      ..$ replyToSID   : chr(0) 
      ..$ id           : chr "563297217713545216"
      ..$ replyToUID   : chr(0) 
      ..$ statusSource : chr "<a href="http://twitter.com" rel="nofollow">Twitter Web Client</a>"
      ..$ screenName   : chr "Unknown"
      ..$ retweetCount : num 27
      ..$ isRetweet    : logi TRUE
      ..$ retweeted    : logi FALSE
      ..$ longitude    : chr(0) 
      ..$ latitude     : chr(0) 
      ..$ urls         :'data.frame':   1 obs. of  5 variables:
      .. ..$ url         : chr "http://t.co/AJDi3qQywE"
      .. ..$ expanded_url: chr "http://buff.ly/1z5NYD5"
      .. ..$ display_url : chr "buff.ly/1z5NYD5"
      .. ..$ start_index : num 109
      .. ..$ stop_index  : num 131
      ..and 51 methods, of which 39 are possibly relevant:
      ..  getCreated, getFavoriteCount, getFavorited, getId, getIsRetweet, getLatitude,
      ..  getLongitude, getReplyToSID, getReplyToSN, getReplyToUID, getRetweetCount,
      ..  getRetweeted, getRetweeters, getRetweets, getScreenName, getStatusSource, getText,
      ..  getTruncated, getUrls, initialize, setCreated, setFavoriteCount, setFavorited,
      ..  setId, setIsRetweet, setLatitude, setLongitude, setReplyToSID, setReplyToSN,
      ..  setReplyToUID, setRetweetCount, setRetweeted, setScreenName, setStatusSource,
      ..  setText, setTruncated, setUrls, toDataFrame, toDataFrame#twitterObj
     $ listedCount      : num 4
     $ followRequestSent: logi FALSE
     $ profileImageUrl  : chr "http://pbs.twimg.com/profile_images/2669346805/a7fe4a431fd3b401b3dd26fac1a10b98_normal.png"
     and 57 methods, of which 45 are possibly relevant:
       getCreated, getDescription, getFavorites, getFavoritesCount, getFavouritesCount,
       getFollowerIDs, getFollowers, getFollowersCount, getFollowRequestSent, getFriendIDs,
       getFriends, getFriendsCount, getId, getLang, getLastStatus, getListedCount,
       getLocation, getName, getProfileImageUrl, getProtected, getScreenName,
       getStatusesCount, getUrl, getVerified, initialize, setCreated, setDescription,
       setFavoritesCount, setFollowersCount, setFollowRequestSent, setFriendsCount, setId,
       setLang, setLastStatus, setListedCount, setLocation, setName, setProfileImageUrl,
       setProtected, setScreenName, setStatusesCount, setUrl, setVerified, toDataFrame,
       toDataFrame#twitterObj

Objek `user` terdiri dari 18 variabel. Untuk mengatahui penjelasan
masing-masing variabel, buka dokumentasi dengan perintah `?user`.

## Mengakses variabel/kolom

Kolom-kolom pada objeck `status` maupun `user` dapat diakses dengan
menggunakan fungsi `sapply(<nama-objek>,<nama-kolom>)`. Misalnya,

* User yang menulis tweet tentang bogor:

  ```r
  name = sapply(tw.bogor, screenName)
  ```

* Menampilkan teks tweet

  ```r
  text = sapply(tw.bogor, statusText)
  ```
  
* Melihat jumlah follower dari objek *users*

  ```r
  nfollowers = sapply(users, followersCount)
  ```

## Konversi ke *data frame*

Objek *status* maupun *user* dapat dikonversi menjadi *data frame*
dengan fungsi `twListToDF()`. Misalnya:

```r
tw.bogor.df = twListToDF(tw.bogor)
names(tw.bogor.df)

# [1] "text"          "favorited"     "favoriteCount" "replyToSN"     "created"       # "truncated"    
# [7] "replyToSID"    "id"            "replyToUID"    "statusSource"  "screenName"    "retweetCount" 
# [13] "isRetweet"     "retweeted"     "longitude"     "latitude"  
```

```r
head(tw.bogor.df$text)

# [1] "Testimoni MASKER SPIRULINA http://t.co/s96cHQKKim SMS: 089676926626 | HERBAL, BPOM #Cisarua #Bogor"                                          
# [2] "He amin insyaalloh @MU_joybanget: @restinyunyun teh naha bisa geulis kitu ? Ameng atuh ka bogor xed�� xed�u0086”"                          
# [3] "Hari ini endy shoot di PGB bogor"                                                                                                            
# [4] "RT @infobogor: Hari ini! @batiknight2015 | @KuntoAjiW @endahNrhesa @GBluesShelter | info 081286890980 http://t.co/yqemocVWGO http://t.co/pz…"
# [5] "Weekendnya Dinas pagi dong xed��xed�u0086xed��xed�� (at puskesmas bogor selatan) — https://t.co/v2OpaFLTGx"                             
# [6] "RT @Indiradewiputri: Selamat pagi Bogor ;;)"  
```

```r
users.df = twListToDF(users)
names(users.df)

#  [1] "description"       "statusesCount"     "followersCount"    "favoritesCount"   
#  [5] "friendsCount"      "url"               "name"              "created"          
#  [9] "protected"         "verified"          "screenName"        "location"         
# [13] "lang"              "id"                "listedCount"       "followRequestSent"
# [17] "profileImageUrl" 
```

```r
users.df$name
# [1] "Nur Andi Setiabudi" "Prabowo Subianto"   "Joko Widodo"
```