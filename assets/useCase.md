# 

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