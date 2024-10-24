# Laporan Proyek Machine Learning - Sistem Rekomendasi

Proyek Sistem Rekomendasi Anime

## Domain Proyek

Anime adalah sebuah animasi khas yang berasal dari jepang, ini biasanya dicirikan melalui gambar-gambar berwarna-warni yang menampilkan tokoh dalam berbagai macam lokasi dan cerita. Anime sendiri telah berkembang pesat dalam beberapa dekade terakhir dan menjadi salah satu bentuk hiburan yang populer di seluruh dunia. Dengan banyaknya judul anime yang tersedia sekarang, peminat anime kadang mengalami kesulitan dalam menentukan judul yang sesuai dengan minat mereka. Hal ini dapat menyebabkan pemborosan waktu, terutama ketika banyaknya pilihan yang dapat mereka tonton.

Sistem rekomendasi hadir sebagai salah satu solusi untuk mengatasi masalah ini. Dengan memanfaatkan algoritma dan analisis data yang telah ada, sistem rekomendasi dapat memberikan saran yang dibuat berdasarkan mminat setiap orang. Dalam konteks ini, sistem rekomendasi dapat menganalisis berbagai faktor seperti genre, rating, popularitas dan lain sebagainya untuk menyajikan rekomendasi yang relevan dan menarik.

Sistem rekomendasi yang akan dibuat juuga dapat membantu platform penyedia layanan agar dapat memberikan rekomendasi terbaik kepada setiap user, dengan begitu user dapat dengan mudah menemukan anime yang mereka ingin tonton sesuai prefernsi mereka. Tentu saja ini akan menaikkan viewer penonton pada suatu platform bahkan bisa berdampak positif bagi penyedia platform.

## Business Understanding

Berdasarkan data yang diambil dari kaggle mengenai database anime, terdapat beberapa faktor yang bisa digunakan sebagai rekomendasi seperti genre, rating dan jumlah member. Metode yang akan kita gunakan saat ini ada rekomendasi bebasis konten (conten-based filtering) yang akan merekomendasikan item yang mirip dengan item yang disukai pengguna di masa lalu dan yang akan menjadi faktor utamanya adalah genre dari anime yang pernah mereka tonton sebelumnya.

### Problem Statements

Problem statements yang ingin dibahas adalah:

1. Berdasarkan dataset yang digunakan, bagaimana membuat sistem rekomendasi yang dipersonalisasikan dengan teknik content-based filtering?
2. Berdasarkan dataset yang digunakan, 5 rekomendasi apa yang paling sesuai berdasarkan genre yang sama?

### Goals

Goals/tujuan dari poyek ini adalah:

1. Menghasilkan sejumlah rekomendasi anime yang dipersonalisasi untuk pengguna dengan teknik content-based filtering.
2. Menghasilkan 5 rekomendasi anime yang sesuai dengan genre yang pernah dikunjungi sebelumnya menggunakan teknik content-based filtering.

### Solution statements

Beberapa solusi yang akan coba terapkan adalah:

1. Melakukan eksplorasi fitur seperti cek duplikasi data, cek missing variabel di tiap data
2. Untuk mendapatkan data yang bersih sebelum di buat permodelan. Dilakukan preparation data yang terdiri dari Menghapus missing value, Menghapus rating user yang bernilai -1, menggabungkan data `anime` dan `ratings`, mengatasi duplicate data fitur `name`, membuat dictionary pada fitur `anime_id`, `name` dan `genre` dan yang terakhir kita akan merepresentasikan fitur genre menggunakan fungsi TF-IDF Vectorizer.
3. Permodelan akan dilakukan dengan metode Content Based Filtering untuk menghasilkan 5 rekomendasi berdasakan genre yang sama.

## Data Understanding

Data yang saya gunakan berasal dari Kaggle dengan judul Anime Recommendations Database [anime](https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database), Data tersebut berisi 2 file dengan rincian sebagai berikut

1. file `anime` terdiri dari 12.294 baris dengan 7 kolom dengan rincian sebagai berikut:

- `anime_id`: Id unik untuk judul anime (numerik)
- `name`: Nama anime (kategorik)
- `genre`: Genre anime (kategorik)
- `type`: Tipe seperti Movie, TV, OVA, dll (kategorik)
- `episodes`: Jumlah episode (numerik)
- `rating`: rating untuk anime (numerik)
- `members`: jumlah anggota komunitas anime tersebut (numerik)

