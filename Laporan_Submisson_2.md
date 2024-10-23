# Rekomendasi-Anime

Proyek Sistem Rekomendasi Pertama

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

1. Menghasilkan sejumlah rekomendasi restoran yang dipersonalisasi untuk pengguna dengan teknik content-based filtering.
2. Menghasilkan 5 rekomendasi anime yang sesuai dengan genre yang pernah dikunjungi sebelumnya menggunakan teknik content-based filtering.

### Solution statements

Beberapa solusi yang akan coba terapkan adalah:

1. Melakukan eksplorasi fitur seperti cek duplikasi data, cek missing variabel di tiap data
2. Untuk mendapatkan data yang bersih sebelum di buat permodelan. Dilakukan preparation data yang terdiri dari Menghapus missing value, Menghapus rating user yang bernilai -1, menggabungkan data `anime` dan `ratings`, mengatasi duplicate data fitur `name`, membuat dictionary pada fitur `anime_id`, `name` dan `genre`.
3. Permodelan akan dilakukan dengan metode Content Based Filtering untuk menghasilkan 5 rekomendasi berdasakan genre yang sama.

## Data Understanding

Data yang saya gunakan berasal dari Kaggle dengan judul Anime Recommendations Database [anime](https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database), Data tersebut berisi 2 file dengan rincian sebagai berikut

1. file `anime`

- `anime_id`: Id unik untuk judul anime
- `name`: Nama anime
- `genre`: Genre anime
- `type`: Tipe seperti Movie, TV, OVA, dll
- `episodes`: Jumlah episode
- `rating`: rating untuk anime
- `members`: jumlah anggota komunitas anime tersebut

2. file `rating`

- `user_id`: Id user.
- `anime_id`: id anime.
- `rating`: Rating dari user (jika bernilai -1 berarti user hanya menonton dan tidak memberi rating).

Tahapan yang akan saya lakukan pada variabel `anime` adalah sebagai berikut:

1. Membuat dataset bernama `anime`, lalu menampilkannya

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

1. Membuat dataset bernama `ratings`, lalu menampilkannya

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

## Modeling

Pada tahap ini, saya akan mengembangkan model machine learning dengan tiga algoritma. Kemudian, saya akan mengevaluasi performa masing-masing algoritma dan menentukan algoritma mana yang memberikan hasil prediksi terbaik. Ketiga algoritma yang akan saya gunakan, antara lain:

1. K-Nearest Neighbor
   - Kelebihan:
     - Sederhana dan mudah diimplementasikan: Tidak memerlukan asumsi distribusi data.
     - Non-parametrik: Tidak membuat asumsi tentang bentuk distribusi data.
     - Fleksibel: Dapat digunakan untuk klasifikasi dan regresi.
   - Kekurangan:
     - Lambat pada data besar: Perhitungan jarak untuk semua data memerlukan banyak waktu.
     - Sensitif terhadap skala fitur: Performa bisa terganggu jika skala fitur tidak dinormalisasi.
     - Rentan terhadap outlier: Outlier dapat mempengaruhi prediksi.
2. Random Forest
   - Kelebihan:
     - Akurasi tinggi: Menghasilkan model yang kuat melalui penggabungan banyak pohon keputusan.
     - Resisten terhadap overfitting: Karena menggunakan banyak pohon, cenderung tidak overfit.
     - Dapat menangani data yang hilang dan fitur penting: Mampu menangani data yang tidak lengkap.
   - Kekurangan:
     - Kurang interpretatif: Sulit untuk menafsirkan hasil model karena kompleksitas pohon yang dihasilkan.
     - Lambat dalam prediksi: Meskipun cepat dalam pelatihan, bisa lambat saat melakukan prediksi pada dataset besar.
     -
