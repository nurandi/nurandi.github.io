---
title: Grafik Workout Strava dengan ggplot2 dan plotly
author: Nur Andi Setiabudi
date: '2023-11-28'
slug: grafik-workout-strava-dengan-ggplot2-dan-plotly
categories:
  - r
  - visualization
tags:
  - r
  - ggplot2
  - plotly
  - strava
draft: false
---

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<script src="{{< blogdown/postref >}}index_files/plotly-binding/plotly.js"></script>
<script src="{{< blogdown/postref >}}index_files/typedarray/typedarray.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/plotly-htmlwidgets-css/plotly-htmlwidgets.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/plotly-main/plotly-latest.min.js"></script>

Sebagai pelari, salah satu grafik favorit saya di Strava adalah *workout analysis* yang sangat berguna untuk mengevaluasi latihan kecepatan, seperti interval, tempo, *fartlek* dan sejenisnya. Grafik ini sebetulnya adalah diagram batang atau *bar chart* yang menampilkan *pace* (menit per kilometer) untuk setiap *split*/*lap*. Hanya saja, *bar chart* ini sedikit dimodifikasi sedemikian rupa, lebar dari batang bervariasi sesuai dengan panjang/pendeknya *split* (berdasarkan waktu atau jarak). Contohnya *workout analysis* dari salah satu [latihan](https://www.strava.com/activities/3197482313) dengan menu:

- 20 menit *warming-up*
- 18x 1 menit pada *pace* 5k dengan 1 menit *recovery*
- 3 menit *recovery*
- 10 menit tempo
- *cooling-down*

*Workout* tersebut digambarkan sangat apik oleh [Strava](https://www.strava.com/activities/3197482313/pace-analysis):

<figure>
<img src="{{< blogdown/postref >}}index_files/figure-html/strava.png" alt="Strava" />
<figcaption aria-hidden="true">Strava</figcaption>
</figure>

Saya tertarik membuat replikasi grafik tersebut menggunakan R dengan memanfaatkan paket populer `ggplot2` yang dipadukan dengan `plotly` agar menjadi interaktif. Karena Strava tidak menyediakan, saya *download* [*dataset*-nya](data/split.csv) dari Garmin Connect. Kebetulan saya menggunakan jam Garmin untuk merekam latihan saat itu.

Pada `ggplot2`, jenis objek geometri (`geom`) yang cocok untuk menggambarkan chart ini adalah `geom_rect` yang berguna untuk membuat persegi panjang *(rectangle)* pada sebuah plot. Jenis geometri ini memerlukan titik koordinat sudut kiri bawah dan sudut kanan atas dari setiap persegi panjang.

## Persiapan data

Data `split` terdiri dari tiga kolom yaitu `Laps`, `Time` berformat H:M:S dan `Distance` dalam km.

``` r
split <- read.csv("data/split.csv")
head(split)
```

    ##   Laps  Time Distance
    ## 1    1 20:34     3.42
    ## 2    2  1:00     0.23
    ## 3    3  1:00     0.12
    ## 4    4  1:00     0.24
    ## 5    5  1:00     0.11
    ## 6    6  1:00     0.24

Untuk membuat *chart*, saya membutuhkan beberapa variabel tambahan untuk setiap lap yang diturunkan dari ketiga variabel tersebut, yaitu:

- Batas bawah dan batas atas dari `Distance` untuk sumbu X, dan
- Batas bawah dan batas atas dari `Pace` atau waktu per km untuk sumbu Y.

Saya akan memanfaatkan `dplyr` untuk keperluan ini, dibantu oleh `lubridate` untuk mengelola kolom bertipe waktu.

``` r
library(dplyr)
library(lubridate)

split <- split %>%
  arrange(Laps) %>%
  mutate(Cumulative_distance = cumsum(Distance),
         Distance_start = Cumulative_distance - Distance,
         Time_sec = period_to_seconds(ms(Time)),
         Pace_sec = Time_sec/Distance,
         Base_pace_sec = max(Pace_sec) + 30,
         Pace = format(as_datetime(Pace_sec),'%M:%S'))

head(split)
```

    ##   Laps  Time Distance Cumulative_distance Distance_start Time_sec Pace_sec
    ## 1    1 20:34     3.42                3.42           0.00     1234 360.8187
    ## 2    2  1:00     0.23                3.65           3.42       60 260.8696
    ## 3    3  1:00     0.12                3.77           3.65       60 500.0000
    ## 4    4  1:00     0.24                4.01           3.77       60 250.0000
    ## 5    5  1:00     0.11                4.12           4.01       60 545.4545
    ## 6    6  1:00     0.24                4.36           4.12       60 250.0000
    ##   Base_pace_sec  Pace
    ## 1      575.4545 06:00
    ## 2      575.4545 04:20
    ## 3      575.4545 08:20
    ## 4      575.4545 04:10
    ## 5      575.4545 09:05
    ## 6      575.4545 04:10

Saya sengaja menjadikan kolom waktu (`Time` dan `Pace`) dalam detik/*second*. Konversi detik menjadi waktu akan dikerjakan dalam `ggplot2`. Saya rasa ini lebih mudah. Atau ada pendapat lain?

## Buat *chart* dengan `ggplot2`

Dengan `ggplot2`, membuat *chart* **ala** Strava relatif simpel. Setelah inisiasi *layer* `ggplot`, cukup input kolom-kolom yang menjadi batas dalam `aes` pada `geom_rect`. `aes` dalam `ggplot` sebetulnya tidak diperlukan di sini, hanya saja akan berguna untuk menampilkan *tooltips* nanti.

``` r
library(ggplot2)

p <- ggplot(split, aes(label=Laps, label1=Distance, label2=Time, label3=Pace)) +
  geom_rect(
    aes(xmin=Distance_start, xmax=Cumulative_distance,
        ymin=as_datetime(Pace_sec), ymax=as_datetime(Base_pace_sec), 
        fill=Pace_sec), 
    colour="white") +
  scale_y_time(labels = function(t) strftime(t, "%M:%S"))  +
  theme(legend.position='none') +
  xlab("Distance (km)") +
  ylab("Pace (minutes/km)") +
  labs(title = "Running Analysis: 18 x 1 minute 5k pace + 10 minutes tempo") 

p
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />

Nice, sudah terlihat bentuknya.

Hanya saja, *axis-Y* perlu dibalik (*reverse*). Sebetulnya dulu saya berhasil membalik koordinat dari *axis-Y* seperti [ini](https://rpubs.com/nurandi/strava-chart) dengan trik dari [Stackoverflow](https://stackoverflow.com/a/43626186/3713478). Hanya saja, `gglot2` sepertinya tidak mendukung lagi. Jadi kali ini kita manfaatkan fungsi yang ada pada `gglotly` saja.

## Jadikan lebih interaktif dengan `plotly`

[**Plotly**](https://plotly.com/) adalah salah satu *library* untuk membuat grafik interaktif berbasis *JavaScript* yang mendukung berbagai bahasa pemrograman seperti R, Python, Julia, MATLAB, dan lain-lain. Dalam R, `plotly` “kompatibel” dengan `ggplot2`. Grafik statis yang dibuat dengan `ggplot2`, dapat dikonversi menjadi grafik `plotly` yang interaktif hanya dengan menggunakan perintah sederhana.

Kita coba konversikan plot `p` tadi dengan perintah berikut ini.

``` r
library(plotly)

ggplotly(p, tooltip = c("label", "label1", "label2", "label3")) %>%
  layout(yaxis = list(autorange = "reversed")) 
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-1" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"data":[{"x":[4.8200000000000003,4.8200000000000003,5.0700000000000003,5.0700000000000003,4.8200000000000003],"y":[240,575.4545454545455,575.4545454545455,240,240],"text":"Laps: 10<br />Distance: 0.25<br />Time: 1:00<br />Pace: 04:00","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(19,43,67,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[6.6099999999999994,6.6099999999999994,6.8599999999999994,6.8599999999999994,6.6099999999999994],"y":[240,575.4545454545455,575.4545454545455,240,240],"text":"Laps: 20<br />Distance: 0.25<br />Time: 1:00<br />Pace: 04:00","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(19,43,67,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[7.3300000000000001,7.3300000000000001,7.5800000000000001,7.5800000000000001,7.3300000000000001],"y":[240,575.4545454545455,575.4545454545455,240,240],"text":"Laps: 24<br />Distance: 0.25<br />Time: 1:00<br />Pace: 04:00","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(19,43,67,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[8.4100000000000001,8.4100000000000001,8.6600000000000001,8.6600000000000001,8.4100000000000001],"y":[240,575.4545454545455,575.4545454545455,240,240],"text":"Laps: 30<br />Distance: 0.25<br />Time: 1:00<br />Pace: 04:00","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(19,43,67,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[9.1199999999999992,9.1199999999999992,9.3699999999999992,9.3699999999999992,9.1199999999999992],"y":[240,575.4545454545455,575.4545454545455,240,240],"text":"Laps: 34<br />Distance: 0.25<br />Time: 1:00<br />Pace: 04:00","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(19,43,67,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[9.4900000000000002,9.4900000000000002,9.7400000000000002,9.7400000000000002,9.4900000000000002],"y":[240,575.4545454545455,575.4545454545455,240,240],"text":"Laps: 36<br />Distance: 0.25<br />Time: 1:00<br />Pace: 04:00","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(19,43,67,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[3.7699999999999996,3.7699999999999996,4.0099999999999998,4.0099999999999998,3.7699999999999996],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps:  4<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[4.1200000000000001,4.1200000000000001,4.3600000000000003,4.3600000000000003,4.1200000000000001],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps:  6<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[4.4699999999999998,4.4699999999999998,4.71,4.71,4.4699999999999998],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps:  8<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[5.1799999999999997,5.1799999999999997,5.4199999999999999,5.4199999999999999,5.1799999999999997],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps: 12<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[5.54,5.54,5.7800000000000002,5.7800000000000002,5.54],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps: 14<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[5.8899999999999997,5.8899999999999997,6.1299999999999999,6.1299999999999999,5.8899999999999997],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps: 16<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[6.25,6.25,6.4900000000000002,6.4900000000000002,6.25],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps: 18<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[6.9699999999999998,6.9699999999999998,7.21,7.21,6.9699999999999998],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps: 22<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[7.6999999999999993,7.6999999999999993,7.9399999999999995,7.9399999999999995,7.6999999999999993],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps: 26<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[8.0499999999999989,8.0499999999999989,8.2899999999999991,8.2899999999999991,8.0499999999999989],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps: 28<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[8.7699999999999996,8.7699999999999996,9.0099999999999998,9.0099999999999998,8.7699999999999996],"y":[250,575.4545454545455,575.4545454545455,250,250],"text":"Laps: 32<br />Distance: 0.24<br />Time: 1:00<br />Pace: 04:10","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(21,47,72,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[3.4199999999999999,3.4199999999999999,3.6499999999999999,3.6499999999999999,3.4199999999999999],"y":[260.86956521739131,575.4545454545455,575.4545454545455,260.86956521739131,260.86956521739131],"text":"Laps:  2<br />Distance: 0.23<br />Time: 1:00<br />Pace: 04:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(23,51,78,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[10.09,10.09,12.24,12.24,10.09],"y":[280,575.4545454545455,575.4545454545455,280,280],"text":"Laps: 38<br />Distance: 2.15<br />Time: 10:02<br />Pace: 04:40","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(27,59,88,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[12.24,12.24,15.91,15.91,12.24],"y":[350.95367847411444,575.4545454545455,575.4545454545455,350.95367847411444,350.95367847411444],"text":"Laps: 39<br />Distance: 3.67<br />Time: 21:28<br />Pace: 05:50","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(42,88,128,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[0,0,3.4199999999999999,3.4199999999999999,0],"y":[360.81871345029242,575.4545454545455,575.4545454545455,360.81871345029242,360.81871345029242],"text":"Laps:  1<br />Distance: 3.42<br />Time: 20:34<br />Pace: 06:00","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(44,92,133,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[3.6499999999999999,3.6499999999999999,3.77,3.77,3.6499999999999999],"y":[500,575.4545454545455,575.4545454545455,500,500],"text":"Laps:  3<br />Distance: 0.12<br />Time: 1:00<br />Pace: 08:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(75,155,218,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[5.4199999999999999,5.4199999999999999,5.54,5.54,5.4199999999999999],"y":[500,575.4545454545455,575.4545454545455,500,500],"text":"Laps: 13<br />Distance: 0.12<br />Time: 1:00<br />Pace: 08:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(75,155,218,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[6.1299999999999999,6.1299999999999999,6.25,6.25,6.1299999999999999],"y":[500,575.4545454545455,575.4545454545455,500,500],"text":"Laps: 17<br />Distance: 0.12<br />Time: 1:00<br />Pace: 08:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(75,155,218,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[6.4899999999999993,6.4899999999999993,6.6099999999999994,6.6099999999999994,6.4899999999999993],"y":[500,575.4545454545455,575.4545454545455,500,500],"text":"Laps: 19<br />Distance: 0.12<br />Time: 1:00<br />Pace: 08:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(75,155,218,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[7.21,7.21,7.3300000000000001,7.3300000000000001,7.21],"y":[500,575.4545454545455,575.4545454545455,500,500],"text":"Laps: 23<br />Distance: 0.12<br />Time: 1:00<br />Pace: 08:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(75,155,218,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[7.5800000000000001,7.5800000000000001,7.7000000000000002,7.7000000000000002,7.5800000000000001],"y":[500,575.4545454545455,575.4545454545455,500,500],"text":"Laps: 25<br />Distance: 0.12<br />Time: 1:00<br />Pace: 08:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(75,155,218,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[8.2900000000000009,8.2900000000000009,8.4100000000000001,8.4100000000000001,8.2900000000000009],"y":[500,575.4545454545455,575.4545454545455,500,500],"text":"Laps: 29<br />Distance: 0.12<br />Time: 1:00<br />Pace: 08:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(75,155,218,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[9.370000000000001,9.370000000000001,9.4900000000000002,9.4900000000000002,9.370000000000001],"y":[500,575.4545454545455,575.4545454545455,500,500],"text":"Laps: 35<br />Distance: 0.12<br />Time: 1:00<br />Pace: 08:20","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(75,155,218,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[9.7400000000000002,9.7400000000000002,10.09,10.09,9.7400000000000002],"y":[520.28571428571433,575.4545454545455,575.4545454545455,520.28571428571433,520.28571428571433],"text":"Laps: 37<br />Distance: 0.35<br />Time: 03:02.1<br />Pace: 08:40","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(80,165,231,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[4.0099999999999998,4.0099999999999998,4.1200000000000001,4.1200000000000001,4.0099999999999998],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps:  5<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[4.3599999999999994,4.3599999999999994,4.4699999999999998,4.4699999999999998,4.3599999999999994],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps:  7<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[4.71,4.71,4.8200000000000003,4.8200000000000003,4.71],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps:  9<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[5.0699999999999994,5.0699999999999994,5.1799999999999997,5.1799999999999997,5.0699999999999994],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps: 11<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[5.7799999999999994,5.7799999999999994,5.8899999999999997,5.8899999999999997,5.7799999999999994],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps: 15<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[6.8599999999999994,6.8599999999999994,6.9699999999999998,6.9699999999999998,6.8599999999999994],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps: 21<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[7.9400000000000004,7.9400000000000004,8.0500000000000007,8.0500000000000007,7.9400000000000004],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps: 27<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[8.6600000000000001,8.6600000000000001,8.7699999999999996,8.7699999999999996,8.6600000000000001],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps: 31<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[9.0099999999999998,9.0099999999999998,9.1199999999999992,9.1199999999999992,9.0099999999999998],"y":[545.4545454545455,575.4545454545455,575.4545454545455,545.4545454545455,545.4545454545455],"text":"Laps: 33<br />Distance: 0.11<br />Time: 1:00<br />Pace: 09:05","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"rgba(255,255,255,1)","dash":"solid"},"fill":"toself","fillcolor":"rgba(86,177,247,1)","hoveron":"fills","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.762557077625573,"r":7.3059360730593621,"b":40.182648401826491,"l":54.794520547945211},"plot_bgcolor":"rgba(235,235,235,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"title":{"text":"Running Analysis: 18 x 1 minute 5k pace + 10 minutes tempo","font":{"color":"rgba(0,0,0,1)","family":"","size":17.534246575342465},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-0.7955000000000001,16.705500000000001],"tickmode":"array","ticktext":["0","5","10","15"],"tickvals":[0,5.0000000000000018,10,15],"categoryorder":"array","categoryarray":["0","5","10","15"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"Distance (km)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":"reversed","range":[223.22727272727272,592.22727272727275],"tickmode":"array","ticktext":["04:00","06:00","08:00"],"tickvals":[240,360,480],"categoryorder":"array","categoryarray":["04:00","06:00","08:00"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"Pace (minutes/km)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":false,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"6cb86f3bfa":{"label":{},"label1":{},"label2":{},"label3":{},"xmin":{},"xmax":{},"ymin":{},"ymax":{},"fill":{},"type":"scatter"}},"cur_data":"6cb86f3bfa","visdat":{"6cb86f3bfa":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

Taddaaa !!! *Split-chart* Strava ala-ala berhasil kita buat.

Perintah `ggplotly` berfungsi untuk mengkonversi grafik `ggplot` menjadi `plotly`. Hampir semua `geom_` dapat dikonversi menggunakan fungsi ini. `autorange = "reversed"` berfungsi untuk membalik titik koordinat. Sedangkan `tooltips` berguna untuk menampilkan *tooltips* atau data yang akan muncul ketika chart kita sorot dengan *mouse*. Kolom `label.?` sudah kita siapkan sebelumnya saat membuat `ggplot` tadi.

Semoga bermanfaat :)