2. file `rating` terdiri dari 7.813.737 baris dengan 3 kolom dengan rincian sebagai berikut:

- `user_id`: Id user. (numerik)
- `anime_id`: id anime. (numerik)
- `rating`: Rating dari user (jika bernilai -1 berarti user hanya menonton dan tidak memberi rating). (numerik)

Tahapan yang akan saya lakukan pada variabel `anime` adalah sebagai berikut:

1. menammpilkan dataset `anime`

| anime_id | name                             | genre                                               | type  | episodes | rating | members |
| -------- | -------------------------------- | --------------------------------------------------- | ----- | -------- | ------ | ------- |
| 32281    | Kimi no Na wa.                   | Drama, Romance, School, Supernatural                | Movie | 1        | 9.37   | 200630  |
| 5114     | Fullmetal Alchemist: Brotherhood | Action, Adventure, Drama, Fantasy, Magic, Military  | TV    | 64       | 9.26   | 793665  |
| 28977    | Gintama°                         | Action, Comedy, Historical, Parody, Samurai, Sci-Fi | TV    | 51       | 9.25   | 114262  |
| 9253     | Steins;Gate                      | Sci-Fi, Thriller                                    | TV    | 24       | 9.17   | 673572  |
| 9969     | Gintama'                         | Action, Comedy, Historical, Parody, Samurai, Sci-Fi | TV    | 51       | 9.16   | 151266  |

2. cek data `anime`

| Column   | Non-Null Count | Dtype   |
| -------- | -------------- | ------- |
| anime_id | 12,294         | int64   |
| name     | 12,294         | object  |
| genre    | 12,232         | object  |
| type     | 12,269         | object  |
| episodes | 12,294         | object  |
| rating   | 12,064         | float64 |
| members  | 12,294         | int64   |

berdasarkan data tersebut terdapat 12.294 baris dengan 7 kolom.

3. Cek jumlah genre dan apa saja genrenya

```
print('Banyak genre anime: ', len(anime.genre.unique()))
print('genre: ', anime.genre.unique())
```

ouput:

```
Banyak genre anime:  3265
genre:  ['Drama, Romance, School, Supernatural'
 'Action, Adventure, Drama, Fantasy, Magic, Military, Shounen'
 'Action, Comedy, Historical, Parody, Samurai, Sci-Fi, Shounen' ...
 'Hentai, Sports' 'Drama, Romance, School, Yuri' 'Hentai, Slice of Life']
```

4. Terahir cek missing value, berdasarkan hasil pengecekan missing value terdapat beberapa data yang memiliki missing value dengan rincian sebagai berikut:

- `genre`: 62 data
- `type`: 25 data
- `rating`: 230 data

Selanjutnya, tahapan yang akan saya lakukan pada variabel `rating` adalah sebagai berikut:

1. Menampilkan dataset `ratings`

| user_id | anime_id | rating |
| ------- | -------- | ------ |
| 1       | 20       | -1     |
| 1       | 24       | -1     |
| 1       | 79       | -1     |
| 1       | 226      | -1     |
| 1       | 241      | -1     |

2. cek data `ratings`

| Column   | Dtype |
| -------- | ----- |
| user_id  | int64 |
| anime_id | int64 |
| rating   | int64 |

berdasarkan data tersebut terdapat 7.813.737 baris dengan 3 kolom.

3. cek missing value, berdasarkan hasil pengecekan missing value tidak terdapat data yang memiliki missing value

4. cek jumlah pada fitur `rating` yang memiliki nilai -1

```
ratings[ratings.rating == -1].shape
```

berdasarkan pada outputnya, didapatkan 1.476.495 baris yang memiliki nilai -1, ini menandakan banyak sekali user yang hanya menonton tanpa memberikan penilaian.

## Data Preparation

Data preparation merupakan tahapan penting dalam proses pengembangan model machine learning. Ini adalah tahapan dilakukannya proses transformasi pada data sehingga menjadi bentuk yang cocok untuk proses pemodelan. Dalam data preparation akan dilakukan 3 tahapan, yakni Menghapus missing value, Menghapus rating yang bernilai -1, Menggabungkan data `anime` dan `ratings`, Mengatasi duplicate data pada fitur `name`, dan yang terakhir Membuat dictionary pada fitur `anime_id`, `name`, dan `genre`.

