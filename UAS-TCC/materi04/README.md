Lab Praktek Orkestrasi Docker

Di lab ini Anda akan bermain-main dengan fitur orkestrasi wadah Docker. Anda akan menggunakan aplikasi sederhana ke satu host dan mempelajari cara kerjanya. Kemudian, Anda akan mengkonfigurasi Docker Swarm Mode, dan belajar untuk menggunakan aplikasi sederhana yang sama di beberapa host. Anda kemudian akan melihat bagaimana skala aplikasi dan memindahkan beban kerja di host yang berbeda dengan mudah.

Bagian 1: Apa itu Orkestrasi
Jadi, apa itu orkestrasi? Yah, orkestrasi mungkin paling baik digambarkan menggunakan contoh. Katakanlah Anda memiliki aplikasi yang memiliki lalu lintas tinggi bersama dengan persyaratan ketersediaan tinggi. Karena persyaratan ini, Anda biasanya ingin menggunakan setidaknya 3+ mesin, sehingga jika tuan rumah gagal, aplikasi Anda masih dapat diakses dari setidaknya dua lainnya. Jelas, ini hanya sebuah contoh dan kasus penggunaan Anda kemungkinan akan memiliki persyaratan sendiri, tetapi Anda mendapatkan idenya.

Menyebarkan aplikasi Anda tanpa Orkestrasi biasanya sangat memakan waktu dan rawan kesalahan, karena Anda harus secara manual SSH ke setiap mesin, mulai aplikasi Anda, dan kemudian terus mengawasi hal-hal untuk memastikan itu berjalan seperti yang Anda harapkan.

Tetapi, dengan Perkakas orkestrasi, Anda biasanya dapat melepas sebagian besar pekerjaan manual ini dan membiarkan otomatisasi melakukan pekerjaan berat. Salah satu fitur keren Orkestrasi dengan Docker Swarm, adalah Anda dapat menggunakan aplikasi di banyak host dengan hanya satu perintah (setelah mode Swarm diaktifkan). Plus, jika salah satu node pendukung mati di Docker Swarm Anda, node lain akan secara otomatis mengambil beban, dan aplikasi Anda akan terus bersenandung seperti biasa.

Jika Anda biasanya hanya menggunakan docker run untuk menyebarkan aplikasi Anda, maka kemungkinan besar Anda akan mendapat manfaat dari menggunakan Docker Compose, Docker Swarm mode, atau keduanya Docker Compose dan Swarm.

Bagian 2: Konfigurasikan Mode Gerombolan
Aplikasi dunia nyata biasanya digunakan di beberapa host seperti yang dibahas sebelumnya. Ini meningkatkan kinerja aplikasi dan ketersediaan, serta memungkinkan komponen aplikasi individu untuk skala secara mandiri. Docker memiliki alat asli yang tangguh untuk membantu Anda melakukan ini.

Contoh menjalankan berbagai hal secara manual dan pada satu host adalah membuat wadah baru pada node1 dengan menjalankan buruh pelabuhan -dt ubuntu sleep infinity.
Tapi, ini hanya pada satu node. Apa yang terjadi jika simpul ini turun? Nah, aplikasi kita baru saja mati dan tidak pernah restart. Untuk memulihkan layanan, kita harus masuk secara manual ke mesin ini, dan mulai mengubah hal-hal untuk membuatnya kembali dan berjalan. Jadi, akan sangat membantu jika kita memiliki beberapa jenis sistem yang memungkinkan kita untuk menjalankan aplikasi / layanan "tidur" ini di banyak mesin.

Di bagian ini Anda akan mengkonfigurasi Mode Swarm. Ini adalah mode opsional baru di mana beberapa host Docker membentuk kelompok mesin yang mengatur sendiri disebut swarm. Mode Swarm memungkinkan fitur baru seperti layanan dan bundel yang membantu Anda menyebarkan dan mengelola aplikasi multi-kontainer di beberapa host Docker.

Anda akan menyelesaikan yang berikut ini:

Konfigurasikan mode Swarm
Jalankan aplikasi
Skala aplikasi
Kuras node untuk perawatan dan penjadwalan ulang kontainer
Untuk sisa lab ini kita akan merujuk ke pengelompokan asli Docker sebagai mode Swarm. Koleksi mesin Docker yang dikonfigurasi untuk mode Swarm akan disebut sebagai swarm.

Segerombolan terdiri dari satu atau lebih Node Manajer dan satu atau lebih Node Pekerja. Node manajer menjaga kondisi gerombolan dan menjadwalkan wadah aplikasi. Node pekerja menjalankan wadah aplikasi. Pada Docker 1.12, tidak ada backend eksternal, atau komponen pihak ke-3, yang diperlukan untuk segerombolan yang berfungsi penuh - semuanya built-in!

Di bagian demo ini Anda akan menggunakan ketiga node di lab Anda. node1 akan menjadi manajer Swarm, sedangkan node2 dan node3 akan menjadi node pekerja. Mode Swarm mendukung node manajer redundan yang sangat tersedia, tetapi untuk keperluan lab ini Anda hanya akan menggunakan node manajer tunggal.

Langkah 2.1 - Buat simpul Manajer
Pada langkah ini Anda akan menginisialisasi Swarm baru, bergabung dengan node pekerja tunggal, dan verifikasi operasi berhasil.

Bagian 3: Menyebarkan aplikasi di beberapa host
Sekarang setelah Anda memiliki swarm up dan running, sekarang saatnya untuk menggunakan aplikasi tidur kami yang sangat sederhana.

Anda akan melakukan prosedur berikut dari node1.

Langkah 3.1 - Menyebarkan komponen aplikasi sebagai layanan Docker
Aplikasi tidur kami menjadi sangat populer di internet (karena mengenai Reddit dan HN). Orang suka itu. Jadi, Anda harus mengukur aplikasi Anda untuk memenuhi permintaan puncak. Anda harus melakukan ini di beberapa host untuk ketersediaan tinggi juga. Kami akan menggunakan konsep Layanan untuk mengukur aplikasi kami dengan mudah dan mengelola banyak wadah sebagai satu kesatuan.
Bagian 4: Skala aplikasi
Permintaannya gila! Semua orang menyukai aplikasi tidur Anda! Sudah waktunya untuk meningkatkan skala.

Salah satu hal hebat tentang layanan adalah Anda dapat menaikkan dan menurunkannya untuk memenuhi permintaan. Pada langkah ini Anda akan meningkatkan layanan dan kemudian kembali turun.

Anda akan melakukan prosedur berikut dari node1.

Skala jumlah kontainer dalam layanan sleep-app ke 7 dengan pembaruan layanan buruh pelabuhan - replika 7 perintah sleep-app. replika adalah istilah yang kami gunakan untuk menggambarkan wadah identik yang menyediakan layanan yang sama.
Bagian 5: Kuras simpul dan jadwalkan ulang wadah
Aplikasi tidur Anda telah melakukan yang luar biasa setelah mencapai Reddit dan HN. Sekarang nomor 1 di App Store! Anda telah meningkatkan selama liburan dan turun selama musim yang lambat. Sekarang Anda sedang melakukan pemeliharaan di salah satu server Anda sehingga Anda harus dengan anggun mengeluarkan server dari kerumunan tanpa mengganggu layanan kepada pelanggan Anda.
