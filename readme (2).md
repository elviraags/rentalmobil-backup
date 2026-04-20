# FarzaToys Rental

<div align="center">

![Flutter](https://img.shields.io/badge/Flutter-3.x-02569B?style=for-the-badge&logo=flutter&logoColor=white)
![Dart](https://img.shields.io/badge/Dart-3.x-0175C2?style=for-the-badge&logo=dart&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-2.x-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

**Aplikasi manajemen penyewaan mobil mainan berbasis Flutter & Supabase**

*Kelola armada, transaksi sewa, dan antrian pelanggan — semuanya dalam satu aplikasi.*

</div>

---

## Deskripsi

**FarzaToys Rental** adalah aplikasi mobile manajemen penyewaan mobil mainan yang dibangun menggunakan Flutter dengan backend real-time Supabase. Aplikasi ini dirancang untuk memudahkan operator/pemilik usaha dalam mengelola stok unit, mencatat transaksi sewa, memantau pendapatan harian, hingga mengelola antrian pelanggan — semuanya secara digital dan efisien.

Aplikasi mendukung multi-akun dengan sistem autentikasi berbasis Supabase Auth, di mana admin memiliki akses penuh termasuk kelola akun pengguna lain.

---

## Fitur Utama

### Autentikasi & Manajemen Akun
- Login & logout menggunakan Supabase Auth
- Registrasi akun baru (khusus Admin)
- Role-based access: **Admin** (`admin@gmail.com`) memiliki menu tambahan untuk kelola akun
- Session persisten — pengguna tetap login setelah menutup aplikasi

### Manajemen Unit (Mobil Mainan)
- Tambah, edit, dan hapus unit mobil
- Upload foto unit menggunakan **Image Picker**
- Atur harga sewa per 15 menit secara individual
- Toggle ketersediaan unit (Tersedia / Disewa)
- Tampilkan warna, nama, catatan, dan gambar unit

### Manajemen Penyewaan
- Buat transaksi sewa baru dengan data penyewa lengkap:
  - Nama, nomor telepon, alamat
  - Pilih unit & durasi sewa
  - Status pembayaran (lunas / belum)
- Kembalikan unit dan otomatis tandai unit sebagai tersedia
- Perpanjang waktu sewa + tambah biaya
- Hapus transaksi (unit otomatis dikembalikan jika masih aktif)
- Filter: **Semua** / **Aktif** / **Selesai**

### Pembayaran & Pendapatan
- Tandai transaksi sebagai **Lunas** atau **Belum Dibayar**
- Dashboard pendapatan **hari ini** — otomatis dihitung dari transaksi yang selesai dan lunas
- Harga dihitung otomatis berdasarkan durasi dan tarif per unit

### Notifikasi Lokal
- Jadwalkan pengingat otomatis saat durasi sewa akan habis
- Notifikasi berbasis **flutter_local_notifications** dengan timezone lokal (Asia/Makassar)
- Dukungan alarm (Android)

### Antrian Pelanggan
- Tambah pelanggan ke daftar antrian jika unit sedang penuh
- Opsional: tentukan unit target yang ditunggu
- Hapus antrian saat pelanggan sudah mulai menyewa

### Tampilan & Tema
- Desain **bold & playful** dengan warna kuning primer (`#FFEB3B`) dan ungu (`#7B1FA2`)
- Custom AppBar dengan bottom border tebal bergaya neo-brutalist
- SnackBar custom dengan border tebal dan aksi "OK"

---

## Struktur Widget Utama

| Widget / Screen | Deskripsi |
|---|---|
| `AuthWrapper` | StreamBuilder yang memantau sesi auth Supabase dan mengarahkan ke HomeScreen atau LoginScreen |
| `HomeScreen` | Halaman utama berisi dashboard, daftar unit, dan transaksi aktif |
| `LoginScreen` | Form login dengan Supabase Auth |
| `RegisterScreen` | Form registrasi akun baru (hanya admin) |
| `CustomAppBar` | AppBar reusable dengan aksi menu (kelola akun & logout), role-aware |
| `CarCard` | Kartu unit mobil dengan gambar, status, dan tombol aksi |
| `RentalCard` | Kartu transaksi sewa dengan info penyewa, durasi, dan status bayar |
| `QueueList` | Daftar antrian pelanggan yang menunggu unit |
| `AddCarDialog` | Dialog tambah/edit unit mobil |
| `AddRentalSheet` | Bottom sheet form transaksi sewa baru |
| `ExtendTimeDialog` | Dialog perpanjangan waktu sewa |

---

## Arsitektur & State Management

```
lib/
├── main.dart                  # Entry point, inisialisasi Supabase & Provider
├── app_store.dart             # State management global (ChangeNotifier)
├── notification_service.dart  # Layanan notifikasi lokal
├── models/
│   ├── car.dart               # Model data unit mobil
│   ├── rental.dart            # Model data transaksi sewa
│   └── queue_item.dart        # Model data antrian
├── services/
│   └── supabase_service.dart  # Abstraksi operasi database Supabase
├── screens/
│   ├── home_screen.dart
│   ├── login_screen.dart
│   └── register_screen.dart
└── widgets/
    └── custom_app_bar.dart
```

**State Management:** Provider (`ChangeNotifier`) melalui `AppStore` — satu sumber kebenaran untuk data unit, sewa, dan antrian.

---

## Skema Database (Supabase)

### Tabel `cars`
| Kolom | Tipe | Keterangan |
|---|---|---|
| `id` | UUID | Primary key |
| `name` | text | Nama unit |
| `color` | text | Warna unit |
| `note` | text | Catatan tambahan |
| `is_available` | boolean | Status ketersediaan |
| `image_url` | text | URL foto unit |
| `price_per_15_mins` | integer | Harga per 15 menit (default: 20.000) |

### Tabel `rentals`
| Kolom | Tipe | Keterangan |
|---|---|---|
| `id` | UUID | Primary key |
| `car_id` | UUID | FK → cars |
| `car_name` | text | Nama unit (snapshot) |
| `renter_name` | text | Nama penyewa |
| `renter_phone` | text | Nomor telepon |
| `renter_address` | text | Alamat penyewa |
| `start_time` | timestamptz | Waktu mulai sewa |
| `end_time` | timestamptz | Waktu selesai sewa |
| `duration_minutes` | integer | Durasi dalam menit |
| `total_price` | integer | Total harga |
| `is_paid` | boolean | Status pembayaran |
| `status` | text | `active` / `returned` |

---


## Dependencies

| Package | Versi | Fungsi |
|---|---|---|
| `provider` | ^6.1.1 | State management |
| `supabase_flutter` | ^2.3.4 | Backend & Auth |
| `flutter_dotenv` | ^5.1.0 | Konfigurasi environment |
| `intl` | ^0.18.1 | Format tanggal & angka (Bahasa Indonesia) |
| `flutter_local_notifications` | ^21.0.0 | Notifikasi lokal terjadwal |
| `timezone` | ^0.11.0 | Zona waktu (Asia/Makassar) |
| `image_picker` | ^1.2.1 | Upload foto dari galeri/kamera |
| `flutter_launcher_icons` | ^0.14.1 | Generate ikon aplikasi |

---

## Konfigurasi Notifikasi (Android)

Tambahkan permission berikut di `android/app/src/main/AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
<uses-permission android:name="android.permission.VIBRATE"/>
<uses-permission android:name="android.permission.USE_EXACT_ALARM"/>
<uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM"/>
```

---

## Akun Default

| Role | Email | Akses |
|---|---|---|
| **Admin** | `admin@gmail.com` | Semua fitur + kelola akun pengguna |
| **Operator** | *(akun lain)* | Fitur operasional standar |

> Admin dapat membuat akun operator melalui menu **Kelola Akun** di AppBar.

---

## Screenshot

> *Tambahkan screenshot aplikasi di sini*

| Login | Home | Daftar Unit | Transaksi |
|---|---|---|---|
| *(ss)* | *(ss)* | *(ss)* | *(ss)* |

---