### Menghapus missing value

Setelah di cek, hanya beberapa fitur yang memiliki missing value seperti fitur `genre`, `type` dan `rating`. Langsung saja kita hapus 3 fitur tersebut

```
anime.dropna(subset=['genre', 'type', 'rating'], inplace=True)
```

Missing value telah berhasil di hapus.

### Menghapus rating yang bernilai -1

Nilai -1 pada fitur `rating` tidak akan berpengaruh karena tidak menggambarkan apapun, nilai -1 ini akan dihapuskan saja.

```
ratings = ratings[ratings.rating != -1]
```

Setelah nilai -1 dihapus, juumlah data pada variabel `ratings` menjadi 6.337.241 baris.

### Menggabungkan data `anime` dan `ratings`

Data akan `anime` dan `ratings` akan digabungkan dengan `merge` pada fitur `anime_id`

```
df = pd.merge(anime, ratings, on='anime_id', how='inner')
```

lalu tampilkan data gabungan dengan nama `df`

| anime_id | name           | genre                                | type  | episodes | rating_x | members | user_id | rating_y |
| -------- | -------------- | ------------------------------------ | ----- | -------- | -------- | ------- | ------- | -------- |
| 32281    | Kimi no Na wa. | Drama, Romance, School, Supernatural | Movie | 1        | 9.37     | 200630  | 99      | 5        |
| 32281    | Kimi no Na wa. | Drama, Romance, School, Supernatural | Movie | 1        | 9.37     | 200630  | 152     | 10       |
| 32281    | Kimi no Na wa. | Drama, Romance, School, Supernatural | Movie | 1        | 9.37     | 200630  | 244     | 10       |
| 32281    | Kimi no Na wa. | Drama, Romance, School, Supernatural | Movie | 1        | 9.37     | 200630  | 271     | 10       |
| 32281    | Kimi no Na wa. | Drama, Romance, School, Supernatural | Movie | 1        | 9.37     | 200630  | 322     | 10       |

berdasarkan data yang telah digabung, jumlah data menjadi 6.337.146 baris dengan 9 kolom. ada 2 fitur yang memiliki nama yang sama dan diubah menjadi:

- `rating_x`: berasal dari fitur `rating` di variabel `anime` berisi nilai rating keseluruhan
- `rating_y`: berasal dari fitur `rating` di variabel `ratings` berisi nilai rating yang diberikan user

### Mengatasi duplicate data pada fitur `name`

Cek data duplicate pada fitur `name` agar tidak ada nama anime yang sama, setelahnya hapus data yang sama.
Setelah duplicate data dihapus, lalu kita masukkan ke dalam data baru bernama `preparation`

```
preparation = df.drop_duplicates('name')
```

Setelah berhasil dihapus, jumlah baris `preparation` menjadi 9892 baris saja.

### Membuat dictionary pada fitur `anime_id`, `name`, dan `genre`

Selanjutnya, kita perlu melakukan konversi data series menjadi list. Dalam hal ini, kita menggunakan fungsi tolist() dari library numpy.

```
anime_id = preparation['anime_id'].tolist()
name = preparation['name'].tolist()
genre = preparation['genre'].tolist()
```

Tahap berikutnya, kita akan membuat dictionary untuk menentukan pasangan key-value pada data anime_id, name, dan genre yang telah kita siapkan sebelumnya dan dimasukkan dalam dataset baru bernama `df_new`.

```
df_new = pd.DataFrame({
    'anime_id': anime_id,
    'name': name,
    'genre': genre
})
df_new
```

output:

| anime_id | name                                               | genre                                               |
| -------- | -------------------------------------------------- | --------------------------------------------------- |
| 32281    | Kimi no Na wa.                                     | Drama, Romance, School, Supernatural                |
| 5114     | Fullmetal Alchemist: Brotherhood                   | Action, Adventure, Drama, Fantasy, Magic, Military  |
| 28977    | Gintama°                                           | Action, Comedy, Historical, Parody, Samurai, Sci-Fi |
| 9253     | Steins;Gate                                        | Sci-Fi, Thriller                                    |
| 9969     | Gintama'                                           | Action, Comedy, Historical, Parody, Samurai, Sci-Fi |
| 5541     | The Satisfaction                                   | Hentai                                              |
| 9316     | Toushindai My Lover: Minami tai Mecha-Minami       | Hentai                                              |
| 5543     | Under World                                        | Hentai                                              |
| 5621     | Violence Gekiga David no Hoshi                     | Hentai                                              |
| 6133     | Violence Gekiga Shin David no Hoshi: Inma Densetsu | Hentai                                              |

