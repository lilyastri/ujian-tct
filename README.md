## Responsi Praktikum Teknologi Cloud Terapan
----

### Apa itu docker??
---

Docker adalah sebuah aplikasi yang bersifat open source yang berfungsi sebagai wadah/container untuk mengepak/memasukkan sebuah software secara lengkap beserta semua hal lainnya yang dibutuhkan oleh software tersebut dapat berfungsi. Pengaturan software beserta file/hal pendukung lainnya akan menjadi sebuah Image (istilah yang diberikan oleh docker). Kemudian sebuah instan dari Image tersebut kemudian disebut Container.

---

### Lalu Dockerfile itu apa ??
---

Dockerfile adalah sebuah berkas utama dalam Docker yang memuat perintah-perintah atau skrip yang akan dilakukan ketika sebuah image dibuat. 
Dockerfile tak lain merupakan sebuah berkas yang berisikan perintah seperti kita melakukan instalasi terhadap suatu aplikasi ataupun konfigurasi.

Contoh, bila kita ingin melakukan instalasi web server seperti Apache, hal pertama yang harus kita lakukan adalah melakukan pembaruan terhadap repositori dengan menggunakan "apt-get update" atau "yum update" dan sejenisnya yang bisa disesuaikan dengan sistem operasi masing-masing. Setelah itu, kita akan melakukan instalasi Apache dengan menggunakan "apt-get install apache2" atau "yum install httpd".

Diatas adalah jenis perintah yang akan kita temukan pada sebuah Dockerfile. Sesuatu seperti command, memanggil skrip lain, mengatur enviroment pada sistem operasi, menambahkan berkas, mengatur hak akses dan itu semua bisa dilakukan dengan Dockerfile.

Dengan Docker kita bisa memilih sistem operasi yang akan kita pakai sesuai dengan kebutuhan. Pada contoh ini kita akan menggunakan CentOS sebagai bahan uji coba. Namun, disini saya hanya akan menjelaskan garis besarnya saja tentang Dockerfile. 

Berikut adalah contoh Dockerfile untuk melakukan instalasi dan menjalankan apache2/httpd pada CentOS:

    FROM centos:latest
    MAINTAINER http://www.centos.org
    LABEL Vendor="CentOS"
    LABEL License=GPLv2
    LABEL Version=2.4.6-31

    RUN yum -y update && yum clean all
    RUN yum -y install httpd && yum clean all

    EXPOSE 80
    CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]

Dockerfile diambil dari : https://github.com/CentOS/CentOS-Dockerfiles/blob/master/httpd/centos7/Dockerfile

Ada beberapa hal yang mungkin akan sering kita temukan pada Dockerfile, diantaranya yaitu :
- Pada baris pertama menjelaskan sistem operasi yang kita pakai saat menjalankan Docker, pada contoh kali ini kita akan menggunakan CentOS versi terbaru yaitu CentOS 7. Bila ingin menggunakan CentOS 6 bisa menggantinya dengan "FROM centos:centos6" atau bila ingin menggunakan Ubuntu bisa dengan "FROM ubuntu:latest". Itu semua tergantung dari kebutuhan dan keinginan kita.
- Pada baris kedua menjelaskan tentang siapa yang mengelola(baca: maintainer) Dockerfile tersebut. Bila kita ingin mencatumkan informasi pribadi bisa mengganti informasi tersebut sesuai dengan yang kita inginkan, biasanya mencatumkan nama dan email agar mudah untuk dihubungi ketika Dockerfile tersebut mengalami masalah. Pada License menyatakan lisensi yang dipakai oleh aplikasi tersebut dan Version menyatakan versi yang akan kita pasang.
- Untuk baris selanjutnya menjelaskan untuk melakukan pembaruan terhadap sistem operasi dan melakukan pembersihan terhadap package yang memang tidak digunakan. Dalam Dockerfile lebih disarankan untuk menggabungkan proses menjadi satu dibandingkan dengan memisahkan nya. Maka dari itu kita menggunakan "&&" untuk menggabungkan proses, sama seperti kita menjalankan perintah di GNU/Linux. 
- Pada baris selanjutnya melakukan instalasi apache2/httpd dan membersihkan package ketika selesai dipasang. EXPOSE memberitahukan bahwa Docker tersebut menggunakan port tertentu untuk mengaktifkan jaringan antara proses yang sedang berjalan dengan jaringan yang ada, sederhana nya Docker melakukan binding port.
- Pada baris terakhir merupakan perintah yang akan berjalan ketika container dijalankan. Perintah ini hanya boleh dideklarasikan sekali saja, karena Dockerfile hanya akan melakukan eksekusi pada perintah CMD terakhir. Ini dilakukan untuk menjaga agar dalam satu container hanya berjalan satu proses.

