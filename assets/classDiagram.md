# GET A JOB - Class Diagram

## Deskripsi

Class Diagram ini menggambarkan struktur objek utama sistem GET A JOB, meliputi atribut, method, inheritance, enum, polymorphism, encapsulation, dan relasi antarclass.

Rancangan menggunakan `Pengguna` sebagai abstract superclass. Class tersebut diturunkan menjadi `User`, `AdminPerusahaan`, `HRD`, dan `AdminSistem`.

Class domain utama adalah `Perusahaan`, `Lowongan`, `Lamaran`, dan `PengajuanPerusahaan`.

## Struktur Class

### Pengguna

`Pengguna` merupakan abstract class untuk atribut dan perilaku dasar seluruh akun.

Atribut:
- `idPengguna`
- `nama`
- `email`
- `passwordHash`
- `statusAktif`

Method:
- `ubahProfil()`
- `aktifkanAkun()`
- `nonaktifkanAkun()`
- `getRole()`

`getRole()` menjadi titik polymorphism. Subclass melakukan overriding dengan signature yang sama dan implementasi role yang berbeda.

### User

`User` merupakan subclass `Pengguna` untuk pencari kerja.

Atribut:
- `nomorTelepon`
- `alamat`
- `cv`

Method:
- `unggahCV()` untuk mengunggah CV.
- `cariLowongan()` untuk mencari lowongan.
- `kirimLamaran()` untuk mengirim lamaran.
- `pantauLamaran()` untuk memantau lamaran.
- `ajukanPerusahaan()` untuk membuat pengajuan perusahaan.
- `getRole()` sebagai override.

### AdminPerusahaan

`AdminPerusahaan` merupakan subclass `Pengguna` yang mengelola perusahaan dan akun HRD.

Method:
- `kelolaProfilPerusahaan()`
- `buatAkunHRD()`
- `ubahAkunHRD()`
- `nonaktifkanHRD()`
- `getRole()` sebagai override.

### HRD

`HRD` merupakan subclass `Pengguna` yang menangani proses rekrutmen.

Atribut:
- `perusahaan`
- `jabatan`

Method:
- `buatLowongan()`
- `ubahLowongan()`
- `bukaLowongan()`
- `tutupLowongan()`
- `reviewLamaran()`
- `putuskanStatus()`
- `getRole()` sebagai override.

### AdminSistem

`AdminSistem` merupakan subclass `Pengguna` yang menangani administrasi tingkat aplikasi.

Method:
- `verifikasiPengajuan()`
- `kelolaPengguna()`
- `kelolaPerusahaan()`
- `pantauLowonganGlobal()`
- `getRole()` sebagai override.

### Perusahaan

`Perusahaan` menyimpan data perusahaan.

Atribut:
- `idPerusahaan`
- `nama`
- `alamat`
- `deskripsi`
- `email`
- `statusVerifikasi`

Method:
- `ubahProfil()`
- `tambahHRD()`
- `tambahLowongan()`
- `getStatusVerifikasi()`

Satu perusahaan dapat memiliki banyak HRD dan banyak lowongan.

### Lowongan

`Lowongan` merepresentasikan posisi pekerjaan yang dipublikasikan perusahaan.

Atribut:
- `idLowongan`
- `posisi`
- `deskripsi`
- `persyaratan`
- `lokasi`
- `gajiMin`
- `gajiMax`
- `status`
- `tanggalDibuat`
- `tanggalTutup`

Method:
- `ubahDetail()`
- `buka()`
- `tutup()`
- `sesuaiKriteria()`

### Lamaran

`Lamaran` menyimpan transaksi ketika User melamar suatu lowongan.

Atribut:
- `idLamaran`
- `tanggalLamaran`
- `status`
- `catatanHRD`

Method:
- `ajukan()`
- `ubahStatus()`
- `tambahCatatan()`
- `getStatus()`

`Lamaran` menjadi class tersendiri karena hubungan antara `User` dan `Lowongan` mempunyai data dan perilaku sendiri.

### PengajuanPerusahaan

`PengajuanPerusahaan` menyimpan proses pengajuan pendaftaran perusahaan.

Atribut:
- `idPengajuan`
- `tanggalPengajuan`
- `status`
- `alasanPenolakan`

Method:
- `ajukan()`
- `setujui()`
- `tolak()`
- `getStatus()`

