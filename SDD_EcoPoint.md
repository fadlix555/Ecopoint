# Software Design Description
## EcoPoint — Sistem Informasi Daur Ulang Sampah Berbasis Poin

> **Version:** 1.1
> **Prepared by:** Tim Pengembang EcoPoint
> **Organization:** Tim Pengembang EcoPoint — Bandung, Jawa Barat, Indonesia
> **Date:** Juni 2026

---

## Table of Contents

- [1. Introduction](#1-introduction)
  - [1.1 Document Purpose](#11-document-purpose)
  - [1.2 Subject Scope](#12-subject-scope)
  - [1.3 Definitions, Acronyms, and Abbreviations](#13-definitions-acronyms-and-abbreviations)
  - [1.4 References](#14-references)
  - [1.5 Document Overview](#15-document-overview)
- [2. Design Overview](#2-design-overview)
  - [2.1 Stakeholder Concerns](#21-stakeholder-concerns)
  - [2.2 Selected Viewpoints](#22-selected-viewpoints)
- [3. Design Views](#3-design-views)
  - [3.1 Context View](#31-context-view)
  - [3.2 Composition View](#32-composition-view)
  - [3.3 Logical View](#33-logical-view)
  - [3.4 Information View](#34-information-view)
  - [3.5 Interface View](#35-interface-view)
  - [3.6 Interaction View](#36-interaction-view)
  - [3.7 Algorithm View](#37-algorithm-view)
  - [3.8 State Dynamics View](#38-state-dynamics-view)
  - [3.9 Deployment View](#39-deployment-view)
- [4. Decisions](#4-decisions)
  - [DEC-001: Arsitektur Web Monolitik Berbasis PHP](#dec-001-arsitektur-web-monolitik-berbasis-php)
  - [DEC-002: Basis Data Relasional MySQL/MariaDB](#dec-002-basis-data-relasional-mysqlmariadb)
  - [DEC-003: Integrasi IoT via HTTP REST](#dec-003-integrasi-iot-via-http-rest)
- [5. Appendixes](#5-appendixes)
  - [Appendix A: Ringkasan Kelayakan TELOS](#appendix-a-ringkasan-kelayakan-telos)
  - [Appendix B: Tim Pengembang EcoPoint](#appendix-b-tim-pengembang-ecopoint)
  - [Appendix C: Spesifikasi Teknis Minimum](#appendix-c-spesifikasi-teknis-minimum)

---

## 1. Introduction

### 1.1 Document Purpose

Dokumen *Software Design Description* (SDD) ini menjabarkan arsitektur dan rancangan teknis sistem **EcoPoint** — Sistem Informasi Daur Ulang Sampah Berbasis Poin. Dokumen ini disusun sebagai acuan implementasi bagi tim pengembang dan sebagai referensi pemeliharaan bagi operator sistem.

Audiens utama dokumen ini meliputi:

- **Programmer / IoT Engineer** — mengimplementasikan komponen front-end, back-end, dan integrasi IoT.
- **System Analyst** — merancang dan memvalidasi arsitektur.
- **Tester** — memverifikasi kesesuaian implementasi dengan desain.
- **Project Manager & Product Owner** — memerlukan pemahaman teknis tingkat tinggi.

Dokumen ini merupakan tindak lanjut dari dokumen **SRS EcoPoint** dan **Studi Kelayakan (Feasibility Study)** yang telah disusun sebelumnya.

---

### 1.2 Subject Scope

Sistem yang dirancang dalam dokumen ini adalah **EcoPoint v1.0**, sebuah platform web responsif untuk manajemen bank sampah digital. Tujuan utama sistem adalah mengotomatisasi seluruh alur kerja bank sampah — mulai dari registrasi pengguna, pengajuan setoran sampah melalui formulir online, penimbangan oleh tim EcoPoint menggunakan perangkat IoT, kalkulasi poin, hingga pencairan poin ke dompet digital (e-wallet) atau uang tunai.

**Alur inti sistem EcoPoint:**

1. Pengguna mendaftar dan memiliki akun di platform EcoPoint.
2. Pengguna mengisi formulir setoran: memilih jenis sampah (organik/anorganik) dan memperkirakan berat dalam kilogram.
3. Pengguna mengirimkan/mengantarkan sampah yang telah dikumpulkan ke gudang EcoPoint.
4. Tim EcoPoint (Admin Gudang) menimbang sampah menggunakan timbangan digital yang terintegrasi IoT.
5. Sistem menghitung poin secara otomatis berdasarkan berat aktual, harga sampah per kilogram, dan persentase insentif.
6. Poin masuk ke saldo akun pengguna dan dapat dicairkan menjadi saldo e-wallet (GoPay/OVO/DANA) atau uang tunai.
7. Sampah yang terkumpul dijual oleh tim EcoPoint: sampah organik ke tempat pembuatan pupuk kompos, sampah anorganik ke pengepul atau pendaur ulang.

**Kapabilitas utama sistem mencakup:**

- Integrasi real-time dengan timbangan digital berbasis ESP32/Arduino.
- Kalkulasi poin otomatis berdasarkan formula: `Poin = Berat (kg) × Harga per kg × Persentase Insentif (%)`.
- Manajemen inventori sampah organik dan anorganik beserta pencatatan penjualan ke mitra.
- Pencairan poin ke e-wallet (GoPay, OVO, DANA) atau tunai.
- Pelaporan dampak lingkungan (opsional, fase berikutnya).

**Sistem EcoPoint mencakup:**

- Aplikasi web responsif (front-end) untuk tiga peran: User, Admin Gudang, Super Admin.
- Layanan back-end berbasis PHP dengan basis data MySQL/MariaDB.
- Modul integrasi IoT untuk komunikasi real-time dengan timbangan digital.
- API untuk integrasi dengan layanan e-wallet (GoPay, OVO, DANA).

**Sistem EcoPoint tidak mencakup:**

- Sistem logistik atau penjemputan sampah dari lokasi pengguna (pengguna mengantarkan sendiri).
- Pembayaran langsung ke rekening bank konvensional.
- Penjualan langsung produk pupuk kompos kepada konsumen akhir.

---

### 1.3 Definitions, Acronyms, and Abbreviations

| Istilah / Singkatan | Definisi |
|---|---|
| **EcoPoint** | Nama sistem informasi daur ulang sampah berbasis poin digital yang dibangun. |
| **Bank Sampah** | Fasilitas/gudang tempat masyarakat menyetorkan sampah untuk ditimbang dan dikonversi menjadi poin. |
| **Poin** | Satuan insentif digital yang diperoleh pengguna dari hasil setoran sampah, setara dengan nilai rupiah tertentu. |
| **Formulir Setoran** | Form online yang diisi pengguna sebelum mengantarkan sampah; berisi jenis sampah dan estimasi berat. |
| **Sampah Organik** | Sampah yang dapat terurai secara biologis (sisa makanan, daun, dll); dijual ke tempat pembuatan pupuk kompos. |
| **Sampah Anorganik** | Sampah yang tidak mudah terurai (plastik, logam, kertas, kaca, dll); dijual ke pengepul atau pendaur ulang. |
| **Persentase Insentif** | Persentase dari nilai jual sampah yang diberikan kepada pengguna sebagai poin. Dikonfigurasi oleh Super Admin. |
| **IoT** | *Internet of Things* — teknologi yang memungkinkan perangkat fisik terhubung ke internet dan mengirim data secara real-time. |
| **E-Wallet** | Dompet digital (GoPay, OVO, DANA) yang digunakan sebagai tujuan pencairan saldo poin. |
| **Redeem** | Proses pencairan poin menjadi saldo e-wallet atau uang tunai oleh pengguna. |
| **Super Admin** | Pengguna dengan hak akses tertinggi; mengelola seluruh data, konfigurasi harga, dan menyetujui pencairan poin. |
| **Admin Gudang** | Petugas lapangan yang menerima setoran sampah, memverifikasi berat via timbangan IoT, dan mengelola inventori. |
| **Mitra Penjualan** | Pihak ketiga pembeli sampah dari EcoPoint: pabrik pupuk kompos (organik) dan pengepul/pendaur ulang (anorganik). |
| **ESP32/Arduino** | Mikrokontroler yang digunakan sebagai platform perangkat IoT timbangan digital. |
| **SDD** | *Software Design Description* — dokumen rancangan perangkat lunak ini. |
| **SRS** | *Software Requirement Specification* — dokumen kebutuhan perangkat lunak EcoPoint. |
| **FR** | *Functional Requirement* — kebutuhan fungsional. |
| **UCP** | *Use Case Point* — metode estimasi ukuran dan waktu pengembangan. |
| **API** | *Application Programming Interface* — antarmuka pemrograman untuk integrasi layanan. |
| **RBAC** | *Role-Based Access Control* — kontrol akses berbasis peran pengguna. |
| **TELOS** | *Technical, Economic, Legal, Operational, Schedule* — kerangka penilaian kelayakan proyek. |
| **UU PDP** | Undang-Undang Perlindungan Data Pribadi No. 27 Tahun 2022. |

---

### 1.4 References

| No | Judul Dokumen | Penulis / Pemilik | Tipe |
|---|---|---|---|
| 1 | Software Requirement Specification (SRS) EcoPoint v1.0 | Tim Pengembang EcoPoint, 2026 | Normatif |
| 2 | Studi Kelayakan & System Request EcoPoint | Tim Pengembang EcoPoint, 2026 | Normatif |
| 3 | UU No. 18 Tahun 2008 tentang Pengelolaan Sampah | Pemerintah Republik Indonesia, 2008 | Normatif |
| 4 | UU No. 27 Tahun 2022 tentang Perlindungan Data Pribadi (PDP) | Pemerintah Republik Indonesia, 2022 | Normatif |
| 5 | *Systems Analysis and Design* (8th ed.) | Kendall & Kendall, Pearson Education, 2011 | Informatif |

---

### 1.5 Document Overview

Dokumen SDD ini dibagi menjadi lima bagian utama:

- **Bagian 1 (Introduction)** — konteks, ruang lingkup, dan referensi.
- **Bagian 2 (Design Overview)** — kekhawatiran pemangku kepentingan dan viewpoint yang dipilih.
- **Bagian 3 (Design Views)** — elemen desain utama sistem per viewpoint.
- **Bagian 4 (Decisions)** — keputusan arsitektur signifikan beserta rasionalnya.
- **Bagian 5 (Appendixes)** — material pendukung (TELOS, tim, spesifikasi teknis).

---

## 2. Design Overview

### 2.1 Stakeholder Concerns

| Pemangku Kepentingan | Kekhawatiran Utama | Viewpoint yang Menjawab |
|---|---|---|
| **User (Masyarakat)** | Kemudahan pengisian formulir setoran, transparansi saldo poin, keamanan data pribadi, kemudahan pencairan ke e-wallet | Context, Interface, Interaction |
| **Admin Gudang** | Keandalan integrasi IoT timbangan, kemudahan verifikasi berat, pengelolaan inventori sampah per jenis | Composition, Interaction, State Dynamics |
| **Super Admin** | Konfigurasi harga sampah dan persentase insentif, persetujuan pencairan poin, laporan inventori dan penjualan ke mitra | Composition, Information, Interface |
| **Mitra Penjualan (Pabrik Pupuk / Pengepul)** | Keakuratan data inventori dan tonase sampah yang tersedia per jenis | Information |
| **Tim Pengembang** | Modularitas kode, kemudahan integrasi IoT, maintainability, skalabilitas | Logical, Deployment |
| **Project Manager** | Estimasi waktu implementasi, risiko teknis integrasi hardware | Composition, Deployment |
| **Product Owner / Sponsor** | ROI dari penjualan sampah ke mitra, dampak lingkungan, kepatuhan hukum (UU PDP & UU Sampah) | Context, Information, Deployment |

---

### 2.2 Selected Viewpoints

#### 2.2.1 Context
Mendefinisikan sistem EcoPoint sebagai kotak hitam, mengidentifikasi batas-batasnya, aktor eksternal (User, Admin Gudang, Super Admin, Perangkat IoT, Penyedia E-Wallet, Mitra Penjualan), dan layanan yang ditawarkan.
**Menjawab:** Batasan sistem, interaksi dengan pihak eksternal.

#### 2.2.2 Composition
Mendeskripsikan bagaimana sistem dirakit dari komponen-komponen utama: Modul Autentikasi, Modul Formulir Setoran, Modul IoT Gateway, Modul Kalkulasi Poin, Modul Redeem, Modul Inventori & Penjualan Mitra, dan Modul Admin.
**Menjawab:** Modularitas, alokasi tanggung jawab, integrasi.

#### 2.2.3 Logical
Menangkap struktur desain statis sistem dalam bentuk entitas, kelas, dan hubungannya.
**Menjawab:** Abstraksi domain, enkapsulasi, ketergantungan antar entitas.

#### 2.2.4 Information
Memodelkan struktur data persisten: entitas Users, Deposits, Points, Redemptions, Inventory, PriceSetting, dan PartnerSales.
**Menjawab:** Integritas data, persistensi, manajemen akses data.

#### 2.2.5 Interface
Menspesifikasikan antarmuka eksternal: REST API endpoints, protokol IoT (HTTP), dan kontrak integrasi e-wallet.
**Menjawab:** Interoperabilitas, risiko integrasi.

#### 2.2.6 Interaction
Mengilustrasikan kolaborasi runtime: alur pengisian formulir setoran, verifikasi IoT, kalkulasi poin, dan pencairan poin.
**Menjawab:** Urutan pesan, propagasi error, logika distribusi.

#### 2.2.7 Algorithm
Merinci logika pemrosesan internal operasi kritis: algoritma kalkulasi poin berdasarkan berat aktual, harga per kg, dan persentase insentif.
**Menjawab:** Kompleksitas komputasi, determinisme hasil kalkulasi.

#### 2.2.8 State Dynamics
Mendetailkan evolusi status setoran sampah dan permintaan redeem.
**Menjawab:** Manajemen status, transisi event, konsistensi data.

#### 2.2.9 Deployment
Mendeskripsikan pemetaan komponen ke lingkungan eksekusi: cloud server, perangkat IoT di gudang, dan browser pengguna.
**Menjawab:** Topologi deployment, ketersediaan, skalabilitas.

---

## 3. Design Views

### 3.1 Context View

> **Viewpoint:** Context | **Bahasa:** UML Use Case (deskripsi tekstual)

#### ID: 001 — System Context EcoPoint

Sistem EcoPoint berinteraksi dengan lima entitas eksternal utama:

| Entitas Eksternal | Deskripsi Interaksi |
|---|---|
| **User (Masyarakat)** | Mengakses sistem melalui web browser untuk mendaftar, mengisi formulir setoran, memantau saldo poin, dan mengajukan pencairan. |
| **Admin Gudang** | Mengakses panel admin untuk menerima setoran yang masuk, memverifikasi berat aktual via timbangan IoT, dan mengelola inventori sampah per jenis. |
| **Super Admin** | Mengakses sistem dengan hak penuh untuk mengonfigurasi harga sampah dan persentase insentif, menyetujui pencairan poin, dan memantau laporan. |
| **Perangkat IoT (Timbangan Digital)** | Mengirimkan data berat aktual secara real-time ke sistem melalui koneksi WiFi menggunakan mikrokontroler ESP32/Arduino setelah sampah ditimbang oleh Admin Gudang. |
| **Payment Gateway / API E-Wallet** | Menerima permintaan pencairan poin dari sistem dan memproses transfer saldo ke e-wallet pengguna (GoPay, OVO, DANA) atau mencatat pencairan tunai. |
| **Mitra Penjualan** | Pihak eksternal (pabrik pupuk kompos dan pengepul/pendaur ulang) yang membeli sampah dari EcoPoint; dicatat dalam sistem sebagai transaksi penjualan inventori. |

**Use Case Utama:**

| Kode | Use Case | Aktor Utama |
|---|---|---|
| UC-01 | Mendaftar Akun | User |
| UC-02 | Melakukan Login | User, Admin Gudang, Super Admin |
| UC-03 | Mengisi Formulir Setoran | User |
| UC-04 | Mengantarkan Sampah ke Gudang | User (offline) |
| UC-05 | Verifikasi Berat via IoT | Admin Gudang, Perangkat IoT |
| UC-06 | Kalkulasi Poin Otomatis | Sistem (otomatis setelah UC-05) |
| UC-07 | Melihat Saldo & Riwayat Poin | User |
| UC-08 | Mengajukan Redeem Poin | User |
| UC-09 | Menyetujui Redeem Poin | Super Admin |
| UC-10 | Kelola Inventori & Catat Penjualan ke Mitra | Admin Gudang |
| UC-11 | Kelola Harga Sampah & Persentase Insentif | Super Admin |
| UC-12 | Kelola Akun Pengguna | Super Admin |

> **Referensi Keputusan:** DEC-001, DEC-002 | **Kebutuhan SRS:** FR-01 s.d. FR-12

---

### 3.2 Composition View

> **Viewpoint:** Composition | **Bahasa:** Dekomposisi Komponen Hirarkis

#### ID: 002 — Dekomposisi Komponen Sistem EcoPoint

```
EcoPoint System
├── A. Lapisan Presentasi (Front-End Web)
│   ├── Modul Autentikasi UI         → Halaman registrasi & login
│   ├── Dashboard User               → Saldo poin, riwayat setoran & redeem, formulir setoran
│   ├── Panel Admin Gudang           → Antrian setoran masuk, antarmuka IoT timbangan, kelola inventori
│   └── Panel Super Admin            → Konfigurasi harga & persentase insentif, persetujuan redeem, laporan
│
├── B. Lapisan Logika Bisnis (Back-End PHP)
│   ├── Modul Autentikasi & Otorisasi    → Registrasi, login, sesi, RBAC (3 peran)
│   ├── Modul Formulir Setoran           → Penerimaan formulir (jenis sampah + estimasi berat), validasi, status
│   ├── Modul IoT Gateway                → Endpoint ESP32, penerimaan berat aktual, update status setoran
│   ├── Modul Kalkulasi Poin             → Formula: Poin = Berat × Harga/kg × Persentase Insentif (%)
│   ├── Modul Manajemen Poin & Redeem    → Saldo poin, pengajuan pencairan, persetujuan, integrasi API e-wallet
│   ├── Modul Inventori & Penjualan Mitra→ Pencatatan stok per jenis sampah + transaksi jual ke pabrik pupuk / pengepul
│   ├── Modul Konfigurasi Harga          → Pengaturan harga sampah/kg dan persentase insentif (Super Admin)
│   └── Modul Notifikasi                 → Notifikasi in-app: setoran diterima, poin masuk, redeem diproses
│
├── C. Lapisan Data
│   └── MySQL / MariaDB                  → Basis data relasional seluruh entitas sistem
│
└── D. Integrasi Eksternal
    ├── IoT Device Layer                 → ESP32/Arduino via WiFi → HTTP POST ke /api/iot/weight
    └── Payment Gateway API              → GoPay, OVO, DANA untuk pencairan poin
```

> **Referensi Keputusan:** DEC-001, DEC-003 | **Kebutuhan SRS:** FR-01, FR-03, FR-04, FR-06, FR-07, FR-09

---

### 3.3 Logical View

> **Viewpoint:** Logical | **Bahasa:** Deskripsi Entitas dan Hubungan

#### ID: 003 — Model Entitas Domain EcoPoint

| Entitas | Atribut Utama | Keterangan |
|---|---|---|
| **User** | `id`, `nama`, `no_telepon`, `email`, `password_hash`, `poin_saldo`, `peran`, `status`, `created_at` | Merepresentasikan semua pengguna sistem. Kolom `peran` membedakan User / Admin Gudang / Super Admin. |
| **Deposit** | `id`, `user_id`, `jenis_sampah`, `estimasi_berat_kg`, `berat_aktual_kg`, `poin_diperoleh`, `status`, `admin_id`, `created_at` | Satu transaksi setoran sampah — dari pengisian formulir online oleh User hingga verifikasi timbang oleh Admin Gudang. |
| **PointTransaction** | `id`, `user_id`, `deposit_id`, `tipe`, `jumlah_poin`, `keterangan`, `created_at` | Riwayat setiap perubahan saldo poin (`tipe`: kredit saat setoran berhasil / debit saat redeem). |
| **Redemption** | `id`, `user_id`, `jumlah_poin`, `metode`, `no_ewallet`, `status`, `admin_id`, `created_at` | Pengajuan pencairan poin oleh User ke e-wallet atau tunai. |
| **InventoryItem** | `id`, `jenis_sampah`, `jumlah_kg`, `status_stok`, `admin_id`, `created_at` | Catatan stok sampah di gudang per jenis (organik/anorganik) yang belum terjual ke mitra. |
| **PartnerSale** | `id`, `jenis_sampah`, `jumlah_kg`, `harga_jual_per_kg`, `total_nilai`, `mitra_tujuan`, `admin_id`, `tanggal_penjualan` | Catatan transaksi penjualan sampah ke mitra: organik → pabrik pupuk kompos; anorganik → pengepul/pendaur ulang. |
| **PriceSetting** | `id`, `jenis_sampah`, `harga_per_kg`, `persentase_insentif`, `updated_by`, `updated_at` | Konfigurasi harga dasar sampah dan persentase insentif untuk pengguna; dikelola Super Admin. |

**Relasi antar entitas:**

- `User` 1 → N `Deposit`
- `User` 1 → N `PointTransaction`
- `User` 1 → N `Redemption`
- `Deposit` 1 → 1 `PointTransaction` (kredit)
- `PriceSetting` 1 → N `Deposit`
- `InventoryItem` 1 → N `PartnerSale`

> **Referensi Keputusan:** DEC-002 | **Kebutuhan SRS:** FR-04, FR-05, FR-06, FR-07, FR-11

---

### 3.4 Information View

> **Viewpoint:** Information | **Bahasa:** Entity-Relationship (deskripsi skema)

#### ID: 004 — Skema Basis Data EcoPoint

| Tabel | Kunci Primer | Kunci Asing | Catatan |
|---|---|---|---|
| `users` | `id` (INT, AUTO_INCREMENT) | — | `peran` ENUM(`user`, `admin_gudang`, `super_admin`). Password disimpan sebagai hash bcrypt. |
| `deposits` | `id` (INT, AUTO_INCREMENT) | `user_id → users.id`; `admin_id → users.id` | `jenis_sampah` ENUM(`organik`, `anorganik`). `status` ENUM(`menunggu`, `diverifikasi`, `ditolak`, `selesai`). Kolom `berat_aktual_kg` diisi oleh IoT Gateway atau input manual Admin Gudang. |
| `point_transactions` | `id` (INT, AUTO_INCREMENT) | `user_id → users.id`; `deposit_id → deposits.id` | `tipe` ENUM(`kredit`, `debit`). Setiap setoran berhasil menghasilkan satu baris kredit. |
| `redemptions` | `id` (INT, AUTO_INCREMENT) | `user_id → users.id`; `admin_id → users.id` | `status` ENUM(`menunggu`, `disetujui`, `ditolak`). `metode` ENUM(`gopay`, `ovo`, `dana`, `tunai`). |
| `inventory_items` | `id` (INT, AUTO_INCREMENT) | `admin_id → users.id` | `jenis_sampah` ENUM(`organik`, `anorganik`). `status_stok` ENUM(`tersedia`, `terjual`). |
| `partner_sales` | `id` (INT, AUTO_INCREMENT) | `admin_id → users.id` | `mitra_tujuan` VARCHAR — nama pabrik pupuk kompos atau pengepul/pendaur ulang. |
| `price_settings` | `id` (INT, AUTO_INCREMENT) | `updated_by → users.id` | Satu baris per jenis sampah; nilai aktif ditentukan oleh `updated_at` terbaru. Kolom `persentase_insentif` menentukan berapa persen dari nilai jual yang diberikan ke pengguna. |

**Integritas data dijaga melalui:**

- `FOREIGN KEY` constraints pada semua relasi antar tabel.
- `NOT NULL` pada kolom wajib.
- `UNIQUE` constraint pada kolom `email` di tabel `users`.
- Database transactions (ACID) pada operasi kalkulasi poin dan pencairan saldo.

> **Referensi Keputusan:** DEC-002 | **Kebutuhan SRS:** FR-01, FR-04, FR-05, FR-06, FR-07, FR-08, FR-09, FR-11

---

### 3.5 Interface View

> **Viewpoint:** Interface | **Bahasa:** Spesifikasi REST API & Protokol IoT

#### ID: 005 — REST API Endpoints Utama EcoPoint

| Method | Endpoint | Deskripsi | Peran |
|---|---|---|---|
| `POST` | `/api/auth/register` | Registrasi akun pengguna baru. | Publik |
| `POST` | `/api/auth/login` | Autentikasi pengguna, mengembalikan token sesi. | Semua |
| `POST` | `/api/deposits` | Membuat pengajuan setoran baru (isi formulir: jenis sampah + estimasi berat). | User |
| `GET` | `/api/deposits` | Mengambil daftar setoran (filter by status/user). | Admin, Super Admin |
| `GET` | `/api/deposits/{id}` | Mengambil detail satu setoran. | User (milik sendiri), Admin |
| `POST` | `/api/deposits/{id}/verify` | Menerima berat aktual dari IoT atau input manual, menghitung poin. | Admin Gudang |
| `POST` | `/api/deposits/{id}/reject` | Menolak setoran yang tidak valid. | Admin Gudang |
| `GET` | `/api/users/{id}/balance` | Mengambil saldo poin dan riwayat transaksi poin pengguna. | User (milik sendiri) |
| `POST` | `/api/redemptions` | Mengajukan permintaan pencairan poin ke e-wallet atau tunai. | User |
| `PUT` | `/api/redemptions/{id}/approve` | Menyetujui pengajuan redeem dan memproses transfer. | Super Admin |
| `PUT` | `/api/redemptions/{id}/reject` | Menolak pengajuan redeem. | Super Admin |
| `GET` | `/api/inventory` | Mengambil data stok inventori per jenis sampah. | Admin Gudang, Super Admin |
| `POST` | `/api/inventory/sell` | Mencatat penjualan sampah ke mitra (pabrik pupuk / pengepul). | Admin Gudang |
| `GET` | `/api/settings/prices` | Mengambil konfigurasi harga dan persentase insentif. | Semua (authenticated) |
| `PUT` | `/api/settings/prices` | Mengubah harga dasar sampah dan persentase insentif. | Super Admin |
| `POST` | `/api/iot/weight` | Penerimaan data berat aktual dari perangkat ESP32. | IoT Device (API Key) |

#### ID: 006 — Protokol Komunikasi IoT

Perangkat ESP32 mengirimkan data berat ke `/api/iot/weight` menggunakan **HTTP POST** dengan payload JSON:

```json
{
  "device_id": "SCALE-GUDANG-001",
  "weight_kg": 3.75,
  "timestamp": "2026-06-10T10:30:00Z",
  "deposit_id": 42
}
```

- **Autentikasi:** API key statis dikonfigurasi pada firmware ESP32 dan divalidasi server via header `X-API-Key`.
- **Fallback:** Jika perangkat IoT offline atau tidak tersedia, Admin Gudang dapat memasukkan berat aktual secara manual melalui Panel Admin Gudang.

> **Referensi Keputusan:** DEC-003 | **Kebutuhan SRS:** FR-03, FR-05, FR-06

---

### 3.6 Interaction View

> **Viewpoint:** Interaction | **Bahasa:** Sequence Diagram (deskripsi langkah)

#### ID: 007 — Alur Lengkap Setoran Sampah (Happy Path)

Alur interaksi dari **UC-03 (Formulir Setoran)** hingga **UC-06 (Kalkulasi Poin)**:

```
User              Admin Gudang        Sistem (Back-End)     ESP32 (IoT)     Database
 |                     |                     |                   |               |
 |-- Isi formulir setoran (jenis + estimasi berat) →            |               |
 |                     |    POST /api/deposits                   |               |
 |                     |←-- status: "menunggu" ------------------|               |
 |-- Antar sampah ke gudang (offline) ------→                   |               |
 |                     |-- Terima sampah fisik                   |               |
 |                     |-- GET /api/deposits?status=menunggu →   |               |
 |                     |←-- Daftar antrian setoran              |               |
 |                     |-- Letakkan sampah di timbangan          |               |
 |                     |-- POST /api/iot/weight (trigger) -------|               |
 |                     |                     |←-- weight_kg: 3.75|               |
 |                     |←-- Tampil berat aktual di panel        |               |
 |                     |-- Klik "Konfirmasi Berat" →             |               |
 |                     |    POST /api/deposits/{id}/verify       |               |
 |                     |                     |-- calculatePoints()|              |
 |                     |                     |-- BEGIN TRANSACTION→              |
 |                     |                     |               UPDATE users.poin_saldo→|
 |                     |                     |               INSERT point_transactions→|
 |                     |                     |               UPDATE deposits.status→|
 |                     |                     |               COMMIT →            |
 |←-- Notifikasi: "Poin berhasil ditambahkan" ←                 |               |
```

#### ID: 008 — Alur Pencairan Poin (Happy Path)

Alur interaksi **UC-08 (Ajukan Redeem)** hingga **UC-09 (Setujui Redeem)**:

```
User              Sistem (Back-End)       Super Admin        Payment Gateway API
 |                     |                     |                     |
 |-- POST /api/redemptions (jumlah poin + metode e-wallet) →     |
 |                     |-- Validasi: saldo ≥ jumlah diminta       |
 |                     |-- Simpan status "menunggu"               |
 |                     |-- Kirim notifikasi ke Super Admin →      |
 |                     |                    |-- Tinjau pengajuan  |
 |                     |                    |-- PUT /api/redemptions/{id}/approve
 |                     |←-- Konfirmasi persetujuan               |
 |                     |-- Panggil API E-Wallet ----------------→ |
 |                     |                    |          ←-- Transfer berhasil
 |                     |-- Debit saldo poin User                  |
 |                     |-- Update status "disetujui"              |
 |←-- Notifikasi: "Pencairan berhasil, cek e-wallet kamu"        |
```

#### ID: 009 — Alur Penjualan Sampah ke Mitra

Alur interaksi **UC-10 (Catat Penjualan Mitra)**:

```
Admin Gudang        Sistem (Back-End)        Database
 |                       |                       |
 |-- Pilih jenis sampah (organik/anorganik)       |
 |-- Masukkan jumlah kg & nama mitra tujuan       |
 |-- POST /api/inventory/sell →                   |
 |                       |-- Validasi stok tersedia
 |                       |-- INSERT partner_sales →|
 |                       |-- UPDATE inventory_items.status = 'terjual' →|
 |←-- Konfirmasi penjualan tercatat              |
```

> **Referensi Keputusan:** DEC-003 | **Kebutuhan SRS:** FR-03, FR-05, FR-06, FR-07, FR-10, FR-11

---

### 3.7 Algorithm View

> **Viewpoint:** Algorithm | **Bahasa:** Pseudocode

#### ID: 010 — Algoritma Kalkulasi Poin

Dieksekusi oleh **Modul Kalkulasi Poin** setelah Admin Gudang mengkonfirmasi berat aktual via IoT atau input manual (FR-05).

```
FUNCTION calculatePoints(deposit_id, berat_aktual_kg):

  1. GET jenis_sampah FROM deposits WHERE id = deposit_id

  2. GET harga_per_kg, persentase_insentif FROM price_settings
         WHERE jenis_sampah = jenis_sampah
         ORDER BY updated_at DESC LIMIT 1

  3. IF harga_per_kg IS NULL THEN
         RAISE Error("Harga belum dikonfigurasi untuk jenis sampah ini")

  4. nilai_sampah = berat_aktual_kg × harga_per_kg
     poin         = ROUND(nilai_sampah × (persentase_insentif / 100))

  5. IF poin <= 0 THEN
         RAISE Error("Nilai poin tidak valid, periksa konfigurasi harga")

  6. BEGIN TRANSACTION
       UPDATE users
         SET poin_saldo = poin_saldo + poin
         WHERE id = deposit.user_id

       INSERT INTO point_transactions
         (user_id, deposit_id, tipe, jumlah_poin, keterangan)
         VALUES (user_id, deposit_id, 'kredit', poin,
                 CONCAT(berat_aktual_kg, ' kg ', jenis_sampah))

       UPDATE deposits
         SET berat_aktual_kg = berat_aktual_kg,
             poin_diperoleh  = poin,
             status          = 'selesai'
         WHERE id = deposit_id

       INSERT INTO inventory_items
         (jenis_sampah, jumlah_kg, status_stok, admin_id)
         VALUES (jenis_sampah, berat_aktual_kg, 'tersedia', admin_id)

  7. COMMIT TRANSACTION

  8. TRIGGER notifikasi in-app ke user_id:
       "Setoran kamu berhasil! Kamu mendapatkan {poin} poin."

  RETURN poin
```

**Catatan:**

- **Formula:** `Poin = Berat (kg) × Harga Pasar (Rp/kg) × Persentase Insentif (%)`
- **Contoh:** Sampah plastik 5 kg, harga Rp 3.000/kg, insentif 60% → Poin = 5 × 3.000 × 60% = **9.000 poin** (setara Rp 9.000).
- **Atomisitas:** Seluruh pembaruan (poin user, riwayat transaksi, status setoran, inventori) dibungkus dalam satu database transaction. Jika satu langkah gagal, seluruh operasi di-rollback.
- **Kompleksitas:** O(1) — operasi konstan pada pencarian harga dan kalkulasi aritmetika.

> **Referensi Keputusan:** DEC-002 | **Kebutuhan SRS:** FR-05

---

### 3.8 State Dynamics View

> **Viewpoint:** State Dynamics | **Bahasa:** State Transition Table

#### ID: 011 — State Machine Status Setoran

```
                              ┌──────────────────────────────────────────────────────┐
                              │                                                      │
          Input valid         │  Admin Gudang timbang    Kalkulasi berhasil          │
(Awal) ─────────────────► Menunggu ──────────────► Diverifikasi ──────────────► Selesai
                           Verifikasi
                               │                       │
                               │ Admin tolak            │ Kalkulasi gagal / rollback
                               ▼                       ▼
                            Ditolak             Menunggu Verifikasi
```

| State Asal | Event / Trigger | Guard / Kondisi | State Tujuan | Efek |
|---|---|---|---|---|
| (Awal) | User mengisi & mengirim formulir setoran | Jenis sampah & estimasi berat diisi; User login | **Menunggu Verifikasi** | Data setoran tersimpan; notifikasi antrian ke Admin Gudang. |
| Menunggu Verifikasi | Admin Gudang konfirmasi berat via IoT / manual | Berat aktual > 0 | **Diverifikasi** | Berat aktual tersimpan; Modul Kalkulasi Poin dipanggil. |
| Menunggu Verifikasi | Admin Gudang menolak setoran | Sampah tidak sesuai / terindikasi fraud | **Ditolak** | Notifikasi penolakan dikirim ke User. |
| Diverifikasi | Kalkulasi poin berhasil | Poin > 0; database transaction berhasil | **Selesai** | Saldo poin User diperbarui; stok inventori bertambah; notifikasi ke User. |
| Diverifikasi | Kalkulasi poin gagal | Error database atau harga tidak dikonfigurasi | **Menunggu Verifikasi** | Rollback; status kembali ke antrian; error log dicatat. |

#### ID: 012 — State Machine Status Pencairan Poin (Redemption)

| State Asal | Event / Trigger | Guard / Kondisi | State Tujuan | Efek |
|---|---|---|---|---|
| (Awal) | User mengajukan redeem | Saldo poin ≥ jumlah yang diminta | **Menunggu Persetujuan** | Data redeem tersimpan; notifikasi ke Super Admin. |
| Menunggu Persetujuan | Super Admin menyetujui | API e-wallet tersedia / metode tunai | **Disetujui** | Transfer saldo ke e-wallet atau pencatatan tunai; saldo poin User didebit. |
| Menunggu Persetujuan | Super Admin menolak | Alasan penolakan dicatat | **Ditolak** | Notifikasi penolakan ke User; saldo poin tidak berubah. |

> **Referensi Keputusan:** DEC-002 | **Kebutuhan SRS:** FR-07, FR-08

---

### 3.9 Deployment View

> **Viewpoint:** Deployment | **Bahasa:** Deskripsi Infrastruktur

#### ID: 013 — Arsitektur Deployment EcoPoint

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              INTERNET                                        │
│                                                                              │
│  ┌──────────────────┐   HTTPS    ┌───────────────────────────────────────┐  │
│  │  Perangkat User  │◄─────────►│          Cloud Server (Linux)          │  │
│  │  (Browser)       │            │  ┌─────────────────┐ ┌─────────────┐  │  │
│  │  Smartphone /    │            │  │  Web Server     │ │ Aplikasi PHP│  │  │
│  │  PC/Laptop       │            │  │ (Apache/Nginx)  │ │ (Back-End)  │  │  │
│  └──────────────────┘            │  └─────────────────┘ └─────────────┘  │  │
│                                  │           ▲                 ▲           │  │
│  ┌──────────────────┐ HTTP POST  │  ┌────────────────────────────────┐    │  │
│  │  IoT Device      │───────────►│  │    MySQL / MariaDB (DB)        │    │  │
│  │  (ESP32/Arduino) │            │  └────────────────────────────────┘    │  │
│  │  Timbangan IoT   │            └───────────────────────────────────────┘  │
│  └──────────────────┘                              ▲                         │
│         ▲                                          │ HTTPS                   │
│         │ WiFi Lokal Gudang          ┌─────────────────────────┐             │
│                                      │  Payment Gateway API    │             │
│                                      │  (GoPay / OVO / DANA)  │             │
│                                      └─────────────────────────┘             │
└──────────────────────────────────────────────────────────────────────────────┘
```

| Node | Komponen yang Di-deploy | Spesifikasi | Koneksi |
|---|---|---|---|
| **Cloud Server (Linux)** | Aplikasi PHP, Web Server (Apache/Nginx), MySQL/MariaDB | Min. 2 vCPU, 4 GB RAM, 50 GB SSD. Target uptime 99%. | Internet (HTTPS/443); menerima koneksi dari browser & perangkat IoT. |
| **Perangkat IoT (Gudang)** | Firmware ESP32/Arduino, Driver Sensor Timbangan | Mikrokontroler ESP32 + modul WiFi; timbangan digital dengan output serial. | WiFi lokal gudang → Internet → Cloud Server (HTTP POST `/api/iot/weight`). |
| **Perangkat Pengguna** | Web Browser (Chrome, Firefox, Safari, Edge) | Smartphone Android/iOS min. 2 GB RAM; atau PC/Laptop min. 4 GB RAM. | Internet (HTTPS) → Cloud Server. |
| **Layanan Eksternal** | API GoPay, OVO, DANA | Third-party payment gateway. | Cloud Server → HTTPS → API E-Wallet (outbound). |

**Urutan Deployment:**

1. Provisioning cloud server dan konfigurasi MySQL database.
2. Deployment aplikasi PHP dan konfigurasi web server.
3. Konfigurasi firmware ESP32 dan pengujian koneksi IoT di gudang.
4. Konfigurasi harga sampah dan persentase insentif oleh Super Admin.
5. Pengujian integrasi end-to-end (simulasi setoran, kalkulasi poin, pencairan).
6. Go-live dan monitoring awal.

> **Referensi Keputusan:** DEC-001, DEC-003 | **Kebutuhan SRS:** NFR Ketersediaan 99%

---

## 4. Decisions

### DEC-001: Arsitektur Web Monolitik Berbasis PHP

| Atribut | Detail |
|---|---|
| **ID** | DEC-001 |
| **Judul** | Pemilihan Arsitektur Web Monolitik Berbasis PHP & MySQL |
| **Konteks** | Tim membutuhkan arsitektur yang dapat dikembangkan oleh tim kecil (5 orang) dalam 4 bulan. Tim memiliki familiaritas tinggi dengan PHP dan MySQL. |
| **Opsi yang Dipertimbangkan** | 1. Monolitik PHP + MySQL ✅ (dipilih) · 2. Microservices (Node.js/Python) · 3. Framework modern (Laravel/CodeIgniter) |
| **Keputusan** | **Monolitik PHP + MySQL**, karena kesederhanaan deployment, kurva belajar minimal, dan kecukupan untuk skala awal hingga 10.000 pengguna aktif. |
| **Konsekuensi Positif** | Pengembangan lebih cepat, debugging lebih mudah, deployment sederhana pada satu server. |
| **Konsekuensi Negatif** | Skalabilitas horizontal lebih sulit pada fase lanjut; perlu dievaluasi ulang jika pengguna melebihi 50.000. |

---

### DEC-002: Basis Data Relasional MySQL/MariaDB

| Atribut | Detail |
|---|---|
| **ID** | DEC-002 |
| **Judul** | Pemilihan MySQL/MariaDB sebagai Sistem Manajemen Basis Data |
| **Konteks** | Data sistem EcoPoint terstruktur dengan relasi jelas antar entitas. Integritas data sangat kritis untuk kalkulasi poin, pencairan saldo, dan pencatatan inventori penjualan ke mitra. |
| **Opsi yang Dipertimbangkan** | 1. MySQL/MariaDB ✅ (dipilih) · 2. PostgreSQL · 3. NoSQL (MongoDB) |
| **Keputusan** | **MySQL/MariaDB**, karena dukungan ACID transactions untuk konsistensi kalkulasi poin, familiaritas tim yang tinggi, dan kompatibilitas penuh dengan ekosistem PHP. |
| **Konsekuensi Positif** | Integritas data terjamin, query relasional optimal, tooling yang matang. |
| **Konsekuensi Negatif** | Kurang fleksibel untuk data tidak terstruktur di masa depan. |

---

### DEC-003: Integrasi IoT via HTTP REST

| Atribut | Detail |
|---|---|
| **ID** | DEC-003 |
| **Judul** | Protokol Komunikasi IoT: HTTP REST vs MQTT |
| **Konteks** | Perangkat ESP32 di gudang perlu mengirimkan data berat ke server setiap kali Admin Gudang menimbang sampah pengguna. Frekuensi pengiriman rendah (sekali per setoran). |
| **Opsi yang Dipertimbangkan** | 1. HTTP REST POST ✅ (dipilih) · 2. MQTT dengan broker (Mosquitto) · 3. WebSocket |
| **Keputusan** | **HTTP REST POST**, karena integrasi langsung ke back-end PHP tanpa broker tambahan, mudah di-debug, dan frekuensi pengiriman data yang rendah tidak memerlukan overhead MQTT. |
| **Konsekuensi Positif** | Implementasi lebih sederhana, tidak memerlukan broker tambahan, mudah diuji dengan Postman/curl. |
| **Konsekuensi Negatif** | Latensi sedikit lebih tinggi dibanding MQTT; jika di masa depan dibutuhkan monitoring real-time berkelanjutan, perlu dievaluasi ke WebSocket atau MQTT. |

---

## 5. Appendixes

### Appendix A: Ringkasan Kelayakan TELOS

Berdasarkan Studi Kelayakan EcoPoint (April 2026):

| Aspek | Nilai (1–10) | Catatan Utama |
|---|:---:|---|
| **Technical** | 8.5 | Infrastruktur dan SDM siap; sedikit risiko integrasi hardware IoT timbangan. |
| **Economic** | 9.0 | BEP cepat; proyeksi keuntungan dari penjualan sampah organik (pupuk) & anorganik (pengepul) + pengurangan biaya TPA. |
| **Legal** | 10.0 | Mematuhi UU No. 18/2008 (Pengelolaan Sampah) dan UU No. 27/2022 (PDP). |
| **Operational** | 8.0 | Antarmuka user sesederhana mungkin; staf gudang perlu pelatihan singkat untuk timbangan IoT. |
| **Schedule** | 9.0 | Estimasi 4–5 bulan sangat rasional dengan tim 5 orang (UCP = 45). |
| **Total Skor** | **44.5 / 50** | ✅ Dinyatakan **SANGAT LAYAK**. |

---

### Appendix B: Tim Pengembang EcoPoint

| Nama | Peran | Tanggung Jawab Utama |
|---|---|---|
| Muhamad Reisya Wijaya | User / Product Owner | Menentukan kebutuhan bisnis, menyetujui fitur, mewakili kepentingan organisasi. |
| Abdullah Daffa Al Ghozi | Project Manager | Koordinasi tim, manajemen jadwal, penandatangan kontrak SRS. |
| Muhammad Ilham Ropi Nawawi | System Analyst | Analisis kebutuhan, perancangan arsitektur sistem, dokumentasi SRS & SDD. |
| Muhammad Fadly Mahesar | Programmer / IoT Engineer | Pengembangan back-end PHP, front-end web, integrasi firmware ESP32. |
| M. Zaenal Khoirul Zaman | Tester | Pengujian fungsional, validasi kualitas, laporan bug. |

> **Tanggal Kontrak:** 10 April 2026 | **Lokasi:** Bandung, Jawa Barat, Indonesia

---

### Appendix C: Spesifikasi Teknis Minimum

| Komponen | Spesifikasi |
|---|---|
| **Platform Pengguna** | Web Browser (Chrome, Firefox, Safari, Edge) versi terbaru — berbasis web, tidak perlu instalasi aplikasi. |
| **Perangkat Pengguna** | Smartphone Android/iOS min. 2 GB RAM; atau PC/Laptop min. 4 GB RAM dengan koneksi internet aktif. |
| **Koneksi Internet (User)** | Minimum 4G/LTE atau WiFi dengan kecepatan unduh ≥ 5 Mbps. |
| **Perangkat IoT Gudang** | Mikrokontroler ESP32 atau Arduino dengan modul WiFi; timbangan digital dengan output serial (load cell + HX711). |
| **Koneksi IoT Gudang** | WiFi lokal gudang dengan akses internet untuk komunikasi ke cloud server. |
| **Server Back-End** | Cloud server Linux; min. 2 vCPU, 4 GB RAM, 50 GB SSD storage. |
| **Basis Data** | MySQL / MariaDB (sistem basis data relasional). |
| **Teknologi Back-End** | PHP (native atau framework ringan); web server Apache atau Nginx. |

---

### Appendix D: Timeline Pengembangan

| Bulan | Fase | Kegiatan Utama |
|---|---|---|
| Bulan 1 | Perancangan | Finalisasi SRS & SDD, desain database, desain UI/UX, perancangan sirkuit IoT |
| Bulan 2 | Implementasi Inti | Pengembangan modul autentikasi, formulir setoran, panel admin gudang, integrasi IoT timbangan |
| Bulan 3 | Implementasi Lanjutan | Modul kalkulasi poin, pencairan e-wallet, inventori & penjualan mitra, notifikasi |
| Bulan 4 | Testing & Deployment | Pengujian fungsional end-to-end, bug fixing, deployment ke cloud, pelatihan staf gudang |

---

*Dokumen ini dikelola oleh Tim Pengembang EcoPoint. Untuk pertanyaan atau perubahan, hubungi Project Manager: Abdullah Daffa Al Ghozi.*

*© 2026 Tim Pengembang EcoPoint — Bandung, Jawa Barat, Indonesia*
