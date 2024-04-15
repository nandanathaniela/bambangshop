# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. ***In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?***

Observer umumnya menggunakan interface agar bisa menerapkan berbagai cara pembaruan oleh Observer yang berbeda. Jika di Rust, ini seringkali dilakukan dengan trait untuk mengizinkan berbagai struktur mengimplementasikan Observer. Namun, untuk BambangShop, jika semua subscriber itu sama dan tidak memerlukan variasi, satu struktur Model saja mungkin sudah memadai. Pada dasarnya, apakah menggunakan trait atau hanya Model bergantung pada apa yang dibutuhkan oleh aplikasi kita.

2. ***Did in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?*** 

Pakai DashMap itu lebih pas daripada Vec untuk cari Subscriber dengan URL unik karena dengan DashMap, pencarian jauh lebih cepat, hanya butuh waktu konstan, tidak peduli banyaknya data. DashMap juga dibuat untuk diakses oleh banyak thread sekaligus tanpa khawatir akan terjadi tabrakan data.

3. ***When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread-safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?***

Singleton Pattern memastikan cuma ada satu instance dari sebuah kelas dan semua bisa akses dari satu titik global, jadi kita yakin hanya ada satu variabel SUBSCRIBERS di seluruh aplikasi. Sementara itu, DashMap adalah jenis Hashmap yang bisa diakses oleh banyak thread sekaligus dengan aman, tanpa risiko benturan data atau masalah lain seputar concurrency. Kalau kita cuma pakai Hashmap standar dengan pola Singleton, aplikasi yang menggunakan banyak thread bakal rentan terhadap masalah benturan data. Jadi, untuk aplikasi BambangShop yang menggunakan banyak thread, DashMap adalah pilihan yang lebih tepat.

#### Reflection Publisher-2
1. ***In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?***

Dalam pola MVC, seringkali Model yang menangani terlalu banyak responsibility. Oleh karena itu, dengan memisahkan Service dan Repository dari Model, kita bisa atasi berbagai masalah dan hasilkan kode yang lebih rapi dan mudah di maintenance. Pemisahan ini sesuai dengan Prinsip Single Responsibility Principle (SRP), bikin sistem lebih terpisah dan fleksibel, dan memperbaiki kemudahan dalam pengujian serta penggunaan kembali kode.

2. ***What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?***

Kalau kita cuma gunakan Model, tiap model jadi rumit dan sulit diurus karena tiap-tiap model harus tangani akses data, business logic, dan hubungan dengan model lain sendirian. Model-model ini jadi terlalu bergantung satu sama lain, sehingga perubahan di satu model bisa pengaruhi yang lain, yang bisa buat bug muncul lebih gampang. Dan ini bertentangan dengan Prinsip SRP karena satu model harus tangani banyak tugas sekaligus.

3. ***Have you explored more about Postman? Tell us how this tool helps you to test your current work. Maybe you want to also list which features in Postman you are interested in or feel like it’s helpful to help your Group Project or any of your future software engineering projects.***

Setelah mengeksplorasi, Postman terbukti berguna untuk tes API pada aplikasi yang kita buat, memastikan semua fungsi kerja sesuai harapan. Kita bisa atur metode HTTP buat kirim permintaan ke endpoint API dan cek respons yang diterima udah pas atau belum. Nanti, rencananya Postman bakal dipakai buat tes API, baik buat proyek kelompok maupun proyek rekayasa perangkat lunak yang lain.

#### Reflection Publisher-3
1. ***Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?***

Dari model `subscriber.rs` dan log yang muncul, Observer yang dipakai di tutorial ini adalah Push Model. Ini terlihat dari notifikasi yang langsung dikirim ke subscriber setelah mereka berlangganan, yang menandakan bahwa penerbit secara aktif mengirimkan kabar tentang produk yang baru dibuat, dihapus, atau dipublikasikan. `NotificationService` melakukan ini dengan memanggil metode notifikasi yang melalui semua subscribernya untuk memberi tahu mereka. Dan dari metode pembaruan di Model Subscribe, kita bisa lihat kalau metode ini, ketika dipanggil, akan mengirim payload ke URL dari subscriber tersebut.

2. ***What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)***

Kalau kita pilih model pull, subscribernyaa yang atur kapan mereka mau ambil info dari Publisher. Ini bagus karena subscriber ambil data cuma pas butuh aja, jadi sumber daya nggak terbuang sia-sia. Tapi, kekurangan model pull itu subscriber harus rajin minta update sendiri, yang bisa bikin info terbaru telat sampe kalau subscriber nggak minta-minta data sering-sering atau lagi nggak aktif. Terus, buat ngatur model pull itu sendiri juga lebih ribet dibandingkan model push, karena pelanggan kudu bikin cara sendiri buat minta data dari Publisher dan ngatur semua permintaan yang dikirim.

3. ***Explain what will happen to the program if we decide to not use multi-threading in the notification process.***

Kalau kita nggak pakai multi-threading, notifikasi bakal dikirim satu per satu ke subscriber, yang bisa bikin notifikasi jadi lama banget sampe ke semua subscriber. Ini bisa jadi masalah kalau ada banyak subscriber yang harus diberi tahu, karena notifikasi bakal numpuk dan jadi lama. Kalau kita pakai multi-threading, notifikasi bisa dikirim ke semua subscriber sekaligus, jadi notifikasi bakal lebih cepat dan nggak numpuk. Ini juga bisa bikin aplikasi jadi lebih responsif dan nggak terlalu terganggu sama notifikasi yang lama.