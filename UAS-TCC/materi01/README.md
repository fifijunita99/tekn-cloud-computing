                                Docker for Beginners - Linux
Tugas 0: Prasyarat
Anda akan membutuhkan semua yang berikut untuk menyelesaikan lab ini:

Klon dari repo GitHub laboratorium.
DockerID.
Clone the GitHub Repo Lab
Gunakan perintah berikut untuk mengkloning repo lab dari GitHub (Anda dapat mengklik perintah atau mengetiknya secara manual). Ini akan membuat salinan repo laboratorium di sub-direktori baru bernama linux_tweet_app.

Tugas 1: Jalankan beberapa wadah Docker sederhana
Ada berbagai cara untuk menggunakan wadah. Ini termasuk:

Untuk menjalankan satu tugas: Ini bisa berupa skrip shell atau aplikasi khusus.
Interaktif: Ini menghubungkan Anda ke wadah yang mirip dengan cara Anda SSH ke server jauh.
Di latar belakang: Untuk layanan jangka panjang seperti situs web dan basis data.
Di bagian ini Anda akan mencoba masing-masing opsi tersebut dan melihat bagaimana Docker mengelola beban kerja.

jalankan satu tugas dalam wadah Alpine Linux
Pada langkah ini kami akan memulai sebuah wadah baru dan memintanya untuk menjalankan perintah nama host. Kontainer akan mulai, jalankan perintah hostname, lalu keluar.

-  Docker menjaga wadah berjalan selama proses itu dimulai di dalam wadah masih berjalan. Dalam hal ini proses nama host keluar segera setelah output ditulis. Ini artinya wadah berhenti. Namun, Docker tidak menghapus sumber daya secara default, sehingga wadah masih ada dalam status Keluar.

Jalankan wadah Ubuntu interaktif
Anda dapat menjalankan sebuah wadah berdasarkan versi Linux yang berbeda dari yang dijalankan pada host Docker Anda.

Perhatikan bahwa host VM kami menjalankan Alpine Linux, namun kami dapat menjalankan wadah Ubuntu. Seperti disebutkan sebelumnya, distribusi Linux di dalam wadah tidak perlu cocok dengan distribusi Linux yang berjalan di host Docker.

Namun, wadah Linux mengharuskan host Docker menjalankan kernel Linux. Misalnya, wadah Linux tidak dapat berjalan langsung di host Windows Docker. Hal yang sama berlaku untuk kontainer Windows - mereka harus dijalankan pada host Docker dengan kernel Windows.

Wadah interaktif berguna ketika Anda menyusun gambar Anda sendiri. Anda dapat menjalankan sebuah wadah dan memverifikasi semua langkah yang Anda butuhkan untuk menggunakan aplikasi Anda, dan menangkapnya di Dockerfile.

Tugas 2: Paket dan menjalankan aplikasi khusus menggunakan Docker
Pada langkah ini Anda akan belajar cara mengemas aplikasi Anda sendiri sebagai gambar Docker menggunakan Dockerfile.

Sintaks Dockerfile sangat mudah. Dalam tugas ini, kami akan membuat situs web NGINX sederhana dari Dockerfile

Tugas 3: Memodifikasi situs web yang sedang berjalan
Saat Anda bekerja secara aktif pada aplikasi, tidak nyaman harus menghentikan wadah, membangun kembali gambar, dan menjalankan versi baru setiap kali Anda membuat perubahan pada kode sumber Anda.

Salah satu cara untuk merampingkan proses ini adalah dengan memasang direktori kode sumber pada mesin lokal ke wadah yang sedang berjalan. Ini akan memungkinkan setiap perubahan yang dibuat pada file pada host untuk segera tercermin dalam wadah.

Kami melakukan ini menggunakan sesuatu yang disebut bind mount.

Saat Anda menggunakan bind mount, file atau direktori pada mesin host dipasang ke sebuah wadah yang berjalan di host yang sama.