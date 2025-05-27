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

1. Informasi Umum Dataset

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

2. Pembersihan Kolom Tidak Relevan

Pada tahap ini, dilakukan penghapusan kolom-kolom yang tidak diperlukan atau tidak memiliki nilai informasi penting untuk analisis dan pemodelan. Kolom yang dihapus antara lain:
- Coordinate
- Unnamed: 11
- Unnamed: 12

Kolom-kolom ini berasal dari hasil ekspor file CSV yang kurang bersih atau tidak relevan dengan tujuan analisis. Penghapusan dilakukan secara otomatis untuk setiap dataset (user, tourism, rating, dan package) jika kolom tersebut ditemukan. 

3. Pengecekan Missing Values

Pada tahap ini, dilakukan pengecekan untuk melihat apakah ada nilai yang hilang (missing values) di dalam setiap dataset. Missing values dapat mempengaruhi kualitas model, oleh karena itu perlu dilakukan penanganan lebih lanjut seperti pengisian atau penghapusan data yang hilang.
![image](https://github.com/user-attachments/assets/66e5a1d4-c28a-463e-9bc4-a755c7ccfc03)

Hasil pengecekan missing values akan menunjukkan jumlah missing values di setiap kolom pada dataset df_package, df_rating, df_tourism, dan df_user. Ini akan memberi gambaran tentang kualitas data yang dimiliki.

4. Menangani Duplikat Data

Di bawah ini adalah pemeriksaan jumlah data duplikat untuk masing-masing dataset. Duplikat dapat mempengaruhi kualitas analisis dan model, sehingga penting untuk mengidentifikasinya dan mengambil tindakan yang sesuai.

![image](https://github.com/user-attachments/assets/46dddec0-ef10-43ec-a8e2-32ffdfad7201)

5. Visualisasi Distribusi Fitur Numerik

Bagian ini bertujuan untuk memvisualisasikan distribusi fitur numerik yang terdapat pada setiap dataset untuk membantu memahami data lebih baik.
- numeric_dfs: List ini berisi DataFrame yang hanya mencakup kolom numerik dari setiap dataset.
- titles: Daftar berisi judul untuk setiap plot yang akan ditampilkan, sesuai dengan dataset terkait.
- Loop untuk plot histogram: Setiap dataset divisualisasikan menggunakan histogram dengan 20 bin untuk menunjukkan distribusi fitur numerik yang ada. tight_layout() digunakan untuk memastikan bahwa label dan elemen plot tidak saling tumpang tindih.

![image](https://github.com/user-attachments/assets/1fa3c207-2ed9-4d87-b0e3-cf359e32e2f7)
![image](https://github.com/user-attachments/assets/b9f48f6e-0bb5-4022-bd02-cefae63f4bc2)
![image](https://github.com/user-attachments/assets/7f9b24f4-0454-445f-aed8-7ae059a766f8)
![image](https://github.com/user-attachments/assets/9a5b6016-1a79-48e3-9cc2-c5e9a75fce82)

Visualisasi ini membantu kita memahami sebaran data numerik, serta mendeteksi adanya pencilan atau pola distribusi tertentu di setiap dataset.

6. Visualisasi Pairplot untuk Fitur Numerik

Bagian ini berfungsi untuk memvisualisasikan hubungan antar fitur numerik menggunakan pairplot. Pairplot memungkinkan kita untuk melihat distribusi dari setiap fitur dan korelasi antar fitur dalam dataset.

![image](https://github.com/user-attachments/assets/f04c2136-cf4b-4bde-84e4-b92e1aea4a6f)

![image](https://github.com/user-attachments/assets/5a6a161d-ed3c-414d-a13b-8604ea4579c3)

![image](https://github.com/user-attachments/assets/db0053ae-da1f-486e-adf6-919041fd41e5)

Penjelasan:
- numeric_dfs: List ini berisi DataFrame yang hanya mencakup kolom numerik dari setiap dataset.
- titles: Daftar berisi judul untuk setiap plot yang akan ditampilkan, sesuai dengan dataset yang bersangkutan.
- Pairplot: Digunakan untuk memvisualisasikan hubungan antar fitur numerik. Namun, hanya DataFrame dengan kurang dari 5 kolom numerik yang diproses menggunakan pairplot, untuk mencegah plot yang terlalu ramai dan sulit dibaca.
Jika dataset memiliki lebih dari 5 kolom numerik, pairplot akan dilewati untuk menjaga visualisasi tetap relevan dan tidak membingungkan.


7. Visualisasi Heatmap Korelasi

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


8. Visualisasi Distribusi Kategorikal

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


9. Statistik Deskriptif Fitur Kategorikal

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


10. Distribusi Jumlah Rating per User dan per Tempat Wisata

Visualisasi ini bertujuan untuk memahami pola interaksi pengguna terhadap tempat wisata berdasarkan data rating. Terdapat dua histogram yang ditampilkan:

Distribusi Jumlah Rating per User
Menunjukkan seberapa banyak rating yang diberikan oleh masing-masing pengguna. Ini berguna untuk mengetahui tingkat keaktifan pengguna dalam memberikan penilaian terhadap tempat wisata. Jika sebagian besar pengguna hanya memberikan sedikit rating, maka data bersifat sparsity dan bisa memengaruhi performa collaborative filtering.

Distribusi Jumlah Rating per Tempat Wisata
Menampilkan jumlah rating yang diterima oleh masing-masing tempat wisata. Visualisasi ini membantu mengidentifikasi tempat-tempat yang populer (sering dinilai) dan tempat yang kurang populer (jarang dinilai), yang dapat memengaruhi akurasi sistem rekomendasi. Tempat dengan rating terbanyak biasanya menjadi kandidat kuat untuk direkomendasikan.

![image](https://github.com/user-attachments/assets/082cde43-ee6c-4cbc-8309-6345bbf07afb)

Kedua visualisasi ini penting dalam proses eksplorasi data untuk memberikan gambaran umum tentang distribusi interaksi antara pengguna dan item (tempat wisata) sebelum membangun sistem rekomendasi.


11. Distribusi Data Berdasarkan Lokasi, Kategori, dan Kota

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


## 4. Data Preparation

1. Penanganan Missing Values

Untuk memastikan kualitas data, dilakukan pemeriksaan dan penghapusan nilai kosong (missing values) pada setiap dataset (df_user, df_rating, df_tourism, dan df_package). Proses ini dilakukan menggunakan fungsi dropna(), diikuti dengan pengecekan ulang untuk memastikan tidak ada nilai yang hilang tersisa.

![image](https://github.com/user-attachments/assets/7d01a501-f773-46fd-af62-8b7818a70728)

2. penghapusan data duplikat

Pada tahap awal preprocessing, dilakukan penghapusan data yang bersifat duplikat pada seluruh dataframe yang digunakan, yaitu: df_user, df_rating, `dfdf_tourism, dan df_package. Kebera

Langkah ini memastikan bahwa setiap entri dalam dataset bersifat unik dan valid sebelum dilakukan proses lebih lanjut seperti penanganan nilai yang hilang atau pengkodean data.

![image](https://github.com/user-attachments/assets/b33d2de2-9d46-402e-86f3-600261f14eb8)


3. Deteksi Outlier Menggunakan Boxpl

Untuk mengidentifikasi adanya outlier dalam data numerik, dilakukan visualisasi boxplot pada masing-masing dataframe: df_package, `df_ratindf_rating, `df_pariwisata_df_tourism, dan df_user.

Langkah ini penting dilakukan sebelum melakukan normalisasi atau proses machine learning lainnya, karena outlier dapat mempengaruhi performa model secara signifikan. Dengan visualisasi ini, pengguna dapat dengan mudah mengamati distribusi data dan menentukan perlakuan selanjutnya terhadap outlier

![image](https://github.com/user-attachments/assets/9077d896-6f5d-41d6-ad1a-d3effa4c6c5f)

![image](https://github.com/user-attachments/assets/3f98314d-4dbf-4f9d-9b37-d994448aeb4c)

![image](https://github.com/user-attachments/assets/db13ba90-6182-4848-939f-b377ff65c87b)

![image](https://github.com/user-attachments/assets/7952a9d9-1f42-44fc-9538-e38f52aa1201)


4. Menghapus Outlier Menggunakan Metode IQR

Pada bagian ini, dilakukan pembersihan data dari nilai-nilai ekstrem (outlier) menggunakan metode Interquartile Range (IQR). Outlier dapat mempengaruhi hasil analisis dan performa model. Oleh karena itu, penting untuk menghapusnya.

Fungsi remove_outliers_iqr digunakan untuk memfilter data berdasarkan nilai Q1 (kuartil bawah) dan Q3 (kuartil atas), dengan batas toleransi sebesar 1.5 * IQR. Fungsi ini diterapkan ke seluruh kolom numerik dari beberapa dataset, seperti df_user, df_rating, df_tourism, dan df_package. Sebelum dan sesudah proses, ukuran dataset dibandingkan untuk mengetahui seberapa banyak data yang terdeteksi sebagai outlier dan dihapus.

![image](https://github.com/user-attachments/assets/05c38233-f8db-4f88-9c2d-f622957dc1f3)



5. Visualisasi Boxplot setelah Penghapusan Outlier
Pada tahap ini, dilakukan visualisasi menggunakan boxplot untuk masing-masing dataset (User, Rating, Tourism, dan Package). Boxplot digunakan untuk:
- Melihat distribusi data numerik
- Mengidentifikasi outlier secara visual
- Memahami sebaran dan variasi data

Setiap grafik menampilkan kolom-kolom numerik dari masing-masing DataFrame. Proses ini membantu dalam validasi apakah metode penghapusan outlier sebelumnya telah bekerja secara efektif.

![image](https://github.com/user-attachments/assets/16894ae8-5f39-4c35-ace2-8e0c202a1c7b)

![image](https://github.com/user-attachments/assets/95b538cb-3968-4285-9f3b-ab4e33ab9167)

![image](https://github.com/user-attachments/assets/267977d0-2d11-4976-8e55-00a7a802c978)

![image](https://github.com/user-attachments/assets/db69d040-d987-4747-b231-80d4555bc4db)



## Modeling

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

















