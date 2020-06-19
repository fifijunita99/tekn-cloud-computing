                            Docker Networking Hands-on Lab
Bagian # 1 - Dasar-Dasar Jaringan
Langkah 1: Perintah Jaringan Docker
Perintah jaringan buruh pelabuhan adalah perintah utama untuk mengonfigurasi dan mengelola jaringan kontainer. Jalankan perintah jaringan buruh pelabuhan dari terminal pertama.
Output perintah menunjukkan bagaimana menggunakan perintah dan juga semua sub-perintah jaringan buruh pelabuhan. Seperti yang Anda lihat dari output, perintah jaringan buruh pelabuhan memungkinkan Anda membuat jaringan baru, mendaftar jaringan yang ada, memeriksa jaringan, dan menghapus jaringan. Ini juga memungkinkan Anda untuk menghubungkan dan memutuskan wadah dari jaringan.

Langkah 2: Daftar jaringan
Jalankan jaringan buruh pelabuhan adalah perintah untuk melihat jaringan kontainer yang ada pada host Docker saat ini
Jaringan baru yang Anda buat juga akan muncul di output dari perintah docker network ls.
Anda dapat melihat bahwa setiap jaringan mendapatkan ID dan NAME yang unik. Setiap jaringan juga dikaitkan dengan satu driver. Perhatikan bahwa jaringan "jembatan" dan jaringan "host" memiliki nama yang sama dengan driver masing-masing.

Langkah 3: Periksa jaringan
Perintah inspeksi jaringan buruh pelabuhan digunakan untuk melihat detail konfigurasi jaringan. Rincian ini meliputi; nama, ID, driver, driver IPAM, info subnet, wadah terhubung, dan banyak lagi.
Gunakan jaringan buruh pelabuhan periksa <network> untuk melihat detail konfigurasi jaringan wadah pada host Docker Anda. Perintah di bawah ini menunjukkan rincian jaringan yang disebut jemba

bagian 4: Daftar plugin driver jaringan
Perintah info buruh pelabuhan menunjukkan banyak informasi menarik tentang pemasangan Docker.
Jalankan perintah info buruh pelabuhan dan temukan daftar plugin jaringan.

Bagian # 2 - Jembatan Jaringan
Langkah 1: Dasar-Dasarnya
Setiap instalasi Docker yang bersih dilengkapi dengan jaringan yang disebut jembatan. Verifikasi ini dengan jaringan buruh pelabuhan ls
Output di atas menunjukkan bahwa jaringan jembatan dikaitkan dengan driver jembatan. Penting untuk dicatat bahwa jaringan dan driver terhubung, tetapi mereka tidak sama. Dalam contoh ini jaringan dan driver memiliki nama yang sama - tetapi mereka bukan hal yang sama!

Output menunjukkan bahwa jaringan jembatan dicakup secara lokal. Ini berarti bahwa jaringan hanya ada pada host Docker ini. Ini berlaku untuk semua jaringan yang menggunakan driver jembatan - driver jembatan menyediakan jaringan host tunggal.
Semua jaringan yang dibuat dengan driver jembatan didasarkan pada Linux bridge (a.k.a. switch virtual).
Instal perintah brctl dan gunakan untuk mendaftar jembatan Linux pada host Docker Anda. Anda dapat melakukan ini dengan menjalankan sudo apt-get install bridge-utils.

Langkah 2: Hubungkan wadah
Jaringan jembatan adalah jaringan default untuk wadah baru. Ini berarti bahwa kecuali Anda menentukan jaringan yang berbeda, semua kontainer baru akan terhubung ke jaringan jembatan.
Buat wadah baru dengan menjalankan docker run -dt ubuntu sleep infinity.

Langkah 3: Uji konektivitas jaringan
Output ke perintah memeriksa jaringan buruh pelabuhan sebelumnya menunjukkan alamat IP wadah baru. Dalam contoh sebelumnya ini adalah "172.17.0.2" tetapi milik Anda mungkin berbeda.
Ping alamat IP wadah dari prompt shell host Docker Anda dengan menjalankan ping -c5 <Alamat IPv4>. Ingatlah untuk menggunakan IP wadah di lingkungan Anda.
Langkah 4: Konfigurasikan NAT untuk konektivitas eksternal
Dalam langkah ini kami akan memulai wadah NGINX baru dan memetakan port 8080 pada host Docker ke port 80 di dalam wadah. Ini berarti bahwa lalu lintas yang mengenai host Docker di port 8080 akan diteruskan ke port 80 di dalam wadah.
CATATAN: Jika Anda memulai wadah baru dari gambar NGINX resmi tanpa menentukan perintah untuk dijalankan, wadah akan menjalankan server web dasar pada port 80.

Bagian # 3 - Overlay Networking
Langkah 1: Dasar-Dasarnya
Pada langkah ini Anda akan menginisialisasi Swarm baru, bergabung dengan node pekerja tunggal, dan verifikasi operasi berhasil.
Jalankan docker swarm init --advertise-addr $ (hostname -i).
Langkah 2: Buat jaringan overlay
Sekarang Anda memiliki Swarm yang diinisialisasi saatnya untuk membuat jaringan overlay.
Buat jaringan overlay baru yang disebut "overnet" dengan menjalankan jaringan docker buat -d overlay overnet.
Langkah 3: Buat layanan
Sekarang kami memiliki jaringan Swarm yang diinisialisasi dan overlay, saatnya untuk membuat layanan yang menggunakan jaringan.
Jalankan perintah berikut dari terminal pertama untuk membuat layanan baru yang disebut myservice pada jaringan overnet dengan dua tugas / replika.
Step 4: Test the network
To complete this step you will need the IP address of the service task running on node2 that you saw in the previous step (10.0.0.3).
Execute the following commands from the first terminal.
Langkah 5: Uji penemuan layanan
Sekarang Anda memiliki layanan yang berfungsi menggunakan jaringan overlay, mari kita uji penemuan layanan.
Jika Anda masih berada di dalam kontainer, masuk kembali ke dalamnya dengan docker exec -it <CONTAINER ID> / bin / bash command.
