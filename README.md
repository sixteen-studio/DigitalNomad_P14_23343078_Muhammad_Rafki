# DigitalNomad_P14_23343078_Muhammad_Rafki

# Ringkasan Analitis Keamanan Mobile untuk Pengembangan Flutter

**Nama:** Muhammad Rafki  
**NIM:** 23343078  
**Format Tugas:** DigitalNomad_P14_23343078_Muhammad_Rafki  
**Topik:** Security Code Review dan Analisis Keamanan Mobile pada Pengembangan Flutter  

---

## Sumber 1: OWASP Mobile Top 10 2024

### Temuan Utama

OWASP Mobile Top 10 2024 menjelaskan sepuluh risiko keamanan utama pada aplikasi mobile. Beberapa risiko yang paling relevan dengan pengembangan Flutter adalah penggunaan kredensial yang tidak aman, autentikasi dan otorisasi yang lemah, komunikasi API yang tidak aman, validasi input/output yang kurang memadai, penyimpanan data yang tidak aman, serta penggunaan kriptografi yang tidak tepat.

Dalam konteks aplikasi mobile, masalah keamanan tidak hanya berasal dari backend, tetapi juga dari cara aplikasi menyimpan data, mengirim request ke API, menangani token, memproses input user, dan melindungi data sensitif di perangkat pengguna.

Risiko seperti insecure data storage menjadi penting karena aplikasi mobile sering menyimpan token, session, profil pengguna, email, nomor HP, atau data pribadi lain di perangkat. Jika data tersebut disimpan menggunakan storage biasa tanpa enkripsi, maka data dapat lebih mudah diekstrak, terutama pada perangkat yang sudah di-root atau melalui proses reverse engineering.

Risiko insecure communication juga menjadi perhatian utama. Aplikasi yang masih menggunakan HTTP, mengirim token melalui query parameter, atau tidak memvalidasi koneksi TLS dengan baik dapat membuka peluang serangan man-in-the-middle. Dalam aplikasi mobile, komunikasi API harus dirancang aman sejak awal karena pengguna sering memakai jaringan publik seperti Wi-Fi umum.

Selain itu, insufficient input/output validation menunjukkan bahwa data dari user maupun response API tidak boleh langsung dipercaya. Input seperti email, password, NIK, nomor HP, dan data form harus divalidasi sebelum dikirim ke server. Response dari API juga perlu diperiksa agar aplikasi tidak memproses data kosong, rusak, atau tidak sesuai struktur.

### Relevansi untuk Pengembangan Flutter

Dalam Flutter, risiko-risiko OWASP tersebut sangat relevan karena Flutter sering digunakan untuk membangun aplikasi Android dan iOS dengan satu basis kode. Jika developer salah mengelola token, storage, validasi input, atau komunikasi API, maka celah keamanan dapat berdampak pada dua platform sekaligus.

Pada arsitektur MVVM atau Repository Pattern, keamanan sebaiknya dipisahkan sesuai layer:

- View menangani input awal dan validasi tampilan.
- ViewModel mengatur logika presentasi dan status.
- Repository mengatur alur data dari remote dan local source.
- Remote Data Source menangani request API dan parsing response.
- Local Data Source menangani penyimpanan data lokal.

Dengan pembagian ini, logic keamanan tidak tercampur langsung dengan UI. Misalnya, penyimpanan token tidak dilakukan di halaman login, tetapi dikelola oleh repository atau local data source menggunakan secure storage.

### Rekomendasi Implementasi

1. Gunakan `flutter_secure_storage` untuk menyimpan token, refresh token, atau session.
2. Hindari menyimpan password secara lokal.
3. Gunakan HTTPS untuk semua komunikasi API.
4. Simpan token di header `Authorization`, bukan di query parameter.
5. Tambahkan timeout pada request API.
6. Terapkan validasi input menggunakan `Form` dan `TextFormField.validator`.
7. Tetap lakukan validasi ulang di backend karena validasi client dapat dilewati.
8. Hindari menampilkan token, password, atau data pribadi melalui `print()` atau log debug.
9. Pisahkan logic keamanan ke layer repository, service, atau data source agar mudah diuji.
10. Pertimbangkan certificate pinning untuk aplikasi yang menangani data sensitif.

