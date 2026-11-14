# GET A JOB - Class Diagram

## Deskripsi & Ruang Lingkup

**Class Diagram** ini menggambarkan struktur statis lapisan **Domain Model (`models/`)** pada sistem **GET A JOB**, meliputi kelas abstrak, kelas turunan (*inheritance*), enumerasi (*enum*), polimorfisme (*method overriding*), enkapsulasi, serta relasi dan *multiplicity* antar-objek.

Rancangan menggunakan **`Pengguna`** sebagai *abstract superclass* yang diturunkan menjadi empat aktor: **`User`**, **`AdminPerusahaan`**, **`HRD`**, dan **`AdminSistem`**. Sementara itu, entitas domain utamanya terdiri atas **`Perusahaan`**, **`Lowongan`**, **`Lamaran`**, dan **`PengajuanPerusahaan`**.

> **Batasan Ruang Lingkup Diagram (Anti Over-Engineering):** Lapisan orkestrasi proses bisnis (`services/`), akses database MySQL (`dao/*_dao.py` & `*_dao_impl.py`), serta utilitas teknis (`util/`) dipisahkan dari diagram ini sesuai prinsip *Separation of Concerns*, sehingga diagram berfokus penuh pada objek domain dan aturan bisnis inti.

---

## Standar Presisi Tipe Data (UML – Python – MySQL)

Seluruh tipe data pada diagram menggunakan *type hinting* Python yang presisi dan selaras dengan skema tabel MySQL:

| Tipe pada Diagram | Tipe Python | Padanan Kolom MySQL | Keterangan |
| :--- | :--- | :--- | :--- |
| `int` | `int` | `INT AUTO_INCREMENT` | Primary Key & Foreign Key |
| `str` | `str` | `VARCHAR` / `TEXT` | Identitas teks, email, deskripsi, path CV |
| `float` | `float` | `DECIMAL(12, 2)` | Nominal gaji (`gaji_min`, `gaji_max`), wajib $\ge 0.0$ |
| `bool` | `bool` | `TINYINT(1)` / `BOOLEAN` | Status aktif/nonaktif akun |
| `datetime` | `datetime.datetime` | `DATETIME` | Waktu pembuatan lowongan, lamaran, dan pengajuan |
| `StatusVerifikasi` | `Enum` | `ENUM(...)` | `DIAJUKAN`, `DISETUJUI`, `DITOLAK` |
| `StatusLowongan` | `Enum` | `ENUM(...)` | `DRAFT`, `TERBUKA`, `DITUTUP` |
| `StatusLamaran` | `Enum` | `ENUM(...)` | **3 Status Final:** `MENUNGGU_REVIEW`, `DITERIMA`, `DITOLAK` |
| `StatusPengajuan` | `Enum` | `ENUM(...)` | `DIAJUKAN`, `DISETUJUI`, `DITOLAK` |
| `list<T>` | `list[T]` | Relasi One-to-Many | Koleksi objek anak (mis. `list[HRD]`, `list[Lowongan]`) |
| `Optional<T>` | `Optional[T]` | `NULLABLE FK` | Referensi objek yang dapat bernilai `None` |

---

## Penerapan 4 Pilar OOP & Enkapsulasi Best Practice

### 1. Abstraction & Inheritance
* **`Pengguna`** didefinisikan sebagai **Abstract Base Class (`ABC`)** menggunakan modul `abc`, sehingga tidak dapat diinstansiasi secara langsung.
* Keempat kelas aktor (**`User`**, **`AdminPerusahaan`**, **`HRD`**, **`AdminSistem`**) mewarisi atribut dan perilaku dasar dari `Pengguna` menggunakan `super().__init__(...)`.

### 2. Polymorphism
* `Pengguna` mendeklarasikan dua *abstract method* (`@abstractmethod`): `get_role() : str` dan `tampilkan_dashboard() : None`.
* Masing-masing subclass melakukan *method overriding* dengan *signature* yang identik tetapi menghasilkan peran dan tampilan menu dashboard yang berbeda. Pendekatan ini digunakan karena Python tidak memiliki *method overloading* statis seperti Java atau C++.

