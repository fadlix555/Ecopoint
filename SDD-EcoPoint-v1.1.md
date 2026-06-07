# Software Design Description
## For EcoPoint — Sistem Informasi Daur Ulang Sampah Berbasis Poin

Version 0.1  
Prepared by Muhammad Fadly Mahesar & Tim Pengembang  
Teknik Informatika, Universitas Halim Sanusi  
Juni 2026

## Table of Contents
* [1. Introduction](#1-introduction)
  * [1.1 Document Purpose](#11-document-purpose)
  * [1.2 Subject Scope](#12-subject-scope)
  * [1.3 Definitions, Acronyms, and Abbreviations](#13-definitions-acronyms-and-abbreviations)
  * [1.4 References](#14-references)
  * [1.5 Document Overview](#15-document-overview)
* [2. Design Overview](#2-design-overview)
  * [2.1 Stakeholder Concerns](#21-stakeholder-concerns)
  * [2.2 Selected Viewpoints](#22-selected-viewpoints)
* [3. Design Views](#3-design-views)
* [4. Decisions](#4-decisions)
* [5. Appendixes](#5-appendixes)

## Revision History

| Name | Date | Reason For Changes | Version |
|------|------|--------------------|---------|
| Muhammad Fadly M. | 07 Juni 2026 | Inisialisasi draf dokumen berdasarkan template SDD standar. | 0.1 |

## 1. Introduction

### 1.1 Document Purpose
Dokumen *Software Design Description* (SDD) ini menjabarkan arsitektur dan rancangan teknis sistem EcoPoint — Sistem Informasi Daur Ulang Sampah Berbasis Poin. Dokumen ini disusun sebagai acuan implementasi bagi tim pengembang dan sebagai referensi pemeliharaan bagi operator sistem di tahap siklus hidup perangkat lunak.

### 1.2 Subject Scope
Sistem yang dirancang adalah EcoPoint versi 1.0, platform web responsif untuk manajemen bank sampah digital. Tujuan utamanya adalah mendigitalisasi seluruh alur kerja bank sampah, mulai dari registrasi pengguna, pengajuan setoran, penimbangan manual oleh Admin Gudang, input kilogram aktual, penambahan poin oleh Super Admin, pencairan poin, hingga pengelolaan inventori dan penjualan ke mitra.

**Alur inti sistem EcoPoint:**
* Pengguna mendaftar dan memiliki akun.
* Pengguna mengisi formulir setoran dengan jenis sampah dan estimasi berat.
* Pengguna mengantarkan sampah ke gudang.
* Admin Gudang menimbang sampah secara manual.
* Admin Gudang menginput berat aktual ke sistem.
* Super Admin meninjau data timbangan dan menambahkan poin.
* Pengguna dapat melihat saldo poin dan mengajukan redeem.
* Sampah yang terkumpul masuk inventori dan dapat dijual ke mitra.

### 1.3 Definitions, Acronyms, and Abbreviations
| Term | Definition |
|------|--------------------------------------------------------------------------------------------------------------------------|
| API  | Application Programming Interface - A set of definitions and protocols for building and integrating application software |
| SDD  | Software Design Document - A document that describes the intended purpose, requirements, and nature of a software        |
| EcoPoint | Sistem informasi daur ulang sampah berbasis poin digital. |
| Formulir Setoran | Form online yang diisi pengguna sebelum mengantarkan sampah. |
| Timbangan Manual | Timbangan konvensional yang dibaca Admin Gudang lalu diinput ke sistem. |
| Poin | Insentif digital yang diperoleh pengguna dari hasil setoran sampah. |
| Redeem | Pencairan poin ke e-wallet atau uang tunai. |
| RBAC | Role-Based Access Control. |
| SRS | Software Requirement Specification. |

### 1.4 References
| No | Judul Dokumen | Penulis / Pemilik | Tipe |
|---|---|---|---|
| 1 | Software Requirement Specification (SRS) EcoPoint v1.0 | Tim Pengembang EcoPoint, 2026 | Normatif |
| 2 | Studi Kelayakan & System Request EcoPoint | Tim Pengembang EcoPoint, 2026 | Normatif |
| 3 | UU No. 18 Tahun 2008 tentang Pengelolaan Sampah | Pemerintah Republik Indonesia, 2008 | Normatif |
| 4 | UU No. 27 Tahun 2022 tentang Perlindungan Data Pribadi | Pemerintah Republik Indonesia, 2022 | Normatif |

### 1.5 Document Overview
Dokumen ini dibagi menjadi lima bagian utama:
* **Bagian 1 (Introduction)** mencakup tujuan dokumen, ruang lingkup subjek, definisi, dan referensi.
* **Bagian 2 (Design Overview)** mendeskripsikan kekhawatiran pemangku kepentingan dan sudut pandang (viewpoint) yang dipilih.
* **Bagian 3 (Design Views)** mendokumentasikan elemen desain arsitektur menggunakan bahasa pemodelan seperti UML.
* **Bagian 4 (Decisions)** mendokumentasikan catatan keputusan arsitektur (ADR) dan justifikasinya.
* **Bagian 5 (Appendixes)** menyediakan material pendukung opsional seperti metrik kelayakan. Pembaruan dokumen ini dikelola melalui tabel Revision History di awal dokumen.

## 2. Design Overview

### 2.1 Stakeholder Concerns
| Pemangku Kepentingan | Kekhawatiran Utama | Viewpoint yang Menjawab |
|---|---|---|
| **User** | Kemudahan setoran, transparansi saldo, kemudahan redeem | Context, Interface, Interaction |
| **Admin Gudang** | Kemudahan input timbangan manual dan kelola inventori | Composition, Interaction, State Dynamics |
| **Super Admin** | Kontrol penambahan poin, approval redeem, laporan | Information, Interface, Interaction |
| **Tim Pengembang** | Modularitas kode, maintainability, dan kejelasan model | Logical, Deployment |

### 2.2 Selected Viewpoints
* **Context**: Mendefinisikan batas sistem, aktor lingkungan (users, external systems), dan layanan yang ditawarkan.
* **Composition**: Mendeskripsikan bagaimana sistem dirakit dari subsistem dan modul utama.
* **Logical**: Menangkap struktur desain statis sistem melalui entitas dan relasinya.
* **Information**: Memodelkan struktur data persisten dan skema akses.
* **Interface**: Menspesifikasikan antarmuka eksternal antar subsistem.
* **Interaction**: Mengilustrasikan kolaborasi entitas saat runtime melalui aliran pesan.
* **Algorithm**: Merinci logika pemrosesan internal untuk kalkulasi poin.
* **State Dynamics**: Mendetailkan evolusi status komponen (deposit dan redeem) terhadap pemicu waktu.
* **Deployment**: Mendeskripsikan pemetaan perangkat lunak ke lingkungan eksekusi fisik.

## 3. Design Views

* **ID:** 001-ContextView
* **Title:** EcoPoint Context Architecture
* **Viewpoint:** Context
* **Representation:** Menggunakan UML Use Case Diagram. Sistem berinteraksi dengan aktor eksternal: User, Admin Gudang, Super Admin, Payment Gateway / API E-Wallet, dan Mitra Penjualan. Mencakup 15 Use Case utama (Mendaftar Akun, Login, Isi Formulir Setoran, Lihat Saldo, Ajukan Redeem, Input Berat Aktual, Tambah Poin, Kelola Inventori, dll).
  
  ![Use Case Diagram](https://t90182767054.p.clickup-attachments.com/t90182767054/b9c56294-bb0a-40db-8257-f1b56fe0603d/generated-image-869268ee-0d3f-42f0-8125-b9c4e9264cf1.png?view=open)

* **More Information:** Diagram konteks ini membantu membatasi interaksi API dengan E-wallet dan modul manajemen internal.

* **ID:** 002-CompositionView
* **Title:** EcoPoint Component Decomposition
* **Viewpoint:** Composition
* **Representation:** Sistem didekomposisi menjadi: Lapisan Presentasi (Dashboard UI), Lapisan Logika Bisnis (Autentikasi, Setoran, Kalkulasi Poin, Inventori), Lapisan Data (MySQL/MariaDB), dan Integrasi Eksternal (API E-Wallet).
* **More Information:** Modul manajemen terisolasi secara peran melalui RBAC (Role-Based Access Control).

* **ID:** 003-LogicalView
* **Title:** Core Domain Model
* **Viewpoint:** Logical
* **Representation:** UML Class Diagram. Entitas utama meliputi: `User`, `Deposit`, `PointTransaction`, `Redemption`, `InventoryItem`, `PartnerSale`, dan `PriceSetting`. Relasi kardinalitas utama mencakup `User 1 -> N Deposit` dan `Deposit 1 -> 0..1 PointTransaction`.
  
  ![Class Diagram](https://t90182767054.p.clickup-attachments.com/t90182767054/aeef25a5-c88b-45f2-b7d5-8fb50ba67f9d/generated-image-55ea74a7-90c0-4200-84c9-1a118566cf2e.png?view=open)

* **More Information:** Memastikan enkapsulasi data poin dan riwayat transaksi setoran tetap konsisten antar kelas pengguna.

* **ID:** 004-InformationView
* **Title:** Persistent Data Schema
* **Viewpoint:** Information
* **Representation:** Skema Database Relasional dengan tabel `users`, `deposits`, `point_transactions`, `redemptions`, `inventory_items`, `partner_sales`, dan `price_settings`. Prinsip integritas mencakup `FOREIGN KEY`, `NOT NULL`, dan `UNIQUE` pada `users.email`.
* **More Information:** Penggunaan transaksi database diwajibkan pada proses tambah poin dan pencairan (redeem).

* **ID:** 005-InterfaceView
* **Title:** REST API Specifications & UI Wireframes
* **Viewpoint:** Interface
* **Representation:** Terdapat endpoint RESTful utama seperti `POST /api/deposits` (membuat pengajuan setoran), `POST /api/deposits/{id}/weigh` (input berat aktual), dan `POST /api/deposits/{id}/add-points` (tambah poin). UI mencakup Dashboard User, Panel Admin Gudang, dan Panel Super Admin.
  
  ![UI Wireframe](https://t90182767054.p.clickup-attachments.com/t90182767054/cb8f61bc-189c-43f1-badf-d07aba476ad7/generated-image-0e1da234-d124-4714-bfa3-9929add6eda9.png?view=open)

* **More Information:** Desain layar berfokus pada kemudahan akses antrian setoran dan verifikasi timbangan manual.

* **ID:** 006-InteractionView
* **Title:** Waste Deposit Sequence
* **Viewpoint:** Interaction
* **Representation:** UML Sequence Diagram. Alur: User mengisi formulir -> Deposit disimpan (`menunggu_verifikasi`) -> Admin Gudang menimbang manual dan input berat aktual -> Status menjadi `menunggu_poin` -> Super Admin meninjau dan konfirmasi penambahan poin -> Sistem mengeksekusi transaksi atomik.
  
  ![Sequence Diagram](https://t90182767054.p.clickup-attachments.com/t90182767054/4e070753-101a-4b48-9be4-3a2eebca4ce0/generated-image-f2dc0c1f-8178-4f52-990a-1fdcad4430a5.png?view=open)

* **More Information:** Sinkronisasi pembaruan status inventori dilakukan bersamaan dengan transaksi penambahan poin.

* **ID:** 007-AlgorithmView
* **Title:** Point Calculation Logic
* **Viewpoint:** Algorithm
* **Representation:** Pseudocode dan formulasi matematis: `Poin = Berat (kg) × Harga per kg × Persentase Insentif (%)`. Langkah: Ambil deposit berstatus `menunggu_poin`, cek harga aktif berdasarkan jenis sampah, hitung poin, dan catat `PointTransaction` jika dikonfirmasi.
* **More Information:** Algoritma ini berjalan pada sisi backend dan dieksekusi secara ketat oleh Super Admin.

* **ID:** 008-StateDynamicsView
* **Title:** Deposit & Redemption State Machine
* **Viewpoint:** State Dynamics
* **Representation:** UML State Machine Diagram. Status Deposit: `Awal -> Menunggu Verifikasi -> Menunggu Poin (atau Ditolak) -> Selesai`. Status Redemption: `Awal -> Menunggu Persetujuan -> Disetujui (atau Ditolak)`.
  
  ![Activity Diagram](https://t90182767054.p.clickup-attachments.com/t90182767054/d6861be1-f60e-430f-8bdc-e5b81271b5ff/generated-image-41bdeb10-63b4-4ae5-af32-d561b70bb329.png?view=open)

* **More Information:** Transisi status dibatasi secara ketat (guards) oleh role aktor yang sedang login.

* **ID:** 009-DeploymentView
* **Title:** Production Infrastructure
* **Viewpoint:** Deployment
* **Representation:** Lingkungan eksekusi menggunakan Cloud Server (Linux) yang memuat aplikasi PHP, web server, dan MySQL/MariaDB. Node klien menggunakan browser web responsif pada smartphone atau PC.
* **More Information:** Timbangan manual beroperasi sebagai perangkat fisik terpisah (offline) dan tidak memerlukan topologi IoT.

## 4. Decisions

* **ID:** DEC-001
* **Title:** Arsitektur Web Monolitik Berbasis PHP
* **Context:** Mengingat skala awal sistem dan keterbatasan sumber daya tim, diperlukan pola arsitektur yang mudah dipelihara dan di-deploy tanpa kompleksitas jaringan internal.
* **Options:** 1. Arsitektur Monolitik PHP, 2. Microservices berbasis Node.JS.
* **Outcome:** Arsitektur Web Monolitik Berbasis PHP, karena model ini sederhana untuk tim kecil, deployment mudah, dan cukup tangguh untuk menangani skala pengguna di fase awal peluncuran.
* **More Information:** Dapat dieksekusi langsung pada single virtual private server standar.

* **ID:** DEC-002
* **Title:** Basis Data Relasional MySQL/MariaDB
* **Context:** Modul poin, deposit, dan transaksi inventori membutuhkan konsistensi data yang sangat ketat untuk mencegah kebocoran saldo atau insentif.
* **Options:** 1. MySQL/MariaDB (Relational), 2. MongoDB (NoSQL).
* **Outcome:** MySQL/MariaDB, karena mendukung kapabilitas ACID (Atomicity, Consistency, Isolation, Durability) transaction yang krusial untuk fitur keuangan/poin, relasi data jelas, dan paling kompatibel dengan ekosistem monolitik PHP.
* **More Information:** Transaksi dipaksa pada seluruh operasi write di tabel `point_transactions`.

* **ID:** DEC-003
* **Title:** Penimbangan Manual oleh Admin Gudang
* **Context:** Otomatisasi pembacaan timbangan seringkali meningkatkan kerumitan perangkat keras dan biaya pemeliharaan alat di lingkungan operasional gudang yang kasar.
* **Options:** 1. Timbangan Manual (input data web), 2. Integrasi Timbangan IoT via HTTP REST API.
* **Outcome:** Penimbangan Manual oleh Admin Gudang, dipilih untuk menghilangkan kompleksitas perangkat keras IoT, menurunkan anggaran infrastruktur, dan mempercepat proses implementasi.
* **More Information:** Memerlukan proses bisnis tambahan yakni *Review* oleh Super Admin (UC-08) guna mitigasi kecurangan *human-error*.

## 5. Appendixes

### Appendix A: Ringkasan Kelayakan TELOS
* Technical: 9.0
* Economic: 9.0
* Legal: 10.0
* Operational: 8.5
* Schedule: 9.5

### Appendix B: Struktur Organisasi Tim Pengembang
* Product Owner
* Project Manager
* System Analyst
* Programmer
* Tester

### Appendix C: Spesifikasi Teknis Minimum
* Browser modern
* Smartphone/PC untuk user dan admin
* Cloud server Linux
* MySQL / MariaDB
* Timbangan manual standar

### Appendix D: Timeline Pengembangan
* Bulan 1: Perancangan
* Bulan 2: Implementasi inti
* Bulan 3: Implementasi lanjutan
* Bulan 4: Testing dan deployment