---

## Sumber 2: Zimperium 2025 Global Mobile Threat Report

### Temuan Utama

Zimperium 2025 Global Mobile Threat Report menunjukkan bahwa perangkat dan aplikasi mobile masih menjadi target utama serangan siber. Laporan ini menyoroti ancaman pada platform Android dan iOS, termasuk malware, phishing, aplikasi berbahaya, perangkat dengan sistem operasi usang, risiko supply chain, dan kelemahan pada aplikasi mobile.

Salah satu temuan penting adalah banyak perangkat mobile masih berjalan pada sistem operasi yang outdated. Perangkat yang tidak diperbarui lebih rentan terhadap eksploitasi karena patch keamanan terbaru belum diterapkan. Hal ini menunjukkan bahwa developer aplikasi mobile tidak bisa hanya mengandalkan keamanan dari sistem operasi, tetapi juga perlu menerapkan perlindungan tambahan pada aplikasi.

Laporan ini juga menekankan bahwa aplikasi mobile dapat menjadi pintu masuk serangan melalui permission yang berlebihan, penyimpanan data yang buruk, komunikasi API yang tidak aman, serta penggunaan library pihak ketiga yang tidak diaudit. Dalam praktik pengembangan modern, dependency atau package yang digunakan aplikasi juga perlu diperhatikan karena risiko supply chain dapat muncul dari library yang rentan atau tidak terawat.

Bagi aplikasi yang menangani data pengguna, ancaman seperti pencurian token, penyalahgunaan session, malware pada perangkat, dan reverse engineering menjadi hal yang harus diperhitungkan. Mobile app tidak berjalan dalam lingkungan yang sepenuhnya aman karena perangkat pengguna bisa saja terinfeksi malware, di-root, atau menggunakan jaringan tidak terpercaya.

### Relevansi untuk Pengembangan Flutter

Flutter menggunakan banyak package pihak ketiga melalui `pub.dev`. Hal ini mempermudah pengembangan, tetapi juga menambah risiko supply chain. Package seperti HTTP client, secure storage, state management, Firebase, atau library autentikasi harus dipilih dengan hati-hati.

Dalam Flutter, developer juga perlu memperhatikan permission aplikasi. Jangan meminta akses yang tidak dibutuhkan, seperti lokasi, kamera, kontak, atau storage jika fitur aplikasi tidak memerlukannya. Permission yang berlebihan dapat meningkatkan risiko privasi dan menurunkan kepercayaan pengguna.

Zimperium juga relevan dengan praktik secure coding pada Flutter karena aplikasi mobile sering berjalan di lingkungan yang tidak dapat dikontrol sepenuhnya. Oleh karena itu, data sensitif harus tetap dilindungi meskipun perangkat pengguna tidak ideal.

### Rekomendasi Implementasi

1. Gunakan package yang populer, aktif diperbarui, dan memiliki dokumentasi jelas.
2. Audit dependency secara berkala melalui `flutter pub outdated`.
3. Hindari package yang tidak jelas maintainer-nya.
4. Batasi permission hanya sesuai kebutuhan fitur.
5. Jangan menyimpan data sensitif di SharedPreferences.
6. Gunakan secure storage untuk token dan session.
7. Terapkan logout yang benar dengan menghapus token lokal.
8. Hindari hardcoded API key, token, atau credential di source code.
9. Gunakan environment variable atau konfigurasi build yang aman untuk data konfigurasi.
10. Lakukan testing pada skenario jaringan tidak aman, response API gagal, token expired, dan input tidak valid.

