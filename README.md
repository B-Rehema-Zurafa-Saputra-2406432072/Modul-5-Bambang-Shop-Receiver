# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
Penggunaan RwLock diperlukan karena framework web Rocket berjalan secara multi-threaded. Jika hanya menggunakan Vec biasa, beberapa thread bisa saja mencoba menambah notifikasi ke dalam Vec pada saat yang sama, menyebabkan Data Race dan membuat program crash. Pemilihan RwLock (Read-Write Lock) dibandingkan Mutex (Mutual Exclusion) karena alasan optimisasi performa yang spesifik pada kasus penggunaan. Mutex hanya mengizinkan satu therad untuk mengakses data pada satu waktu tanpa melihat apakah thread ingin read atau write. Sementara, RwLock mengizinkan thread untuk membaca data secara bersamaan, jika tidak ada yang menulis. Namun, jika ada satu thread yang ingin menulis data, RwLock akan mengunci akses untuk semua thread lain. Dalam aplikasi notifikasi ini, orang yang melihat daftar notifikasi biasanya jauh lebih banyak dibandingkan notifikasi baru. Menggunakan RwLock membuat proses membaca read berjalan cepat, tanpa antrian seperti menggunakan Mutex.

Perbedaan static antara Java dan Rust terjadi karena filosofi bahasa pemrograman tersebut yang berbeda. Java mengizinkan membuat variabel static yang mutable dan mengubahnya dari fungsi mana saja. Java memercayakan sepenuhnya kepada developer untuk mengatur synchronisasi. Jadi walaupun tidak melakukannya, Java masih akan melakukan kompilasi. Sementara Rust, ingin membuat aturan bahwa kalau bisa dikompilasi, maka tidak ada data race. Memiliki variabel global yang bisa diubah oleh banyak thread merupakan hal yang tidak aman untuk data race, sehingga rust tidak memperbolehkan static variable yang mutable. Selain itu, variable static harus diketahui pasti ukuran dan nilai awalnya. lazy_static! memecahkan kedua masalah diatas dengan menunda (lazy) pembuatan Vec atau DashMap tersebut sampai detik pertama variabel tersebut dipanggil saat aplikasi berjalan (runtime), dan secara otomatis membungkusnya dengan pengaman thread-safe sehingga compiler Rust memperbolehkannya.

#### Reflection Subscriber-2
Mengeksplorisasi src/lib.rs penting karena memiliki beberapa komponen penting. Terdapat komponen config seperti APP_CONFIG, REQWEST_CLIENT, serta abstraksi error_response. lib.rs dapat digunakan sebagai tempat sentral konfigurasi dan tempat penyimpanan logika dasar yang dapat digunakan berulang kali.

Observer pattern memudahkan penambahan subsriber karena sistem yang didesign bersifat loosely coupled. Saat menjalankan beberapa receiver sekaligus, tidak perlu ada perubahan baris kode di main app. Jadi tinggal memanggil endpoint /subscribe, dan sistem akan langsung bekerja. Untuk penambahan Main App lebih menantang. Karena status subscriber sekarang disimpan di DashMap main app. Jika seseorang subscriber terdaftar di Main App 1 tidak akan mendapatkan notifikasi baru jika produk ditambah di Main APp 2. Untuk memudahkan penambahan Main app. diperlukan shared database untuk semua instance Main App.

Saya sendiri belum pernah membuat Unit Test atau bermain dengan documentation Postman, namun saya menyadari bagaimana kedua hal tersebut merupakan hal yang penting. Unit Tests berfungsi untuk mengautomasikan testing API seperti menulis tes untuk fungsi format pada Notification untuk memastikan bahwa pesan yang sampai ke user tidaak berantakan. Selain itu, dokumentasi yang rapi dapat membantu dalam group project agar tidak perlu menebak tipe data apa yang harus dikurangi. Ini akan membuat pengembangan Backend dan Frontend lebih mulus. 