### 3. Encapsulation (Konvensi Python & `@property`)
* **Protected (`#` $\rightarrow$ `_atribut`):** Diterapkan pada atribut `Pengguna` (`_id_pengguna`, `_nama`, `_email`, `_status_aktif`) agar dapat diakses secara sah oleh subclass turunannya.
* **Private (`-` $\rightarrow$ `__atribut`):** Diterapkan pada data kredensial (`__password_hash`) dan seluruh atribut entitas domain untuk mencegah perubahan langsung dari luar kelas (*name mangling*).
* **Getter & Setter Tervalidasi (`@property` & `@setter`):**
  * **`Pengguna`:** Setter `email` memvalidasi format email wajib mengandung `@`; `__password_hash` tidak memiliki setter publik dan hanya dapat diubah melalui `ubah_password()`.
  * **`User`:** Setter `cv_path` memastikan path berkas CV tidak kosong dan berformat valid sebelum `kirim_lamaran()` dieksekusi.
  * **`Lowongan`:** Setter `gaji_min` dan `gaji_max` memvalidasi bahwa gaji tidak bernilai negatif dan `gaji_max >= gaji_min`.
  * **`Lamaran`:** Atribut `__status` bersifat *read-only* dari luar (hanya getter) dan hanya berubah melalui `ubah_status()` yang mengunci transisi dari `MENUNGGU_REVIEW` ke `DITERIMA` atau `DITOLAK` (status akhir tidak dapat diubah kembali).

---

## Struktur Class

### 1. Enum

Enum digunakan untuk membatasi nilai status agar konsisten di seluruh lapisan aplikasi:
* **`StatusVerifikasi`:** `DIAJUKAN`, `DISETUJUI`, `DITOLAK`
* **`StatusLowongan`:** `DRAFT`, `TERBUKA`, `DITUTUP`
* **`StatusLamaran`:** `MENUNGGU_REVIEW`, `DITERIMA`, `DITOLAK` *(mengikuti aturan final 3 status)*
* **`StatusPengajuan`:** `DIAJUKAN`, `DISETUJUI`, `DITOLAK`

### 2. Pengguna (Abstract Superclass)

`Pengguna` merupakan *abstract class* yang menyimpan atribut dan perilaku dasar seluruh akun pengguna.

* **Atribut:**
  * `# _id_pengguna : int`
  * `# _nama : str`
  * `# _email : str`
  * `- __password_hash : str`
  * `# _status_aktif : bool`
* **Method:**
  * `+ <<property>> id_pengguna() : int`
  * `+ <<property>> nama() : str`
  * `+ <<setter>> nama(nilai : str) : None`
  * `+ <<property>> email() : str`
  * `+ <<setter>> email(nilai : str) : None`
  * `+ <<property>> status_aktif() : bool`
  * `+ ubah_profil(nama : str, email : str) : None`
  * `+ ubah_password(password_lama : str, password_baru : str) : bool`
  * `+ verifikasi_password(password : str) : bool`
  * `+ aktifkan_akun() : None`
  * `+ nonaktifkan_akun() : None`
  * `+ {abstract} get_role() : str`
  * `+ {abstract} tampilkan_dashboard() : None`

### 3. User

`User` merupakan subclass `Pengguna` untuk pencari kerja.

* **Atribut:**
  * `- __nomor_telepon : str`
  * `- __alamat : str`
  * `- __cv_path : str`
* **Method:**
  * `+ <<property>> nomor_telepon() : str`
  * `+ <<setter>> nomor_telepon(nilai : str) : None`
  * `+ <<property>> alamat() : str`
  * `+ <<setter>> alamat(nilai : str) : None`
  * `+ <<property>> cv_path() : str`
  * `+ <<setter>> cv_path(nilai : str) : None`
  * `+ unggah_cv(path : str) : None`
  * `+ cari_lowongan(kriteria : str) : list<Lowongan>`
  * `+ kirim_lamaran(lowongan : Lowongan) : Lamaran`
  * `+ pantau_lamaran() : list<Lamaran>`
  * `+ ajukan_perusahaan(nama : str, alamat : str, email : str, deskripsi : str) : PengajuanPerusahaan`
  * `+ get_role() : str` *(override)*
  * `+ tampilkan_dashboard() : None` *(override)*

