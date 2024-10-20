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
2. Dengan data rating yang Anda miliki, bagaimana perusahaan dapat merekomendasikan restoran lain yang mungkin disukai dan belum pernah dikunjungi oleh pengguna? 

### Goals

Goals/tujuan dari poyek ini adalah:

1. Menghasilkan sejumlah rekomendasi restoran yang dipersonalisasi untuk pengguna dengan teknik content-based filtering.
2. Menghasilkan sejumlah rekomendasi restoran yang sesuai dengan preferensi pengguna dan belum pernah dikunjungi sebelumnya dengan teknik collaborative filtering.

### Solution statements

Beberapa solusi yang akan coba terapkan adalah:

1. Melakukan eksplorasi fitur seperti cek duplikasi data, cek missing variabel di tiap data
2. Untuk mendapatkan data yang bersih sebelum di buat permodelan. Dilakukan preparation data yang terdiri dari Menghapus missing value, Menghapus rating user yang bernilai -1, Encoding Fitur Kategori, Train-Test-Spit dan Standarisasi.
3. Permodelan akan dilakukan dengan 2 metode yaitu.

## Data Understanding

Data yang saya gunakan berasal dari Kaggle dengan judul Weather Type Classification [cuaca](https://www.kaggle.com/datasets/nikhil7280/weather-type-classification), Data tersebut berisi 13200 data dan 11 kolom dengan 7 data numerik dan 4 data katgorik. Berikut adalah detail masing-masing kolomnya:

### Variabel-variabel pada dataset adalah sebagai berikut:

- `Temperature` (numeric) : Temperatur suhu dalam celcius
- `Humidity` (numeric) : Presentase Kelembaban
- `Wind Speed` (numeric) : Kecepatan angin dalam kilometer/jam
- `Precipitation (%)` (numeric) : Presentase curah hujan
- `Cloud Cover` (categorical) : Deskripsi tutupan awan yang berisi clear, cloudy, overcast dan party cloudy
- `Atmospheric Pressure` (numeric) : Tekanan atmosfer dalam hPa
- `UV index` (numeric) : Indeks UX yang menunjukkan kekuatan radiasi UV
- `Season` (categorical) : Jenis musim mulai dari Autumn, Spring, Summer dan Winter
- `Visibility (km)` (numeric) : Jarak pandang dalam km
- `Location` (categorical) : Lokasi dimana data di ambil seperti coastal, inland dan muntain
- `Weather Type` (categorical) : Jenis cuaca yang berisi Cloudy, Rainy, Snowy dan Sunny (Target Klasifikasi)

Tahapan yang akan saya lakukan adalah:

### Exploratory Data Analysis

Exploratory data analysis atau sering disingkat EDA merupakan proses investigasi awal pada data untuk menganalisis karakteristik, menemukan pola, anomali, dan memeriksa asumsi pada data. Teknik ini biasanya menggunakan bantuan statistik dan representasi grafis atau visualisasi.

Berikut adalah tahapan EDA yang dilakukan
- cek nilai duplikat pada data

```
duplicate_rows = cuaca[cuaca.duplicated()]
print("Jumlah baris duplikat:", duplicate_rows.shape[0])
```

Output: Tidak terdapat baris duplikat

- cek missing value pada data

```
print(cuaca.isnull().sum())
```

output:

```
Temperature             0
Humidity                0
Wind Speed              0
Precipitation (%)       0
Cloud Cover             0
Atmospheric Pressure    0
UV Index                0
Season                  0
Visibility (km)         0
Location                0
Weather Type            0
dtype: int64
```

Tidak terdapat missing value

### Univariate Analysis

Univariate Analysis adalah jenis analisis data yang memeriksa satu variabel saja. Tujuannya uuntuk menggambarkan data dan menemukan pola distribusi data

Sebelum mulai analysis data akan dibagi menjadi 2 bagian, yakni `numerical_fitur` untuk data numerik dan `categorical_features` untuk data kategorik

```
numerical_features = ['Temperature', 'Humidity', 'Wind Speed', 'Precipitation (%)', 'Atmospheric Pressure', 'UV Index', 'Visibility (km)', 'Weather Type']
categorical_features = ['Cloud Cover', 'Season', 'Location']
```

data telah terbagi menjadi numerical_features untuk data numerik dan categorical_features untuk data kategorik

#### Categorical Features

Menampilkan data fitur dalam bentuk grafik

```
feature = categorical_features[2]
count = cuaca[feature].value_counts()
percent = 100*cuaca[feature].value_counts(normalize=True)
df = pd.DataFrame({'jumlah sampel':count, 'persentase':percent.round(1)})
print(df)
count.plot(kind='bar', title=feature);
```

output:

- Fitur CLoud Cover

  ![uni1](https://github.com/user-attachments/assets/eb54bde9-018c-4812-bd29-512a7e631bd7)

  Berdasarkan grafik pada fitur `Cloud Cover` di atas:

  - `overcast` memiliki 6090 data
  - `party cloud` memiliki 4560 data
  - `clear` memiliki 2139 data
  - `cloudy` memiliki 411 data

- Fitur Season

  ![uni2](https://github.com/user-attachments/assets/89a79189-0ca8-4c7a-aa51-5ff1c5ce3347)

  Berdasarkan grafik pada fitur `Season` di atas:

  - `winter` memiliki 5610 data
  - `Spring` memiliki 2598 data
  - `Autumn` memiliki 2500 data
  - `Summer` memiliki 2492 data
  -

- Fitur Location

  ![uni3](https://github.com/user-attachments/assets/8c64caaf-f523-4d1a-9b46-f8e3d156f937)

  Berdasarkan grafik pada fitur `Location` di atas:

  - `inland` memiliki 4816 data
  - `mountain` memiliki 4813 data
  - `coastal` memiliki 3571 data

- Fitur Weather Type

  ![uni4](https://github.com/user-attachments/assets/54c538c7-c9e4-4724-9716-01aa95a835d5)

  Berdasarkan grafik pada fitur `Weather Type` di atas, nilai pada kolom ini terlihat seimbang dengan rincian sebagai berikut:

- `Rainy` memiliki 3300 data
- `Cluody` memiliki 3300 data
- `Sunny` memiliki 3300 data
- `Snowy` memiliki 3300 data

#### Numerical Features

Menampilkan data numerik dalam bentuk grafik

![uni numerical](https://github.com/user-attachments/assets/86aecae9-6efc-4857-998e-95d1963c7072)

Berdasarkan grafik diatas, hampir semmua kolom skewnessnya mengarah ke kiri kecuali `Humidity`, `Precipitaion (%)` dan `Atmospheric Pressure`.

### Multivariate Analysis

Multivariate Analysis menunjukkan hubungan antara dua atau lebih variabel pada data. Multivariate Analysis yang menunjukkan hubungan antara dua variabel biasa disebut sebagai bivariate Analysis. Selanjutnya, akan dilakukan analisis data pada fitur kategori dan numerik.

#### Categorical Features

Menampilkan hubungan fitur kategori dengan target `Weather Type`

```
for feature in categorical_features[:-1]:
  plt.figure(figsize=(10, 6))
  sns.countplot(x=feature, hue='Weather Type', data=cuaca)
  plt.title(f'Hubungan {feature} dengan Weather Type')
  plt.xlabel(feature)
  plt.ylabel('Jumlah Data')
  plt.legend(title='Weather Type')
  plt.show()
```

output:

- Fitur `Cloud Cover` dengan `Weather Type`
  ![multi 1](https://github.com/user-attachments/assets/87fc3e98-d64e-40e9-9e40-63b7ac6489c9)

- Fitur `Season` dengan `Weather Type`
  ![multi 2](https://github.com/user-attachments/assets/e5ddf0dc-65e4-4e3d-8855-658535b1614d)

- Fitur `Location` dengan `Weather Type`
  ![multi 3](https://github.com/user-attachments/assets/f6a32fd5-234f-4ae4-93f7-0550e2c9b371)

Berdasarkan grafik di atas didapatn:

- Pada Fitur `Cloud Cover`

1. Pada `party cloudy` jumlah `Coudy` menjadi yang terbanyak hampir mendekati 2000 data
2. pada `Clear` semua nilainya diisi dengan tipe `Sunny`
3. pada `overccast` nilia terendahnya adda pada `Sunny` dan yang terbanyak adalah `Rainy` dan `Snowy`
4. pada `cludy`, hampr semua datanya rata

- Pada fitur `Season`

  1. pada `winter` jumlah `Snowy` menjadi yang paling besar melebihi 3000 data, sedangkan yang lain ada di bawah 1000 data
  2. pada `Spring`, `Summer` dan `Autumn` datanya hampir rata antara 500 - 1000 data kecuali pada data `Snowy` yang jumlahnya sangat sedikit

- Pada fitur `Location`
  1. pada `inland` dan `mountain` jumlah datanya hampir sama dengan data tertinggi ada pada `Snowy`
  2. Sedangkan pada `coastal`, data `Snowy` menjdai yang terendah dengan jumlah kurang dari 200 data

#### Numerical Features

Menampilkan hubungan antar fitur numerik dengan target `Weather Type`

```
sns.pairplot(cuaca, hue='Weather Type')
plt.show()
```

output:

![multi numerical](https://github.com/user-attachments/assets/ae924be0-0161-4275-a87b-ef70d05fba11)

Berdasarkan visualisasi data diatas, tidak terlihat adanya hubungan yang signifikan antara fitur dengan target `Weather Type`

## Data Preparation

Data preparation merupakan tahapan penting dalam proses pengembangan model machine learning. Ini adalah tahapan dilakukannya proses transformasi pada data sehingga menjadi bentuk yang cocok untuk proses pemodelan. Dalam data preparation akan dilakukan 3 tahapan, yakni Encoding Fiitur Kategori, Train-Test-Split dan Standarisasi.

### Menangani Outlier

Outlier adalah titik data yang secara signifikan berada di sebgaian data dalam kumpulan data. Outlier ini bisa muncul karena banyak faktor salah satunya adalah kesalahan pengamatan.

- Menampilkan data outlier

  ```
  for column in cuaca.select_dtypes(include=np.number).columns:
  plt.figure(figsize=(8, 6))
  sns.boxplot(x=cuaca[column])
  plt.title(f'Boxplot of {column}')
  plt.show()
  ```

  output:

  ![outlier1](https://github.com/user-attachments/assets/233ce168-9edd-4823-b346-9c895d9435b4)
  ![outlier3](https://github.com/user-attachments/assets/1e0c1417-06d9-47eb-bc20-9cb80bc10512)
  ![outlier5](https://github.com/user-attachments/assets/f7d3f274-a220-45d8-80ba-39b330c48cdd)
  ![outlier7](https://github.com/user-attachments/assets/6bb6db3f-88aa-4cbd-a4e3-85db348acab0)

  berdasarkan boxplot tersebut, ada 4 fitur yang memiliki outlier yakni fitur `Temperature`, `Wind           Speed`, `Athmospheric Pressure`, dan `Visibility (km)`

- Outlier pada 4 fitur tersebut perlu dihapus untuk mendapatkan model yang bagus

  ```
  numeric_cuaca = cuaca.select_dtypes(include=np.number)

  Q1 = numeric_cuaca.quantile(0.25)
  Q3 = numeric_cuaca.quantile(0.75)
  IQR = Q3 - Q1
  lower_bound = Q1 - 1.5 * IQR
  upper_bound = Q3 + 1.5 * IQR

  cuaca = cuaca[~((numeric_cuaca < lower_bound) | (numeric_cuaca > upper_bound)).any(axis=1)]
  ```

  Outlier telah berhasil dihapus.

- Menampilkan data terbaru setelah outliers dihapus

  ```
  cuaca.shape
  ```

  output:
  `(11689, 11)`

  Jumlah data terbaru sekarang adalah 11689 data

### Mengubah Type Data

Pada bagian ini, semua data kategorik akan diubah menjadi data numerik untuk mempermudah permodelan dan menentukan nilai uji korelasi antar fitur.

- Mengubah data

  ```
  le = LabelEncoder()
  cuaca['Cloud Cover'] = le.fit_transform(cuaca['Cloud Cover'])
  cuaca['Location'] = le.fit_transform(cuaca['Location'])
  cuaca['Season'] = le.fit_transform(cuaca['Season'])
  cuaca['Weather Type'] = le.fit_transform(cuaca['Weather Type'])
  cuaca.head()
  ```

  |     | Temperature | Humidity | Wind Speed | Precipitation (%) | Cloud Cover | Atmospheric Pressure | UV Index | Season | Visibility (km) | Location | Weather Type |
  | --- | ----------- | -------- | ---------- | ----------------- | ----------- | -------------------- | -------- | ------ | --------------- | -------- | ------------ |
  | 0   | 14.0        | 73       | 9.5        | 82.0              | 3           | 1010.82              | 2        | 3      | 3.5             | 1        | 1            |
  | 1   | 39.0        | 96       | 8.5        | 71.0              | 3           | 1011.43              | 7        | 1      | 10.0            | 1        | 0            |
  | 2   | 30.0        | 64       | 7.0        | 16.0              | 0           | 1018.72              | 5        | 1      | 5.5             | 2        | 3            |
  | 3   | 38.0        | 83       | 1.5        | 82.0              | 0           | 1026.25              | 7        | 1      | 1.0             | 0        | 3            |
  | 4   | 27.0        | 74       | 17.0       | 66.0              | 2           | 990.67               | 1        | 3      | 2.5             | 2        | 1            |

data kategorik sudah berhasil diubah menjadi data numerik

- Melakukan uji korelasi

  ```
   # Mengetahui skor korelasi
   plt.figure(figsize=(10, 8))
   correlation_matrix = cuaca.corr().round(2)

   # Untuk menge-print nilai di dalam kotak, gunakan parameter anot=True
   sns.heatmap(data=correlation_matrix, annot=True, cmap='coolwarm', linewidths=0.5, )
   plt.title("Correlation Matrix untuk Fitur Numerik ", size=20)
   plt.tight_layout()
  ```

  output:

  ![korelasi](https://github.com/user-attachments/assets/901da1a1-4c83-409a-a41e-2478dd19e511)

Berdasarkan nilai korelasi di atas `Temperature`, `Visibilty (km)`, dan `Location` adalah fitur yang mempunyai nilai korelasi paling kecil dengan target `Weather Type` dan akan di hapus

### Hapus Kolom dengan Korelasi Terendah

bagian ini adalah proses penghapusan fitur-fitur yang memiliki korelasi rendah terhadap variabel target dari dataset. Langkah ini diambil berdasarkan asumsi bahwa fitur dengan korelasi rendah tidak memberikan kontribusi signifikan terhadap prediksi yang dibuat oleh model.

```
cuaca.drop(['Temperature', 'Visibility (km)', 'Location'], inplace=True, axis=1)
cuaca.head()
```

output:

|     | Humidity | Wind Speed | Precipitation (%) | Cloud Cover | Atmospheric Pressure | UV Index | Season | Weather Type |
| --- | -------- | ---------- | ----------------- | ----------- | -------------------- | -------- | ------ | ------------ |
| 0   | 73       | 9.5        | 82.0              | 3           | 1010.82              | 2        | 3      | 1            |
| 1   | 96       | 8.5        | 71.0              | 3           | 1011.43              | 7        | 1      | 0            |
| 2   | 64       | 7.0        | 16.0              | 0           | 1018.72              | 5        | 1      | 3            |
| 3   | 83       | 1.5        | 82.0              | 0           | 1026.25              | 7        | 1      | 3            |
| 4   | 74       | 17.0       | 66.0              | 2           | 990.67               | 1        | 3      | 1            |

Penghapusan fitur `Temperature` ,`Visibilty (km)`, `Location` karena memiliki nilai korelasi yang rendah. Berdasarkan data terbaru, tersisa 8 kolom

### Train-Test-Split

Train-Test-Split adalah metode untuk membagi dataset menjadi data latih (train) dan data uji (test). Biasanya data akan dibagi dengan proporsi tertentu. Dalam kasus ini saya akan membagi data menjadi 90:10 dimana 90% untuk training dan 10% untuk testing

```
X = cuaca.drop(['Weather Type'],axis =1)
y = cuaca['Weather Type']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.1, random_state = 123)
```

Berdasarkan output diatas kita telah sukses melakukan proses Train-Test-Split, terlihat bahwa:

- Dataset train memiliki 10520 data
- Dataset test memiliki 1169 data

### Standarisasi

Standardisasi adalah teknik transformasi yang paling umum digunakan dalam tahap persiapan pemodelan. Untuk fitur numerik, kita tidak akan melakukan transformasi dengan one-hot-encoding seperti pada fitur kategori. Kita akan menggunakan teknik StandarScaler dari library Scikitlearn.

StandardScaler melakukan proses standarisasi fitur dengan mengurangkan mean (nilai rata-rata) kemudian membaginya dengan standar deviasi untuk menggeser distribusi. StandardScaler menghasilkan distribusi dengan standar deviasi sama dengan 1 dan mean sama dengan 0. Sekitar 68% dari nilai akan berada di antara -1 dan 1.

Pada kasus ini kita hanya akan melakukan standarisai pada data latih dan data uji.

1. Standarisasi pada data latih
```
scaler = StandardScaler()
X_train[:] = scaler.fit_transform(X_train[:])
X_train.head()
```
Mengecek nilai mean dan standar deviasi setelah proses standarisasi

```
X_train[numerical_features].describe().round(4)
```

output:

|       | Humidity   | Wind Speed | Precipitation (%) | Cloud Cover | Atmospheric Pressure | UV Index   | Season     |
| ----- | ---------- | ---------- | ----------------- | ----------- | -------------------- | ---------- | ---------- |
| count | 10520.0000 | 10520.0000 | 10520.0000        | 10520.0000  | 10520.0000           | 10520.0000 | 10520.0000 |
| mean  | 0.0000     | -0.0000    | -0.0000           | -0.0000     | -0.0000              | 0.0000     | 0.0000     |
| std   | 1.0000     | 1.0000     | 1.0000            | 1.0000      | 1.0000               | 1.0000     | 1.0000     |
| min   | -2.5471    | -1.6399    | -1.6189           | -1.9194     | -3.3554              | -1.0061    | -1.6132    |
| 25%   | -0.5379    | -0.7509    | -1.0579           | 0.0147      | -0.8084              | -0.7287    | -0.7533    |
| 50%   | 0.0288     | -0.1287    | 0.1263            | 0.0147      | 0.1312               | -0.4512    | 0.1067     |
| 75%   | 0.7501     | 0.7602     | 0.9054            | 0.9817      | 0.7751               | 0.6585     | 0.9666     |
| max   | 2.0380     | 2.9825     | 1.7780            | 0.9817      | 3.3214               | 2.8781     | 0.9666     |

2. Standarisasi pada data uji
```
scaler = StandardScaler()
X_test[:] = scaler.fit_transform(X_test[:])
X_test.head()
```
Mengecek nilai mean dan standar deviasi setelah proses standarisasi

```
X_test.describe().round(4)
```

output:

|           | Humidity | Wind Speed | Precipitation (%) | Cloud Cover | Atmospheric Pressure | UV Index | Season |
|-----------|----------|------------|-------------------|-------------|----------------------|----------|--------|
| count     | 1169.0000| 1169.0000  | 1169.0000         | 1169.0000   | 1169.0000            | 1169.0000| 1169.0000 |
| mean      | -0.0000  | 0.0000     | -0.0000           | 0.0000      | 0.0000               | -0.0000  | 0.0000   |
| std       | 1.0004   | 1.0004     | 1.0004            | 1.0004      | 1.0004               | 1.0004   | 1.0004   |
| min       | -2.5762  | -1.6572    | -1.6548           | -1.9693     | -3.1622              | -0.9804  | -1.6304  |
| 25%       | -0.5187  | -0.7520    | -1.0564           | -0.0059     | -0.7894              | -0.7000  | -0.7684  |
| 50%       | 0.0471   | -0.1183    | 0.1089            | -0.0059     | 0.0988               | -0.4197  | 0.0936   |
| 75%       | 0.7672   | 0.7869     | 0.8648            | 0.9758      | 0.7765               | 0.4214   | 0.9557   |
| max       | 2.0017   | 2.8689     | 1.7781            | 0.9758      | 3.2476               | 2.9444   | 0.9557   |

Seperti yang disebutkan sebelumnya, proses ini akan mengubah nilai rata-rata (mean) menjadi 0 dan standar deviasi menjadi 1.

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

|           | Train   | Test    |
|-----------|---------|---------|
| KNN       | 94.12%  | 92.13%  |
| RF        | 99.05%  | 94.78%  |
| Boosting  | 80.14%  | 81.27%  |

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
````

 Selanjutnya kita uji prediksinya menggunakan beberapa nilai dalam data dan mendapatkan hasil prediksi sebagai berikut
| y_true | prediksi_KNN | prediksi_RF | prediksi_Boosting |
|--------|--------------|-------------|-------------------|
| Rainy  | Snowy        | Rainy       | Snowy             |


Berdasarkan hasil visualisasi dan nilai data diatas, terlihat bahwa model `K-Nearest Neighbors` memiliki nilai `Accuracy` dengan nilai 92,13%, `Precision` dengan nilai 92,12%, `Recall` dengan nilai 92,13%, dan `F1 Score` dengan nilai 92,10%. Pada model `Random Forest` memiliki `Accuracy` dengan nilai 94,78%, `Precision` dengan nilai 94,8%, `Recall` dengan nilai 94,78%, dan `F1 Score` dengan nilai 94,76%, sedangkan Pada model `Boosting Algorithm` memiliki `Accuracy` dengan nilai 91,27%, `Precision` dengan nilai 82,86%, `Recall` dengan nilai 81,27%, dan `F1 Score` dengan nilai 80,95%. Berdasarkan data tersebut, `Random Forest` menjadi model dengan nilai terbaik.
Selain itu, hasil prediksi `K-Nearest Neighbors` dan `Random Forest` menjadi yang paling mendekati nilai sebenarnya. Maka dari itu permodelan yang akan digunakan untuk mengklasifikasikan cuaca adalah model `Random Forest`, semoga dengan model ini bisa membantu menentukan klasifikasi cuaca yang terbaik sesuai data.

Untuk hubungan antara fitur dengan target, `UV Index` dan `Weather Type` memiliki nilai korelasi 0,41 menunjukkan adanya korelasi positif sedang antara kedua variabel tersebut. Korelasi positif berarti bahwa ketika `UV Index` meningkat, kemungkinan besar `Weather Type` juga akan berubah ke arah yang lebih tinggi.
Dalam konteks ini, interpretasinya bisa berarti bahwa semakin tinggi nilai UV Index (yang biasanya menunjukkan sinar matahari yang lebih kuat), tipe cuaca cenderung bergerak ke arah cuaca yang lebih cerah atau lebih berpotensi terkena sinar matahari langsung. Ini masuk akal karena indeks UV biasanya lebih tinggi pada hari-hari cerah dan berkurang pada hari mendung atau hujan.
Namun, karena nilai korelasinya 0,41, ini hanya menunjukkan korelasi sedang, yang berarti UV Index adalah salah satu dari beberapa faktor yang mempengaruhi Weather Type.

Berbeda dengan `UV Index`, `Cloud Cover` memiliki nilai korelasi -0,57 antara `Cloud cover` (penutupan awan) dan `Weather Type` (tipe cuaca) menunjukkan bahwa terdapat korelasi negatif sedang antara kedua variabel tersebut. Korelasi negatif berarti bahwa ketika `Cloud cover` meningkat, kemungkinan besar `Weather Type` bergerak ke arah yang lebih rendah (atau berlawanan).
Dalam hal ini, interpretasi sederhana bisa berarti bahwa semakin tinggi penutupan awan (cuaca mendung), kemungkinan besar tipe cuaca yang berkaitan dengan cerah atau matahari akan lebih kecil, sementara tipe cuaca yang lebih mendung, hujan, atau badai lebih mungkin terjadi.