Data kini telah siap untuk dimasukkan ke dalam pemodelan dengan jumlah 9.892 baris dan 3 kolom.

### TF-IDF Vectorizer

Satu tahapan lagi sebelum permodelan, kita akan menggunakan fungsi tfidfvectorizer() dari library sklearn untuk merepresentasikan fitur-fitur penting dalam penentuan rekomendasi. pertama kita akan mapping array dari fitur index integer ke fitur nama dan menghasilkan array sebagai berikut:
```
array(['action', 'adventure', 'ai', 'arts', 'cars', 'comedy', 'dementia',
       'demons', 'drama', 'ecchi', 'fantasy', 'fi', 'game', 'harem',
       'hentai', 'historical', 'horror', 'josei', 'kids', 'life', 'magic',
       'martial', 'mecha', 'military', 'music', 'mystery', 'of', 'parody',
       'police', 'power', 'psychological', 'romance', 'samurai', 'school',
       'sci', 'seinen', 'shoujo', 'shounen', 'slice', 'space', 'sports',
       'super', 'supernatural', 'thriller', 'vampire', 'yaoi', 'yuri'],
      dtype=object)
```

Selanjutnya, kita lakukan fit dan transformasi ke dalam bentuk matriks. hasil fit dan transformasi menghasilkan matriks dengan ukuran (9882, 47). Nilai 9882 merupakan ukuran data dan 47 adalah merupakan matriks dari genre.
Kemudiaan kita akan menghasilkan vektor tf-idf dalam bentuk matriks, kita juga akan menggunakan fungsi todense() untuk melihat matriks tf-idf untuk beberapa judul anime `name` dan `genre` nya. Terakhir kita akan menghitung derajat kesamaan antara satu judul anime dengan judul lainnya untuk menghasilkan kandidat judul `name` yang akan direkomendasikan.


## Modeling

Pada tahap ini, saya akan mengembangkan sistem rekomendasi dengan pendekatan content based filtering. sistem rekomendasi berbasis konten (content-based filtering) adalah merekomendasikan item yang mirip dengan item yang disukai pengguna di masa lalu dalam kasus ini merekomendasikan anime berdasarkan item `genre` yang sama. 

Content-based filtering sendiri mempelajari profil minat pengguna baru berdasarkan data dari objek yang telah dinilai pengguna. Algoritma ini bekerja dengan menyarankan item serupa yang pernah disukai di masa lalu atau sedang dilihat di masa kini kepada pengguna. Semakin banyak informasi yang diberikan pengguna, semakin baik akurasi sistem rekomendasi.

