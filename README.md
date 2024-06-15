# agunghidayat
# Arduino Ethernet Temperature Monitoring

Proyek ini adalah aplikasi sederhana yang menggunakan Arduino dan sensor suhu LM35 untuk memonitor suhu dan mengirim data melalui server Ethernet. Data suhu, bersama dengan beberapa informasi pribadi, ditampilkan di Serial Monitor dan dapat diakses melalui server web yang berjalan di Arduino.

## Perangkat Keras yang Dibutuhkan

1. **Arduino UNO** (atau model lain yang kompatibel)
2. **Ethernet Shield** (seperti W5100)
3. **Sensor Suhu LM35**
4. **Kabel Jumper**
5. **Komputer** dengan **Arduino IDE**
6. **Proteus** (untuk simulasi, jika diperlukan)

## Perangkat Lunak yang Dibutuhkan

1. **Arduino IDE**
2. **UIPEthernet Library** - [Unduh di sini](https://github.com/UIPEthernet/UIPEthernet)

## Instalasi

1. **Pasang UIPEthernet Library**:
   - Unduh dan pasang library UIPEthernet dari [UIPEthernet GitHub](https://github.com/UIPEthernet/UIPEthernet).
   - Ekstrak dan pindahkan folder UIPEthernet ke dalam folder `libraries` di direktori Arduino Anda.

2. **Unggah Kode ke Arduino**:
   - Buka Arduino IDE.
   - Salin dan tempel kode berikut ke dalam editor Arduino IDE.

```cpp
#include <UIPEthernet.h>
#include <EthernetClient.h>

// Pengaturan IP Address
byte mac[] = { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED };
IPAddress ip(192, 168, 1, 177);

EthernetServer server(80);

// Pin untuk sensor suhu LM35
const int sensorPin = A0;

// Data diri
String nama = "Nama Anda";
String nim = "NIM Anda";
String tanggalLahir = "Tanggal Lahir Anda";
String email = "Email Anda";

void setup() {
  // Memulai komunikasi serial
  Serial.begin(9600);

  // Memulai Ethernet
  Ethernet.begin(mac, ip);
  server.begin();
  Serial.print("Server IP: ");
  Serial.println(Ethernet.localIP());
}

void loop() {
  // Membaca nilai dari sensor suhu
  int sensorValue = analogRead(sensorPin);
  float temperature = (sensorValue / 1024.0) * 5.0 * 100;

  // Menampilkan data suhu dan data diri pada terminal serial
  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" C");
  Serial.print("Nama: ");
  Serial.println(nama);
  Serial.print("NIM: ");
  Serial.println(nim);
  Serial.print("Tanggal Lahir: ");
  Serial.println(tanggalLahir);
  Serial.print("Email: ");
  Serial.println(email);

  // Menunggu beberapa detik sebelum membaca kembali
  delay(5000);

  // Mendengarkan klien
  EthernetClient client = server.available();
  if (client) {
    Serial.println("New client");
    boolean currentLineIsBlank = true;
    while (client.connected()) {
      if (client.available()) {
        char c = client.read();
        Serial.write(c);

        // Jika kita sampai pada akhir baris HTTP request
        if (c == '\n' && currentLineIsBlank) {
          // Mengirim respon standar HTTP
          client.println("HTTP/1.1 200 OK");
          client.println("Content-Type: text/html");
          client.println("Connection: close");
          client.println();

          // Mengirim data suhu
          client.print("Temperature: ");
          client.print(temperature);
          client.println(" C");
          client.println("<br>");
          client.print("Nama: ");
          client.println(nama);
          client.print("NIM: ");
          client.println(nim);
          client.print("Tanggal Lahir: ");
          client.println(tanggalLahir);
          client.print("Email: ");
          client.println(email);
          break;
        }
        if (c == '\n') {
          currentLineIsBlank = true;
        } else if (c != '\r') {
          currentLineIsBlank = false;
        }
      }
    }
    delay(1);
    client.stop();
    Serial.println("Client disconnected");
  }
}


Sambungkan Komponen:

Sambungkan sensor LM35 ke pin A0 pada Arduino.
Sambungkan Ethernet Shield ke Arduino.
Pastikan koneksi antara sensor dan Arduino benar.
Jalankan Simulasi di Proteus:

Buka Proteus dan buat skematik sesuai dengan sambungan perangkat keras.
Tambahkan Virtual Terminal dan sambungkan ke pin RX dan TX Arduino.
Jalankan simulasi dan cek output di Virtual Terminal.
Penggunaan
Monitor Suhu di Serial Monitor:

Buka Serial Monitor di Arduino IDE (Ctrl+Shift+M).
Lihat data suhu dan informasi pribadi yang ditampilkan.
Akses Data via Web Browser:

Pastikan Arduino terhubung ke jaringan yang sama dengan komputer Anda.
Buka browser dan masukkan IP address yang ditampilkan di Serial Monitor.
Anda akan melihat data suhu dan informasi pribadi yang ditampilkan di halaman web.
Troubleshooting
Virtual Terminal Tidak Menampilkan Data:

Pastikan koneksi antara Arduino dan Virtual Terminal benar.
Periksa pengaturan baud rate di Virtual Terminal dan pastikan sesuai dengan pengaturan di kode (Serial.begin(9600);).
Data Tidak Muncul di Browser:

Pastikan Ethernet Shield terhubung dengan benar.
Pastikan Arduino menggunakan IP address yang valid dan tidak bentrok dengan perangkat lain di jaringan.
Coba restart Arduino dan cek kembali koneksi jaringan.
Lisensi
Proyek ini dilisensikan di bawah lisensi MIT. Lihat file LICENSE untuk informasi lebih lanjut.

Kontribusi
Kontribusi selalu diterima! Silakan buat Pull Request atau buka Issue untuk mengajukan perbaikan atau fitur baru.