## Enum

Enum digunakan untuk membatasi nilai status:

- `StatusVerifikasi`: `DIAJUKAN`, `DISETUJUI`, `DITOLAK`
- `StatusLowongan`: `DRAFT`, `TERBUKA`, `DITUTUP`
- `StatusLamaran`: `DIAJUKAN`, `DIREVIEW`, `DITERIMA`, `DITOLAK`
- `StatusPengajuan`: `DIAJUKAN`, `DISETUJUI`, `DITOLAK`

## Inheritance

Relasi inheritance:

- `Pengguna <|-- User`
- `Pengguna <|-- AdminPerusahaan`
- `Pengguna <|-- HRD`
- `Pengguna <|-- AdminSistem`

Keempat subclass mewarisi atribut dan method dasar dari `Pengguna`.

## Polymorphism

Polymorphism menggunakan method overriding.

`getRole()` didefinisikan pada `Pengguna`, kemudian diimplementasikan kembali oleh setiap subclass. Signature tetap sama, sedangkan hasil role menyesuaikan jenis akun.

Rancangan ini tidak menggunakan method overloading sebagai mekanisme polymorphism utama karena Python tidak menyediakan method overloading native seperti Java atau C++.

## Encapsulation

Atribut menggunakan visibility private yang ditandai `-`. Method yang dapat digunakan dari luar class ditandai `+`.

Contoh:

```text
- email : String
- passwordHash : String
- statusAktif : boolean

+ ubahProfil() : void
+ aktifkanAkun() : void
+ nonaktifkanAkun() : void
```

Dengan rancangan ini, data internal objek dikelola melalui perilaku yang disediakan class.

## Relasi Antarclass

1. `Perusahaan "1" -- "1" AdminPerusahaan`
   Satu perusahaan dikelola oleh satu Admin Perusahaan sesuai rancangan saat ini.

2. `Perusahaan "1" -- "0..*" HRD`
   Satu perusahaan dapat memiliki banyak HRD.

3. `Perusahaan "1" -- "0..*" Lowongan`
   Satu perusahaan dapat menyediakan banyak lowongan.

4. `HRD "1" -- "0..*" Lowongan`
   Satu HRD dapat mengelola banyak lowongan.

5. `User "1" -- "0..*" Lamaran`
   Satu User dapat memiliki banyak lamaran.

6. `Lowongan "1" -- "0..*" Lamaran`
   Satu lowongan dapat menerima banyak lamaran.

7. `User "1" -- "0..*" PengajuanPerusahaan`
   Satu User dapat membuat banyak pengajuan perusahaan.

8. `AdminSistem "1" -- "0..*" PengajuanPerusahaan`
   Admin Sistem dapat memverifikasi banyak pengajuan.

9. `PengajuanPerusahaan "0..1" -- "1" Perusahaan`
   Sebuah pengajuan dapat menghasilkan satu perusahaan setelah proses pengajuan berhasil.

## Konsistensi dengan Implementasi

Class Diagram ini menjadi acuan ketika proses coding dilakukan. Nama class, atribut, method, parameter, return type, inheritance, enum, dan relasi sebaiknya mengikuti diagram agar desain dan implementasi tetap konsisten.

DAO dan utilitas teknis seperti koneksi database, session, dan validator tidak dimasukkan karena diagram ini berfokus pada domain object dan tanggung jawab utama sistem GET A JOB.

## PlantUML

Kode PlantUML berikut dipertahankan sebagai sumber diagram agar diagram dapat diregenerasi atau diedit melalui PlantUML maupun draw.io.

