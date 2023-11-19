+++
author = "Antonio Saiful Islam"
title = "Config Service pada Spring Boot"
date = "2023-11-19"
description = "Konfigurasi Spring Boot Config Server dan Penggunaannya"
tags = ["software developer", "spring boot", "coding", "microservice", "java"]
image = "https://cdn-images-1.medium.com/max/2000/1*zO-9qAkaL1IzvpLb448zKA.jpeg"
+++


Spring Cloud Config Server memungkinkan kita untuk menyimpan file konfigurasi di dalam aplikasi menjadi tersentralisasi dan terdistribusi. File Config yang dimaksud adalah sebuah file yang berisi informasi yg digunakan dalam aplikasi, misal database credentials, Username, password, API Keys, host&port untuk redis, kafka dll.

Hal ini dilakukan dengan berbagai macam alasan. Berikut alasan utama menurut saya pribadi:

1. **Sentralisasi: **Dengan hadir nya Config Server aplikasi kita tidak lagi menyimpan file konfigurasi di aplikasi. Namun di Config Repo

1. **Keamanan: **Mencegah aplikasi kita menyimpan informasi confidential seperti API Keys, Username, Password dll

1. **Dinamis & Fleksibilitas: **Apabila terdapat sebuah perubahan pada konfigurasi maka aplikasi kita tidak memerlukan effort lebih seperti recompile dari awal. Karena sudah tersentralisasi pada config server dan hal ini sangat menguntukan developer.

## Pembahasan

Diatas merupakan gambar sebuah alur Penggunaan Config Server. Dibutuhkan setidak nya
**1. Config Repo**: Berisikan file untuk menyimpan sebuah konfigurasi seperti apa yg telah dijelaskan. Struktur forlder yang akan kita gunakan adalah*** {app_name}/{app_name}-{profile}.yml***

    Config Repo/
    ├── app_1/
    │   ├── app_1-dev.yml
    │   └── app_1-prod.yml
    └── app_2/
        ├── app_2-dev.yml
        └── app_2-prod.yml

**2. Config Server: **Merupakan sebuah Project Spring Boot yang bertanggung jawab untuk menghandle request dari aplikasi / config client.

**3. Config Client: **Adalah aplikasi kita yang akan menggunakan layanan dari Config Server

## Membuat Config Repo

Pada kasus kali ini kita cukup membuat satu folder config untuk satu aplikasi saja namun terdiri dari 2 profile, yaitu: prod dan dev


{{<  highlight yaml >}}
# folder_path: app_test/app_test-dev.yml
spring:
  application:
    name: app_test

server:
  port: 8090

value:
  message: "Dev Config is Active"
{{< / highlight >}}

dan untuk prod nya

{{< highlight yaml >}}
# folder_path: app_test/app_test-prod.yml
spring:
  application:
    name: app_test

server:
  port: 8090

value:
  message: "Prod Config is Active"
{{< / highlight >}}

setelah itu kita bisa melakukan commit dan push ke server Git. Bisa menggunakan GitHub, GitLab ataupun BitBucket. Pada contoh kali ini teman-teman bisa melihat contoh nya di [https://github.com/antoniosai/spring-boot-config-repo-demo](https://github.com/antoniosai/spring-boot-config-repo-demo)

## **Setup Config Server**

Inti dari pembahasan kita terletak disini. Baik nya kita membuat project Spring Boot kosongan dengan dependency ***spring-cloud-config-server***


{{< highlight xml>}}
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-config-server</artifactId>
</dependency>
{{< / highlight >}}

Kemudian kita buka class utama nya dan tambahkan annotation ***@EnableConfigServer***

{{< highlight java >}}
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

  public static void main(String[] args) {
  SpringApplication.run(ConfigServerApplication.class, args);
  }
}
{{< / highlight >}}

Langkah terakhir adalah melakukan konfigurasi di application.properties ataupun application.yml (saya prefer application.yml)

{{< highlight yaml >}}
# path: src/main/resources/application.yml
server:
  port: 8000
spring:
  application:
    name: configserver
  output:
    ansi:
      enabled: ALWAYS
  cloud:
    config:
      server:
        git:
          uri: https://github.com/antoniosai/spring-boot-config-repo-demo
          searchPaths: "{application}"
          default-label: master
    enabled: true
{{< / highlight >}}

Penjelasan:

* searchPaths: merupakan property yang digunakan untuk mencari lokasi atau direktori dimana file tersebut berada. *{application}*

* **uri: **Merupakan URL repository ke Git yang digunakan untuk menyimpan file konfigurasi

* **default-label**: Branch dari Git Repository yang akan digunakan secara default

untuk penjelasan lebih lanjut teman-teman bisa dipelajari di [https://docs.spring.io/spring-cloud-config/docs/current/reference/html/](https://docs.spring.io/spring-cloud-config/docs/current/reference/html/)

## Setup Config Client

Tambahkan dependency pada maven untuk aplikasi yang akan menggunakan layanan dari Config Server


{{< highlight xml >}}
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
{{< / highlight >}}

Ubah application.yml menjadi


{{< highlight yaml >}}
# path: src/resources/application.yml
spring:
  profiles:
    active: dev
  application:
    name: app_test
  output:
    ansi:
      enabled: ALWAYS
  config:
    import: "optional:configserver:"
  cloud:
    config:
      fail-fast: true
      uri: http://localhost:8000
      enabled: true
{{< / highlight >}}

Langkah konfigurasi selesai. Mari kita coba membuat sebuah controller percobaan untuk menampilkan value dari config repo dan sekaligus membuktikan berhasil atau tidak nya.


{{< highlight java >}}
@RestController
@RequestMapping("hello")
public class DummyController {

    @Value("${value.message}")
    private String greeting;

    @GetMapping
    public String sayHello() {
        return greeting;
    }
}
{{< / highlight >}}

Coba eksekusi di browser


<img src="https://cdn-images-1.medium.com/max/2000/1*0rCZ0iXpTQtPJlaoRe0P5w.png"  style="width: 500px" >

menggunakan config prod

<img src="https://cdn-images-1.medium.com/max/2000/1*Q_mpZ6pRasPByY_TsUyAeQ.png"  style="width: 500px" >

Yeay! Untuk konfigurasi sudah selesai.

Kita juga Bisa menambahkan argument untuk mengoverride profile dengan menggunakan -Dspring.profiles.active=prod|dev


{{<  highlight bash >}}
java -jar app.jar -Dpsring.profiles.active=prod
{{< / highlight >}}
## Penutup

Sekian atas pembahasan kita kali ini, mohon maaf apabila apabila (pasti ada sih) kekurangan. Sampaikan saja di kolom komentar, mudah2an saya response.. Tetap semangat, belajar terus.. Karena belajar adalah salah satu kewajiban kita yg mesti kita tunaikan sampai mati


## Link Github
- Config Repo: [https://github.com/antoniosai/spring-boot-config-repo-demo](https://github.com/antoniosai/spring-boot-config-repo-demo)
- Config Server: [https://github.com/antoniosai/spring-boot-config-server-demo](https://github.com/antoniosai/spring-boot-config-server-demo)
- Config Client: [https://github.com/antoniosai/spring-boot-config-client-demo](https://github.com/antoniosai/spring-boot-config-client-demo)
