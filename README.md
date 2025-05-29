# Laporan Proyek Machine Learning - M Dai Bahtiar

## 1. Domain Proyek

Pariwisata merupakan salah satu sektor penting dalam perekonomian Indonesia. Dengan ribuan destinasi wisata yang tersebar di berbagai provinsi, diperlukan sistem yang mampu memberikan rekomendasi tempat wisata yang sesuai dengan preferensi wisatawan. Proyek ini bertujuan untuk membangun sistem rekomendasi tempat wisata di Indonesia berbasis data menggunakan pendekatan Content-Based Filtering (CBF) dan Collaborative Filtering (CF).

Masalah ini penting untuk diselesaikan agar dapat membantu wisatawan dalam memilih destinasi yang sesuai dan meningkatkan pengalaman wisata mereka. Di sisi lain, sistem ini juga dapat membantu pemangku kebijakan dan pelaku industri pariwisata untuk memahami tren preferensi wisatawan. Berdasarkan laporan BPS (Badan Pusat Statistik), sektor pariwisata Indonesia menyumbang lebih dari 4% terhadap Produk Domestik Bruto (PDB) pada tahun 2019. Namun, menurut studi oleh Ginting dan Nasution (2020), salah satu tantangan dalam pengembangan pariwisata Indonesia adalah ketimpangan distribusi kunjungan wisatawan, yang cenderung terpusat di destinasi populer seperti Bali dan Yogyakarta. Hal ini menunjukkan adanya kebutuhan akan sistem rekomendasi cerdas untuk memperkenalkan destinasi lain yang memiliki potensi serupa.

Sistem rekomendasi wisata telah banyak diterapkan dalam riset terdahulu. Menurut Ricci et al. (2015), sistem rekomendasi berbasis konten dan interaksi pengguna dapat meningkatkan kepuasan pengguna serta distribusi kunjungan ke destinasi yang kurang dikenal.


## 2. Business Understanding
### Problem Statements
1. Bagaimana memberikan rekomendasi tempat wisata berdasarkan preferensi pengguna?
2. Bagaimana membangun sistem rekomendasi yang memanfaatkan ulasan dan rating pengguna untuk memberikan saran destinasi?
### Goals
1. Menghasilkan rekomendasi tempat wisata yang relevan berdasarkan fitur konten destinasi.
2. Menghasilkan rekomendasi berdasarkan interaksi pengguna (rating) dengan destinasi wisata.
### Solution Approach
- Solution 1: Content-Based Filtering (CBF)Pendekatan ini merekomendasikan destinasi wisata yang mirip dengan tempat yang disukai pengguna berdasarkan fitur konten seperti kategori, deskripsi, dan lokasi.
- Solution 2: Collaborative Filtering (CF)Menggunakan pendekatan user-item interaction untuk memberikan rekomendasi berdasarkan pola perilaku pengguna lain yang serupa.


## 3. Data Understanding
### Dataset yang digunakan bersumber dari Kaggle: [Indonesia Tourism Destination Dataset](https://www.kaggle.com/datasets/aprabowo/indonesia-tourism-destination)

### Dataset ini terdiri dari beberapa file:
#### 1. User Dataset
- Jumlah entri: 300
- Kolom:
  - User_Id: ID unik pengguna
  - Location: Lokasi pengguna
  - Age: Usia pengguna
#### 2. Tourism Dataset
- Jumlah entri: 437
- Kolom:
  - Place_Id: ID unik destinasi
  - Place_Name: Nama destinasi
  - Description: Deskripsi destinasi
  - Category: Kategori wisata (alam, budaya, dll.)
  - City: Kota tempat destinasi berada
  - Price: Harga tiket masuk
  - Rating: Rating keseluruhan
  - Time_Minutes: Estimasi waktu kunjungan (banyak yang null)
  - Coordinate: Koordinat lokasi dalam format teks
  - Lat, Long: Koordinat latitude dan longitude
  - Unnamed: 11 & 12: Fitur tidak relevan, akan dihapus saat preprocessing
#### 3. Tourism Rating Dataset
- Jumlah entri: 10.000
- Kolom:
  - User_Id: ID pengguna
  - Place_Id: ID destinasi yang dinilai
  - Place_Ratings: Nilai rating yang diberikan (skala 1–5)
#### 4. Package Tourism Dataset
- Jumlah entri: 100
- Kolom:
  - Package: ID paket wisata
  - City: Kota tujuan paket
  - Place_Tourism1–5: Daftar destinasi dalam paket (banyak missing value di kolom 4 dan 5)

## 4. EDA (Exploratory Data Analysis)
Exploratory Data Analysis (EDA) adalah tahap awal dalam proses analisis data di mana kita mengeksplorasi dan memahami karakteristik data secara menyeluruh sebelum membangun model machine learning. Tujuan utamanya adalah:
- Memahami struktur dan isi dataset.
- Mendeteksi masalah dalam data seperti missing values, duplikat, atau outlier.
- Mengidentifikasi pola, hubungan antar fitur, dan distribusi data.
- Menyediakan dasar pengambilan keputusan untuk preprocessing atau pemilihan model.

#### 1. Informasi Umum Dataset

Pada bagian ini ditampilkan informasi struktural dari setiap dataset, yaitu:
- df_user: Dataset pengguna — menunjukkan jumlah entri (baris), tipe data setiap kolom, dan jumlah non-null values.
- df_tourism: Dataset tempat wisata — berisi data tentang destinasi wisata beserta ID dan kategorinya.
- df_rating: Dataset rating pariwisata — mencakup informasi tentang rating yang diberikan pengguna untuk tempat wisata tertentu.
- df_package: Dataset paket wisata — menyimpan informasi tentang paket wisata yang tersedia.