---

### Contoh Penggunaan Dockerfile untuk membuat images custom
---

Dalam hal ini kita beranggapan berada didalam direktori path “/home/sekolahlinux/” didalam folder tersebut ada file (hello-world.jar, Dockerfile)

- yang pertama masuk ke file Dockerfile
    
       vim Dockerfile

- kemudian isikan dengan paramater dibawah didalam filenya

       FROM openjdk
       MAINTAINER sekolahlinux
       RUN mkdir -p /home/sekolahlinux
       COPY ./hello-world.jar /home/sekolahlinux
       EXPOSE 8080
       ENTRYPOINT exec java -jar /home/sekolahlinux/hello-world.jar

Penjelasan mengenai script diatas :

- FROM: digunakan untuk memilih base docker image mana yang akan digunakan untuk membuat docker image baru
- MAINTAINER: digunakan untuk set siapa author yang membuat custom image ini
- RUN: digunakan untuk menjalankan perintah didalam base docker image, misalkan membuat folder, install tool, ataupun update base docker image
- COPY: digunakan untuk mengcopy file yang ada di direktori lokal kedalam base docker image
- EXPOSE: digunakan untuk memberitahu docker port mana yang akan digunakan secara default protocol yang digunakan adalah TCP
- ENTRYPOINT: digunakan untuk menanamkan perintah ke base docker image, dan perintah itu akan eksekusi ketika custom docker image nantinya dijalakan, atau lebih mirip applikasi yang berjalan saat proses startup
- masih banyak paramater-paramaer yang bisa kalian gunakan, untuk detailnya kalian bisa berkunjung ke link berikut https://docs.docker.com/engine/reference/builder/

Kemudian jika selesai, jalankan perintah berikut :

    cd /home/sekolahlinux
    docker build -t java-sekolahlinux .

Maka akan keluar seperti dibawah ini :

    [root@localhost sekolahlinux]# docker build -t java-sekolahlinux:latest .
    Sending build context to Docker daemon  2.56 kB
    Step 1 : FROM openjdk
     ---> 6077adce18ea
    Step 2 : MAINTAINER sekolahlinux
    ---> Running in 947c8811261f
    ---> 1c8bd2f8a71f
    Removing intermediate container 947c8811261f
    Step 3 : RUN mkdir -p /home/sekolahlinux
    ---> Running in 24ad9665d48f
    ---> 6ac2d1e0a299
    Removing intermediate container 24ad9665d48f
    Step 4 : COPY ./hello-world.jar /home/sekolahlinux
    ---> f3a5fa1f14d3
    Removing intermediate container 6b5e3ec6be03
    Step 5 : EXPOSE 8080
    ---> Running in ba9b4f0b4cb1
    ---> 296f308ea363
    Removing intermediate container ba9b4f0b4cb1
    Step 6 : ENTRYPOINT exec java -jar /home/sekolahlinux/hello-world.jar
    ---> Running in 33056dc8dd7f
    ---> e94a5b7ce21a
    Removing intermediate container 33056dc8dd7f
    Successfully built e94a5b7ce21a

kemudian yang terakhir coba jalankan perintah "docker images"

    [root@localhost sekolahlinux]# docker images
    REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
    java-sekolahlinux     latest              e94a5b7ce21a        14 seconds ago      739.8 MB
    docker.io/openjdk     latest              6077adce18ea        2 weeks ago         739.8 MB
    [root@localhost sekolahlinux]#

dari hasil diatas diketahui bahwa images baru sudah berhasil dibuat. 

--- 






 
