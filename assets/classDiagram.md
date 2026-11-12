# Kode PlantUML
Menowo nek pengem revisi, nggae sg kyk ngene mbot

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