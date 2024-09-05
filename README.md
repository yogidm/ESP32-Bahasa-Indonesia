# Belajar ESP32

Repositori ini merupakan langkah untuk mendokumentasikan kegiatan praktikum embedded system menggunakan ESP32. Untuk semua kegiatan yang akan dilakukan, silakan request jika ingin menambah atau mengubah konten. Sharing is caring... :D

## Daftar Isi

1. [Pinout ESP32](#1-pinout-esp32)
2. [Penggunaan Pertama ESP32](#2-penggunaan-pertama-esp32)
   * [Download Arduino IDE Legacy](#21-download-arduino-ide-legacy)
   * [Tes Upload Kode Pertama](#22-tes-upload-kode-pertama)
   * [Tes Upload SPIFFS](#23-tes-upload-spiffs)
3. [Membuat Access Point WiFi](#3-membuat-access-point-wifi)
   * [Setup ESP32 sebagai WiFi AP](#31-setup-esp32-sebagai-wifi-ap)
   * [Setup Static IP sesuai kelompok](#32-setup-static-ip-sesuai-kelompok)
4. [Membuat Webserver sebagai akses GUI berbasis web](#4-membuat-webserver-sebagai-akses-gui-berbasis-web)
   * [Setup WebServer](#41-setup-webserver)
   * [Tes GUI untuk mengontrol LED](#42-tes-gui-untuk-mengontrol-led)
   * [Ujicoba handleRoot()](#43-ujicoba-handleroot)

---

## 1. Pinout ESP32

Bagian ini menjelaskan pinout dari ESP32. ESP32 memiliki beberapa pin GPIO, power, serta pin khusus seperti ADC, DAC, dan lainnya. Pastikan memahami fungsi setiap pin sebelum menggunakannya.

### Referensi Pinout ESP32:
- GPIO: General Purpose Input/Output
- ADC: Analog to Digital Converter (Pin GPIO 34-39)
- DAC: Digital to Analog nverter

![pinOUT](https://content.instructables.com/FOL/YWLI/JEOILQ5U/FOLYWLIJEOILQ5U.png?auto=webp&frame=1&width=1024&fit=bounds&md=a74a05c18f4b4d9f0db00ec63c7bc3c0)

---

## 2. Penggunaan Pertama ESP32

### 2.1 Download Arduino IDE Legacy

Langkah pertama adalah mengunduh Arduino IDE versi terbaru atau versi Legacy yang kompatibel dengan ESP32. Download Arduino IDE [di sini](https://www.arduino.cc/en/software).

### 2.2 Tes Upload Kode Pertama

Dalam bagian ini, kita akan mencoba mengunggah kode pertama ke ESP32. Kode sederhana ini akan menyalakan dan mematikan LED internal.

```cpp
// Mengakses LED internal (biasanya GPIO 2)
int ledPin = 2;

void setup() {
  pinMode(ledPin, OUTPUT);
}

void loop() {
  digitalWrite(ledPin, HIGH);  // Nyalakan LED
  delay(1000);                 // Tunggu 1 detik
  digitalWrite(ledPin, LOW);   // Matikan LED
  delay(1000);                 // Tunggu 1 detik
}

```

## 2.3 Tes Upload SPIFFS

SPIFFS (SPI Flash File System) memungkinkan Anda menyimpan file di ESP32 dan mengaksesnya dari kode Arduino.

```cpp
#include "SPIFFS.h"

void setup() {
  Serial.begin(115200);
  if (!SPIFFS.begin(true)) {
    Serial.println("Gagal memulai SPIFFS");
    return;
  }
  
  File file = SPIFFS.open("/test.txt", FILE_WRITE);
  if (!file) {
    Serial.println("Gagal membuka file untuk menulis");
    return;
  }
  file.println("Ini adalah tes SPIFFS");
  file.close();
  
  file = SPIFFS.open("/test.txt", FILE_READ);
  if (file) {
    Serial.println("Isi file:");
    while (file.available()) {
      Serial.write(file.read());
    }
    file.close();
  }
}

```

Kode ini menggunakan SPIFFS (SPI Flash File System) pada ESP32 untuk menyimpan dan membaca file dari memori flash ESP32. SPIFFS memungkinkan Anda menyimpan file, seperti teks atau konfigurasi, langsung di dalam ESP32, mirip dengan cara kerja sistem file di komputer. Berikut penjelasan secara sederhana:

Inisialisasi SPIFFS:
Di dalam fungsi setup(), komunikasi serial dimulai dengan kecepatan 115200 baud untuk mengirimkan data ke komputer melalui USB. Setelah itu, sistem file SPIFFS diaktifkan menggunakan SPIFFS.begin(true). Jika SPIFFS gagal dimulai, akan muncul pesan "Gagal memulai SPIFFS" di serial monitor, dan program berhenti menjalankan sisa kode. SPIFFS bekerja seperti hard drive kecil di ESP32 yang digunakan untuk menyimpan file, dan langkah ini adalah inisialisasi agar sistem file tersebut bisa digunakan.

Menulis dan Membaca File:
Program kemudian mencoba membuat atau membuka file teks bernama /test.txt dengan mode penulisan (FILE_WRITE). Jika gagal, ESP32 menampilkan pesan "Gagal membuka file untuk menulis". Jika berhasil, string "Ini adalah tes SPIFFS" dituliskan ke dalam file tersebut, lalu file ditutup dengan file.close() agar data tersimpan. Setelah itu, program membuka kembali file yang sama dalam mode baca (FILE_READ). Jika file berhasil dibuka, program membaca isi file dan menampilkannya satu karakter demi satu karakter ke serial monitor. Jika file tersedia (masih ada data yang belum dibaca), data akan terus dibaca dan ditampilkan sampai seluruh isi file selesai dibaca, lalu file ditutup.



## 2.3.1 Tutorial Mengunggah File JPG dan HTML ke SPIFFS pada ESP32

Tutorial ini menjelaskan cara mengunggah file **JPG** dan **HTML** ke **SPIFFS** menggunakan Arduino IDE dengan plugin **ESP32 Sketch Data Upload**. Dengan SPIFFS, Anda dapat menyimpan file dan mengaksesnya dari program di ESP32.

### Langkah 1: Instalasi Plugin **ESP32 Sketch Data Upload**

1. **Download Plugin:**
   - Unduh plugin **ESP32 Sketch Data Upload** dari [repositori GitHub ini](https://github.com/me-no-dev/arduino-esp32fs-plugin).
   - Pilih versi yang sesuai dengan sistem operasi Anda (Windows, Mac, atau Linux).

2. **Ekstrak dan Install Plugin:**
   - Ekstrak file ZIP.
   - Copy folder hasil ekstrak ke dalam direktori Arduino IDE:
     - **Windows**: `C:\Users\<NamaUser>\Documents\Arduino\tools\ESP32FS\tool\ESP32FS.jar`
     - **Mac/Linux**: Letakkan folder di dalam folder Arduino (di lokasi yang sama seperti pada Windows).

3. **Restart Arduino IDE:**
   - Setelah plugin ditempatkan di folder yang benar, tutup Arduino IDE dan buka kembali.

### Langkah 2: Membuat Folder Data dan Menambahkan File JPG dan HTML

1. **Membuat Folder `data`:**
   - Buka Arduino IDE.
   - Buat sketch baru atau buka sketch yang sudah ada.
   - Di dalam folder proyek sketch Anda (biasanya di `C:\Users\<NamaUser>\Documents\Arduino\<NamaSketch>`), buat folder baru bernama `data`.

2. **Menambahkan File JPG dan HTML:**
   - Tempatkan file-file yang ingin diunggah (misalnya, `image.jpg` dan `index.html`) ke dalam folder `data`.

3. **Contoh Isi File `index.html`:**
   - Buat file HTML sederhana bernama `index.html` dengan isi berikut:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
     <title>ESP32 SPIFFS Example</title>
   </head>
   <body>
     <h1>Welcome to ESP32 Web Server</h1>
     <img src="/image.jpg" alt="Test Image">
   </body>
   </html>
   ```
   - Pastikan juga file gambar `image.jpg` ada di folder data.


### Langkah 3: Mengunggah File ke SPIFFS

### Mengatur Board ESP32:
1. Buka Arduino IDE.
2. Pilih board ESP32 dengan masuk ke **Tools** > **Board** > **ESP32 Dev Module** (atau board ESP32 lain yang Anda gunakan).

### Upload File ke SPIFFS:
1. Pastikan **Upload Speed** diatur ke `115200` dan port ESP32 sudah dipilih.
2. Klik **Tools** dan Anda akan melihat opsi baru bernama **ESP32 Sketch Data Upload**.
3. Pilih opsi ini dan tunggu hingga proses pengunggahan selesai. Semua file di dalam folder `data` akan diunggah ke SPIFFS ESP32.

### Langkah 4: Akses File dari SPIFFS di dalam Kode Arduino

Setelah file diunggah, Anda bisa mengaksesnya dari program ESP32. Berikut adalah contoh kode yang menggunakan SPIFFS untuk menyajikan file HTML dan JPG melalui server web:


```cpp
#include "SPIFFS.h"
#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "Kelompok_1";
const char* password = "12345678";

WebServer server(80);

void handleRoot() {
  File file = SPIFFS.open("/index.html", "r");
  if (!file) {
    server.send(404, "text/plain", "File not found");
    return;
  }
  server.streamFile(file, "text/html");
  file.close();
}

void handleImage() {
  File file = SPIFFS.open("/image.jpg", "r");
  if (!file) {
    server.send(404, "text/plain", "File not found");
    return;
  }
  server.streamFile(file, "image/jpeg");
  file.close();
}

void setup() {
  Serial.begin(115200);

  // Start SPIFFS
  if (!SPIFFS.begin(true)) {
    Serial.println("An error has occurred while mounting SPIFFS");
    return;
  }
  
  // Start WiFi
  WiFi.softAP(ssid, password);
  Serial.println("Access Point Started");

  // Configure routes
  server.on("/", handleRoot);
  server.on("/image.jpg", handleImage);

  server.begin();
  Serial.println("HTTP server started");
}

void loop() {
  server.handleClient();
}

```


- **SPIFFS.begin(true)**: Inisialisasi SPIFFS. Ini mempersiapkan sistem file SPIFFS agar bisa digunakan untuk menyimpan dan membaca file.

- **server.on("/", handleRoot)**: Saat seseorang mengakses halaman utama (`/`), file `index.html` akan dikirim ke browser. Fungsi `handleRoot` bertanggung jawab untuk mengirimkan file HTML ini.

- **server.on("/image.jpg", handleImage)**: File gambar `image.jpg` akan disajikan saat diakses melalui URL `/image.jpg`. Fungsi `handleImage` bertanggung jawab untuk mengirimkan file gambar ini.

### Langkah 5: Uji Koneksi

1. Setelah program diunggah ke ESP32, sambungkan perangkat (misalnya, laptop atau smartphone) ke WiFi dengan nama **Kelompok_1**.

2. Buka browser dan masukkan alamat IP default ESP32, biasanya `192.168.4.1`.

3. Halaman web yang di-host oleh ESP32 akan menampilkan pesan "Welcome to ESP32 Web Server" dan gambar yang diunggah.



## Penggunaan Pertama ESP32

Kita akan melakukan pemrograman ESP32 menggunakan Arduino IDE versi 1.8.x daripada Arduino IDE versi 2.3.x atau yang terbaru. Ada beberapa fitur dari Arduino versi lama yang belum ada pada versi terbaru. Jadi silahkan [download Arduino IDE 1.8.x](https://www.arduino.cc/en/software) pada link berikut ya... 

![Legacy Arduino IDE](https://github.com/yogidm/ESP32-Bahasa-Indonesia/blob/main/images/download_ArduinoIDE.jpg)

