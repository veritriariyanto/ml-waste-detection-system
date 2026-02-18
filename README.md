
# Dokumentasi Lengkap: Sistem Deteksi Sampah Otomatis Berbasis Machine Learning & IoT

## 1. Deskripsi Umum

Sistem ini adalah solusi deteksi dan klasifikasi sampah otomatis berbasis machine learning (ML) dan Internet of Things (IoT). Sistem terdiri dari:
- **Aplikasi deteksi berbasis Python (Streamlit)**
- **Model ML TensorFlow Lite**
- **ESP32-CAM sebagai kamera IP**
- **ESP32 + Servo sebagai aktuator pemilah**
- **Integrasi MQTT (Ubidots) untuk komunikasi cloud**

Sistem mampu mendeteksi jenis sampah (organik/anorganik/tidak terdeteksi) secara real-time dari kamera, lalu mengirimkan perintah ke perangkat IoT untuk memilah sampah secara otomatis.

---

## 2. Arsitektur Sistem

### a. Alur Kerja

1. **ESP32-CAM** menangkap gambar sampah dan menyediakan endpoint HTTP (`/capture`).
2. **Aplikasi Python (Streamlit)** mengambil gambar dari ESP32-CAM, melakukan prediksi menggunakan model TFLite, dan menampilkan hasil deteksi.
3. Jika terdeteksi organik/anorganik, aplikasi mengirimkan perintah via MQTT ke **ESP32 Servo** melalui Ubidots.
4. **ESP32 Servo** menerima perintah dan menggerakkan servo untuk memilah sampah sesuai jenisnya.

### b. Diagram Alur

```
[ESP32-CAM] --(HTTP)--> [Aplikasi Streamlit + ML] --(MQTT/Ubidots)--> [ESP32 Servo + Aktuator]
```

---

## 3. Penjelasan Tiap Komponen

### a. Aplikasi Python (Streamlit)

- File utama: `app.py`
- Mengambil gambar dari ESP32-CAM (IP Camera)
- Melakukan inferensi ML dengan model TensorFlow Lite (`model.tflite`)
- Menampilkan hasil deteksi dan confidence
- Mengirim perintah ke ESP32 Servo via MQTT (Ubidots)
- Terdapat juga `test.py` untuk pengujian manual dengan upload gambar/kamera laptop

#### Dependensi:
- streamlit
- opencv-python
- numpy
- tensorflow
- requests
- paho-mqtt

#### Cara Menjalankan:
1. Install dependensi:  
	```
	pip install -r requirements.txt
	```
2. Jalankan aplikasi:  
	```
	streamlit run app.py
	```
3. Pastikan ESP32-CAM dan ESP32 Servo sudah terhubung ke jaringan yang sama.

### b. Model Machine Learning

- File: `model.tflite` (model terkuantisasi, siap untuk edge device)
- Label: `labels.txt` (organik, anorganik, tidakterdeteksi)
- Model menerima input gambar, mengklasifikasikan ke salah satu dari 3 kelas.

### c. ESP32-CAM

- Kode: `CameraWebServer/CameraWebServer.ino`
- Menginisialisasi kamera, menghubungkan ke WiFi, dan menyediakan endpoint HTTP untuk mengambil gambar.
- Konfigurasi WiFi dapat diubah pada variabel `ssid` dan `password`.

### d. ESP32 Servo

- Kode: `esp32servo/esp32servo.ino`
- Menghubungkan ke WiFi dan broker MQTT (Ubidots)
- Menerima perintah dari aplikasi (via MQTT) untuk menggerakkan dua buah servo (kanan/kiri) sesuai jenis sampah.
- Servo bergerak ke posisi tertentu untuk memilah organik/anorganik.

---

## 4. Integrasi Cloud (Ubidots MQTT)

- Digunakan untuk komunikasi antara aplikasi Python dan ESP32 Servo.
- Token, device label, dan variabel dapat diatur pada kode.
- Topik MQTT: `/v1.6/devices/esp32-servo`
- Payload:  
  - `{"kontrol": 0}` untuk organik  
  - `{"kontrol": 1}` untuk anorganik

---

## 5. Petunjuk Penggunaan

### a. Persiapan Hardware

- Siapkan ESP32-CAM dan ESP32 (untuk servo) dengan koneksi WiFi yang sama dengan komputer.
- Upload kode ke masing-masing board menggunakan Arduino IDE.
- Pastikan servo terpasang pada pin yang sesuai (lihat kode `esp32servo.ino`).

### b. Menjalankan Sistem

1. Jalankan ESP32-CAM, pastikan dapat diakses via browser (cek IP di serial monitor).
2. Jalankan ESP32 Servo, pastikan terkoneksi ke WiFi dan Ubidots (cek serial monitor).
3. Jalankan aplikasi Streamlit di komputer.
4. Sistem akan otomatis mengambil gambar dari ESP32-CAM, melakukan deteksi, dan mengirim perintah ke ESP32 Servo jika terdeteksi sampah.

### c. Pengujian Manual

- Gunakan `test.py` untuk menguji model dengan upload gambar dari file atau kamera laptop.

---

## 6. File & Struktur Proyek

```
ml-waste-detection-system/
│
├── app.py                # Aplikasi utama Streamlit
├── test.py               # Aplikasi pengujian manual
├── model.tflite          # Model ML TensorFlow Lite
├── labels.txt            # Label kelas model
├── requirements.txt      # Daftar dependensi Python
│
├── CameraWebServer/
│   └── CameraWebServer.ino   # Kode ESP32-CAM
│
├── esp32servo/
│   └── esp32servo.ino        # Kode ESP32 + Servo
│
└── README.md             # Dokumentasi proyek
```

---

## 7. Catatan & Tips

- Pastikan IP ESP32-CAM di `app.py` sesuai dengan IP perangkat Anda.
- Gunakan background hitam/putih saat mengambil gambar agar deteksi lebih akurat.
- Model dapat diupdate dengan melatih ulang dan mengganti file `model.tflite` serta `labels.txt`.
- Untuk lomba, siapkan video demo dan dokumentasi deployment.

---

## 8. Kontak & Kontributor

- Nama Tim: [Isi Nama Tim Anda]
- Anggota: [Isi Nama Anggota]
- Email: [Isi Email Kontak]

---

Dokumentasi ini sudah siap untuk keperluan lomba SIC6. Jika ingin menambahkan gambar, diagram, atau video, silakan lampirkan pada bagian terpisah.
