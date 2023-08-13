+++
author = "Antonio Saiful Islam"
title = "Simple CRUD RestfulAPI with Spring Boot 3"
date = "2023-04-05"
description = "Simple CRUD with Spring Boot"
tags = ["spring", "spring boot", "java", "crud"]
image = "https://cdn-images-1.medium.com/max/2996/1*-gNZUCf7unsVZTr1GdiF_A.png"
+++

# Simple CRUD Restful API dengan Spring Boot 3

Dibagian pertama pada artikel ini, kita akan mencoba membuat sebuah Restful API Server dengan menggunakan Spring Boot 3

Sebelum melanjutkan ke dalam proses coding disini saya akan menjelaskan SDK/dependencies/kebutuhan yang saya gunakan, yaitu :

- **JDK 17.0.5 LTS**

- **IntellijIDEA Community Edition**

- **Maven 3.8.7**

![SDK yang terinstall pada host saya](https://cdn-images-1.medium.com/max/2000/1*kAWTJXt4YkP43hjZxoy9pQ.png)_SDK yang terinstall pada host saya_

## **Persiapan**

Pertama kali yg harus kita lakukan adalah mendapatkan project bootstraping yang di generate dari Spring Initializr. Teman-teman bisa mengakses nya disini [http://start.spring.io/](http://start.spring.io/)

Memilih dependencies yang akan kita butuhkan, diantara nya yaitu

![Bootstrapping Java Spring Project menggunakan Spring Initializr ([http://start.spring.io](http://start.spring.io))](https://cdn-images-1.medium.com/max/2996/1*-gNZUCf7unsVZTr1GdiF_A.png)_Bootstrapping Java Spring Project menggunakan Spring Initializr ([http://start.spring.io](http://start.spring.io))_

- **Lombok
  **Sebuah library yang digunakan untuk membantu pembuatan POJO. Contoh nya adalah kita bisa menggunakan annotation @Setter, @Getter, @Constructor

- **PostgreSQL Driver
  **Sesuai nama nya.. Yang dimana Project Java Spring Boot kita dapat mengakses Database PostgreSQL

- **Spring Web
  **Library yang memungkinkan kita untuk membuat sebuah RESTful API dengan annotation yang disediakan seperti @RestController

- [\*\*Spring Data JDBC](https://medium.com/blogging-guide/medium-bullet-points-numbered-lists-and-custom-lists-e48a8a45b4a7)
  \*\*Sebuah Object-Relational Mapper yang digunakan untuk utilitas dasar dalam database.

- **Spring BootDevTools
  **Sebuah library yang memungkinkan kita untuk restart aplikasi, live reload beserta configuration. Hal ini sangat bermanfaat dalam proses development / coding.

- **Spring Data JPA
  **Sebuah abstraksi dari data layer class yang dapat kita gunakan untuk membuat sebuah CRUD

- **H2 Database
  **Sebuah database management system

Setelah semua depedencies dipilih pada Spring Initializr. Teman-teman bisa langsung Generate di tombol yang telah disediakan. Maka teman-teman bisa langsung menggunakan menggunakan nya. Selanjut nya teman-teman bisa langsung Open Project menggunakan IntellijIDEA

## Proses Coding

Setelah bootstrapping yang baru saja teman-teman buat di Spring Initialzr, maka yang harus teman-teman lakukan adalah mengekstrak nya dan membuka nya menggunakan IntellijIDEA.

### #1 Struktur Forlder

![](https://cdn-images-1.medium.com/max/2000/1*_B2xuMKx2tGW0qmjWjTGmw.png)

Perancangan Struktur Folder yang akan kita lakukan adalah seperti pada gambar. Struktur tersebut saya pilih karena mengimplementasikan Domain-Driven Design.

### #1 Setup & Konfigurasi

Di dalam folder **_src/main/resources _**adalah direktori yang digunakan untuk menyimpan sebuah konfigurasi. Mari kita buat sebuah file bernama **_application.yml _**di dalam folder tersebut. Berikut isi dari

    # src/main/resources/application.yml

    server:
      port: 8001
    spring:
      application:
        name: simple-crud-app
        version: 1.0.0
      datasource:
        driverClassName: org.postgresql.Driver
        hikari:
          idle-timeout: 5000
          max-lifetime: 10000
          maximum-pool-size: 10
          minimum-idle: 3
          schema: school
        initialization-mode: always
        platform: postgres
        url: jdbc:postgresql://localhost:5432/simplecrudapp
        username: username
        password: myverystrongpassword
      jpa:
        hibernate:
          ddl-auto: update
        properties:
          hibernate:
            format_sql: false
        show-sql: true
      output:
        ansi:
          enabled: ALWAYS

### #2 Pembuatan Entity

Pertama kali yang kita lakukan pada tahap coding kali ini adalah Pembuatan Entity. Class Entity ini adalah sebuah Class yang merepresentasikan sebuah table dan column beserta tipe data nya pada Database.

    package com.antoniosai.crud.modules.book;


    import jakarta.persistence.*;
    import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.hibernate.annotations.CreationTimestamp;
    import org.hibernate.annotations.UpdateTimestamp;
    import org.springframework.data.annotation.LastModifiedDate;

    import java.util.Date;

    @Entity
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    @Table(name = "book", schema = "public")
    public class BookEntity {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        private String title;

        private String description;

        private String year;

        private String image;

        @CreationTimestamp
        private Date created_at;

        @UpdateTimestamp
        @LastModifiedDate
        private Date updated_at;

    }

Penjelasan terhadap annotations yang digunakan di dalam class Entity

- **@Entity** merepresentasikan bahwa class tersebut adalah sebuah Entitas

- **@Getter** & @**Setter **merupakan sebuah annotation untuk membuat sebuah class Getter & Setter pada sebuah non-static fields

- **@NoArgsConstructor** sebuah annotation untuk membuat konstruktor kosongan

- **@AllArgsContructor** sebuah annotation yang akan membuat constructor dengan jumlah parameter nya sesuai dengan banyak nya field

- **@Id** merupakan sebuah annotation yang merepresentasikan bahwa field id adalah sebuah Identifier

- **@CreationTimestamp **dengan menambahkan annotation ini. Hibernate akan mengisi value nya berdasarkan tanggal pembuatan saat itu ketika pembuatan sebuah row dalam table

- **@UpdateTimestamp **sama hal nya seperti CreationTimestamp. UpdateTimestamp akan merubah value pada column ketika sebuah row nya mengalami perubahan

### #3 Pembuatan Repository

Kabar baik dengan menggunakan JPA adalah kita tidak perlu repot membuat sebuah fungsi CRUD yang standar. Karena fungsi CRUD telah disediakan oleh nya. Jika hanya perlu meng ‘extends’ class JpaRepository agar bisa menggunakan nya. Berikut adalah contoh nya

    package com.antoniosai.crud.modules.book;

    import org.springframework.data.jpa.repository.JpaRepository;
    import org.springframework.stereotype.Repository;

    @Repository
    public interface BookRepository extends JpaRepository<BookEntity, Long> {
    }

### #4 Pembuatan Service

Di layer kali ini adalah merupakan sebuah Class yang akan memproses sebuah Data akan diolah sedemikian rupa tergantung kebutuhan dan juga class Service ini adalah yang akan diakses langsung oleh controller. Di class Service ini kita akan membuat sebuah method yang berkaitan dengan CRUD dasar seperti **_getBooks(), getBookById(Long id), updateBook(BookEntity book, Long id), saveBook(BookEntity book), destroyBook(Long id) _**DataBerikut source code service nya

    package com.antoniosai.crud.modules.book;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;

    import java.util.List;

    @Service
    public class BookService {
        @Autowired
        private BookRepository bookRepository;

        public List<BookEntity> getBooks() {
            return this.bookRepository.findAll();
        }

        public BookEntity getBookById(Long id) {
            return this.bookRepository.findById(id).orElse(null);
        }

        public BookEntity saveBook(BookEntity book) {
            return this.bookRepository.saveAndFlush(book);
        }

        public BookEntity updateBook(BookEntity book, Long id) {
            BookEntity bookToBeUpdated = this.bookRepository.findById(id).orElse(null);

            if (bookToBeUpdated != null) {
                book.setId(id);

                bookRepository.save(book);
            }
            return bookToBeUpdated;
        }

        public String destroyBook(Long id) {
            this.bookRepository.deleteById(id);

            return "Successfully deleted a Book";
        }
    }

### #5 Pembuatan Controller

Akhir nya, kita telah sampai pada langkah terakhir. Yaitu pembuatan Controller. Pada langkah kita akan membuat sebuah endpoint yang akan diconsume menggunakan HTTP Client

    package com.antoniosai.crud.modules.book;

    import jakarta.validation.Valid;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.http.HttpStatus;
    import org.springframework.http.ResponseEntity;
    import org.springframework.validation.annotation.Validated;
    import org.springframework.web.bind.annotation.*;

    import java.util.List;

    @RestController
    @Validated
    @RequestMapping("/book")
    public class BookController {

        @Autowired
        private BookService bookService;

        @GetMapping(produces = "application/json")
        public List<BookEntity> getBooks() {
            return this.bookService.getBooks();
        }

        @ResponseStatus(HttpStatus.OK)
        @GetMapping("/{bookId}")
        public ResponseEntity<BookEntity> getStudentById(@PathVariable("bookId") @Valid Long bookId) {
            BookEntity book = bookService.getBookById(bookId);

            return new ResponseEntity<>(book, HttpStatus.OK);
        }

        @ResponseStatus(HttpStatus.CREATED)
        @PostMapping(produces = "application/json")
        public ResponseEntity<BookEntity> saveNewBook(@Valid @RequestBody BookEntity book) {
            System.out.println("Book => " + book);
            BookEntity newBook = this.bookService.saveBook(book);

            return new ResponseEntity<>(newBook, HttpStatus.CREATED);
        }

        @ResponseStatus(HttpStatus.OK)
        @PutMapping("/{bookId}")
        public ResponseEntity<BookEntity> getStudentById(@PathVariable("bookId") @Valid Long bookId, @Valid @RequestBody BookEntity book) {
            BookEntity updatedBook = bookService.updateBook(book, bookId);

            return new ResponseEntity<>(book, HttpStatus.OK);
        }

        @ResponseStatus(HttpStatus.OK)
        @DeleteMapping("/{bookId}")
        public ResponseEntity<String> dropBook(@PathVariable("bookId") @Valid Long bookId) {
            String deleteMessage = bookService.destroyBook(bookId);

            return new ResponseEntity<>(deleteMessage, HttpStatus.OK);
        }
    }

### #6 Test menggunakan HTTP Client / Postman

Setelah semua langkah kita lakukan. Maka saat nya untuk mencoba aplikasi kita menggunakan Postman.

![Hit POST /book untuk Store new Book](https://cdn-images-1.medium.com/max/2000/1*Tqig3XP5_MIUqn6sC2WSPg.png)_Hit POST /book untuk Store new Book_

![Hit GET /book untuk Show All Books](https://cdn-images-1.medium.com/max/2000/1*babQFN20bTgQDWibfKOe8Q.png)_Hit GET /book untuk Show All Books_

![Hit GET /book/{bookId} untuk menampilkan buku berdasarkan ID](https://cdn-images-1.medium.com/max/2000/1*4ObbKmbG7SyojZvn1-mzTQ.png)_Hit GET /book/{bookId} untuk menampilkan buku berdasarkan ID_

![Hit PUT /book/{bookId} untuk mengupdate data buku](https://cdn-images-1.medium.com/max/2000/1*7ZDwAttRyP-DAzuZk0V5tg.png)_Hit PUT /book/{bookId} untuk mengupdate data buku_

![Hit DELETE /book/{bookId} untuk menghapus data buku](https://cdn-images-1.medium.com/max/2000/1*g_hdU97OmVEP37qb82wKTg.png)_Hit DELETE /book/{bookId} untuk menghapus data buku_

## Penutup

Demikian artikel kali ini. Harapan saya adalah pembaca yang baru memulai belajar framework Java Spring Boot sedikit nya terbantu. Saya sadar masih banyak kekurangan yang baik disisi penulisan, penyampaian, tata bahasa maupun penerapan source seperti belum menggunakan validation, exception, DTO dan lain nya yang mudah-mudahan saya akan membahas di lain artikel.

Source Code pada artikel ini teman-teman bisa mengakses nya di Repo [\*\*https://github.com/antoniosai/simple-crud-app-spring-boot](https://github.com/antoniosai/simple-crud-app-spring-boot)\*\*

Terdapat lanjutan nya yang akan membahas Containerizing Projek dengan Spring Boot, teman-teman bisa mengakses nya disini : [\*\*https://medium.com/@antoniosae/containerizing-projek-spring-boot-dengan-docker-100ed7482ebe](https://medium.com/@antoniosae/containerizing-projek-spring-boot-dengan-docker-100ed7482ebe)\*\*

Sekian, saya ucapkan terimakasih dan semangat belajr!
