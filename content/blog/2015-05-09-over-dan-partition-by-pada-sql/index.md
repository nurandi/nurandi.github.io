---
layout: post
slug: over-dan-partition-by-pada-sql
title: OVER dan PARTITION BY pada SQL
date: 2015-05-09 04:36:00.000000000 +07:00
published: true
status: publish
tags:
- sql
excerpt: OVER PARTITION adalah salah satu fitur SQL yang sangat saya andalkan, 
  sehingga jangan heran jika banyak *script* yang saya tulis mengandung fungsi itu di dalamnya.
redirect_from:
- /2015/05/09/over-dan-partition-by-pada-sql/
- /sql/over-dan-partition-by-pada-sql/
---
Beberapa waktu lalu saya diminta untuk menjelaskan *logic* dari *SQL
script* yang saya buat ke salah salah satu rekan. *"Belum ngerti"*,
katanya, *"banyak over partition-nya"*. Padahal OVER PARTITION adalah
salah satu fitur SQL yang sangat saya andalkan, sehingga jangan heran
jika banyak *script* yang saya tulis mengandung fungsi itu di dalamnya.
Apa sebenarnya fungsi dari fitur ini dan bagaimana penggunaannya. Mari
kita lihat apa dan bagaimana fungsi OVER dan PARTITION BY pada SQL
bekerja.

## `OVER`

Digunakan untuk mendapatkan nilai aggregat (seperti `SUM`, `AVG`,
`COUNT`, `MIN`, `MAX`) tanpa menggunakan `GROUP BY`. Dengan `OVER` kita
tetap mendapatkan seluruh baris secara detail beserta nilai aggregatnya.
Mari kita gunakan tabel `Orders` berikut:

    | REGION |   PRODUCT  | QTY |
    |:------:|:----------:|:---:|
    | east   | desktop    | 332 |
    | east   | notebook   | 350 |
    | east   | software   | 125 |
    | center | tablet     | 325 |
    | center | smartphone | 550 |
    | center | desktop    | 186 |
    | center | notebook   | 220 |
    | center | software   |  54 |
    | west   | tablet     | 432 |
    | west   | smartphone | 621 |
    | west   | desktop    | 125 |
    | west   | notebook   | 188 |
    | west   | software   |  75 |
    | west   | camera     |  65 |

Query:

```sql
SELECT *
    , SUM(Qty) OVER() Sum_qty 
FROM Orders;
```

Akan menghasilkan:

    | REGION |   PRODUCT  | QTY | SUM_QTY |
    |:------:|:----------:|:---:|:-------:|
    | east   | desktop    | 332 |    3648 |
    | east   | notebook   | 350 |    3648 |
    | east   | software   | 125 |    3648 |
    | west   | tablet     | 432 |    3648 |
    | west   | smartphone | 621 |    3648 |
    | west   | desktop    | 125 |    3648 |
    | west   | notebook   | 188 |    3648 |
    | west   | software   |  75 |    3648 |
    | west   | camera     |  65 |    3648 |
    | center | tablet     | 325 |    3648 |
    | center | smartphone | 550 |    3648 |
    | center | desktop    | 186 |    3648 |
    | center | notebook   | 220 |    3648 |
    | center | software   |  54 |    3648 |

`SUM(Qty) OVER()` jika diterjemahkan kurang lebih berarti

-   `SUM(Qty)` : hitung jumlah `Qty`
-   `OVER` : untuk semua baris
-   `()` : secara keseluruhan

## `OVER (PARTITION BY)`

Pada contoh di atas, `Sum_qty` adalah jumlah `Qty` dari seluruh baris
yang ada pada dataset. Kita dapat memisahkan hasil perhitungan dengan
menggunakan `PARTITION BY`.

```sql
SELECT *
    , SUM(Qty) OVER(PARTITION BY Product) Sum_qty 
FROM orders;
```