![ilustrasi](https://github.com/user-attachments/assets/93eda825-47b6-47be-b039-4471052dc334)

Untuk membuat profil pengguna, dua informasi ini penting bagi sistem dengan pendekatan content-based filtering yaitu model preferensi pengguna dan riwayat interaksi pengguna dengan sistem rekomendasi. 


Pada tahapan ini, fungsi yang akan kita gunakan adalan fungsi cosine_similarity dari library sklearn untuk melihat matriks kesamaan setiap anime dengan menampilkan nama anime. Dengan cosine similarity, kita berhasil mengidentifikasi kesamaan antara satu `name` dengan `name` lainnya. Shape (9892, 9892) merupakan ukuran matriks similarity dari data yang kita miliki. Berdasarkan data yang ada, matriks di atas sebenarnya berukuran 9892 `name` x 9892 `name` (masing-masing dalam sumbu X dan Y). Artinya, kita mengidentifikasi tingkat kesamaan pada 9892 judul `name`. 

Selanjutnya kita akan menguji model kita dengan membuat `fungsi_recommendations` dengan beberapa parameter sebagai berikut:
- Nama_anime : Nama anime (index kemiripan dataframe).
- Similarity_data : Dataframe mengenai similarity yang telah kita definisikan sebelumnya.
- Items : Nama dan fitur yang digunakan untuk mendefinisikan kemiripan, dalam hal ini adalah `name` dan `genre`.
- k : Banyak rekomendasi yang ingin diberikan (dalam hal ini saya  menampilkan 5 rekomendasi).

```
def anime_recommendations(nama_anime, similarity_data=cosine_sim_df, items=df_new[['name', 'genre']], k=5):
    index = similarity_data.loc[:,nama_anime].to_numpy().argpartition(
        range(-1, -k, -1))

    # Mengambil data dengan similarity terbesar dari index yang ada
    closest = similarity_data.columns[index[-1:-(k+2):-1]]

    # Drop nama_anime agar nama anime yang dicari tidak muncul dalam daftar rekomendasi
    closest = closest.drop(nama_anime, errors='ignore')

    return pd.DataFrame(closest).merge(items).head(k)
```

Selanjutnya, kita akan terapkan kode di atas untuk menemukan rekomendasi anime yang mirip dengan `Detective Conan OVA 11: A Secret Order from London`. Terapkan kode berikut:

```
df_new[df_new.name.eq('Detective Conan OVA 11: A Secret Order from London')]
```
output:

|       | anime_id | name                                            | genre                                      |
|-------|----------|-------------------------------------------------|--------------------------------------------|
| 1493  | 10703    | Detective Conan OVA 11: A Secret Order from Lo...| Adventure, Comedy, Mystery, Police, Shounen|

Anime dengan judul `Detective Conan OVA 11: A Secret Order from London` memliki genre `Adventure, Comedy, Mystery, Police, Shounen`. Sekarang kita akan mencoba mendapatkan rekomendasi dengan genre yang sama menggunakan kode berikut:

```
# Menampilkan 5 rekomendasi
anime_recommendations('Detective Conan OVA 11: A Secret Order from London')
```

output:

| name                                                  | genre                                      |
|-------------------------------------------------------|--------------------------------------------|
| Detective Conan OVA 03: Conan and Heiji and the...     | Adventure, Comedy, Mystery, Police, Shounen|
| Detective Conan Movie 01: The Timed Skyscraper         | Adventure, Comedy, Mystery, Police, Shounen|
| Aoyama Goushou Tanpenshuu                             | Adventure, Comedy, Mystery, Police, Shounen|
| Detective Conan Movie 04: Captured in Her Eyes        | Adventure, Comedy, Mystery, Police, Shounen|
| Detective Conan: Black History 2                      | Adventure, Comedy, Mystery, Police, Shounen|


Berdasarkan dari 5 rekomendasi yang ditampilkan, semuanya memiliki genre yang persi sama dengan
`Detective Conan OVA 11: A Secret Order from London` dengan genre `Adventure, Comedy, Mystery, Police, Shounen`.

## Evaluation

Pada proses evaluasi kita akan menggunakan metrik `Precision`. `precision` adalah metrik evaluasi yang mengukur seberapa relevan item yang direkomendasikan dibandingkan dengan semua item yang benar-benar direkomendasikan. secara rumus bisa dituliskan sebagai berikut:

![preci](https://github.com/user-attachments/assets/115f0b3f-86a7-4935-9ff5-bc2c5caa641a)

Precision menilai kualitas rekomendasi, yaitu seberapa besar proporsi dari item yang direkomendasikan adalah benar-benar relevan bagi pengguna. Metrik ini penting ketika ingin memastikan bahwa sebagian besar rekomendasi yang diterima pengguna memang sesuai dengan minat atau kebutuhan mereka. Precision sangat berguna ketika jumlah rekomendasi yang diberikan cukup terbatas, dan kualitas lebih penting daripada kuantitas.
Pada sistem rekomendasi ini, evaluasi akan dilakukan manual dengan kode sebaga berikut:

```
# Evaluasi model
item_relevan = 5
jumlah_rekomendasi = 5

Precision = (item_relevan / jumlah_rekomendasi)
print(Precision)
```

output:
`1.0`

Berdasarkan hasil evaluasi di atas menggunakan metrik `precision` didapatkan nilai 1, ini menandakan semua rekomendasi yang ditampilkan sesuai atau relevan dengan genre anime yang pernah di tonton sebelumnya. Berdasarkan nilai tersebut juga membuktikan bahwa sistem memiliki kemampuan yang sangat baik dalam menyeleksi dan merekomendasikan item yang tepat untuk pengguna.