Dengan fungsi .info(), kita dapat mengecek:
- Apakah ada missing values
- Tipe data dari tiap kolom
- Jumlah total entry per dataset
- 
![image](https://github.com/user-attachments/assets/ecf2c643-d373-4c2e-89c7-b2372d6b151a)

![image](https://github.com/user-attachments/assets/04d1d2a5-c50d-4ca3-9860-0732156ad0fd)

![image](https://github.com/user-attachments/assets/e3f8943a-ec6e-4a67-9afe-707b71ff3454)
. 

#### 2. Pengecekan Missing Values

Pada tahap ini, dilakukan pengecekan untuk melihat apakah ada nilai yang hilang (missing values) di dalam setiap dataset. Missing values dapat mempengaruhi kualitas model, oleh karena itu perlu dilakukan penanganan lebih lanjut seperti pengisian atau penghapusan data yang hilang.
![image](https://github.com/user-attachments/assets/66e5a1d4-c28a-463e-9bc4-a755c7ccfc03)

Hasil pengecekan missing values akan menunjukkan jumlah missing values di setiap kolom pada dataset df_package, df_rating, df_tourism, dan df_user. Ini akan memberi gambaran tentang kualitas data yang dimiliki.

#### 3. Menangani Duplikat Data

Di bawah ini adalah pemeriksaan jumlah data duplikat untuk masing-masing dataset. Duplikat dapat mempengaruhi kualitas analisis dan model, sehingga penting untuk mengidentifikasinya dan mengambil tindakan yang sesuai.

![image](https://github.com/user-attachments/assets/46dddec0-ef10-43ec-a8e2-32ffdfad7201)

#### 4. Visualisasi Distribusi Fitur Numerik

Bagian ini bertujuan untuk memvisualisasikan distribusi fitur numerik yang terdapat pada setiap dataset untuk membantu memahami data lebih baik.
- numeric_dfs: List ini berisi DataFrame yang hanya mencakup kolom numerik dari setiap dataset.
- titles: Daftar berisi judul untuk setiap plot yang akan ditampilkan, sesuai dengan dataset terkait.
- Loop untuk plot histogram: Setiap dataset divisualisasikan menggunakan histogram dengan 20 bin untuk menunjukkan distribusi fitur numerik yang ada. tight_layout() digunakan untuk memastikan bahwa label dan elemen plot tidak saling tumpang tindih.

![image](https://github.com/user-attachments/assets/1fa3c207-2ed9-4d87-b0e3-cf359e32e2f7)
![image](https://github.com/user-attachments/assets/b9f48f6e-0bb5-4022-bd02-cefae63f4bc2)
![image](https://github.com/user-attachments/assets/7f9b24f4-0454-445f-aed8-7ae059a766f8)
![image](https://github.com/user-attachments/assets/9a5b6016-1a79-48e3-9cc2-c5e9a75fce82)

Visualisasi ini membantu kita memahami sebaran data numerik, serta mendeteksi adanya pencilan atau pola distribusi tertentu di setiap dataset.

#### 5. Visualisasi Pairplot untuk Fitur Numerik

Bagian ini berfungsi untuk memvisualisasikan hubungan antar fitur numerik menggunakan pairplot. Pairplot memungkinkan kita untuk melihat distribusi dari setiap fitur dan korelasi antar fitur dalam dataset.

![image](https://github.com/user-attachments/assets/f04c2136-cf4b-4bde-84e4-b92e1aea4a6f)

![image](https://github.com/user-attachments/assets/5a6a161d-ed3c-414d-a13b-8604ea4579c3)

![image](https://github.com/user-attachments/assets/db0053ae-da1f-486e-adf6-919041fd41e5)

Penjelasan:
- numeric_dfs: List ini berisi DataFrame yang hanya mencakup kolom numerik dari setiap dataset.
- titles: Daftar berisi judul untuk setiap plot yang akan ditampilkan, sesuai dengan dataset yang bersangkutan.
- Pairplot: Digunakan untuk memvisualisasikan hubungan antar fitur numerik. Namun, hanya DataFrame dengan kurang dari 5 kolom numerik yang diproses menggunakan pairplot, untuk mencegah plot yang terlalu ramai dan sulit dibaca.
Jika dataset memiliki lebih dari 5 kolom numerik, pairplot akan dilewati untuk menjaga visualisasi tetap relevan dan tidak membingungkan.


#### 6. Visualisasi Heatmap Korelasi

Bagian ini bertujuan untuk menampilkan heatmap korelasi antar fitur numerik dalam setiap dataset. Heatmap korelasi memungkinkan kita untuk dengan cepat mengidentifikasi hubungan antar variabel, dengan warna yang menunjukkan tingkat korelasi antara fitur-fitur tersebut.

![image](https://github.com/user-attachments/assets/3a29a45d-a3da-474f-8f1d-d2185936b2e2)
![image](https://github.com/user-attachments/assets/33b9a133-4f48-4e98-a016-64468c61d772)
![image](https://github.com/user-attachments/assets/c2c446da-1997-4dc8-a484-f94c5d0de849)

#### Penjelasan:
- numeric_dfs: List ini berisi DataFrame yang hanya mencakup kolom numerik dari setiap dataset.
- titles: Daftar yang berisi judul untuk setiap plot yang sesuai dengan dataset yang bersangkutan.
- Heatmap: Digunakan untuk menggambarkan korelasi antar fitur numerik. Setiap sel pada heatmap mewakili korelasi antara dua fitur numerik. Korelasi positif ditandai dengan warna yang lebih terang, sementara korelasi negatif ditandai dengan warna yang lebih gelap. Jika dataset hanya memiliki satu kolom numerik, heatmap akan dilewati.


#### Interpretasi:
- Korelasi tinggi: Jika nilai korelasi mendekati 1 atau -1, maka ada hubungan yang sangat kuat antara dua fitur.
- Korelasi rendah: Jika nilai korelasi mendekati 0, maka hubungan antar fitur sangat lemah atau hampir tidak ada.

#### Manfaat:
- Membantu untuk memahami hubungan antar fitur.
- Bisa menjadi indikasi untuk pemilihan fitur atau penghapusan fitur yang sangat berkorelasi tinggi (multikolinearitas).


#### 7. Visualisasi Distribusi Kategorikal

Bagian ini digunakan untuk menampilkan distribusi fitur kategorikal pada setiap dataset. Visualisasi yang digunakan berupa countplot dan barplot, untuk memberikan gambaran mengenai seberapa banyak setiap kategori muncul dalam dataset.

![image](https://github.com/user-attachments/assets/2c5fd4a5-7ae5-46af-942a-36d58993b252)
![image](https://github.com/user-attachments/assets/a8b82ea4-d7eb-4f13-bd43-67ed1aa62e36)
![image](https://github.com/user-attachments/assets/1f2ee38c-f92c-43d6-976a-6fe46b85a134)
![image](https://github.com/user-attachments/assets/e43630c2-55c3-432f-ab23-46c7c6eaf694)
![image](https://github.com/user-attachments/assets/564db492-a476-46a4-a780-331493325a2f)
![image](https://github.com/user-attachments/assets/39046c2d-f036-4b9b-8d38-b2e23ccfbd1f)
![image](https://github.com/user-attachments/assets/02293ae2-b530-4904-a7c2-f077a7ead140)
![image](https://github.com/user-attachments/assets/816ba080-a0aa-4617-8e2d-21f61182b734)
![image](https://github.com/user-attachments/assets/8a4f1d29-c32b-403a-92e6-f4f224f9770f)
![image](https://github.com/user-attachments/assets/2e8f7da6-a4de-4e11-906f-3cfbe871c790)


#### Penjelasan:
- categorical_dfs: List yang berisi DataFrame yang hanya mencakup kolom bertipe kategorikal (tipe object) dari setiap dataset.
- titles: Daftar judul untuk setiap plot berdasarkan dataset yang bersangkutan.
- Distribusi Kategorikal: Jika kolom kategorikal memiliki kurang dari 30 kategori, kita menggunakan countplot untuk menampilkan distribusi kategori tersebut. Jika lebih dari 30 kategori, kita hanya menampilkan top 15 kategori teratas menggunakan barplot.

####  Interpretasi:
- Countplot: Digunakan untuk visualisasi frekuensi setiap kategori. Jika kolom memiliki sedikit kategori (misal: lokasi, kategori tempat wisata), countplot sangat cocok untuk menampilkan distribusinya.
- Barplot: Digunakan jika jumlah kategori lebih banyak. Menampilkan 15 kategori teratas berdasarkan frekuensi kemunculan.

#### Manfaat:
- Membantu memahami distribusi data kategorikal dan memeriksa ketidakseimbangan kelas pada kolom kategorikal.
- Memberikan wawasan untuk memperbaiki data seperti kategori yang jarang muncul (outlier kategorikal), atau memilih fitur kategorikal mana yang penting untuk model.


#### 8. Statistik Deskriptif Fitur Kategorikal

Bagian ini akan menampilkan statistik deskriptif untuk kolom-kolom bertipe kategorikal (tipe data object) di setiap dataset. Statistik deskriptif ini memberikan informasi tentang distribusi nilai dalam kolom kategorikal.

![image](https://github.com/user-attachments/assets/2fcc5dde-2517-4521-8294-cbd7a2eff293)

![image](https://github.com/user-attachments/assets/6b262772-978f-4f2d-93f2-7265f65e0760)


#### Penjelasan:

1. df.describe(include='object'):
- Fungsi ini memberikan statistik deskriptif untuk kolom bertipe object (biasanya untuk kolom kategorikal). Statistik yang dihasilkan antara lain:
  - count: jumlah data yang tidak kosong.
  - unique: jumlah nilai unik dalam kolom.
  - top: nilai yang paling sering muncul.
  - freq: frekuensi kemunculan nilai yang paling sering.
2. Penggunaan Looping for df, title in zip(categorical_dfs, titles):
- Melalui looping ini, kode akan menjalankan statistik deskriptif untuk setiap DataFrame yang berisi kolom-kolom kategorikal.

#### Interpretasi Hasil:
- count: Memberikan informasi seberapa banyak data dalam kolom tersebut, menghindari adanya nilai yang hilang (NaN).
- unique: Memberikan jumlah kategori yang berbeda dalam kolom tersebut, memberi gambaran tentang keragaman data kategorikal.
- top: Menunjukkan kategori yang paling sering muncul, memberikan insight tentang kategori dominan dalam dataset.
- freq: Memberikan frekuensi kemunculan kategori yang paling sering.

#### Manfaat:
- Mengetahui distribusi nilai dalam kolom kategorikal membantu kita memahami variasi dan konsentrasi data.
- Membantu dalam mendeteksi data yang memiliki kategori dominan yang mungkin mempengaruhi model rekomendasi.
- Dapat menunjukkan apakah data tersebut memiliki banyak kategori yang unik atau apakah ada kategori yang sangat jarang, yang penting untuk desain model rekomendasi.


#### 9. Distribusi Jumlah Rating per User dan per Tempat Wisata

Visualisasi ini bertujuan untuk memahami pola interaksi pengguna terhadap tempat wisata berdasarkan data rating. Terdapat dua histogram yang ditampilkan:

Distribusi Jumlah Rating per User
Menunjukkan seberapa banyak rating yang diberikan oleh masing-masing pengguna. Ini berguna untuk mengetahui tingkat keaktifan pengguna dalam memberikan penilaian terhadap tempat wisata. Jika sebagian besar pengguna hanya memberikan sedikit rating, maka data bersifat sparsity dan bisa memengaruhi performa collaborative filtering.

Distribusi Jumlah Rating per Tempat Wisata
Menampilkan jumlah rating yang diterima oleh masing-masing tempat wisata. Visualisasi ini membantu mengidentifikasi tempat-tempat yang populer (sering dinilai) dan tempat yang kurang populer (jarang dinilai), yang dapat memengaruhi akurasi sistem rekomendasi. Tempat dengan rating terbanyak biasanya menjadi kandidat kuat untuk direkomendasikan.

![image](https://github.com/user-attachments/assets/082cde43-ee6c-4cbc-8309-6345bbf07afb)

Kedua visualisasi ini penting dalam proses eksplorasi data untuk memberikan gambaran umum tentang distribusi interaksi antara pengguna dan item (tempat wisata) sebelum membangun sistem rekomendasi.


#### 10. Distribusi Data Berdasarkan Lokasi, Kategori, dan Kota

Bagian ini menampilkan informasi terkait lokasi pengguna, kategori tempat wisata, dan distribusi kota pada paket wisata.
![image](https://github.com/user-attachments/assets/b634bceb-7f2a-4ad0-913c-62419c30673f)
#### Penjelasan:
1. Lokasi Pengguna (df_user['Location']):
- Menampilkan 10 lokasi teratas berdasarkan jumlah pengguna yang terdaftar di lokasi tersebut.
- Membantu untuk melihat di mana pengguna lebih banyak beraktivitas atau berasal.

2. Kategori Tempat Wisata (df_tourism['Category']):
- Menampilkan 10 kategori tempat wisata terbanyak.
- Membantu untuk melihat kategori tempat wisata yang paling sering dipilih oleh pengguna.

3. Kota dalam Paket Wisata (df_package['City']):
- Menampilkan distribusi kota dalam dataset paket wisata.
- Memberikan informasi tentang kota-kota yang paling banyak muncul dalam paket wisata yang ditawarkan.

#### Interpretasi:
- Lokasi Pengguna: Melihat lokasi dengan jumlah pengguna terbanyak memberikan gambaran tentang konsentrasi pengguna. Ini bisa membantu dalam merancang rekomendasi yang lebih relevan berdasarkan lokasi geografis pengguna.
- Kategori Tempat Wisata: Mengetahui kategori tempat wisata terbanyak memberi wawasan tentang preferensi umum pengguna terhadap jenis tempat wisata. Ini bisa digunakan untuk menyesuaikan rekomendasi tempat wisata berdasarkan tren popularitas.
- Kota dalam Paket Wisata: Menunjukkan distribusi kota dalam paket wisata, memberikan gambaran tentang kota-kota yang lebih sering muncul dalam paket wisata yang ditawarkan, dan ini bisa menjadi informasi penting untuk merancang penawaran wisata lebih baik.

#### Manfaat:
- Memberikan informasi tentang distribusi pengguna, tempat wisata, dan kota pada dataset yang membantu dalam perencanaan model rekomendasi berbasis lokasi dan kategori.
- Memberikan wawasan terkait ketidakseimbangan data, seperti lokasi atau kota yang lebih dominan, yang bisa mempengaruhi cara sistem rekomendasi bekerja.


#### 11. Visualisasi Distribusi Kategori Tempat Wisata

Langkah awal dalam Content-Based Filtering adalah memahami karakteristik dari data tempat wisata. Salah satu informasi penting yang dapat dianalisis adalah kategori dari masing-masing destinasi wisata. Visualisasi ini bertujuan untuk melihat sebaran jumlah tempat wisata berdasarkan kategori seperti budaya, alam, sejarah, dan sebagainya.

![image](https://github.com/user-attachments/assets/36e9013f-d047-4549-b98e-512aec6928ad)

![image](https://github.com/user-attachments/assets/8bd88d55-9dad-42cd-8dac-10b01f4bf3c1)

Dengan menggunakan grafik batang, kita dapat dengan mudah mengidentifikasi kategori yang paling dominan maupun yang jarang muncul dalam dataset. Informasi ini juga bermanfaat untuk memahami potensi kesamaan antar tempat dalam proses rekomendasi berbasis konten nantinya.


#### 12. Analisis Part-of-Speech (POS) pada Nama Tempat Wisata

Untuk mendalami lebih jauh karakteristik dari nama tempat wisata, dilakukan analisis Part-of-Speech (POS) menggunakan pustaka TextBlob. POS Tagging bertujuan untuk mengidentifikasi jenis kata seperti kata benda (noun), kata sifat (adjective), kata kerja (verb), dan lainnya dari kumpulan nama tempat.

![image](https://github.com/user-attachments/assets/1412b0ea-335e-41ec-978f-29f7e5926648)


Dengan menggabungkan semua nama tempat menjadi satu teks, kita dapat menganalisis frekuensi masing-masing jenis kata yang muncul. Visualisasi ini akan memberikan gambaran mengenai struktur umum penamaan tempat wisata, misalnya apakah lebih banyak menggunakan kata benda atau kata sifat.

![image](https://github.com/user-attachments/assets/02d376f0-68fb-49a7-83fd-fc05e5b8a515)


Informasi POS ini juga bisa memberikan insight tambahan jika ingin mengembangkan model rekomendasi yang mempertimbangkan karakter linguistik dari nama tempat.


## 4. Data Preparation
Pada tahap ini, dilakukan berbagai proses persiapan data yang mencakup pembersihan kolom, penanganan missing value, deteksi dan penghapusan outlier, pembuatan fitur, encoding ID, dan normalisasi rating. Semua langkah ini bertujuan untuk memastikan bahwa data siap digunakan dalam proses pemodelan sistem rekomendasi, baik dengan pendekatan Content-Based Filtering maupun Collaborative Filtering.

#### 1. Pembersihan Kolom Tidak Relevan

Pada tahap ini, dilakukan penghapusan kolom-kolom yang tidak diperlukan atau tidak memiliki nilai informasi penting untuk analisis dan pemodelan. Kolom yang dihapus antara lain:
- Coordinate
- Unnamed: 11
- Unnamed: 12

Kolom-kolom ini berasal dari hasil ekspor file CSV yang kurang bersih atau tidak relevan dengan tujuan analisis. Penghapusan dilakukan secara otomatis untuk setiap dataset (user, tourism, rating, dan package) jika kolom tersebut ditemukan.

![image](https://github.com/user-attachments/assets/8e82c80b-8751-4eec-9f23-065e2ab7ddfd)


#### 2. Penanganan Missing Values

Untuk memastikan kualitas data, dilakukan pemeriksaan dan penghapusan nilai kosong (missing values) pada setiap dataset (df_user, df_rating, df_tourism, dan df_package). Proses ini dilakukan menggunakan fungsi dropna(), diikuti dengan pengecekan ulang untuk memastikan tidak ada nilai yang hilang tersisa.

![image](https://github.com/user-attachments/assets/837a7856-0afe-41ac-8772-d4d85593b511)

![image](https://github.com/user-attachments/assets/32aae3a5-7284-4e96-bbf6-15dd7330837e)


#### 3. Penghapusan Data Duplikat
Pada tahap awal preprocessing, dilakukan penghapusan data yang bersifat duplikat pada seluruh dataframe yang digunakan, yaitu: df_user, df_rating, df_tourism, dan df_package.

Langkah ini memastikan bahwa setiap entri dalam dataset bersifat unik dan valid sebelum dilakukan proses lebih lanjut seperti penanganan nilai yang hilang atau pengkodean data.

![image](https://github.com/user-attachments/assets/9f367453-4839-4447-bd62-524c0f3103ee)

![image](https://github.com/user-attachments/assets/c1846d55-3709-44e9-9e57-0554626ab337)

#### 4. Deteksi Outlier Menggunakan Boxpl

Untuk mengidentifikasi adanya outlier dalam data numerik, dilakukan visualisasi boxplot pada masing-masing dataframe: df_package, df_rating, df_tourism, dan df_user.

Langkah ini penting dilakukan sebelum melakukan normalisasi atau proses machine learning lainnya, karena outlier dapat mempengaruhi performa model secara signifikan. Dengan visualisasi ini, pengguna dapat dengan mudah mengamati distribusi data dan menentukan perlakuan selanjutnya terhadap outlier

![image](https://github.com/user-attachments/assets/aa7abf02-b07b-44bb-89b5-7abffaa56a0d)

![image](https://github.com/user-attachments/assets/aa6d53ae-d3a6-4c25-9eb9-cbb380ee5e14)

![image](https://github.com/user-attachments/assets/38787f6d-7153-4a75-9f9d-8046d5edc0d1)

![image](https://github.com/user-attachments/assets/cf5b2682-99ea-46a1-8146-1087e509fdca)

![image](https://github.com/user-attachments/assets/f935d9e5-74a1-4bbf-99f5-49e5cc822897)


#### 5. Menghapus Outlier Menggunakan Metode IQR
Pada bagian ini, dilakukan pembersihan data dari nilai-nilai ekstrem (outlier) menggunakan metode Interquartile Range (IQR). Outlier dapat mempengaruhi hasil analisis dan performa model. Oleh karena itu, penting untuk menghapusnya.

![image](https://github.com/user-attachments/assets/3013f003-3d9f-4005-93bb-326ca764bdb0)

![image](https://github.com/user-attachments/assets/b001f192-bdb3-4737-acca-b86b617913cf)

![image](https://github.com/user-attachments/assets/9e04d4cf-a1f4-4a33-a100-fe47054b9858)


Fungsi remove_outliers_iqr digunakan untuk memfilter data berdasarkan nilai Q1 (kuartil bawah) dan Q3 (kuartil atas), dengan batas toleransi sebesar 1.5 * IQR. Fungsi ini diterapkan ke seluruh kolom numerik dari beberapa dataset, seperti df_user, df_rating, df_tourism, dan df_package. Sebelum dan sesudah proses, ukuran dataset dibandingkan untuk mengetahui seberapa banyak data yang terdeteksi sebagai outlier dan dihapus.


#### 6. Visualisasi Boxplot setelah Penghapusan Outlier
Pada tahap ini, dilakukan visualisasi menggunakan boxplot untuk masing-masing dataset (User, Rating, Tourism, dan Package). Boxplot digunakan untuk:
- Melihat distribusi data numerik
- Mengidentifikasi outlier secara visual
- Memahami sebaran dan variasi data

![image](https://github.com/user-attachments/assets/ee36973d-1d8b-4dde-928b-4b36d18ef086)

![image](https://github.com/user-attachments/assets/6323fdb9-db11-4004-9989-20d9263158c5)

![image](https://github.com/user-attachments/assets/f170a31c-97d2-44e6-b4f8-a896a78a40fa)

![image](https://github.com/user-attachments/assets/da25e3b9-61af-4771-9d32-d34416c53717)

![image](https://github.com/user-attachments/assets/e7546af4-16e3-42a9-b2a5-e5c6a6b7a2a7)

Setiap grafik menampilkan kolom-kolom numerik dari masing-masing DataFrame. Proses ini membantu dalam validasi apakah metode penghapusan outlier sebelumnya telah bekerja secara efektif.

#### 7. Penggabungan Fitur Konten

Pada tahap ini, kolom Place_Name dan Category digabung menjadi satu fitur teks (combined_features). Tanda pemisah | pada kategori diubah menjadi spasi agar lebih cocok untuk analisis teks.

Fitur ini digunakan untuk membentuk representasi konten tempat wisata, yang nantinya akan diproses menggunakan TF-IDF dalam metode Content-Based Filtering.

![image](https://github.com/user-attachments/assets/2d709306-d063-4007-b080-cebbe2f25a65)


#### 8. Ekstraksi Fitur dengan TF-IDF

TF-IDF (Term Frequency–Inverse Document Frequency) digunakan untuk mengubah data teks (combined_features) menjadi representasi numerik. Teknik ini menekankan kata-kata yang penting dan mengurangi bobot kata umum (stop words).

![image](https://github.com/user-attachments/assets/1c534dc1-e8c9-4abb-837c-5b3909ec5e15)


#### 9. Penggabungan Data untuk Collaborative Filtering

![image](https://github.com/user-attachments/assets/06c1ad98-95fd-419f-9bc6-b5ff69b13e2a)

Langkah ini menggabungkan tiga data utama: data rating (df_rating), data pengguna (df_user), dan data tempat wisata (df_tourism). Penggabungan dilakukan berdasarkan User_Id dan Place_Id, sehingga membentuk satu dataframe komprehensif (df) yang memuat informasi lengkap tentang siapa yang memberi rating, terhadap tempat wisata mana, dan informasi kontennya.


#### 10. Encoding ID Pengguna dan Tempat

![image](https://github.com/user-attachments/assets/002da12e-bf3d-40c4-b44d-2ff671113e22)


Pada tahap ini, dilakukan transformasi ID pengguna (User_Id) dan ID tempat (Place_Id) menjadi bentuk numerik menggunakan LabelEncoder. Hal ini penting agar data dapat digunakan sebagai input dalam model machine learning.
- df['user'] dan df['place'] berisi representasi numerik dari pengguna dan tempat.
- num_users dan num_places menyimpan jumlah unik pengguna dan tempat wisata, yang dibutuhkan untuk menentukan ukuran embedding dalam model rekomendasi.


#### 11. Normalisasi Rating

![image](https://github.com/user-attachments/assets/d9382fe9-eca7-4987-8f61-e18ba822dea5)

Sebelum pelatihan model, rating dari pengguna perlu dinormalisasi agar berada dalam rentang 0 hingga 1. Ini dilakukan agar model lebih stabil dan cepat konvergen saat proses training.
- min_rating dan max_rating: mencari nilai rating minimum dan maksimum.
- rating_normalized: menghitung nilai rating yang telah dinormalisasi menggunakan rumus:



#### 12. Pembagian Data untuk Pelatihan dan Validasi
Setelah data disiapkan, langkah selanjutnya adalah memisahkan fitur dan label, serta membagi data menjadi data latih dan data validasi.
- x: berisi pasangan (user, place) sebagai input fitur.
- y: berisi nilai rating yang telah dinormalisasi sebagai target.
- train_test_split: membagi data menjadi 80% untuk pelatihan dan 20% untuk validasi agar model dapat dievaluasi performanya selama training.

![image](https://github.com/user-attachments/assets/ed1d1b1e-b8e0-479f-8ceb-8d866a125bfa)


#### 13. Visualisasi Bigram Menggunakan TF-IDF pada Nama Tempat Wisata
   
Pada bagian ini, dilakukan analisis terhadap bigram, yaitu kombinasi dua kata yang muncul secara berurutan dalam nama tempat wisata. Untuk menghitung bobot kemunculan bigram, digunakan pendekatan TF-IDF (Term Frequency-Inverse Document Frequency) yang tidak hanya mempertimbangkan frekuensi kata, tetapi juga signifikansi relatifnya di seluruh dokumen.

Tujuan dari analisis ini adalah untuk mengidentifikasi pasangan kata yang paling sering dan paling penting dalam nama-nama tempat wisata. Hal ini dapat memberikan insight tentang pola penamaan, misalnya apakah tempat wisata sering menggunakan kata seperti "Taman Wisata", "Pantai Indah", atau kombinasi umum lainnya.

Visualisasi ini membantu kita memahami struktur frasa yang dominan, yang dapat digunakan dalam pengembangan model Content-Based Filtering untuk meningkatkan akurasi rekomendasi berdasarkan kemiripan nama atau kategori tempat.

![image](https://github.com/user-attachments/assets/c35bf313-dc34-45a3-953b-f36fb3c81269)


#### 14. Visualisasi Trigram Menggunakan TF-IDF pada Nama Tempat Wisata
   
Setelah analisis bigram, dilakukan pula analisis terhadap trigram, yaitu kombinasi tiga kata yang muncul berurutan dalam nama tempat wisata. Seperti sebelumnya, metode TF-IDF digunakan untuk mengukur pentingnya trigram yang muncul, bukan hanya berdasarkan frekuensinya tetapi juga berdasarkan tingkat kekhususan trigram di seluruh data.

Trigram dapat memberikan informasi yang lebih kontekstual dibanding unigram (satu kata) atau bigram, karena tiga kata berturut-turut cenderung membentuk frasa yang lebih bermakna atau spesifik. Misalnya, frasa seperti "Taman Wisata Alam" atau "Pantai Pasir Putih" bisa muncul sebagai trigram yang signifikan.

Visualisasi ini membantu mengidentifikasi pola atau struktur umum dalam penamaan tempat wisata yang bisa dimanfaatkan lebih lanjut untuk proses ekstraksi fitur dalam sistem rekomendasi berbasis konten.

![image](https://github.com/user-attachments/assets/3d936eee-d1a9-43d5-9396-e58ffc2212b1)



#### 15. Mapping Nama Tempat ke Index

Kode ini membuat Series bernama indices yang berfungsi untuk memetakan nama tempat wisata ke indeks barisnya di df_tourism. Mapping ini penting untuk mengambil indeks tempat tertentu berdasarkan namanya saat melakukan pencarian rekomendasi. Fungsi drop_duplicates() digunakan agar tidak ada nama tempat yang terduplikasi.

![image](https://github.com/user-attachments/assets/76903675-b555-411d-9d84-e648748b14be)


#### 16. Penggabungan Data untuk Collaborative Filtering

Langkah ini menggabungkan tiga data utama: data rating (df_rating), data pengguna (df_user), dan data tempat wisata (df_tourism). Penggabungan dilakukan berdasarkan User_Id dan Place_Id, sehingga membentuk satu dataframe komprehensif (df) yang memuat informasi lengkap tentang siapa yang memberi rating, terhadap tempat wisata mana, dan informasi kontennya.

![image](https://github.com/user-attachments/assets/9b4cb776-a9c3-41dc-bfae-e6f22addb4ae)





## 5. Modeling

Pada tahap Modeling, dilakukan pembangunan sistem rekomendasi yang bertujuan untuk memberikan saran tempat wisata kepada pengguna berdasarkan preferensi dan pola interaksi yang tersedia dalam data. Sistem rekomendasi ini memainkan peran penting dalam membantu pengguna menemukan destinasi yang relevan, menarik, dan sesuai dengan minat mereka, terutama ketika jumlah pilihan sangat banyak.

Dalam proyek ini, digunakan dua pendekatan utama:

#### 1. Content-Based Filtering (CBF)
Content-Based Filtering adalah metode rekomendasi yang memanfaatkan karakteristik atau konten dari item (dalam hal ini: tempat wisata) untuk mencari kemiripan antar item. CBF bekerja dengan menganalisis informasi seperti nama tempat dan kategori wisata untuk membentuk representasi tekstual, lalu menghitung tingkat kemiripan antar tempat menggunakan teknik seperti TF-IDF dan cosine similarity. Tempat-tempat yang memiliki konten serupa akan direkomendasikan kepada pengguna yang tertarik pada tempat tertentu.

Kelebihan dari pendekatan ini adalah:
- Tidak bergantung pada data pengguna lain.
- Dapat bekerja dengan baik meskipun jumlah interaksi pengguna sedikit (cold-start).


#### 2. Collaborative Filtering (CF)
Collaborative Filtering adalah metode rekomendasi yang memanfaatkan interaksi pengguna terhadap item, seperti memberikan rating atau ulasan. Dalam pendekatan ini, sistem belajar dari pola rating pengguna-pengguna lain untuk memprediksi ketertarikan pengguna terhadap tempat yang belum dikunjungi.

Model yang digunakan adalah model berbasis neural network (Neural Collaborative Filtering), di mana setiap pengguna dan tempat wisata direpresentasikan dalam bentuk embedding—vektor berdimensi rendah yang dilatih selama proses pelatihan. Output dari model ini adalah prediksi skor rating yang bisa digunakan untuk menghasilkan rekomendasi tempat terbaik bagi setiap pengguna.

Kelebihan dari pendekatan ini:
- Mampu menangkap preferensi tersembunyi pengguna.
- Semakin banyak data interaksi, semakin baik kualitas rekomendasi.

### Content-Based Filtering (CBF)

#### 1. Perhitungan Cosine Similarity

Cosine Similarity digunakan untuk mengukur tingkat kemiripan antar tempat wisata berdasarkan representasi vektor dari TF-IDF. Nilai kemiripan ini disusun dalam bentuk matriks, sehingga kita dapat melihat seberapa mirip satu tempat dengan tempat lainnya.

Hasilnya disimpan dalam similarity_df, yang menjadi dasar untuk sistem rekomendasi berbasis konten.

![image](https://github.com/user-attachments/assets/e640691f-6389-4318-94d7-5665fe61098c)

#### 2. similarity_df

similarity_df adalah DataFrame yang berisi skor kemiripan (cosine similarity) antar tempat wisata. Setiap baris dan kolom mewakili satu tempat wisata, dan nilai di sel menunjukkan tingkat kemiripan antara dua tempat. Nilai berkisar dari 0 (tidak mirip) hingga 1 (sangat mirip). Data ini menjadi dasar untuk memberikan rekomendasi tempat wisata yang mirip dengan tempat yang dipilih pengguna.

![image](https://github.com/user-attachments/assets/31039cad-323b-4c86-b349-34b858296148)

#### 3. Fungsi Rekomendasi Tempat Wisata

Fungsi get_recommendations digunakan untuk memberikan rekomendasi tempat wisata yang mirip dengan nama tempat yang diberikan sebagai input. Prosesnya meliputi:
- Mengecek apakah nama tempat ada dalam data.
- Mengambil indeks tempat tersebut.
- Menghitung skor kesamaan (cosine similarity) terhadap semua tempat lain.
- Mengurutkan skor dan mengambil 10 tempat teratas yang paling mirip (selain dirinya sendiri).
- Mengembalikan data tempat yang direkomendasikan dalam bentuk DataFrame.

![image](https://github.com/user-attachments/assets/39823ceb-1b71-4e7a-8cbe-2ef47098b411)

#### 4. Contoh Penggunaan Fungsi Rekomendasi

Contoh berikut menunjukkan cara memanggil fungsi get_recommendations dengan memasukkan nama tempat wisata, dalam hal ini "Museum Taman Prasasti". Fungsi akan menampilkan daftar tempat wisata lain yang memiliki kemiripan konten berdasarkan nama dan kategori menggunakan metode Content-Based Filtering (CBF).

![image](https://github.com/user-attachments/assets/51c14b18-4531-4732-adb6-c698e4a9db97)



### Collaborative Filtering (CF)


#### 1. Arsitektur Model Recommender (Collaborative Filtering)

Model rekomendasi ini menggunakan pendekatan Collaborative Filtering berbasis neural network, yang memetakan pengguna dan tempat wisata ke dalam vektor embedding.
- user_embedding dan place_embedding: menghasilkan representasi vektor (embedding) untuk pengguna dan tempat.
- user_bias dan place_bias: menambahkan bias untuk masing-masing pengguna dan tempat.
- dot_user_place: menghitung interaksi (kemiripan) antara pengguna dan tempat dengan operasi perkalian dot product.

![image](https://github.com/user-attachments/assets/9a4d5191-0382-4918-b459-aad951209ec7)


Output dari model ini adalah prediksi skor rating yang mewakili seberapa cocok suatu tempat bagi pengguna tertentu.

#### 2. Definisi Model Rekomendasi – RecommenderNet

Model RecommenderNet merupakan implementasi Collaborative Filtering berbasis neural network menggunakan TensorFlow. Model ini belajar merepresentasikan pengguna dan tempat wisata sebagai vektor dalam ruang berdimensi rendah (embedding), lalu menghitung kecocokan antar keduanya.

Komponen Utama:
- user_embedding & place_embedding: menghasilkan vektor representasi (embedding) dari pengguna dan tempat.
- user_bias & place_bias: menyertakan nilai bias untuk setiap pengguna dan tempat.
- dot_user_place: menghitung skor kecocokan (prediksi rating) dengan perkalian dot product antara embedding pengguna dan tempat, ditambah bias masing-masing.

Output dari model ini adalah nilai prediksi rating antara pengguna dan tempat wisata.

![image](https://github.com/user-attachments/assets/e777a962-483e-421c-92f4-fef267d00c89)

#### 3. Kompilasi Model Rekomendasi

Setelah mendefinisikan arsitektur model RecommenderNet, langkah selanjutnya adalah melakukan kompilasi dengan konfigurasi sebagai berikut:
- Loss Function: mean_squared_error – digunakan untuk mengukur selisih antara rating aktual dan prediksi.
- Optimizer: Adam – algoritma optimisasi yang efisien dengan learning rate sebesar 0.001.
- Metrics:
  - MeanAbsoluteError – mengukur rata-rata kesalahan absolut dari prediksi.
  - RootMeanSquaredError – mengukur akar dari rata-rata kuadrat kesalahan, berguna untuk mengevaluasi seberapa besar penyimpangan prediksi dari nilai sebenarnya.

Langkah ini penting untuk menyiapkan model sebelum proses pelatihan (training) dilakukan.

![image](https://github.com/user-attachments/assets/95ef75c6-c367-4898-ac20-81eb8f2617d6)

#### 4. Pelatihan Model (Model Training)

Model dilatih menggunakan data pelatihan dan validasi dengan parameter berikut:
- Batch Size: 64 – jumlah sampel yang diproses sebelum model diperbarui.
- Epochs: 100 – jumlah maksimum iterasi pelatihan.
- Validation Data: digunakan untuk mengevaluasi performa model pada data yang tidak dilatih.
- Early Stopping: menghentikan pelatihan lebih awal jika val_loss tidak membaik selama 7 epoch berturut-turut, serta mengembalikan bobot model terbaik (dengan restore_best_weights=True).

Callback EarlyStopping membantu mencegah overfitting dan mempercepat pelatihan dengan menghentikan proses saat model tidak lagi mengalami peningkatan.

![image](https://github.com/user-attachments/assets/cb1d1359-824b-4e5c-8426-649ef92a4a59)

#### 5. Menentukan Tempat yang Belum Dikunjungi oleh Pengguna

- user_ID: Mengambil satu user secara acak dari data.
- places_visited: Menyimpan daftar place yang sudah dikunjungi oleh user tersebut.
- places_not_visited: Menyimpan daftar tempat yang belum pernah dikunjungi oleh user, dengan membandingkan semua tempat (all_places) terhadap tempat yang telah dikunjungi (places_visited).

Langkah ini penting untuk membuat rekomendasi hanya pada tempat-tempat baru bagi pengguna.

![image](https://github.com/user-attachments/assets/43d998ed-cac0-4902-b6bd-721d8b01a9b9)

#### 6. Rekomendasi Tempat Wisata Berdasarkan Collaborative Filtering

1. Cek apakah semua tempat sudah dikunjungi
- Jika places_not_visited kosong, berarti pengguna telah mengunjungi semua tempat wisata.
2. Prediksi rating tempat yang belum dikunjungi
- Membentuk array kombinasi user–place (user_place_array).
- Model memprediksi rating untuk masing-masing tempat.
3. Skala ulang prediksi ke rentang rating asli
- Mengembalikan hasil prediksi ke skala awal (misal 1–5).
4. Ambil 10 tempat teratas
- Urutkan berdasarkan skor prediksi tertinggi.
5. Dekode ID user dan tempat ke bentuk aslinya
- Gunakan LabelEncoder.inverse_transform() untuk menampilkan hasil akhir dalam format yang dimengerti.
6. Tampilkan hasil rekomendasi
- Menampilkan nama tempat dan rating prediksi untuk user yang dipilih secara acak.

![image](https://github.com/user-attachments/assets/0f7edcbe-dd81-4849-b165-2c4efcf24ac6)

![image](https://github.com/user-attachments/assets/4d7f327d-4cc1-4f08-a832-4af56f39d8d0)


## 6. Evaluasi Sistem Rekomendasi

Evaluasi sistem rekomendasi bertujuan untuk mengukur seberapa baik model dalam menyarankan tempat wisata yang relevan dan sesuai preferensi pengguna. Dalam proyek ini, dilakukan dua pendekatan utama dalam pembuatan sistem rekomendasi, yaitu:

### 1. Content-Based Filtering (CBF)
### 2. Collaborative Filtering (CF) berbasis neural network menggunakan TensorFlow

Masing-masing pendekatan dievaluasi dengan metrik yang sesuai untuk menilai akurasi dan relevansi rekomendasi yang dihasilkan.


### 1) Evaluasi Content-Based Filtering (CBF)

Metode Evaluasi: Precision@K (dengan K = 10)
Content-Based Filtering menggunakan fitur konten (seperti nama tempat dan kategori) untuk menghitung kemiripan antar tempat wisata, lalu merekomendasikan tempat-tempat yang paling mirip.

Untuk mengevaluasi performa CBF, digunakan metrik Precision@10, yaitu rasio jumlah tempat yang relevan (dengan kategori sama seperti tempat asal) terhadap jumlah total rekomendasi (10 tempat).

#### 1. Menentukan Tempat Uji Coba untuk Evaluasi Content-Based Filtering

Dalam proses evaluasi sistem rekomendasi berbasis konten (Content-Based Filtering), langkah awal yang dilakukan adalah memilih sebuah tempat wisata sebagai titik acuan atau tempat uji coba. Pada tahap ini, tempat yang dipilih adalah Museum Taman Prasasti. Pemilihan tempat ini bukan tanpa alasan—Museum Taman Prasasti memiliki karakteristik kategori yang cukup spesifik, yaitu budaya, sehingga cocok untuk menguji kemampuan model dalam menemukan tempat wisata lain yang memiliki kesamaan konten.

Melalui potongan kode tersebut, sistem mencari data lengkap dari tempat yang dimaksud di dalam DataFrame df_tourism, lalu mengambil nilai kategorinya. Kategori inilah yang nantinya akan dijadikan sebagai dasar dalam mengevaluasi hasil rekomendasi. Jika sebagian besar tempat yang direkomendasikan oleh model juga termasuk dalam kategori budaya, maka bisa dikatakan bahwa model mampu mengenali dan mempertahankan kesesuaian konteks dalam rekomendasi yang dihasilkannya.

Langkah ini menjadi fondasi penting sebelum menghitung metrik seperti Precision@10 karena kita perlu tahu terlebih dahulu apa kategori "benar" dari tempat yang sedang diuji, sehingga kita bisa menentukan apakah rekomendasi yang dihasilkan tergolong relevan atau tidak.

![image](https://github.com/user-attachments/assets/02bad6e7-6a71-4bb1-b90b-1ad5a100c728)

#### 2. Menyusun Ulang Fungsi Rekomendasi untuk Evaluasi

Pada tahap evaluasi sistem rekomendasi berbasis konten, fungsi rekomendasi disusun ulang agar lebih fleksibel dan siap digunakan dalam berbagai skenario uji. Fungsi ini dinamai get_recommendations, dan dilengkapi dengan parameter tambahan top_n untuk menentukan jumlah hasil rekomendasi yang diinginkan—default-nya adalah 10.

Secara umum, fungsi ini bekerja dengan terlebih dahulu memeriksa apakah nama tempat yang diberikan (place_name) terdapat dalam indeks data. Jika tidak ditemukan, fungsi akan mengembalikan DataFrame kosong sebagai tanda bahwa tempat tersebut tidak valid.

Jika tempat valid ditemukan, langkah berikutnya adalah menghitung skor kemiripan (cosine similarity) antara tempat tersebut dengan semua tempat lainnya yang ada di dalam data. Skor-skor ini kemudian diurutkan dari yang paling mirip ke yang paling tidak mirip, lalu diambil sejumlah top_n tempat teratas (dengan pengecualian tempat asal itu sendiri, karena tidak masuk akal merekomendasikan dirinya sendiri).

Fungsi kemudian mengembalikan informasi dasar dari tempat-tempat hasil rekomendasi, yaitu Place_Id, Place_Name, dan Category. Struktur ini memudahkan kita untuk mengevaluasi seberapa relevan rekomendasi yang dihasilkan berdasarkan kesamaan kategori dengan tempat asal. Penyusunan ulang fungsi ini penting agar dapat mendukung pengujian yang konsisten dan terukur selama proses evaluasi.

![image](https://github.com/user-attachments/assets/fe72bc94-ad43-4ecb-8e15-b019d8b2ebda)


#### 3. Menjalankan Rekomendasi CBF untuk Evaluasi

Setelah fungsi rekomendasi disusun ulang, langkah selanjutnya adalah menjalankan fungsi tersebut untuk menghasilkan daftar tempat wisata yang direkomendasikan. Dalam konteks ini, digunakan place_name tertentu—yaitu Museum Taman Prasasti—sebagai titik awal rekomendasi. Parameter top_n diset ke 10, sehingga model akan mengembalikan 10 tempat yang paling mirip berdasarkan perhitungan kemiripan konten.

Hasil dari fungsi ini adalah DataFrame yang berisi 10 tempat wisata teratas yang memiliki nilai kemiripan tertinggi terhadap tempat asal. Informasi yang ditampilkan hanya kolom Place_Name dan Category, karena kedua atribut tersebut yang paling relevan untuk mengevaluasi kesesuaian rekomendasi dengan kategori tempat awal.

Langkah ini menjadi inti dari proses evaluasi karena dari hasil inilah kita bisa menilai seberapa baik model Content-Based Filtering mampu mengenali dan merekomendasikan tempat yang relevan dari sisi konten deskriptif, khususnya kategori wisata.

![image](https://github.com/user-attachments/assets/c7ecdeee-1986-4912-8116-3014a3412cad)

#### 4. Mengukur Kualitas Rekomendasi dengan Precision@10

Setelah mendapatkan daftar 10 tempat wisata hasil rekomendasi dari model Content-Based Filtering, langkah berikutnya adalah menghitung metrik evaluasi untuk mengukur relevansi hasil tersebut. Dalam hal ini, metrik yang digunakan adalah Precision@10, yaitu rasio jumlah tempat yang memiliki kategori sama dengan tempat asal terhadap total jumlah rekomendasi yang diberikan.

Proses ini dilakukan dengan cara membandingkan kategori dari setiap rekomendasi dengan kategori dasar (base_category) dari tempat asal. Jika suatu tempat memiliki kategori yang sama atau memuat kata kunci yang identik, maka ia dianggap sebagai relevan. Seluruh jumlah tempat yang relevan kemudian dibagi dengan jumlah total rekomendasi (dalam hal ini 10) untuk mendapatkan nilai precision.

Nilai Precision@10 yang tinggi menunjukkan bahwa model berhasil merekomendasikan tempat-tempat yang sejenis dan sesuai dengan preferensi pengguna berdasarkan kategori, yang dalam konteks pariwisata bisa sangat penting untuk menjaga kesesuaian minat wisatawan.

![image](https://github.com/user-attachments/assets/24b49d39-50f4-4332-8302-7fa39a8c7833)

Interpretasi Evaluasi Content-Based Filtering (CBF)
Pada tahap evaluasi ini, sistem rekomendasi diuji menggunakan satu tempat wisata sebagai acuan, yaitu Museum Taman Prasasti. Tempat ini memiliki kategori utama Budaya. Model CBF kemudian diminta untuk menghasilkan 10 rekomendasi tempat wisata lain yang dianggap paling mirip berdasarkan kemiripan teks antara nama tempat dan kategorinya (menggunakan teknik TF-IDF dan cosine similarity).

Setelah hasil rekomendasi diperoleh, kategori dari setiap tempat rekomendasi dibandingkan dengan kategori asal. Hasilnya, seluruh tempat yang direkomendasikan berada dalam kategori yang sama atau sangat berkaitan, yakni Budaya. Hal ini menghasilkan Precision@10 sebesar 100%, yang berarti semua rekomendasi relevan terhadap konteks kategori tempat asal.

Hasil evaluasi ini menunjukkan bahwa pendekatan Content-Based Filtering bekerja sangat baik untuk tempat wisata yang memiliki deskripsi kategori yang kuat dan konsisten. Dalam kasus ini, penggunaan fitur teks sederhana seperti nama tempat dan kategori sudah cukup untuk menangkap hubungan semantik antar tempat.

Namun, penting untuk dicatat bahwa evaluasi hanya dilakukan pada satu contoh uji coba. Untuk mendapatkan gambaran kinerja yang lebih menyeluruh, perlu dilakukan pengujian terhadap lebih banyak tempat dari berbagai kategori, seperti Alam, Hiburan, atau Kuliner, agar dapat melihat konsistensi model dalam berbagai konteks.

Secara keseluruhan, model CBF menunjukkan performa yang akurat dan relevan untuk memberikan rekomendasi berdasarkan kemiripan konten, terutama ketika informasi kategori cukup informatif dan representatif.


### 2) Evaluasi Collaborative Filtering (CF) Berbasis Neural Network
   
Pendekatan Collaborative Filtering (CF) berbasis neural network digunakan untuk memprediksi rating atau preferensi pengguna terhadap tempat wisata, berdasarkan pola interaksi pengguna sebelumnya. Berbeda dengan Content-Based Filtering yang menggunakan informasi konten tempat wisata, pendekatan ini murni mengandalkan interaksi historis antar pengguna dan item.

Dalam proyek ini, CF diimplementasikan menggunakan TensorFlow, dan model dilatih untuk mempelajari representasi laten (latent factors) dari pengguna dan tempat wisata dalam bentuk vektor embedding. Proses pelatihan dilakukan menggunakan data rating yang dibagi menjadi data pelatihan dan validasi.

Metode Evaluasi: MAE dan RMSE
Untuk mengevaluasi performa model CF, digunakan dua metrik regresi yang umum digunakan dalam sistem rekomendasi:
#### 1. MAE (Mean Absolute Error): Mengukur rata-rata selisih absolut antara rating yang diprediksi dan rating aktual.
#### 2. RMSE (Root Mean Squared Error): Mengukur akar dari rata-rata kuadrat selisih antara rating yang diprediksi dan aktual, memberikan penalti lebih besar untuk kesalahan yang besar.

Kedua metrik ini dihitung selama proses pelatihan dan divisualisasikan menggunakan grafik, baik untuk data pelatihan maupun data validasi.

#### Hasil Evaluasi Model Collaborative Filtering
Evaluasi model dilakukan menggunakan metrik Mean Absolute Error (MAE) dan Root Mean Squared Error (RMSE) pada data validasi. Nilai akhir diperoleh dari epoch terakhir selama proses training model.

![image](https://github.com/user-attachments/assets/2f521310-1af5-4b26-b0a4-407f13e8131a)


### Hasil Evaluasi Model Collaborative Filtering (CF)
Model Collaborative Filtering Anda menunjukkan hasil evaluasi sebagai berikut:
- Final Validation MAE (Mean Absolute Error): 0.3613
- Final Validation RMSE (Root Mean Squared Error): 0.4318
- 
Angka-angka ini mencerminkan performa model dalam memprediksi rating pada data validasi. MAE sebesar 0.3613 berarti rata-rata selisih absolut antara rating prediksi dan rating aktual adalah sekitar 0.36. Sedangkan RMSE sebesar 0.4318 mengindikasikan deviasi rata-rata (dengan penalti lebih besar untuk kesalahan besar) antara prediksi dan nilai aktual.

Kedua nilai metrik ini tergolong rendah, menunjukkan bahwa model memiliki kemampuan prediksi yang cukup baik dan rating yang diprediksi relatif dekat dengan nilai sebenarnya (mengingat skala rating umum, misalnya 1-5).

### 1. Visualisasi Mean Absolute Error (MAE) Selama Pelatihan

Kode ini digunakan untuk memvisualisasikan perkembangan nilai Mean Absolute Error (MAE) dari model Collaborative Filtering berbasis neural network selama proses pelatihan. Grafik yang dihasilkan menampilkan dua kurva: satu untuk data pelatihan (Train) dan satu untuk data validasi (Validation), yang menunjukkan bagaimana performa model dalam memprediksi rating pada kedua dataset tersebut seiring bertambahnya epoch. Visualisasi ini penting untuk memantau apakah model belajar dengan baik atau mulai mengalami overfitting ketika performa validasi mulai menurun.

![image](https://github.com/user-attachments/assets/376b2251-15e0-4d6e-9feb-bb1d488eaeb3)

![image](https://github.com/user-attachments/assets/2f414eeb-062a-4f32-ae5f-18b89ef06a8c)

### Grafik: Model Metrics – MAE (Mean Absolute Error)

Grafik ini menampilkan perkembangan nilai Mean Absolute Error (MAE) selama proses pelatihan model Collaborative Filtering berbasis Neural Network Anda. Terdapat dua garis pada grafik:

- Garis biru menunjukkan nilai MAE pada data pelatihan (Train).
- Garis oranye menunjukkan nilai MAE pada data validasi (Validation).

Interpretasi:

#### 1. Performa pada Data Pelatihan (Garis Biru):
   
- MAE pada data pelatihan menunjukkan penurunan yang konsisten dan signifikan dari epoch awal hingga akhir. Ini mengindikasikan bahwa model Anda berhasil belajar dengan sangat baik dari data pelatihan dan mampu mengurangi kesalahan prediksi secara progresif seiring bertambahnya iterasi pelatihan. Model berhasil menyesuaikan diri dengan pola-pola dalam data yang telah dilihatnya.

#### 2. Performa pada Data Validasi (Garis Oranye):


- MAE pada data validasi juga menunjukkan penurunan yang baik di awal pelatihan (sekitar epoch 0 hingga 5). Ini menandakan bahwa model mampu menggeneralisasi dengan baik pada data baru yang belum pernah dilihat sebelumnya pada fase awal pelatihan.
- Namun, setelah sekitar epoch ke-8 hingga ke-10, penurunan MAE pada data validasi mulai melambat dan cenderung stagnan, bahkan sedikit meningkat di epoch-epoch terakhir (sekitar epoch 15-20).
- Fenomena ini adalah indikasi adanya overfitting ringan. Model mulai terlalu "menghafal" pola-pola spesifik pada data pelatihan (sehingga MAE train terus turun drastis), namun kehilangan kemampuannya untuk menggeneralisasi dengan baik pada data yang tidak dikenal (data validasi). Kesenjangan antara MAE train dan MAE validation menjadi semakin lebar di epoch-epoch akhir.

### 2. Visualisasi Root Mean Squared Error (RMSE) Selama Pelatihan

Kode ini digunakan untuk memplot perkembangan nilai Root Mean Squared Error (RMSE) dari model Collaborative Filtering selama proses pelatihan. Grafik memperlihatkan perbandingan error antara data pelatihan dan validasi pada setiap epoch. RMSE memberikan gambaran seberapa besar deviasi antara prediksi dan nilai aktual, dengan penalti lebih besar untuk kesalahan yang lebih jauh. Visualisasi ini membantu mengidentifikasi apakah model terus membaik, stagnan, atau mulai mengalami overfitting seiring berjalannya pelatihan.

![image](https://github.com/user-attachments/assets/8dc49ebc-b709-47c4-9062-1609e972e002)

![image](https://github.com/user-attachments/assets/509428db-8bf6-4f83-8fba-464ece274931)

### Grafik: Model Metrics – RMSE (Root Mean Squared Error)

Grafik ini menggambarkan perubahan nilai Root Mean Squared Error (RMSE) selama proses pelatihan model Collaborative Filtering menggunakan Neural Network Anda. Sama seperti grafik MAE, terdapat dua garis yang ditampilkan:

- Garis biru menunjukkan RMSE pada data pelatihan (Train).
- Garis oranye menunjukkan RMSE pada data validasi (Validation).
Interpretasi:

3### 1. Performa pada Data Pelatihan (Garis Biru):
   
- RMSE pada data pelatihan menunjukkan penurunan yang konsisten dan signifikan seiring bertambahnya jumlah epoch. Ini menandakan bahwa model belajar dengan sangat baik dari data pelatihan dan mampu meminimalkan kesalahan prediksi secara efektif pada data yang telah dilihatnya. Penurunan yang curam ini menunjukkan bahwa model terus menyesuaikan bobotnya untuk lebih akurat memprediksi rating yang ada dalam dataset pelatihan.

#### 2. Performa pada Data Validasi (Garis Oranye):

- RMSE pada data validasi juga menunjukkan penurunan awal yang sehat (sekitar epoch 0 hingga 5). Ini menunjukkan bahwa model mampu menggeneralisasi dengan baik pada data baru di fase awal pelatihan.
- Namun, setelah kira-kira epoch ke-8 hingga ke-10, penurunan RMSE pada data validasi mulai melambat secara drastis, stagnan, dan bahkan sedikit meningkat di epoch-epoch terakhir (sekitar epoch 15-20).
- Pola ini sangat mirip dengan grafik MAE validasi dan merupakan indikasi yang kuat adanya overfitting ringan. Model menjadi terlalu spesifik terhadap data pelatihan, sehingga kinerja pada data yang belum pernah dilihat (data validasi) mulai terganggu. Kesenjangan antara RMSE train dan RMSE validation terus melebar.


## Kesimpulan Evaluasi Sistem Rekomendasi

Evaluasi terhadap dua pendekatan sistem rekomendasi, yaitu **Content-Based Filtering (CBF)** dan **Collaborative Filtering (CF)** berbasis neural network, menunjukkan hasil yang cukup menjanjikan dalam konteks rekomendasi tempat wisata di Jakarta. Berikut adalah rangkuman kesimpulan dari masing-masing pendekatan:

### 1. Content-Based Filtering (CBF)
- Dilakukan evaluasi menggunakan **Precision@10** terhadap tempat wisata _Museum Taman Prasasti_ yang memiliki kategori "Budaya".
- Hasil menunjukkan bahwa **10 dari 10 tempat** yang direkomendasikan memiliki kategori yang sama atau sangat relevan.
- **Precision@10 mencapai 100%**, yang menandakan bahwa model berhasil merekomendasikan tempat-tempat wisata yang sangat sesuai dari segi konten deskriptif.
- Pendekatan CBF terbukti sangat efektif, terutama ketika informasi kategori cukup kuat dan representatif.
- Namun, evaluasi hanya dilakukan pada satu tempat. Untuk generalisasi yang lebih baik, evaluasi sebaiknya diperluas ke lebih banyak tempat dan kategori yang beragam.

### 2. Collaborative Filtering (CF) Berbasis Neural Network
- Evaluasi dilakukan dengan memantau metrik **MAE (Mean Absolute Error)** dan **RMSE (Root Mean Squared Error)** selama proses pelatihan.
- Baik MAE maupun RMSE pada data pelatihan menunjukkan penurunan yang konsisten, menandakan bahwa model berhasil mempelajari pola interaksi pengguna dengan baik.
- Pada data validasi, metrik juga menurun di awal pelatihan namun cenderung **stagnan atau sedikit meningkat setelah epoch ke-15**, menandakan potensi **overfitting ringan**.
- Meskipun demikian, nilai error tetap rendah (MAE 0.3613 dan RMSE 0.4318), menunjukkan model memiliki kemampuan prediksi yang cukup akurat.
- Ke depan, performa model dapat lebih ditingkatkan dengan menggunakan **early stopping**, **dropout**, atau strategi regularisasi lainnya untuk menghindari overfitting.

###  Rangkuman Umum:
- **CBF sangat akurat** dalam menangkap kesamaan konten, cocok untuk sistem yang memiliki metadata atau deskripsi yang kaya.
- **CF berbasis neural network** memberikan prediksi yang baik, terutama untuk sistem dengan interaksi pengguna-tempat yang cukup banyak.
- Kombinasi keduanya atau pendekatan hybrid bisa menjadi solusi untuk meningkatkan kualitas rekomendasi lebih lanjut.

Langkah selanjutnya dapat mencakup:
- Evaluasi lebih luas terhadap tempat dari kategori berbeda.
- Penerapan teknik hybrid untuk memadukan kekuatan CBF dan CF.
- Integrasi feedback pengguna secara langsung untuk terus menyempurnakan model rekomendasi.