Hasilnya:

    | REGION |   PRODUCT  | QTY | SUM_QTY |
    |:------:|:----------:|:---:|:-------:|
    | east   | software   | 125 |     254 |
    | center | software   |  54 |     254 |
    | west   | software   |  75 |     254 |
    | east   | desktop    | 332 |     643 |
    | center | desktop    | 186 |     643 |
    | west   | desktop    | 125 |     643 |
    | center | tablet     | 325 |     757 |
    | west   | tablet     | 432 |     757 |
    | west   | camera     |  65 |      65 |
    | east   | notebook   | 350 |     758 |
    | center | notebook   | 220 |     758 |
    | west   | notebook   | 188 |     758 |
    | center | smartphone | 550 |    1171 |
    | west   | smartphone | 621 |    1171 |

Partisi yang saya gunakan adalah `Product`. Artinya, setiap produk akan
diperlakukan secara terpisah dengan produk lainnya. Sehingga saya akan
dapatkan total quantity untuk produk `software`, kemudian total untuk
`desktop` dan seterusnya.

Jika diterjemahkan, `SUM(Qty) OVER(PARTITION BY Product)` berarti

-   `SUM(Qty)` : hitung jumlah `Qty`
-   `OVER` : untuk semua baris
-   `(PARTITION BY Product)` : yang mempunyai produk yang sama

Bandingkan dengan `GROUP BY`

```sql
SELECT SUM(Qty) Sum_qty
FROM Orders
GROUP BY Product;
```

    |   PRODUCT  | SUM_QTY |
    |:----------:|:-------:|
    | software   |     254 |
    | desktop    |     643 |
    | tablet     |     757 |
    | camera     |      65 |
    | notebook   |     758 |
    | smartphone |    1171 |

Tanpa menggunakan `OVER(PARTITION BY)`, query berikut akan memberikan
hasil yang sama (tentu lebih kompleks, ya):

```sql
SELECT t0.*
    , t1.Sum_qty
FROM Orders t0
JOIN (
    SELECT SUM(Qty) Sum_qty
    FROM Orders
    GROUP BY Product ) t1
ON t0.Product = t1.Product;
```

Mudah bukan?

Berikut dua contoh penggunaan fungsi `OVER PARTITION` :

### Menghilangkan duplikat

Dari setiap region akan diambil satu baris saja, yaitu baris dengan Qty
terendah. Untuk tujuan ini kita dapat menggunakan fungsi aggregat
`ROW_NUMBER()`. Sebagai catatan, fungsi ini membutuhkan tambahan
perintah `ORDER BY`.

```sql
SELECT Region, Product, Qty 
FROM (
    SELECT *
        , ROW_NUMBER() OVER(PARTITION BY Region ORDER BY Qty) Obs 
    FROM Orders ) t0
WHERE Obs = 1;
```

    | REGION |  PRODUCT | QTY |
    |:------:|:--------:|:---:|
    | center | software |  54 |
    | west   | camera   |  65 |
    | east   | software | 125 |

### Mengambil sekian persen per kategori

Dari setiap region akan diambil "Top 50% Product".

```sql
SELECT Region, Product, Qty, Obs 
FROM (
    SELECT *
        , ROW_NUMBER() OVER(PARTITION BY Region ORDER BY Qty DESC) Obs 
        , COUNT(*) OVER(PARTITION BY Region) Ctn
    FROM Orders ) t0
WHERE Obs <= CEIL(0.5*Ctn);
```

    | REGION |   PRODUCT  | QTY | OBS |
    |:------:|:----------:|:---:|:---:|
    | center | smartphone | 550 |   1 |
    | center | tablet     | 325 |   2 |
    | center | notebook   | 220 |   3 |
    | west   | smartphone | 621 |   1 |
    | west   | tablet     | 432 |   2 |
    | west   | notebook   | 188 |   3 |
    | east   | notebook   | 350 |   1 |
    | east   | desktop    | 332 |   2 |

Punya contoh lain? Mari kita diskusikan.

Semoga bermanfaat :)
