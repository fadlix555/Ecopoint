# SDD_EcoPoint_v1.2_Revisi.md

Berikut adalah versi **raw Markdown** yang bisa Anda salin langsung sebagai file `.md`.

```plain
# Software Design Description
## EcoPoint — Sistem Informasi Daur Ulang Sampah Berbasis Poin

> **Version:** 1.2A  
> **Prepared by:** Tim Pengembang EcoPoint  
> **Organization:** Tim Pengembang EcoPoint — Bandung, Jawa Barat, Indonesia  
> **Date:** Juni 2026  
> **Catatan Revisi:** Struktur dokumen dipertahankan mengikuti SDD lama. Penambahan pada revisi ini hanya menyisipkan UML dan desain UI ke bagian yang relevan tanpa mengubah susunan bab utama.

---

## Table of Contents
- <a href="1-introduction" type="user" notify="false">@1. Introduction</a>
 - <a href="11-document-purpose" type="user" notify="false">@1.1 Document Purpose</a>
 - <a href="12-subject-scope" type="user" notify="false">@1.2 Subject Scope</a>
 - <a href="13-definitions-acronyms-and-abbreviations" type="user" notify="false">@1.3 Definitions, Acronyms, and Abbreviations</a>
 - <a href="14-references" type="user" notify="false">@1.4 References</a>
 - <a href="15-document-overview" type="user" notify="false">@1.5 Document Overview</a>
- <a href="2-design-overview" type="user" notify="false">@2. Design Overview</a>
 - <a href="21-stakeholder-concerns" type="user" notify="false">@2.1 Stakeholder Concerns</a>
 - <a href="22-selected-viewpoints" type="user" notify="false">@2.2 Selected Viewpoints</a>
- <a href="3-design-views" type="user" notify="false">@3. Design Views</a>
 - <a href="31-context-view" type="user" notify="false">@3.1 Context View</a>
 - <a href="32-composition-view" type="user" notify="false">@3.2 Composition View</a>
 - <a href="33-logical-view" type="user" notify="false">@3.3 Logical View</a>
 - <a href="34-information-view" type="user" notify="false">@3.4 Information View</a>
 - <a href="35-interface-view" type="user" notify="false">@3.5 Interface View</a>
 - <a href="36-interaction-view" type="user" notify="false">@3.6 Interaction View</a>
 - <a href="37-algorithm-view" type="user" notify="false">@3.7 Algorithm View</a>
 - <a href="38-state-dynamics-view" type="user" notify="false">@3.8 State Dynamics View</a>
 - <a href="39-deployment-view" type="user" notify="false">@3.9 Deployment View</a>
- <a href="4-decisions" type="user" notify="false">@4. Decisions</a>
 - <a href="dec-001-arsitektur-web-monolitik-berbasis-php" type="user" notify="false">@DEC-001: Arsitektur Web Monolitik Berbasis PHP</a>
 - <a href="dec-002-basis-data-relasional-mysqlmariadb" type="user" notify="false">@DEC-002: Basis Data Relasional MySQL/MariaDB</a>
 - <a href="dec-003-penimbangan-manual-oleh-admin-gudang" type="user" notify="false">@DEC-003: Penimbangan Manual oleh Admin Gudang</a>
- <a href="5-appendixes" type="user" notify="false">@5. Appendixes</a>
 - <a href="appendix-a-ringkasan-kelayakan-telos" type="user" notify="false">@Appendix A: Ringkasan Kelayakan TELOS</a>
 - <a href="appendix-b-tim-pengembang-ecopoint" type="user" notify="false">@Appendix B: Tim Pengembang EcoPoint</a>
 - <a href="appendix-c-spesifikasi-teknis-minimum" type="user" notify="false">@Appendix C: Spesifikasi Teknis Minimum</a>
 - <a href="appendix-d-timeline-pengembangan" type="user" notify="false">@Appendix D: Timeline Pengembangan</a>

---

## 1. Introduction

### 1.1 Document Purpose
Dokumen *Software Design Description* (SDD) ini menjabarkan arsitektur dan rancangan teknis sistem **EcoPoint** — Sistem Informasi Daur Ulang Sampah Berbasis Poin. Dokumen ini disusun sebagai acuan implementasi bagi tim pengembang dan sebagai referensi pemeliharaan bagi operator sistem.

Pada revisi ini, struktur SDD lama **tetap dipertahankan**, tetapi dilengkapi dengan artefak visual berikut:
- **Use Case Diagram**
- **Class Diagram**
- **Sequence Diagram**
- **Activity Diagram**
- **UI Screen Designs / Wireframe**

> **Catatan Revisi v1.2A:** Revisi ini tetap mengikuti model **timbangan manual**. Admin Gudang menginput hasil timbangan secara manual ke sistem, lalu Super Admin menambahkan poin berdasarkan data tersebut. Tidak ada integrasi perangkat IoT.

### 1.2 Subject Scope
Sistem yang dirancang adalah **EcoPoint v1.2A**, platform web responsif untuk manajemen bank sampah digital. Tujuan utamanya adalah mendigitalisasi seluruh alur kerja bank sampah, mulai dari registrasi pengguna, pengajuan setoran, penimbangan manual oleh Admin Gudang, input kilogram aktual, penambahan poin oleh Super Admin, pencairan poin, hingga pengelolaan inventori dan penjualan ke mitra.

**Alur inti sistem EcoPoint:**
1. Pengguna mendaftar dan memiliki akun.
2. Pengguna mengisi formulir setoran dengan jenis sampah dan estimasi berat.
3. Pengguna mengantarkan sampah ke gudang.
4. Admin Gudang menimbang sampah secara manual.
5. Admin Gudang menginput berat aktual ke sistem.
6. Super Admin meninjau data timbangan dan menambahkan poin.
7. Pengguna dapat melihat saldo poin dan mengajukan redeem.
8. Sampah yang terkumpul masuk inventori dan dapat dijual ke mitra.

### 1.3 Definitions, Acronyms, and Abbreviations
| Istilah / Singkatan | Definisi |
|---|---|
| **EcoPoint** | Sistem informasi daur ulang sampah berbasis poin digital. |
| **Formulir Setoran** | Form online yang diisi pengguna sebelum mengantarkan sampah. |
| **Timbangan Manual** | Timbangan konvensional yang dibaca Admin Gudang lalu diinput ke sistem. |
| **Poin** | Insentif digital yang diperoleh pengguna dari hasil setoran sampah. |
| **Redeem** | Pencairan poin ke e-wallet atau uang tunai. |
| **RBAC** | *Role-Based Access Control*. |
| **SDD** | *Software Design Description*. |
| **SRS** | *Software Requirement Specification*. |

### 1.4 References
| No | Judul Dokumen | Penulis / Pemilik | Tipe |
|---|---|---|---|
| 1 | Software Requirement Specification (SRS) EcoPoint v1.0 | Tim Pengembang EcoPoint, 2026 | Normatif |
| 2 | Studi Kelayakan & System Request EcoPoint | Tim Pengembang EcoPoint, 2026 | Normatif |
| 3 | UU No. 18 Tahun 2008 tentang Pengelolaan Sampah | Pemerintah Republik Indonesia, 2008 | Normatif |
| 4 | UU No. 27 Tahun 2022 tentang Perlindungan Data Pribadi | Pemerintah Republik Indonesia, 2022 | Normatif |

### 1.5 Document Overview
Dokumen ini tetap dibagi menjadi lima bagian utama:
- **Bagian 1 (Introduction)** — konteks, ruang lingkup, dan referensi.
- **Bagian 2 (Design Overview)** — kekhawatiran pemangku kepentingan dan viewpoint yang dipilih.
- **Bagian 3 (Design Views)** — elemen desain utama sistem per viewpoint, termasuk tambahan UML dan desain UI.
- **Bagian 4 (Decisions)** — keputusan arsitektur signifikan.
- **Bagian 5 (Appendixes)** — material pendukung.

---

## 2. Design Overview

### 2.1 Stakeholder Concerns
| Pemangku Kepentingan | Kekhawatiran Utama | Viewpoint yang Menjawab |
|---|---|---|
| **User** | Kemudahan setoran, transparansi saldo, kemudahan redeem | Context, Interface, Interaction |
| **Admin Gudang** | Kemudahan input timbangan manual dan kelola inventori | Composition, Interaction, State Dynamics |
| **Super Admin** | Kontrol penambahan poin, approval redeem, laporan | Information, Interface, Interaction |
| **Tim Pengembang** | Modularitas kode, maintainability, dan kejelasan model | Logical, Deployment |

### 2.2 Selected Viewpoints
- **Context**: batas sistem dan aktor eksternal.
- **Composition**: dekomposisi modul utama.
- **Logical**: struktur entitas dan relasi.
- **Information**: skema data persisten.
- **Interface**: antarmuka eksternal dan desain layar.
- **Interaction**: alur runtime utama.
- **Algorithm**: kalkulasi poin.
- **State Dynamics**: perubahan status deposit dan redeem.
- **Deployment**: topologi eksekusi sistem.

---

## 3. Design Views

### 3.1 Context View
> **Viewpoint:** Context | **Bahasa:** UML Use Case + deskripsi konteks

Sistem EcoPoint berinteraksi dengan lima entitas eksternal utama:
- **User**
- **Admin Gudang**
- **Super Admin**
- **Payment Gateway / API E-Wallet**
- **Mitra Penjualan**

#### Use Case Utama
| Kode | Use Case | Aktor Utama | Tujuan |
|---|---|---|---|
| UC-01 | Mendaftar Akun | User | Membuat akun baru |
| UC-02 | Login | User, Admin Gudang, Super Admin | Mengakses sistem |
| UC-03 | Isi Formulir Setoran | User | Mengajukan setoran |
| UC-04 | Lihat Saldo & Riwayat Poin | User | Memantau poin |
| UC-05 | Ajukan Redeem Poin | User | Mengajukan pencairan |
| UC-06 | Input Berat Aktual Setoran | Admin Gudang | Memasukkan hasil timbang manual |
| UC-07 | Tolak Setoran | Admin Gudang | Menolak setoran tidak valid |
| UC-08 | Review Data Timbangan | Super Admin | Meninjau data sebelum tambah poin |
| UC-09 | Tambah Poin ke Akun User | Super Admin | Mengkredit saldo user |
| UC-10 | Kelola Harga per Kg & Persentase Insentif | Super Admin | Mengatur konfigurasi harga |
| UC-11 | Setujui Redeem | Super Admin | Menyetujui pencairan |
| UC-12 | Tolak Redeem | Super Admin | Menolak pencairan |
| UC-13 | Kelola Inventori | Admin Gudang / Super Admin | Memantau stok |
| UC-14 | Catat Penjualan ke Mitra | Admin Gudang | Mencatat transaksi penjualan |
| UC-15 | Proses Transfer E-Wallet | Payment Gateway | Memproses payout |

#### Relasi Penting
- **Tambah Poin ke Akun User** *include* **Review Data Timbangan**.
- **Setujui Redeem** *include* **Proses Transfer E-Wallet**.

#### Diagram
![Use Case Diagram](https://t90182767054.p.clickup-attachments.com/t90182767054/b9c56294-bb0a-40db-8257-f1b56fe0603d/generated-image-869268ee-0d3f-42f0-8125-b9c4e9264cf1.png?view=open)

### 3.2 Composition View
> **Viewpoint:** Composition | **Bahasa:** Dekomposisi Komponen

Komponen utama EcoPoint terdiri dari:
- **Lapisan Presentasi**: halaman registrasi/login, dashboard user, panel admin gudang, panel super admin.
- **Lapisan Logika Bisnis**: autentikasi, formulir setoran, input timbangan manual, kalkulasi poin, redeem, inventori, penjualan mitra, konfigurasi harga, notifikasi.
- **Lapisan Data**: MySQL / MariaDB.
- **Integrasi Eksternal**: API e-wallet.

### 3.3 Logical View
> **Viewpoint:** Logical | **Bahasa:** Class / Domain Model

#### Entitas Utama
- **User**: `id`, `nama`, `no_telepon`, `email`, `password_hash`, `poin_saldo`, `peran`, `status`, `created_at`
- **Deposit**: `id`, `user_id`, `jenis_sampah`, `estimasi_berat_kg`, `berat_aktual_kg`, `poin_diperoleh`, `status`, `admin_gudang_id`, `super_admin_id`, `created_at`
- **PointTransaction**: `id`, `user_id`, `deposit_id`, `tipe`, `jumlah_poin`, `ditambahkan_oleh`, `keterangan`, `created_at`
- **Redemption**: `id`, `user_id`, `jumlah_poin`, `metode`, `no_ewallet`, `status`, `diproses_oleh`, `created_at`
- **InventoryItem**: `id`, `jenis_sampah`, `jumlah_kg`, `status_stok`, `admin_id`, `created_at`
- **PartnerSale**: `id`, `jenis_sampah`, `jumlah_kg`, `harga_jual_per_kg`, `total_nilai`, `mitra_tujuan`, `admin_id`, `tanggal_penjualan`
- **PriceSetting**: `id`, `jenis_sampah`, `harga_per_kg`, `persentase_insentif`, `updated_by`, `updated_at`

#### Relasi Utama
- `User 1 -> N Deposit`
- `User (Admin Gudang) 1 -> N Deposit`
- `User (Super Admin) 1 -> N Deposit`
- `User 1 -> N PointTransaction`
- `User 1 -> N Redemption`
- `Deposit 1 -> 0..1 PointTransaction`
- `PriceSetting 1 -> N Deposit`
- `InventoryItem 1 -> N PartnerSale`

#### Diagram
![Class Diagram](https://t90182767054.p.clickup-attachments.com/t90182767054/aeef25a5-c88b-45f2-b7d5-8fb50ba67f9d/generated-image-55ea74a7-90c0-4200-84c9-1a118566cf2e.png?view=open)

### 3.4 Information View
> **Viewpoint:** Information | **Bahasa:** Skema Data

Tabel persisten utama:
- `users`
- `deposits`
- `point_transactions`
- `redemptions`
- `inventory_items`
- `partner_sales`
- `price_settings`

Prinsip integritas data:
- `FOREIGN KEY` pada relasi antar entitas
- `NOT NULL` untuk atribut wajib
- `UNIQUE` pada `users.email`
- transaksi database pada proses tambah poin dan redeem

### 3.5 Interface View
> **Viewpoint:** Interface | **Bahasa:** REST API + UI Screen Design

#### Endpoint Utama
| Method | Endpoint | Deskripsi | Peran |
|---|---|---|---|
| `POST` | `/api/auth/register` | Registrasi akun | Publik |
| `POST` | `/api/auth/login` | Login | Semua |
| `POST` | `/api/deposits` | Membuat pengajuan setoran | User |
| `POST` | `/api/deposits/{id}/weigh` | Input berat aktual | Admin Gudang |
| `POST` | `/api/deposits/{id}/add-points` | Tambah poin | Super Admin |
| `POST` | `/api/redemptions` | Ajukan redeem | User |
| `PUT` | `/api/redemptions/{id}/approve` | Setujui redeem | Super Admin |
| `PUT` | `/api/redemptions/{id}/reject` | Tolak redeem | Super Admin |
| `GET` | `/api/inventory` | Lihat inventori | Admin Gudang, Super Admin |
| `POST` | `/api/inventory/sell` | Catat penjualan ke mitra | Admin Gudang |

#### UI Screen Designs
**Dashboard User**
- kartu saldo poin
- ringkasan setoran aktif
- redeem terakhir
- tabel riwayat setoran
- tombol buat setoran baru

**Form Setoran User**
- dropdown jenis sampah
- input estimasi berat
- textarea catatan
- tombol simpan draft dan kirim setoran

**Panel Admin Gudang**
- tabel antrian setoran
- panel detail setoran
- input berat aktual
- tombol simpan berat dan tolak setoran
- ringkasan inventori

**Panel Super Admin**
- tabel menunggu poin
- preview poin breakdown
- tombol tambah poin
- approval redeem
- reporting summary

#### Wireframe
![UI Wireframe](https://t90182767054.p.clickup-attachments.com/t90182767054/cb8f61bc-189c-43f1-badf-d07aba476ad7/generated-image-0e1da234-d124-4714-bfa3-9929add6eda9.png?view=open)

### 3.6 Interaction View
> **Viewpoint:** Interaction | **Bahasa:** Sequence Diagram

#### Alur Lengkap Setoran Sampah
1. User mengisi formulir setoran.
2. Sistem menyimpan deposit dengan status `menunggu_verifikasi`.
3. User mengantar sampah ke gudang.
4. Admin Gudang menimbang manual.
5. Admin Gudang menginput berat aktual.
6. Sistem mengubah status menjadi `menunggu_poin`.
7. Sistem mengirim notifikasi ke Super Admin.
8. Super Admin membuka review dan melihat preview poin.
9. Sistem membaca data deposit dan price setting.
10. Super Admin mengonfirmasi tambah poin.
11. Sistem menjalankan transaksi atomik.
12. User menerima notifikasi sukses.

#### Diagram
![Sequence Diagram](https://t90182767054.p.clickup-attachments.com/t90182767054/4e070753-101a-4b48-9be4-3a2eebca4ce0/generated-image-f2dc0c1f-8178-4f52-990a-1fdcad4430a5.png?view=open)

### 3.7 Algorithm View
> **Viewpoint:** Algorithm | **Bahasa:** Ringkasan Algoritma

Formula utama:  
`Poin = Berat (kg) × Harga per kg × Persentase Insentif (%)`

Langkah kalkulasi:
1. Ambil deposit dengan status `menunggu_poin`.
2. Ambil harga aktif berdasarkan `jenis_sampah`.
3. Hitung nilai sampah dan poin.
4. Tampilkan preview ke Super Admin.
5. Jika dikonfirmasi, update saldo user, catat `PointTransaction`, ubah status deposit, dan tambahkan inventori.

### 3.8 State Dynamics View
> **Viewpoint:** State Dynamics | **Bahasa:** State Flow + Activity Diagram

#### State Deposit
- `(Awal) -> Menunggu Verifikasi`
- `Menunggu Verifikasi -> Menunggu Poin`
- `Menunggu Verifikasi -> Ditolak`
- `Menunggu Poin -> Selesai`

#### State Redemption
- `(Awal) -> Menunggu Persetujuan`
- `Menunggu Persetujuan -> Disetujui`
- `Menunggu Persetujuan -> Ditolak`

#### Diagram Aktivitas
![Activity Diagram](https://t90182767054.p.clickup-attachments.com/t90182767054/d6861be1-f60e-430f-8bdc-e5b81271b5ff/generated-image-41bdeb10-63b4-4ae5-af32-d561b70bb329.png?view=open)

### 3.9 Deployment View
> **Viewpoint:** Deployment | **Bahasa:** Deskripsi Infrastruktur

Node utama:
- **Cloud Server (Linux)**: aplikasi PHP, web server, MySQL/MariaDB
- **Perangkat User/Admin/Super Admin**: browser pada smartphone atau PC
- **Timbangan Manual**: perangkat fisik yang tidak terhubung jaringan
- **Layanan Eksternal**: API GoPay / OVO / DANA

---

## 4. Decisions

### DEC-001: Arsitektur Web Monolitik Berbasis PHP
- Dipilih karena sederhana untuk tim kecil, deployment mudah, dan cukup untuk skala awal.

### DEC-002: Basis Data Relasional MySQL/MariaDB
- Dipilih karena mendukung ACID transaction, relasi data jelas, dan sesuai ekosistem PHP.

### DEC-003: Penimbangan Manual oleh Admin Gudang
- Dipilih untuk menghilangkan kompleksitas IoT, menurunkan biaya, dan mempercepat implementasi.

---

## 5. Appendixes

### Appendix A: Ringkasan Kelayakan TELOS
- Technical: 9.0
- Economic: 9.0
- Legal: 10.0
- Operational: 8.5
- Schedule: 9.5

### Appendix B: Tim Pengembang EcoPoint
- Product Owner
- Project Manager
- System Analyst
- Programmer
- Tester

### Appendix C: Spesifikasi Teknis Minimum
- Browser modern
- Smartphone/PC untuk user dan admin
- Cloud server Linux
- MySQL / MariaDB
- Timbangan manual standar

### Appendix D: Timeline Pengembangan
- Bulan 1: Perancangan
- Bulan 2: Implementasi inti
- Bulan 3: Implementasi lanjutan
- Bulan 4: Testing dan deployment

---

**Catatan Akhir**
- Dokumen ini digunakan sebagai **pengganti file SDD lama** dengan **struktur yang tetap sama**.
- Tambahan UML dan desain UI sudah disisipkan ke bagian yang relevan tanpa membuat struktur bab baru di luar pola SDD sebelumnya.
```

Jika Anda ingin, saya juga bisa lanjut buatkan versi **lebih lengkap** dengan seluruh isi SDD awal yang dipertahankan kata per kata lalu ditambahkan bagian UML/UI di dalamnya.