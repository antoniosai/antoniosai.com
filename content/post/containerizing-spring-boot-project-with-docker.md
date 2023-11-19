+++
author = "Antonio Saiful Islam"
title = "Containerizing Spring Boot Project with Docker"
date = "2023-04-05"
description = "Containerizing Projek Spring Boot dengan Docker"
tags = ["spring", "spring boot", "java", "crud", "docker", "container"]
image = "https://miro.medium.com/v2/resize:fit:786/format:webp/1*GUte532JuhgcuxGE8imZ2w.png"
+++

# Containerizing Projek Spring Boot dengan Docker

Pada tutorial yang lalu kita telah membuat sebuah RESTful API Server untuk project CRUD sederhana menggunakan [\*\*\*Simple CRUD Restful API dengan Spring Boot 3](https://medium.com/@antoniosae/simple-crud-restful-api-dengan-spring-boot-3-1b92adfd3adc)*\*\*. Nah, di proyek tersebut kita akan mencoba untuk meng *Containerizing\* sebuah Application dengan Docker. Pada kasus kali ini tentunya menggunakan Spring Boot

> Disclaimer: Di artikel ini tidak akan terlalu membahas apa itu tentang Docker nya sendiri, ya karena sudah terlalu banyak tutorial yang tersebar di Internet. Hanya saja saya membahas tentang best practice nya saja.

## Persiapan

Untuk memulai mempraktekan apa yang akan dibahas di artikel ini, teman-teman harap menginstall Docker & Docker Compose. Teman-teman bisa searching artikel lain nya untuk menginstall.

Untuk saya sendiri menggunakan **_Docker version 23.0.1_** dan **_Docker Compose version v2.15.1_**

![](https://cdn-images-1.medium.com/max/2000/1*pa7hr99IEtOzZ0F7fu_Vcw.png)

Setelah di device / host teman telah terinstall Docker dan juga Docker Compose, sedikit akan saya jelaskan tentang file yang akan kita buat nanti nya. Ada 3 file yang akan kita buat yaitu

- **docker-compose.yaml**
  Sebuah file yang bertugas untuk mendefinisikan sebuah Container akan dibuat beserta dengan Config/Environment, Network, Volume yang akan digunakan

- **Dockerfile
  **Sebuah file untuk membuat Docker Image

- **run.sh**
  Merupakan inisiatif penulis untuk membuat shell script yang berisi perintah dasar untuk mengeksekusi Dockerfile & docker-compose sekaligus sehingga tidak perlu mengetik dari awal

### Pembuatan Dockerfile

{{< highlight md "linenos=table,hl_lines=8 15-17,linenostart=199" >}}
FROM openjdk:17-jdk-alpine3.14
ENV APP_HOME=/usr/app/
WORKDIR $APP_HOME
COPY target/*.jar app.jar
EXPOSE 8001
ENTRYPOINT ["java","-jar","app.jar"]
{{< / highlight >}}

Source Code diatas merupakan isi dari file **_Dockerfile. _**Image yang digunakan adalah openjdk:17-jdk-alpine3.14 yang tersedia di [\*\*\*hub.docker.com](http://hub.docker.com)\*\*\* ([https://hub.docker.com/layers/library/openjdk/17-ea-jdk-alpine3.14/images/sha256-a996cdcc040704ec6badaf5fecf1e144c096e00231a29188596c784bcf858d05](https://hub.docker.com/layers/library/openjdk/17-ea-jdk-alpine3.14/images/sha256-a996cdcc040704ec6badaf5fecf1e144c096e00231a29188596c784bcf858d05))

Saya sengaja memilih JDK versi 17 karena menggunakan versi LTS yang terbaru dari vendor openjdk karena sangat populer di kalangan komunitas dan alpine karena image yang dihasilkan nya relatif lebih kecil.

Dan juga perhatikan di potongan **EXPOSE 8001 **maksud nya adalah Docker akan melisten port 8001

### Pembuatan file docker-compose.yaml
{{< highlight yaml >}}
version: "3.8"
services:
  simplecrudapp:
    image: antoniosai/simple-crud-app:1.0.0
    build: .
    ports:
      - "8001:8001"
    restart: always
    depends_on:
      - dbapp
    networks:
      - simpleappnetwork
    environment:
      spring.datasource.url: jdbc:postgresql://dbapp:5432/postgres
      spring.datasource.username: admin
      spring.datasource.password: admin
      spring.jpa.hibernate.ddl.auto: create
  dbapp:
    image: postgres:15.1
    restart: always
    ports:
      - "5433:5432"
    environment:
      - POSTGRES_PASSWORD=admin
      - POSTGRES_USER=admin
      - POSTGRES_DB=postgres
    networks:
      - simpleappnetwork
networks:
  simpleappnetwork:

{{</highlight>}}

Diatas merukapan isi dari file **_docker-compose.yaml _**yang akan membuat dua buah container yaitu : **_simplecrudapp_** dan **_dbapp _**dengan masing-masing konfigurasi nya

### Pembuatan file run.sh

{{< highlight bash >}}
mvn clean install && docker build -f Dockerfile -t antoniosai/simple-crud-app:1.0.0 . && docker-compose up -d
{{</ highlight >}}

Langkah terakhir adalah pembuatan file **_run.sh _**shell script ini adalah optional. Saya hanya saja berinisiatif supaya tidak cape untuk ngetik untuk menjalankan perintah yang sama secara berulang. Isi nya merupakan :

- **_mvn clean install
  _**Merupakan command untuk clean & install project spring boot yang nanti akan mengenerate sebuah file \*.jar

- **_docker build -f Dockerfile -t antoniosai/simple-crud-app:1.0.0 .
  _**Command untuk membuat Docker image dari Dockerfile yang telah dibuat dan juga diberi nama container beserta tag nya

- **_docker-compose up -d
  _**Command untuk mengeksekusi file docke-compose.yaml. Maka container akan dibuatkan

### Finishing

Setelah semua nya telah selesai, langkah kita terakhir adalah tinggal mengeksekusi file run.sh. Di linux kita cukup menjalankan command **_‘sh run.sh’_**

Setelah semua nya selesai, maka container sudah siap digunakan

![](https://cdn-images-1.medium.com/max/3410/1*yQFUxNyvHKMYLtyJBV6WVw.png)

Dan juga sudah bisa diakses menggunakan postman

![](https://cdn-images-1.medium.com/max/2000/1*0nMQp87S1YUoZofo2XXFUA.png)

## Pentutup

Demikian sudah bahasan kali ini. Mohon maaf jika terdapat banyak kekurangan, namun saya sangat terbuka terhadap kritik & masukan. Ketik aja ya di kolom komentar.

Harapan saya di bahasan kali ini adalah teman-teman bisa lebih mudah memahami cara kerja nya dengan langsung mempraktekan nya.

Source code juga tersedia di repo saya di [https://github.com/antoniosai/simple-crud-app-spring-boot](https://github.com/antoniosai/simple-crud-app-spring-boot)
