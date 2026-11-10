# Anu, ngunulah pokok e
## Setidaknya, ngkongene isine

```
====================================================================================================
                                 SYSTEM BOUNDARY: GET A JOB
====================================================================================================

      +------------------------+
      |      (1) Register      |
      +------------------------+
                 ^
                 | <<include>>
      +------------------------+
      |       (2) Login        |<---------------------+-------------------+-------------------+
      +------------------------+                      |                   |                   |
                 ^                                    |                   |                   |
                 | <<include>>                        |                   |                   |
      +------------------------+                      |                   |                   |
      |   (3) Tampil Dashboard |                      |                   |                   |
      +------------------------+                      |                   |                   |
                                                      |                   |                   |
  [ USER / PENCARI KERJA ]                            |                   |                   |
      |-- (4) Kelola Profil & Unggah CV               |                   |                   |
      |-- (5) Cari & Filter Lowongan                  |                   |                   |
      |        \                                      |                   |                   |
      |         `-- <<extend>>                        |                   |                   |
      |-- (6) Kirim Lamaran Pekerjaan                 |                   |                   |
      |-- (7) Pantau Status Lamaran                   |                   |                   |
      |-- (8) Ajukan Pendaftaran Perusahaan           |                   |                   |
                                                      |                   |                   |
  [ ADMIN PERUSAHAAN ] -------------------------------+                   |                   |
      |-- (9)  Kelola Profil Perusahaan                                   |                   |
      |-- (10) Buat Akun HRD                                              |                   |
      |-- (11) Kelola Akun HRD (Nonaktifkan/Edit)                         |                   |
                                                                          |                   |
  [ HRD ] ----------------------------------------------------------------+                   |
      |-- (12) Kelola Lowongan (Buat, Edit, Buka, Tutup)                                      |
      |-- (13) Review Berkas & CV Pelamar                                                     |
      |-- (14) Putuskan Status Lamaran (Diterima / Ditolak)                                   |
                                                                                              |
  [ ADMIN APLIKASI ] -------------------------------------------------------------------------+
      |-- (15) Verifikasi Pengajuan Perusahaan (Setujui / Tolak)
      |-- (16) Kelola Seluruh Pengguna & Perusahaan
      |-- (17) Pantau Lowongan Global
====================================================================================================
```

## Plant UML gae draw io su
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