3. Boosting Algorithm
   - Kelebihan:
     - Akurasi sangat tinggi: Memperbaiki kesalahan dari model sebelumnya sehingga cenderung menghasilkan prediksi yang lebih akurat.
     - Bagus untuk data tidak seimbang: Dapat bekerja dengan baik pada data yang memiliki distribusi kelas yang tidak seimbang.
     - Mengurangi bias: Fokus pada kesalahan model sebelumnya mengurangi bias model.
   - Kekurangan:
     - Lebih rentan terhadap overfitting: Jika tidak diatur dengan baik, dapat menghasilkan model yang terlalu fit terhadap data pelatihan.
     - Waktu pelatihan yang lama: Karena model dilatih secara berurutan, pelatihan bisa memakan waktu lebih lama.
     - Memerlukan tuning parameter: Hyperparameter harus diatur dengan cermat untuk performa yang optimal.

Tahap ini hanya digunakan untuk melatih data training dan menyimpan data testing dari semua model untuk tahap evaluasi yang akan dibahas di Modul Evaluasi Model

### Model K-Nearest Neighbor (KNN)

KNN adalah algoritma yang relatif sederhana dibandingkan dengan algoritma lain. Algoritma KNN menggunakan ‘kesamaan fitur’ untuk memprediksi nilai dari setiap data yang baru. Dengan kata lain, setiap data baru diberi nilai berdasarkan seberapa mirip titik tersebut dalam set pelatihan.

KNN bekerja dengan membandingkan jarak satu sampel ke sampel pelatihan lain dengan memilih sejumlah k tetangga terdekat (dengan k adalah sebuah angka positif). Nah, itulah mengapa algoritma ini dinamakan K-nearest neighbor (sejumlah k tetangga terdekat). KNN bisa digunakan untuk kasus klasifikasi dan regresi. Pada modul ini, kita akan menggunakannya untuk kasus regresi.

```
knn = KNeighborsClassifier(n_neighbors=10)
knn.fit(X_train, y_train)
knn_predictions = knn.predict(X_test)
```

pada tahapan ini kita akan melatih data dengan KNN, kita menggunakan `n_neighbors`= 10 tetangga dan metric Euclidean untuk mengukur jarak antara titik.

### Model Random Forest

Algoritma random forest adalah salah satu algoritma supervised learning. Ia dapat digunakan untuk menyelesaikan masalah klasifikasi dan regresi. Random forest juga merupakan algoritma yang sering digunakan karena cukup sederhana tetapi memiliki stabilitas yang mumpuni.

Random forest merupakan salah satu model machine learning yang termasuk ke dalam kategori ensemble (group) learning. Apa itu model ensemble? Sederhananya, ia merupakan model prediksi yang terdiri dari beberapa model dan bekerja secara bersama-sama.

```
RF = RandomForestClassifier(n_estimators=50, max_depth=16, random_state=55, n_jobs=-1)
RF.fit(X_train, y_train)
RF_predictions = RF.predict(X_test)
```

Berikut adalah parameter-parameter yang digunakan:

- `n_estimator`: jumlah trees (pohon) di forest. Di sini nilai set `n_estimator`=50.
- `max_depth`: ukuran seberapa banyak pohon dapat membelah (splitting) untuk membagi setiap node ke dalam jumlah pengamatan yang diinginkan. Di sini nilai set `max_depth`=16.
- `random_state`: digunakan untuk mengontrol random number generator yang digunakan. Di sini nilai set `random_state`=55.
- `n_jobs`: komponen untuk mengontrol thread atau proses yang berjalan secara paralel. Di sini nilai set `n_job`s=-1 artinya semua proses berjalan secara paralel.

### Model Boosting Algorithm

Teknik boosting, model dilatih secara berurutan atau dalam proses yang iteratif. Algoritma yang menggunakan teknik boosting bekerja dengan membangun model dari data latih. Kemudian ia membuat model kedua yang bertugas memperbaiki kesalahan dari model pertama. Model ditambahkan sampai data latih terprediksi dengan baik atau telah mencapai jumlah maksimum model untuk ditambahkan.

Dilihat dari caranya memperbaiki kesalahan pada model sebelumnya, algoritma boosting terdiri dari dua metode:

1.  Adaptive boosting
2.  Gradient boosting
    Pada modul ini, kita akan menggunakan metode adaptive boosting. Salah satu metode adaptive boosting yang terkenal adalah AdaBoost, dikenalkan oleh Freund and Schapire (1995)

```
boosting = AdaBoostClassifier(learning_rate=0.05, random_state=55)
boosting.fit(X_train, y_train)
boosting_predictions = boosting.predict(X_test)
```