### 4. AdminPerusahaan

`AdminPerusahaan` merupakan subclass `Pengguna` yang mengelola profil perusahaan serta akun HRD di perusahaannya (tidak berwenang menyeleksi pelamar).

* **Atribut:**
  * `- __perusahaan : Perusahaan`
* **Method:**
  * `+ <<property>> perusahaan() : Perusahaan`
  * `+ kelola_profil_perusahaan(nama : str, alamat : str, deskripsi : str, email : str) : None`
  * `+ buat_akun_hrd(nama : str, email : str, password : str, jabatan : str) : HRD`
  * `+ ubah_akun_hrd(hrd : HRD, jabatan : str) : None`
  * `+ nonaktifkan_hrd(hrd : HRD) : None`
  * `+ get_role() : str` *(override)*
  * `+ tampilkan_dashboard() : None` *(override)*

### 5. HRD

`HRD` merupakan subclass `Pengguna` yang menangani publikasi lowongan kerja dan proses seleksi pelamar pada perusahaan tempatnya bekerja.

* **Atribut:**
  * `- __perusahaan : Perusahaan`
  * `- __jabatan : str`
* **Method:**
  * `+ <<property>> perusahaan() : Perusahaan`
  * `+ <<property>> jabatan() : str`
  * `+ <<setter>> jabatan(nilai : str) : None`
  * `+ buat_lowongan(posisi : str, deskripsi : str, persyaratan : str, lokasi : str, gaji_min : float, gaji_max : float) : Lowongan`
  * `+ ubah_lowongan(lowongan : Lowongan, posisi : str, deskripsi : str, persyaratan : str, lokasi : str, gaji_min : float, gaji_max : float) : None`
  * `+ buka_lowongan(lowongan : Lowongan) : None`
  * `+ tutup_lowongan(lowongan : Lowongan) : None`
  * `+ review_lamaran(lamaran : Lamaran) : dict`
  * `+ putuskan_status(lamaran : Lamaran, status : StatusLamaran, catatan : str) : None`
  * `+ get_role() : str` *(override)*
  * `+ tampilkan_dashboard() : None` *(override)*

### 6. AdminSistem

`AdminSistem` merupakan subclass `Pengguna` yang menangani administrasi tingkat aplikasi dan verifikasi pengajuan perusahaan baru.

* **Atribut:** *(Menggunakan atribut warisan dari `Pengguna`)*
* **Method:**
  * `+ verifikasi_pengajuan(pengajuan : PengajuanPerusahaan, disetujui : bool, alasan : str) : Optional<Perusahaan>`
  * `+ kelola_pengguna(pengguna : Pengguna, status_aktif : bool) : None`
  * `+ kelola_perusahaan(perusahaan : Perusahaan, status : StatusVerifikasi) : None`
  * `+ pantau_lowongan_global() : list<Lowongan>`
  * `+ get_role() : str` *(override)*
  * `+ tampilkan_dashboard() : None` *(override)*

### 7. Perusahaan

`Perusahaan` menyimpan data identitas badan usaha yang telah terdaftar serta koleksi HRD dan lowongan yang dimilikinya.

* **Atribut:**
  * `- __id_perusahaan : int`
  * `- __nama : str`
  * `- __alamat : str`
  * `- __deskripsi : str`
  * `- __email : str`
  * `- __status_verifikasi : StatusVerifikasi`
  * `- __daftar_hrd : list<HRD>`
  * `- __daftar_lowongan : list<Lowongan>`
* **Method:**
  * `+ <<property>> id_perusahaan() : int`
  * `+ <<property>> nama() : str`
  * `+ <<setter>> nama(nilai : str) : None`
  * `+ <<property>> alamat() : str`
  * `+ <<setter>> alamat(nilai : str) : None`
  * `+ <<property>> deskripsi() : str`
  * `+ <<setter>> deskripsi(nilai : str) : None`
  * `+ <<property>> email() : str`
  * `+ <<setter>> email(nilai : str) : None`
  * `+ <<property>> status_verifikasi() : StatusVerifikasi`
  * `+ <<setter>> status_verifikasi(nilai : StatusVerifikasi) : None`
  * `+ <<property>> daftar_hrd() : list<HRD>`
  * `+ <<property>> daftar_lowongan() : list<Lowongan>`
  * `+ ubah_profil(nama : str, alamat : str, deskripsi : str, email : str) : None`
  * `+ tambah_hrd(hrd : HRD) : None`
  * `+ tambah_lowongan(lowongan : Lowongan) : None`

