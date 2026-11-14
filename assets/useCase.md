# GET A JOB - Use Case Diagram

## Deskripsi

Use Case Diagram ini menggambarkan interaksi fungsional antara empat jenis aktor dengan sistem **GET A JOB**. Sistem berfungsi sebagai portal pencarian lowongan kerja yang menghubungkan pencari kerja, admin perusahaan, HRD, dan admin sistem dalam satu alur rekrutmen yang terstruktur.

## Aktor dan Tanggung Jawab

### 1. User (Pencari Kerja)
* Melakukan registrasi akun dan login ke sistem.
* Melihat dashboard User.
* Mengelola profil pribadi dan mengunggah CV.
* Mencari dan memfilter lowongan pekerjaan (berdasarkan posisi, lokasi, jenis pekerjaan, dan rentang gaji).
* Mengirim lamaran pekerjaan pada lowongan yang berstatus terbuka.
* Memantau status lamaran (`Menunggu Review`, `Diterima`, atau `Ditolak`).
* Mengajukan pendaftaran perusahaan baru.

### 2. Admin Perusahaan
* Login dan melihat dashboard perusahaan.
* Mengelola profil dan informasi perusahaan.
* Membuat akun HRD baru yang terhubung ke perusahaannya.
* Mengubah dan menonaktifkan akun HRD miliknya (tidak berwenang mengelola seleksi pelamar).

### 3. HRD
* Login dan melihat dashboard HRD.
* Mengelola lowongan pekerjaan perusahaannya (membuat, mengubah, membuka, dan menutup lowongan).
* Meninjau berkas serta CV pelamar dan menetapkan keputusan akhir lamaran (`Diterima` atau `Ditolak`).

### 4. Admin Sistem
* Login dan melihat dashboard administrasi sistem.
* Memverifikasi pengajuan pendaftaran perusahaan dari User (menyetujui dan mempromosikan role pemohon menjadi Admin Perusahaan, atau menolak pengajuan).
* Mengelola akun seluruh pengguna dan status perusahaan.
* Memantau data lowongan pekerjaan secara global.

## Alur Utama

Seluruh aktor melakukan **Login** terlebih dahulu sebelum mengakses layanan sesuai hak akses masing-masing. Setelah autentikasi berhasil, sistem menampilkan **Tampil Dashboard** secara dinamis berdasarkan peran (*role*) akun yang masuk.

1. **User (Pencari Kerja)** melengkapi profil serta CV, mencari lowongan yang tersedia, mengajukan lamaran, memantau hasil seleksi 3 tahap, atau mengajukan pendaftaran perusahaan baru.
2. **Admin Perusahaan** mengelola identitas perusahaan dan mengatur akun HRD yang bekerja di bawah perusahaannya.
3. **HRD** memublikasikan lowongan kerja serta menyeleksi berkas lamaran yang masuk ke lowongan perusahaannya.
4. **Admin Sistem** mengawasi operasional keseluruhan aplikasi dan memvalidasi pengajuan perusahaan baru.

## Relasi Include dan Extend

* **`Kirim Lamaran Pekerjaan` `<<include>>` `Kelola Profil & Unggah CV`:** Proses pengiriman lamaran mewajibkan User memiliki data profil dan berkas CV yang valid terlebih dahulu.
* **`Kirim Lamaran Pekerjaan` `<<extend>>` `Cari & Filter Lowongan`:** Setelah menemukan lowongan yang sesuai melalui pencarian/filter, User dapat melanjutkan ke proses pengiriman lamaran secara opsional.

## Batas Sistem

Kotak **`GET A JOB`** merupakan batas sistem (*system boundary*). Seluruh *use case* yang berada di dalam kotak tersebut merupakan layanan internal aplikasi, sedangkan keempat aktor diletakkan di luar kotak sebagai pihak eksternal yang berinteraksi dengan sistem.

## PlantUML

Salin kode PlantUML berikut ke **draw.io** melalui menu **Arrange > Insert > Advanced > PlantUML** untuk menghasilkan diagram dengan tata letak bersih tanpa label panah yang bertumpuk:

```plantuml
@startuml
left to right direction
skinparam packageStyle rectangle
skinparam nodesep 35
skinparam ranksep 60
skinparam shadowing false
skinparam usecase {
    BackgroundColor #E0F2FE
    BorderColor #0369A1
    ArrowColor #334155
}

actor "User (Pencari Kerja)" as User
actor "Admin Perusahaan" as AP
actor "HRD" as HRD
actor "Admin Sistem" as AS

rectangle "GET A JOB" {
    ' Use Case Autentikasi & Umum
    usecase "Registrasi Akun" as UC_Reg
    usecase "Login" as UC_Login
    usecase "Tampil Dashboard" as UC_Dash

    ' Use Case User (diurutkan agar panah include/extend tidak bertabrakan)
    usecase "Cari & Filter Lowongan" as UC_Cari
    usecase "Kirim Lamaran Pekerjaan" as UC_Lamar
    usecase "Kelola Profil & Unggah CV" as UC_Profil
    usecase "Pantau Status Lamaran" as UC_Status
    usecase "Ajukan Pendaftaran Perusahaan" as UC_AjukanPT

    ' Use Case Admin Perusahaan
    usecase "Kelola Profil Perusahaan" as UC_ProfilPT
    usecase "Buat Akun HRD" as UC_BuatHRD
    usecase "Kelola Akun HRD" as UC_KelolaHRD

    ' Use Case HRD
    usecase "Kelola Lowongan\n(Buat/Edit/Buka/Tutup)" as UC_KelolaLoker
    usecase "Putuskan Status Lamaran\n(Diterima/Ditolak)" as UC_Putuskan

    ' Use Case Admin Sistem
    usecase "Verifikasi Pengajuan Perusahaan" as UC_VerifPT
    usecase "Kelola Pengguna & Perusahaan" as UC_KelolaSistem
    usecase "Pantau Lowongan Global" as UC_PantauLoker
}

' Relasi Umum (Login & Dashboard)
User --> UC_Reg
User --> UC_Login
User --> UC_Dash

AP --> UC_Login
AP --> UC_Dash

HRD --> UC_Login
HRD --> UC_Dash

AS --> UC_Login
AS --> UC_Dash

' Relasi User (Pencari Kerja)
User --> UC_Cari
User --> UC_Lamar
User --> UC_Profil
User --> UC_Status
User --> UC_AjukanPT

' Relasi Include & Extend (Jalur langsung tanpa melewati oval lain)
UC_Lamar .> UC_Cari : <<extend>>
UC_Lamar .> UC_Profil : <<include>>

' Relasi Admin Perusahaan
AP --> UC_ProfilPT
AP --> UC_BuatHRD
AP --> UC_KelolaHRD

' Relasi HRD
HRD --> UC_KelolaLoker
HRD --> UC_Putuskan

' Relasi Admin Sistem
AS --> UC_VerifPT
AS --> UC_KelolaSistem
AS --> UC_PantauLoker

@enduml
```