---

## Analisis Perbandingan Kedua Sumber

OWASP Mobile Top 10 2024 lebih fokus pada klasifikasi risiko keamanan aplikasi mobile dari sisi developer dan security reviewer. Sumber ini cocok dijadikan checklist ketika melakukan code review, karena setiap risiko dapat diterjemahkan menjadi bagian kode yang perlu diperiksa, seperti storage, komunikasi API, validasi input, autentikasi, dan konfigurasi keamanan.

Sementara itu, Zimperium 2025 Global Mobile Threat Report memberikan gambaran ancaman nyata pada ekosistem mobile. Laporan ini menunjukkan bahwa risiko keamanan tidak hanya berasal dari bug pada kode aplikasi, tetapi juga dari perangkat pengguna, sistem operasi yang tidak diperbarui, malware, phishing, dan dependency pihak ketiga.

Kedua sumber saling melengkapi. OWASP membantu developer memahami apa yang harus diamankan di level kode, sedangkan Zimperium menunjukkan mengapa perlindungan tersebut penting dalam kondisi dunia nyata.

Untuk pengembangan Flutter, keduanya memberi pesan yang sama: aplikasi mobile harus dirancang dengan asumsi bahwa perangkat, jaringan, dan dependency tidak selalu aman. Karena itu, developer perlu menerapkan secure storage, komunikasi API yang aman, validasi input, dependency audit, serta pengelolaan permission yang minimal.

---

## Rekomendasi Security Checklist untuk Project Flutter

Berdasarkan dua sumber tersebut, checklist keamanan yang dapat diterapkan pada project Flutter adalah:

1. Data sensitif seperti token dan session disimpan menggunakan `flutter_secure_storage`.
2. Password tidak disimpan di perangkat.
3. Token tidak ditulis langsung di source code.
4. Semua request API menggunakan HTTPS.
5. Token dikirim melalui header Authorization.
6. Request API memiliki timeout dan error handling.
7. Input user divalidasi sebelum dikirim ke API.
8. Response API divalidasi sebelum diproses.
9. Package pihak ketiga diperiksa dan diperbarui secara berkala.
10. Permission aplikasi dibatasi sesuai kebutuhan.
11. Log debug tidak menampilkan data sensitif.
12. Aplikasi memiliki mekanisme logout yang menghapus data session.
13. Untuk aplikasi sensitif, certificate pinning dapat dipertimbangkan.
14. Struktur MVVM atau Repository Pattern digunakan agar logic keamanan tidak bercampur dengan UI.

---

## Kesimpulan

Berdasarkan analisis OWASP Mobile Top 10 2024 dan Zimperium 2025 Global Mobile Threat Report, keamanan aplikasi Flutter perlu diperhatikan sejak tahap desain arsitektur, bukan hanya setelah aplikasi selesai dibuat. Risiko utama yang perlu diperhatikan adalah penyimpanan data sensitif, komunikasi API, validasi input, penggunaan dependency, permission aplikasi, dan perlindungan terhadap token/session.

Pada arsitektur MVVM atau Repository Pattern, keamanan dapat diterapkan dengan lebih rapi karena setiap layer memiliki tanggung jawab yang jelas. View fokus pada tampilan dan validasi awal, ViewModel mengatur state, Repository mengatur alur data, Remote Data Source menangani API, dan Local Data Source menangani penyimpanan lokal.

Dengan menerapkan secure storage, HTTPS, validasi input, dependency audit, dan pembatasan permission, aplikasi Flutter dapat memiliki dasar keamanan yang lebih baik dan siap dikembangkan ke tahap production.

---

## Referensi

1. OWASP Mobile Top 10 2024  
   https://owasp.org/www-project-mobile-top-10/

2. Zimperium 2025 Global Mobile Threat Report  
   https://zimperium.com/hubfs/Reports/2025%20Global%20Mobile%20Threat%20Report.pdf