### 8. Lowongan

`Lowongan` merepresentasikan posisi pekerjaan yang dipublikasikan oleh HRD suatu perusahaan.

* **Atribut:**
  * `- __id_lowongan : int`
  * `- __perusahaan : Perusahaan`
  * `- __dibuat_oleh : HRD`
  * `- __posisi : str`
  * `- __deskripsi : str`
  * `- __persyaratan : str`
  * `- __lokasi : str`
  * `- __gaji_min : float`
  * `- __gaji_max : float`
  * `- __status : StatusLowongan`
  * `- __tanggal_dibuat : datetime`
  * `- __tanggal_tutup : Optional<datetime>`
* **Method:**
  * `+ <<property>> id_lowongan() : int`
  * `+ <<property>> perusahaan() : Perusahaan`
  * `+ <<property>> dibuat_oleh() : HRD`
  * `+ <<property>> posisi() : str`
  * `+ <<setter>> posisi(nilai : str) : None`
  * `+ <<property>> deskripsi() : str`
  * `+ <<setter>> deskripsi(nilai : str) : None`
  * `+ <<property>> persyaratan() : str`
  * `+ <<setter>> persyaratan(nilai : str) : None`
  * `+ <<property>> lokasi() : str`
  * `+ <<setter>> lokasi(nilai : str) : None`
  * `+ <<property>> gaji_min() : float`
  * `+ <<setter>> gaji_min(nilai : float) : None`
  * `+ <<property>> gaji_max() : float`
  * `+ <<setter>> gaji_max(nilai : float) : None`
  * `+ <<property>> status() : StatusLowongan`
  * `+ <<property>> tanggal_dibuat() : datetime`
  * `+ <<property>> tanggal_tutup() : Optional<datetime>`
  * `+ ubah_detail(posisi : str, deskripsi : str, persyaratan : str, lokasi : str, gaji_min : float, gaji_max : float) : None`
  * `+ buka() : None`
  * `+ tutup() : None`
  * `+ sesuai_kriteria(kriteria : str) : bool`

### 9. Lamaran

`Lamaran` berfungsi sebagai *association class* yang menyimpan transaksi pengajuan lamaran antara `User` dan `Lowongan` beserta status seleksinya.

* **Atribut:**
  * `- __id_lamaran : int`
  * `- __pelamar : User`
  * `- __lowongan : Lowongan`
  * `- __tanggal_lamaran : datetime`
  * `- __status : StatusLamaran`
  * `- __catatan_hrd : str`
* **Method:**
  * `+ <<property>> id_lamaran() : int`
  * `+ <<property>> pelamar() : User`
  * `+ <<property>> lowongan() : Lowongan`
  * `+ <<property>> tanggal_lamaran() : datetime`
  * `+ <<property>> status() : StatusLamaran`
  * `+ <<property>> catatan_hrd() : str`
  * `+ ubah_status(status : StatusLamaran, catatan : str) : None`
  * `+ tambah_catatan(catatan : str) : None`

### 10. PengajuanPerusahaan

`PengajuanPerusahaan` menyimpan berkas permohonan pendaftaran perusahaan baru oleh `User` hingga diverifikasi oleh `AdminSistem`.

* **Atribut:**
  * `- __id_pengajuan : int`
  * `- __pemohon : User`
  * `- __nama_perusahaan : str`
  * `- __alamat_perusahaan : str`
  * `- __email_perusahaan : str`
  * `- __deskripsi_perusahaan : str`
  * `- __tanggal_pengajuan : datetime`
  * `- __status : StatusPengajuan`
  * `- __alasan_penolakan : str`
  * `- __diverifikasi_oleh : Optional<AdminSistem>`
