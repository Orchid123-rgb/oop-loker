# GET A JOB - Use Case Diagram

## Deskripsi

Use Case Diagram ini menggambarkan interaksi antara empat jenis aktor dengan sistem GET A JOB. Sistem berfungsi sebagai portal pencarian lowongan kerja yang menghubungkan pencari kerja, perusahaan, HRD, dan administrator aplikasi.

## Aktor dan Tanggung Jawab

1. User (Pencari Kerja)
   - Registrasi dan login.
   - Melihat dashboard.
   - Mengelola profil dan mengunggah CV.
   - Mencari dan memfilter lowongan.
   - Mengirim lamaran pekerjaan.
   - Memantau status lamaran.
   - Mengajukan pendaftaran perusahaan.

2. Admin Perusahaan
   - Mengelola profil perusahaan.
   - Membuat akun HRD.
   - Mengedit dan menonaktifkan akun HRD.

3. HRD
   - Mengelola lowongan, termasuk membuat, mengedit, membuka, dan menutup lowongan.
   - Meninjau berkas dan CV pelamar.
   - Menentukan status lamaran diterima atau ditolak.

4. Admin Aplikasi
   - Memverifikasi pengajuan perusahaan.
   - Mengelola seluruh pengguna dan perusahaan.
   - Memantau lowongan secara global.

## Alur Utama

Semua aktor menggunakan Login sebelum menjalankan fungsi utama sesuai perannya. Setelah login, sistem menampilkan dashboard sesuai jenis akun.

User dapat mengelola profil dan CV, mencari lowongan, mengirim lamaran, memantau status lamaran, serta mengajukan pendaftaran perusahaan.

Admin Perusahaan mengelola data perusahaan dan akun HRD.

HRD mengelola lowongan dan memproses lamaran pelamar.

Admin Aplikasi menangani verifikasi perusahaan, administrasi pengguna dan perusahaan, serta pemantauan lowongan global.

## Relasi Include dan Extend

`Kirim Lamaran Pekerjaan` memiliki relasi `<<include>>` terhadap `Kelola Profil & Unggah CV`. Artinya proses lamaran membutuhkan data profil dan CV yang diperlukan.

`Kirim Lamaran Pekerjaan` memiliki relasi `<<extend>>` terhadap `Cari & Filter Lowongan`. Artinya proses pengiriman lamaran dapat dilanjutkan setelah User menemukan lowongan melalui proses pencarian.

## Batas Sistem

`Job Portal CLI System` merupakan batas sistem. Semua use case di dalam batas tersebut merupakan fungsi aplikasi. Keempat aktor berada di luar batas karena mereka berinteraksi dengan sistem.

## PlantUML

Kode PlantUML berikut dipertahankan sebagai sumber diagram agar diagram dapat diregenerasi atau diedit melalui PlantUML maupun draw.io. 

```plantUML
@startuml
left to right direction
skinparam packageStyle rectangle
skinparam usecase {
    BackgroundColor LightCyan
    BorderColor DarkSlateGray
    ArrowColor DimGray
}

actor "User (Pencari Kerja)" as User
actor "Admin Perusahaan" as AP
actor "HRD" as HRD
actor "Admin Aplikasi" as AA

rectangle "Job Portal CLI System" {
    usecase "Registrasi Akun" as UC_Reg
    usecase "Login" as UC_Login
    usecase "Tampil Dashboard" as UC_Dash

    usecase "Kelola Profil & Unggah CV" as UC_Profil
    usecase "Cari & Filter Lowongan" as UC_Cari
    usecase "Kirim Lamaran Pekerjaan" as UC_Lamar
    usecase "Pantau Status Lamaran" as UC_Status
    usecase "Ajukan Pendaftaran Perusahaan" as UC_AjukanPT

    usecase "Kelola Profil Perusahaan" as UC_ProfilPT
    usecase "Buat Akun HRD" as UC_BuatHRD
    usecase "Kelola Akun HRD" as UC_KelolaHRD

    usecase "Kelola Lowongan (Buka/Tutup/Edit)" as UC_KelolaLoker
    usecase "Putuskan Status Lamaran\n(Diterima/Ditolak)" as UC_Putuskan

    usecase "Verifikasi Pengajuan Perusahaan" as UC_VerifPT
    usecase "Kelola Pengguna & Perusahaan" as UC_KelolaSistem
    usecase "Pantau Lowongan Global" as UC_PantauLoker
}

' Relasi Login dan Dashboard untuk semua aktor
User --> UC_Reg
User --> UC_Login
User --> UC_Dash

AP --> UC_Login
AP --> UC_Dash

HRD --> UC_Login
HRD --> UC_Dash

AA --> UC_Login
AA --> UC_Dash

' Relasi User
User --> UC_Profil
User --> UC_Cari
User --> UC_Lamar
User --> UC_Status
User --> UC_AjukanPT

' Relasi Include/Extend pada User
UC_Lamar .> UC_Cari : <<extend>>
UC_Lamar .> UC_Profil : <<include>>

' Relasi Admin Perusahaan
AP --> UC_ProfilPT
AP --> UC_BuatHRD
AP --> UC_KelolaHRD

' Relasi HRD
HRD --> UC_KelolaLoker
HRD --> UC_Putuskan

' Relasi Admin Aplikasi
AA --> UC_VerifPT
AA --> UC_KelolaSistem
AA --> UC_PantauLoker

@enduml
```
