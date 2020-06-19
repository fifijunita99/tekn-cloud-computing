Aplikasi Containerisasi dan Orkestra Layanan Mikro

Dalam tutorial ini kita akan belajar tentang kontainerisasi aplikasi dasar menggunakan Docker dan menjalankan berbagai komponen aplikasi sebagai layanan microser. Kami akan menggunakan Docker Compose untuk orkestrasi selama pengembangan. Tutorial ini ditargetkan untuk pemula yang memiliki pengetahuan dasar dengan Docker. Jika Anda baru menggunakan Docker, kami sarankan Anda untuk memeriksa tutorial Docker untuk Pemula terlebih dahulu.

Kami akan mulai dari skrip Python dasar yang mengikis tautan dari laman web tertentu dan secara bertahap mengubahnya menjadi tumpukan aplikasi multi-layanan. Kode demo tersedia di repo Link Extractor. Kode ini disusun dalam langkah-langkah yang secara bertahap memperkenalkan perubahan dan konsep baru. Setelah selesai, tumpukan aplikasi akan berisi layanan microser berikut:

Aplikasi web yang ditulis dalam PHP dan disajikan menggunakan Apache yang mengambil URL sebagai input dan merangkum tautan yang diekstrak darinya
Aplikasi web berbicara ke server API yang ditulis dengan Python (dan Ruby) yang menangani ekstraksi tautan dan mengembalikan respons JSON
Cache Redis yang digunakan oleh server API untuk menghindari pengambilan berulang dan ekstraksi tautan untuk halaman yang sudah dikikis
Server API hanya akan memuat halaman tautan input dari web jika tidak ada dalam cache. 

Pengaturan Panggung
Mari kita mulai dengan mengkloning repositori kode demo, mengubah direktori kerja, dan memeriksa cabang demo terlebih dahulu.
Langkah 0: Skrip Extractor Tautan Dasar
Periksa cabang step0 dan daftarkan file di dalamnya.
Ini adalah skrip Python sederhana yang mengimpor tiga paket: sys dari perpustakaan standar dan dua permintaan paket pihak ketiga yang populer dan bs4. Argumen baris perintah yang disediakan pengguna (yang diharapkan menjadi URL ke halaman HTML) digunakan untuk mengambil halaman menggunakan paket permintaan, kemudian diuraikan menggunakan BeautifulSoup. Objek yang diuraikan kemudian diiterasi untuk menemukan semua elemen jangkar (mis., Tag <a>) dan mencetak nilai atribut href mereka yang berisi hyperlink.
Di sini kami mendapat pesan ImportError pertama karena kami melewatkan paket pihak ketiga yang dibutuhkan oleh skrip. Kita dapat menginstal paket Python (dan kemungkinan paket lain yang hilang) menggunakan salah satu dari sekian banyak teknik untuk membuatnya berfungsi, tetapi terlalu banyak bekerja untuk skrip sederhana seperti itu, yang mungkin tidak jelas bagi mereka yang tidak terbiasa dengan ekosistem Python .

Bergantung pada mesin dan sistem operasi tempat Anda mencoba menjalankan skrip ini, perangkat lunak apa yang sudah diinstal, dan seberapa banyak akses yang Anda miliki, Anda mungkin menghadapi beberapa kesulitan potensial:

Apakah skrip dapat dieksekusi?
Apakah Python diinstal pada mesin?
Bisakah Anda menginstal perangkat lunak pada mesin?
Apakah pip terpasang?
Apakah permintaan dan pustaka Python beautifulsoup4 diinstal?
Di sinilah alat kontainerisasi aplikasi seperti Docker berguna. Pada langkah selanjutnya kita akan mencoba membuat wadah script ini dan membuatnya lebih mudah untuk dieksekusi.

Langkah 1: Script Extractor Tautan Kontainer
Periksa cabang step1 dan daftarkan file di dalamnya
Langkah 2: Tautkan Modul Extractor dengan URI Penuh dan Anchor Text
Pada langkah ini skrip linkextractor.py diperbarui dengan perubahan fungsional berikut:
- Jalur dinormalisasi ke URL lengkap
- Melaporkan tautan dan teks jangkar
- Dapat digunakan sebagai modul di skrip lain

Langkah 3: Layanan Link Extractor API
Perubahan berikut telah dilakukan pada langkah ini:
Menambahkan skrip server main.py yang menggunakan modul ekstraksi tautan yang ditulis pada langkah terakhir
Dockerfile diperbarui untuk merujuk ke file main.py
Server dapat diakses sebagai API WEB di http: // <hostname> [: <prt>] / api / <url>
Ketergantungan dipindahkan ke file requirement.txt
Membutuhkan pemetaan port untuk membuat layanan dapat diakses di luar wadah (server Flask yang digunakan di sini mendengarkan pada port 5000 secara default)

Langkah 4: Link Extractor API dan Layanan Front Web
Pada langkah ini perubahan berikut telah dilakukan sejak langkah terakhir:

Layanan tautan extractor JSON API (ditulis dengan Python) dipindahkan dalam folder terpisah ./api yang memiliki kode yang sama persis seperti pada langkah sebelumnya
Aplikasi front-end web ditulis dalam PHP di bawah folder ./www yang berbicara dengan API JSON
Aplikasi PHP dipasang di dalam php resmi: gambar Docker 7-apache untuk modifikasi yang lebih mudah selama pengembangan
Aplikasi web dapat diakses di http: // <hostname> [: <prt>] /? Url = <url-encoded-url>
Variabel lingkungan API_ENDPOINT digunakan di dalam aplikasi PHP untuk mengkonfigurasinya untuk berbicara dengan server JSON API
File docker-compose.yml ditulis untuk membangun berbagai komponen dan merekatkannya
Pada langkah ini kami berencana untuk menjalankan dua wadah terpisah, satu untuk API dan yang lainnya untuk antarmuka web. Yang terakhir membutuhkan cara untuk berbicara dengan server API. Agar kedua kontainer dapat saling berbicara, kita dapat memetakan porta mereka pada mesin host dan menggunakannya untuk meminta perutean atau kita dapat menempatkan kontainer dalam satu jaringan pribadi dan mengakses secara langsung. Docker memiliki dukungan jaringan yang sangat baik dan memberikan perintah yang bermanfaat untuk menangani jaringan. Selain itu, dalam jaringan Docker, wadah mengidentifikasi diri mereka menggunakan nama mereka sebagai nama host untuk menghindari perburuan alamat IP mereka di jaringan pribadi. Namun, kami tidak akan melakukan ini secara manual, sebagai gantinya kami akan menggunakan Docker Compose untuk mengotomatiskan banyak tugas ini.

Langkah 5: Layanan Redis untuk Caching
Beberapa perubahan nyata dari langkah sebelumnya adalah sebagai berikut:

Dockerfile lain ditambahkan di folder ./www untuk aplikasi web PHP untuk membangun gambar yang lengkap dan menghindari pemasangan file langsung
Wadah Redis ditambahkan untuk caching menggunakan gambar Redis Docker resmi
Layanan API berbicara dengan layanan Redis untuk menghindari mengunduh dan mem-parsing halaman yang sudah dikerik sebelumnya
Variabel lingkungan REDIS_URL ditambahkan ke layanan API untuk memungkinkannya terhubung ke cache Redis