* **Method:**
  * `+ <<property>> id_pengajuan() : int`
  * `+ <<property>> pemohon() : User`
  * `+ <<property>> nama_perusahaan() : str`
  * `+ <<property>> tanggal_pengajuan() : datetime`
  * `+ <<property>> status() : StatusPengajuan`
  * `+ <<property>> alasan_penolakan() : str`
  * `+ <<property>> diverifikasi_oleh() : Optional<AdminSistem>`
  * `+ setujui(admin : AdminSistem) : Perusahaan`
  * `+ tolak(admin : AdminSistem, alasan : str) : None`

---

## Relasi Antarclass & Multiplicity

1. **`Pengguna <|-- User`, `AdminPerusahaan`, `HRD`, `AdminSistem` (*Generalization*):** Keempat aktor mewarisi atribut dan kontrak perilaku dari kelas induk abstrak `Pengguna`.
2. **`Perusahaan "1" -- "1" AdminPerusahaan` (*Association*):** Satu perusahaan dikelola oleh satu akun Admin Perusahaan.
3. **`Perusahaan "1" *-- "1..*" HRD` (*Composition*):** Satu perusahaan memiliki minimal satu hingga banyak HRD; objek HRD terikat pada perusahaannya.
4. **`Perusahaan "1" *-- "0..*" Lowongan` (*Composition*):** Satu perusahaan dapat menyediakan nol hingga banyak lowongan pekerjaan.
5. **`HRD "1" -- "0..*" Lowongan` (*Association*):** Satu HRD dapat membuat dan mengelola banyak lowongan di perusahaannya.
6. **`User "1" -- "0..*" Lamaran` (*Association*):** Satu User dapat mengirimkan banyak lamaran ke lowongan yang berbeda.
7. **`Lowongan "1" -- "0..*" Lamaran` (*Association*):** Satu lowongan terbuka dapat menerima banyak lamaran dari berbagai pelamar.
8. **`User "1" -- "0..*" PengajuanPerusahaan` (*Association*):** Satu User dapat membuat permohonan pendaftaran perusahaan.
9. **`AdminSistem "1" -- "0..*" PengajuanPerusahaan` (*Association*):** Admin Sistem memverifikasi pengajuan pendaftaran perusahaan yang masuk.
10. **`PengajuanPerusahaan "0..1" --> "1" Perusahaan` (*Directed Association*):** Pengajuan yang disetujui oleh Admin Sistem menghasilkan satu entitas `Perusahaan` baru.

---

## PlantUML

Salin kode PlantUML berikut ke **draw.io** melalui menu **Arrange > Insert > Advanced > PlantUML**:

