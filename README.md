# Membangun Layanan Web yang Tenang
## Apa yang Akan Anda Bangun
Anda akan membangun layanan yang akan menerima permintaan HTTP GET di `http://localhost:8011/greeting`.
Ini akan merespons dengan representasi JSON dari salam, seperti yang ditunjukkan oleh daftar berikut:
```html
{"id":1,"content":"Hello, World!"}
```
Anda dapat menyesuaikan salam dengan parameter opsional `name` dalam string kueri, seperti yang ditunjukkan oleh daftar berikut:
```html
http://localhost:8011/greeting?name=User
```
Nilai `name` parameter menggantikan nilai default `World` dan tercermin dalam respons, seperti yang ditunjukkan oleh daftar berikut:
```html
{"id":1,"content":"Hello, User!"}
```

## Apa yang di butuhkan
* Sekitar 15 menit
* Editor teks atau IDE favorit
* JDK 1.8 atau lebih baru
* Gradle 4+ atau Maven 3.2+
* Anda juga dapat mengimpor kode langsung ke IDE Anda:
    * Spring Tool Suite (STS)
    * IntelliJ IDEA

## Langkah-langkah
#### Langkah 1: Dimulai dengan Spring Initializer
1. Arahkan ke [start.spring.io](https://start.spring.io). Layanan ini menarik semua dependensi yang Anda perlukan untuk aplikasi dan melakukan sebagian besar penyiapan untuk Anda.
2. Pilih Gradle atau Maven dan bahasa yang ingin Anda gunakan. Panduan ini mengasumsikan bahwa Anda memilih Java.
3. Klik **Dependencies** dan pilih **Spring Web**.
4. Klik **Generate**.
5. Unduh file ZIP yang dihasilkan, yang merupakan arsip aplikasi web yang dikonfigurasi dengan pilihan Anda.

![gambar](https://drive.google.com/uc?export=view&id=13r834HjjiULtBs1Ep4hnqkGsWGRagBar)

#### Langkah 2: Buat Kelas Representasi Sumber Daya
Layanan akan menangani `GET` permintaan untuk `/greeting`, secara opsional dengan `name` parameter dalam string kueri. Permintaan `GET` harus mengembalikan `200 OK` respons dengan JSON di badan yang mewakili greeting. Itu harus menyerupai output berikut:
```html
{
    "id": 1,
    "content": "Hello, World!"
}
```
Bidang `id` adalah pengidentifikasi unik untuk greeting, dan contentmerupakan representasi tekstual dari greeting.

Untuk memodelkan representasi ucapan, buat kelas representasi sumber daya. Untuk melakukannya, sediakan objek Java lama biasa dengan bidang, konstruktor, dan pengakses untuk data `id` dan `content`, seperti yang ditunjukkan oleh daftar berikut (dari `src/main/java/com/afifpermana/restservice/Greeting.java`):

```java
package com.afifpermana.restservice;

public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
```

#### Langkah 3: Buat Pengontrol Sumber Daya
Dalam pendekatan Spring untuk membangun layanan web RESTful, permintaan HTTP ditangani oleh pengontrol. Komponen-komponen ini diidentifikasi oleh `@RestControlleranotasi`, dan yang `GreetingController` ditampilkan dalam daftar berikut (dari `src/main/java/com/afifpermana/restservice/GreetingController.java`) menangani `GET` permintaan `/greeting` dengan mengembalikan instance baru dari class `Greeting`:

```java
package com.afifpermana.restservice;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @GetMapping("/greeting")
    public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
        return new Greeting(counter.incrementAndGet(), String.format(template, name));
    }
}
```
#### Langkah 4: Cara menukar port
Pada projek kita, pergi ke **Other Sources** >> **src/main/resources** >> **default package** >> **application.properties**. Lalu buat `server.port= *nomorPort*` pada application.packages agar tidak terjadi bentrok antar port.

![gambar](https://drive.google.com/uc?export=view&id=16uZYRsRXe9Aa8nPRJ3tOiODiGsOA7ntf)

#### Langkah 5: Uji Layanan
Sekarang setelah layanan aktif, kunjungi `http://localhost:8011/greeting`, di mana Anda akan melihat:

![gambar](https://drive.google.com/uc?export=view&id=1pBEXzxWgbqNJAID0GIlpT2FmUF9wgdit)

Berikan `name` parameter string kueri dengan mengunjungi `http://localhost:8011/greeting?name=User`. Perhatikan bagaimana nilai `content` atribut berubah dari `Hello, World!` ke `Hello, User!`, seperti yang ditunjukkan daftar berikut:

![gambar](https://drive.google.com/uc?export=view&id=1mdAfljgwSjrlHbz6mgBo8WerhagNMols)

Perubahan ini menunjukkan bahwa `@RequestParampengaturan` dalam `GreetingController` bekerja seperti yang diharapkan. Parameter `name` telah diberi nilai default `World` tetapi dapat diganti secara eksplisit melalui string kueri.

Perhatikan juga bagaimana `id` atribut telah berubah dari `1` menjadi `2`. Ini membuktikan bahwa Anda bekerja melawan `GreetingControllerinstance` yang sama di beberapa permintaan dan `counter` bidangnya bertambah pada setiap panggilan seperti yang diharapkan.