Berikut merupakan parameter-parameter yang digunakan pada potongan kode di atas.

- `learning_rate`: bobot yang diterapkan pada setiap regressor di masing-masing proses iterasi boosting.
- `random_state`: digunakan untuk mengontrol random number generator yang digunakan.

## Evaluation

Pada proses evaluasi kita akan menggunakan metrik `Accuracy`, `Precision`, `Recall` dan `F1-Score` untuk menentukan peforma mana yang terbaik. berikut penjelasannya,

1. `Accuracy` adalah roporsi dari prediksi yang benar terhadap total jumlah data. Ini adalah metrik paling sederhana untuk mengevaluasi model klasifikasi.

   ![accuracy](https://github.com/user-attachments/assets/1aaffaa4-8dde-4285-9712-e6ec6c13ddc3)

- True Positive (TP): Prediksi positif yang benar.
- True Negative (TN): Prediksi negatif yang benar.
- False Positive (FP): Prediksi positif yang salah (kesalahan tipe I).
- False Negative (FN): Prediksi negatif yang salah (kesalahan tipe II).

Akurasi bagus jika data seimbang, tetapi jika data tidak seimbang (misalnya lebih banyak kelas negatif daripada kelas positif), akurasi bisa menyesatkan.

3. `Precision` adalah proporsi prediksi positif yang benar terhadap seluruh prediksi positif yang dibuat oleh model.

![precision](https://github.com/user-attachments/assets/d4ef8767-3c82-43a0-9341-7925f7ead6ce)

- Digunakan saat kita ingin meminimalkan False Positive. Misalnya, dalam diagnosa penyakit, kita ingin memastikan bahwa hasil positif memang benar-benar positif (tidak ada kesalahan positif palsu).
- Cocok digunakan ketika kesalahan positif palsu sangat mahal atau berbahaya.

3. `Recall` adalah proporsi dari prediksi positif yang benar terhadap seluruh sampel yang benar-benar positif.

   ![Recall](https://github.com/user-attachments/assets/c934fc3a-3779-4855-bedb-cdc5b954d49c)

-Recall penting ketika kita ingin meminimalkan False Negative. Misalnya, dalam skrining penyakit berbahaya seperti kanker, kita ingin mengidentifikasi sebanyak mungkin kasus positif, sehingga recall harus tinggi.

- Cocok digunakan ketika kesalahan negatif palsu lebih kritis.

4. `F1-Score` adalah harmonic mean dari Precision dan Recall. Ini adalah metrik yang baik ketika ada ketidakseimbangan antara Precision dan Recall. F1-Score memberikan nilai keseimbangan antara keduanya.

   ![F1](https://github.com/user-attachments/assets/41a89953-7989-4318-b88e-56698bd8b1c6)

- `F1-Score` berguna jika kita memiliki dataset yang tidak seimbang, di mana kita ingin menjaga keseimbangan antara Precision dan Recall.
- F1 lebih rendah jika salah satu dari Precision atau Recall rendah, karena ini merupakan rata-rata harmonik yang lebih memperhatikan nilai yang rendah dibandingkan rata-rata biasa.

Selanjutnya adalah evaluasi ketiga model

```
evaluasi = pd.DataFrame(columns=['train', 'test'], index = ['KNN', 'RF', 'Boosting'])

model_dict = {'KNN': knn, 'RF': RF, 'Boosting': boosting}

for name, model in model_dict.items():
    evaluasi.loc[name, 'train'] = metrics.accuracy_score(y_true=y_train, y_pred=model.predict(X_train))/1e3
    evaluasi.loc[name, 'test'] = metrics.accuracy_score(y_true=y_test, y_pred=model.predict(X_test))/1e3
```

output:

|          | Train  | Test   |
| -------- | ------ | ------ |
| KNN      | 94.12% | 92.13% |
| RF       | 99.05% | 94.78% |
| Boosting | 80.14% | 81.27% |

selanjutnya kita akan melihat nilai `Accuracy`, `Precision`, `Recall` dan `F1-Score` pada ketiga model

```
# Calculate metrics for each model
model_metrics = {model: calculate_metrics(y_true, predictions) for model, predictions in models.items()}

# Print the metrics for each model
for model, metrics in model_metrics.items():
    print(f"Model: {model}")
    for metric_name, metric_value in metrics.items():
        print(f"{metric_name}: {metric_value:.4f}")
    print("-" * 20)

```

hasilnya adalah

```
Model: KNN
Accuracy: 0.9213
Precision: 0.9212
Recall: 0.9213
F1 Score: 0.9210
--------------------
Model: Random Forest
Accuracy: 0.9478
Precision: 0.9480
Recall: 0.9478
F1 Score: 0.9476
--------------------
Model: Boosting
Accuracy: 0.8127
Precision: 0.8286
Recall: 0.8127
F1 Score: 0.8095
--------------------
```

Selanjutnya kita uji prediksinya menggunakan beberapa nilai dalam data dan mendapatkan hasil prediksi sebagai berikut
| y_true | prediksi_KNN | prediksi_RF | prediksi_Boosting |
|--------|--------------|-------------|-------------------|
| Rainy | Snowy | Rainy | Snowy |

Berdasarkan hasil visualisasi dan nilai data diatas, terlihat bahwa model `K-Nearest Neighbors` memiliki nilai `Accuracy` dengan nilai 92,13%, `Precision` dengan nilai 92,12%, `Recall` dengan nilai 92,13%, dan `F1 Score` dengan nilai 92,10%. Pada model `Random Forest` memiliki `Accuracy` dengan nilai 94,78%, `Precision` dengan nilai 94,8%, `Recall` dengan nilai 94,78%, dan `F1 Score` dengan nilai 94,76%, sedangkan Pada model `Boosting Algorithm` memiliki `Accuracy` dengan nilai 91,27%, `Precision` dengan nilai 82,86%, `Recall` dengan nilai 81,27%, dan `F1 Score` dengan nilai 80,95%. Berdasarkan data tersebut, `Random Forest` menjadi model dengan nilai terbaik.
Selain itu, hasil prediksi `K-Nearest Neighbors` dan `Random Forest` menjadi yang paling mendekati nilai sebenarnya. Maka dari itu permodelan yang akan digunakan untuk mengklasifikasikan cuaca adalah model `Random Forest`, semoga dengan model ini bisa membantu menentukan klasifikasi cuaca yang terbaik sesuai data.

Untuk hubungan antara fitur dengan target, `UV Index` dan `Weather Type` memiliki nilai korelasi 0,41 menunjukkan adanya korelasi positif sedang antara kedua variabel tersebut. Korelasi positif berarti bahwa ketika `UV Index` meningkat, kemungkinan besar `Weather Type` juga akan berubah ke arah yang lebih tinggi.
Dalam konteks ini, interpretasinya bisa berarti bahwa semakin tinggi nilai UV Index (yang biasanya menunjukkan sinar matahari yang lebih kuat), tipe cuaca cenderung bergerak ke arah cuaca yang lebih cerah atau lebih berpotensi terkena sinar matahari langsung. Ini masuk akal karena indeks UV biasanya lebih tinggi pada hari-hari cerah dan berkurang pada hari mendung atau hujan.
Namun, karena nilai korelasinya 0,41, ini hanya menunjukkan korelasi sedang, yang berarti UV Index adalah salah satu dari beberapa faktor yang mempengaruhi Weather Type.

Berbeda dengan `UV Index`, `Cloud Cover` memiliki nilai korelasi -0,57 antara `Cloud cover` (penutupan awan) dan `Weather Type` (tipe cuaca) menunjukkan bahwa terdapat korelasi negatif sedang antara kedua variabel tersebut. Korelasi negatif berarti bahwa ketika `Cloud cover` meningkat, kemungkinan besar `Weather Type` bergerak ke arah yang lebih rendah (atau berlawanan).
Dalam hal ini, interpretasi sederhana bisa berarti bahwa semakin tinggi penutupan awan (cuaca mendung), kemungkinan besar tipe cuaca yang berkaitan dengan cerah atau matahari akan lebih kecil, sementara tipe cuaca yang lebih mendung, hujan, atau badai lebih mungkin terjadi.