```plantuml
@startuml
skinparam classAttributeIconSize 0
skinparam linetype ortho
skinparam shadowing false
skinparam monochrome false
skinparam class {
    BackgroundColor #F8FAFC
    BorderColor #334155
    HeaderBackgroundColor #E2E8F0
    ArrowColor #475569
}

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
    MENUNGGU_REVIEW
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
    # _id_pengguna : int
    # _nama : str
    # _email : str
    - __password_hash : str
    # _status_aktif : bool
    --
    + <<property>> id_pengguna() : int
    + <<property>> nama() : str
    + <<setter>> nama(nilai : str) : None
    + <<property>> email() : str
    + <<setter>> email(nilai : str) : None
    + <<property>> status_aktif() : bool
    + ubah_profil(nama : str, email : str) : None
    + ubah_password(password_lama : str, password_baru : str) : bool
    + verifikasi_password(password : str) : bool
    + aktifkan_akun() : None
    + nonaktifkan_akun() : None
    + {abstract} get_role() : str
    + {abstract} tampilkan_dashboard() : None
}

' =========================
' USER ROLES
' =========================
class User {
    - __nomor_telepon : str
    - __alamat : str
    - __cv_path : str
    --
    + <<property>> nomor_telepon() : str
    + <<setter>> nomor_telepon(nilai : str) : None
    + <<property>> alamat() : str
    + <<setter>> alamat(nilai : str) : None
    + <<property>> cv_path() : str
    + <<setter>> cv_path(nilai : str) : None
    + unggah_cv(path : str) : None
    + cari_lowongan(kriteria : str) : list<Lowongan>
    + kirim_lamaran(lowongan : Lowongan) : Lamaran
    + pantau_lamaran() : list<Lamaran>
    + ajukan_perusahaan(nama : str, alamat : str, email : str, deskripsi : str) : PengajuanPerusahaan
    + get_role() : str
    + tampilkan_dashboard() : None
}

class AdminPerusahaan {
    - __perusahaan : Perusahaan
    --
    + <<property>> perusahaan() : Perusahaan
    + kelola_profil_perusahaan(nama : str, alamat : str, deskripsi : str, email : str) : None
    + buat_akun_hrd(nama : str, email : str, password : str, jabatan : str) : HRD
    + ubah_akun_hrd(hrd : HRD, jabatan : str) : None
    + nonaktifkan_hrd(hrd : HRD) : None
    + get_role() : str
    + tampilkan_dashboard() : None
}

class HRD {
    - __perusahaan : Perusahaan
    - __jabatan : str
    --
    + <<property>> perusahaan() : Perusahaan
    + <<property>> jabatan() : str
    + <<setter>> jabatan(nilai : str) : None
    + buat_lowongan(posisi : str, deskripsi : str, persyaratan : str, lokasi : str, gaji_min : float, gaji_max : float) : Lowongan
    + ubah_lowongan(lowongan : Lowongan, posisi : str, deskripsi : str, persyaratan : str, lokasi : str, gaji_min : float, gaji_max : float) : None
    + buka_lowongan(lowongan : Lowongan) : None
    + tutup_lowongan(lowongan : Lowongan) : None
    + review_lamaran(lamaran : Lamaran) : dict
    + putuskan_status(lamaran : Lamaran, status : StatusLamaran, catatan : str) : None
    + get_role() : str
    + tampilkan_dashboard() : None
}

class AdminSistem {
    + verifikasi_pengajuan(pengajuan : PengajuanPerusahaan, disetujui : bool, alasan : str) : Optional<Perusahaan>
    + kelola_pengguna(pengguna : Pengguna, status_aktif : bool) : None
    + kelola_perusahaan(perusahaan : Perusahaan, status : StatusVerifikasi) : None
    + pantau_lowongan_global() : list<Lowongan>
    + get_role() : str
    + tampilkan_dashboard() : None
}

' =========================
' DOMAIN ENTITIES
' =========================
class Perusahaan {
    - __id_perusahaan : int
    - __nama : str
    - __alamat : str
    - __deskripsi : str
    - __email : str
    - __status_verifikasi : StatusVerifikasi
    - __daftar_hrd : list<HRD>
    - __daftar_lowongan : list<Lowongan>
    --
    + <<property>> id_perusahaan() : int
    + <<property>> nama() : str
    + <<setter>> nama(nilai : str) : None
    + <<property>> alamat() : str
    + <<setter>> alamat(nilai : str) : None
    + <<property>> deskripsi() : str
    + <<setter>> deskripsi(nilai : str) : None
    + <<property>> email() : str
    + <<setter>> email(nilai : str) : None
    + <<property>> status_verifikasi() : StatusVerifikasi
    + <<setter>> status_verifikasi(nilai : StatusVerifikasi) : None
    + <<property>> daftar_hrd() : list<HRD>
    + <<property>> daftar_lowongan() : list<Lowongan>
    + ubah_profil(nama : str, alamat : str, deskripsi : str, email : str) : None
    + tambah_hrd(hrd : HRD) : None
    + tambah_lowongan(lowongan : Lowongan) : None
}

class Lowongan {
    - __id_lowongan : int
    - __perusahaan : Perusahaan
    - __dibuat_oleh : HRD
    - __posisi : str
    - __deskripsi : str
    - __persyaratan : str
    - __lokasi : str
    - __gaji_min : float
    - __gaji_max : float
    - __status : StatusLowongan
    - __tanggal_dibuat : datetime
    - __tanggal_tutup : Optional<datetime>
    --
    + <<property>> id_lowongan() : int
    + <<property>> perusahaan() : Perusahaan
    + <<property>> dibuat_oleh() : HRD
    + <<property>> posisi() : str
    + <<setter>> posisi(nilai : str) : N