```PlantUML
@startuml
skinparam classAttributeIconSize 0
skinparam monochrome true
skinparam shadowing false

title GET A JOB - Class Diagram

' =========================
' ENUMS
' =========================

enum StatusVerifikasi {
    DIAJUKAN
    DISETUJUI
    DITOLAK
}

enum StatusLowongan {
    DRAFT
    TERBUKA
    DITUTUP
}

enum StatusLamaran {
    DIAJUKAN
    DIREVIEW
    DITERIMA
    DITOLAK
}

enum StatusPengajuan {
    DIAJUKAN
    DISETUJUI
    DITOLAK
}

' =========================
' ABSTRACT SUPERCLASS
' =========================

abstract class Pengguna {
    - idPengguna : int
    - nama : String
    - email : String
    - passwordHash : String
    - statusAktif : boolean

    + ubahProfil() : void
    + aktifkanAkun() : void
    + nonaktifkanAkun() : void
    + getRole() : String
}

' =========================
' USER ROLES
' =========================

class User {
    - nomorTelepon : String
    - alamat : String
    - cv : String

    + unggahCV(path : String) : void
    + cariLowongan(kriteria : String) : List<Lowongan>
    + kirimLamaran(idLowongan : int) : Lamaran
    + pantauLamaran() : List<Lamaran>
    + ajukanPerusahaan() : PengajuanPerusahaan
    + getRole() : String
}

class AdminPerusahaan {
    - perusahaan : Perusahaan

    + kelolaProfilPerusahaan() : void
    + buatAkunHRD(data : HRD) : HRD
    + ubahAkunHRD(idHRD : int) : void
    + nonaktifkanHRD(idHRD : int) : void
    + getRole() : String
}

class HRD {
    - perusahaan : Perusahaan
    - jabatan : String

    + buatLowongan(data : Lowongan) : Lowongan
    + ubahLowongan(idLowongan : int) : void
    + bukaLowongan(idLowongan : int) : void
    + tutupLowongan(idLowongan : int) : void
    + reviewLamaran(idLamaran : int) : void
    + putuskanStatus(idLamaran : int, status : StatusLamaran) : void
    + getRole() : String
}

class AdminSistem {

    + verifikasiPengajuan(idPengajuan : int, status : StatusPengajuan) : void
    + kelolaPengguna(idPengguna : int) : void
    + kelolaPerusahaan(idPerusahaan : int) : void
    + pantauLowonganGlobal() : List<Lowongan>
    + getRole() : String
}

' =========================
' DOMAIN ENTITIES
' =========================

class Perusahaan {
    - idPerusahaan : int
    - nama : String
    - alamat : String
    - deskripsi : String
    - email : String
    - statusVerifikasi : StatusVerifikasi

    + ubahProfil() : void
    + tambahHRD(hrd : HRD) : void
    + tambahLowongan(lowongan : Lowongan) : void
    + getStatusVerifikasi() : StatusVerifikasi
}

class Lowongan {
    - idLowongan : int
    - posisi : String
    - deskripsi : String
    - persyaratan : String
    - lokasi : String
    - gajiMin : Decimal
    - gajiMax : Decimal
    - status : StatusLowongan
    - tanggalDibuat : DateTime
    - tanggalTutup : DateTime

    + ubahDetail() : void
    + buka() : void
    + tutup() : void
    + sesuaiKriteria(kriteria : String) : boolean
}

class Lamaran {
    - idLamaran : int
    - tanggalLamaran : DateTime
    - status : StatusLamaran
    - catatanHRD : String

    + ajukan() : void
    + ubahStatus(status : StatusLamaran) : void
    + tambahCatatan(catatan : String) : void
    + getStatus() : StatusLamaran
}

class PengajuanPerusahaan {
    - idPengajuan : int
    - tanggalPengajuan : DateTime
    - status : StatusPengajuan
    - alasanPenolakan : String

    + ajukan() : void
    + setujui() : void
    + tolak(alasan : String) : void
    + getStatus() : StatusPengajuan
}

' =========================
' INHERITANCE
' =========================

Pengguna <|-- User
Pengguna <|-- AdminPerusahaan
Pengguna <|-- HRD
Pengguna <|-- AdminSistem

' =========================
' COMPANY RELATIONSHIPS
' =========================

Perusahaan "1" -- "1" AdminPerusahaan : dikelola oleh
Perusahaan "1" -- "0..*" HRD : memiliki
Perusahaan "1" -- "0..*" Lowongan : menyediakan

' =========================
' HRD RELATIONSHIP
' =========================

HRD "1" -- "0..*" Lowongan : mengelola

' =========================
' APPLICATION RELATIONSHIPS
' =========================

User "1" -- "0..*" Lamaran : mengajukan
Lowongan "1" -- "0..*" Lamaran : menerima

' =========================
' COMPANY REGISTRATION
' =========================

User "1" -- "0..*" PengajuanPerusahaan : mengajukan
AdminSistem "1" -- "0..*" PengajuanPerusahaan : memverifikasi
PengajuanPerusahaan "0..1" -- "1" Perusahaan : menghasilkan

@enduml
```
