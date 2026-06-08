# Software Design Description
## EcoPoint — Sistem Informasi Daur Ulang Sampah Berbasis Poin
 
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

### 1.2 Subject Scope
Sistem yang dirancang adalah **EcoPoint**, platform web responsif untuk manajemen bank sampah digital. Tujuan utamanya adalah mendigitalisasi seluruh alur kerja bank sampah, mulai dari registrasi pengguna, pengajuan setoran, penimbangan manual oleh Admin Gudang, input kilogram aktual, penambahan poin oleh Super Admin, pencairan poin, hingga pengelolaan inventori dan penjualan ke mitra.

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

<svg width="100%" viewBox="0 0 680 620" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="uc-arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#666" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>
  <!-- System boundary -->
  <rect x="160" y="30" width="380" height="560" rx="12" fill="none" stroke="#aaa" stroke-width="1" stroke-dasharray="6 3"/>
  <text x="350" y="22" text-anchor="middle" font-size="12" fill="#666" font-family="Arial, sans-serif">«system» EcoPoint</text>
  <!-- Use cases: User (teal) -->
  <ellipse cx="350" cy="75" rx="80" ry="18" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <text x="350" y="79" text-anchor="middle" font-size="11" fill="#085041" font-family="Arial, sans-serif">UC-01 Daftar akun</text>
  <ellipse cx="350" cy="120" rx="80" ry="18" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <text x="350" y="124" text-anchor="middle" font-size="11" fill="#085041" font-family="Arial, sans-serif">UC-02 Login</text>
  <ellipse cx="350" cy="165" rx="80" ry="18" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <text x="350" y="169" text-anchor="middle" font-size="11" fill="#085041" font-family="Arial, sans-serif">UC-03 Isi formulir setoran</text>
  <ellipse cx="350" cy="210" rx="80" ry="18" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <text x="350" y="214" text-anchor="middle" font-size="11" fill="#085041" font-family="Arial, sans-serif">UC-04 Lihat saldo poin</text>
  <ellipse cx="350" cy="255" rx="80" ry="18" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <text x="350" y="259" text-anchor="middle" font-size="11" fill="#085041" font-family="Arial, sans-serif">UC-05 Ajukan redeem</text>
  <!-- Use cases: Admin Gudang (purple) -->
  <ellipse cx="350" cy="310" rx="90" ry="18" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.8"/>
  <text x="350" y="314" text-anchor="middle" font-size="11" fill="#26215C" font-family="Arial, sans-serif">UC-06 Input berat aktual</text>
  <ellipse cx="350" cy="355" rx="80" ry="18" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.8"/>
  <text x="350" y="359" text-anchor="middle" font-size="11" fill="#26215C" font-family="Arial, sans-serif">UC-07 Tolak setoran</text>
  <ellipse cx="350" cy="400" rx="80" ry="18" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.8"/>
  <text x="350" y="404" text-anchor="middle" font-size="11" fill="#26215C" font-family="Arial, sans-serif">UC-13 Kelola inventori</text>
  <ellipse cx="350" cy="445" rx="95" ry="18" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.8"/>
  <text x="350" y="449" text-anchor="middle" font-size="11" fill="#26215C" font-family="Arial, sans-serif">UC-14 Catat penjualan mitra</text>
  <!-- Use cases: Super Admin (coral) -->
  <ellipse cx="350" cy="500" rx="95" ry="18" fill="#FAECE7" stroke="#993C1D" stroke-width="0.8"/>
  <text x="350" y="504" text-anchor="middle" font-size="11" fill="#4A1B0C" font-family="Arial, sans-serif">UC-08 Review data timbangan</text>
  <ellipse cx="260" cy="550" rx="80" ry="18" fill="#FAECE7" stroke="#993C1D" stroke-width="0.8"/>
  <text x="260" y="554" text-anchor="middle" font-size="11" fill="#4A1B0C" font-family="Arial, sans-serif">UC-09 Tambah poin</text>
  <ellipse cx="440" cy="550" rx="80" ry="18" fill="#FAECE7" stroke="#993C1D" stroke-width="0.8"/>
  <text x="440" y="554" text-anchor="middle" font-size="11" fill="#4A1B0C" font-family="Arial, sans-serif">UC-11 Setujui redeem</text>
  <!-- Actor: User -->
  <circle cx="60" cy="180" r="14" fill="none" stroke="#555" stroke-width="1.2"/>
  <line x1="60" y1="194" x2="60" y2="230" stroke="#555" stroke-width="1.2"/>
  <line x1="38" y1="208" x2="82" y2="208" stroke="#555" stroke-width="1.2"/>
  <line x1="60" y1="230" x2="38" y2="255" stroke="#555" stroke-width="1.2"/>
  <line x1="60" y1="230" x2="82" y2="255" stroke="#555" stroke-width="1.2"/>
  <text x="60" y="270" text-anchor="middle" font-size="11" fill="#555" font-family="Arial, sans-serif">User</text>
  <!-- Actor: Admin Gudang -->
  <circle cx="60" cy="380" r="14" fill="none" stroke="#555" stroke-width="1.2"/>
  <line x1="60" y1="394" x2="60" y2="430" stroke="#555" stroke-width="1.2"/>
  <line x1="38" y1="408" x2="82" y2="408" stroke="#555" stroke-width="1.2"/>
  <line x1="60" y1="430" x2="38" y2="455" stroke="#555" stroke-width="1.2"/>
  <line x1="60" y1="430" x2="82" y2="455" stroke="#555" stroke-width="1.2"/>
  <text x="60" y="472" text-anchor="middle" font-size="10" fill="#555" font-family="Arial, sans-serif">Admin Gudang</text>
  <!-- Actor: Super Admin -->
  <circle cx="620" cy="530" r="14" fill="none" stroke="#555" stroke-width="1.2"/>
  <line x1="620" y1="544" x2="620" y2="580" stroke="#555" stroke-width="1.2"/>
  <line x1="598" y1="558" x2="642" y2="558" stroke="#555" stroke-width="1.2"/>
  <line x1="620" y1="580" x2="598" y2="600" stroke="#555" stroke-width="1.2"/>
  <line x1="620" y1="580" x2="642" y2="600" stroke="#555" stroke-width="1.2"/>
  <text x="620" y="614" text-anchor="middle" font-size="10" fill="#555" font-family="Arial, sans-serif">Super Admin</text>
  <!-- Lines User to UC -->
  <line x1="74" y1="175" x2="268" y2="100" stroke="#ccc" stroke-width="0.8"/>
  <line x1="74" y1="178" x2="268" y2="122" stroke="#ccc" stroke-width="0.8"/>
  <line x1="74" y1="182" x2="268" y2="166" stroke="#ccc" stroke-width="0.8"/>
  <line x1="74" y1="185" x2="268" y2="210" stroke="#ccc" stroke-width="0.8"/>
  <line x1="74" y1="188" x2="268" y2="255" stroke="#ccc" stroke-width="0.8"/>
  <!-- Lines Admin to UC -->
  <line x1="74" y1="378" x2="258" y2="312" stroke="#ccc" stroke-width="0.8"/>
  <line x1="74" y1="381" x2="268" y2="355" stroke="#ccc" stroke-width="0.8"/>
  <line x1="74" y1="384" x2="268" y2="400" stroke="#ccc" stroke-width="0.8"/>
  <line x1="74" y1="387" x2="253" y2="445" stroke="#ccc" stroke-width="0.8"/>
  <!-- Lines Super Admin to UC -->
  <line x1="606" y1="528" x2="447" y2="502" stroke="#ccc" stroke-width="0.8"/>
  <line x1="606" y1="532" x2="342" y2="550" stroke="#ccc" stroke-width="0.8"/>
  <line x1="606" y1="535" x2="522" y2="550" stroke="#ccc" stroke-width="0.8"/>
  <!-- Include UC-09 -> UC-08 -->
  <line x1="260" y1="531" x2="295" y2="518" stroke="#bbb" stroke-width="0.8" stroke-dasharray="4 2" marker-end="url(#uc-arrow)"/>
  <text x="252" y="527" text-anchor="end" font-size="10" fill="#999" font-family="Arial, sans-serif">«include»</text>
  <!-- Include UC-11 -> Payment GW note -->
  <text x="530" y="543" font-size="10" fill="#999" font-family="Arial, sans-serif">«include»</text>
  <text x="530" y="556" font-size="10" fill="#999" font-family="Arial, sans-serif">Payment GW</text>
</svg>

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

<svg width="100%" viewBox="0 0 680 760" xmlns="http://www.w3.org/2000/svg" font-family="Arial, sans-serif">
  <defs><marker id="cd-arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M2 1L8 5L2 9" fill="none" stroke="#666" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></marker></defs>
  <rect x="20" y="30" width="180" height="185" rx="6" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <rect x="20" y="30" width="180" height="26" rx="6" fill="#9FE1CB" stroke="#0F6E56" stroke-width="0.8"/>
  <rect x="20" y="44" width="180" height="12" fill="#9FE1CB"/>
  <text x="110" y="48" text-anchor="middle" font-size="12" font-weight="bold" fill="#04342C">User</text>
  <line x1="20" y1="56" x2="200" y2="56" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="32" y="72" font-size="10" fill="#085041">id: int</text>
  <text x="32" y="87" font-size="10" fill="#085041">nama: string</text>
  <text x="32" y="102" font-size="10" fill="#085041">no_telepon: string</text>
  <text x="32" y="117" font-size="10" fill="#085041">email: string</text>
  <text x="32" y="132" font-size="10" fill="#085041">password_hash: string</text>
  <text x="32" y="147" font-size="10" fill="#085041">poin_saldo: int</text>
  <text x="32" y="162" font-size="10" fill="#085041">peran: enum</text>
  <text x="32" y="177" font-size="10" fill="#085041">status: enum</text>
  <text x="32" y="192" font-size="10" fill="#085041">created_at: datetime</text>
  <rect x="240" y="30" width="190" height="200" rx="6" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.8"/>
  <rect x="240" y="30" width="190" height="26" rx="6" fill="#AFA9EC" stroke="#534AB7" stroke-width="0.8"/>
  <rect x="240" y="44" width="190" height="12" fill="#AFA9EC"/>
  <text x="335" y="48" text-anchor="middle" font-size="12" font-weight="bold" fill="#26215C">Deposit</text>
  <line x1="240" y1="56" x2="430" y2="56" stroke="#534AB7" stroke-width="0.5"/>
  <text x="252" y="72" font-size="10" fill="#26215C">id: int</text>
  <text x="252" y="87" font-size="10" fill="#26215C">user_id: int (FK)</text>
  <text x="252" y="102" font-size="10" fill="#26215C">jenis_sampah: string</text>
  <text x="252" y="117" font-size="10" fill="#26215C">estimasi_berat_kg: float</text>
  <text x="252" y="132" font-size="10" fill="#26215C">berat_aktual_kg: float</text>
  <text x="252" y="147" font-size="10" fill="#26215C">poin_diperoleh: int</text>
  <text x="252" y="162" font-size="10" fill="#26215C">status: enum</text>
  <text x="252" y="177" font-size="10" fill="#26215C">admin_gudang_id: int (FK)</text>
  <text x="252" y="192" font-size="10" fill="#26215C">super_admin_id: int (FK)</text>
  <text x="252" y="207" font-size="10" fill="#26215C">created_at: datetime</text>
  <rect x="470" y="30" width="190" height="170" rx="6" fill="#FAEEDA" stroke="#854F0B" stroke-width="0.8"/>
  <rect x="470" y="30" width="190" height="26" rx="6" fill="#FAC775" stroke="#854F0B" stroke-width="0.8"/>
  <rect x="470" y="44" width="190" height="12" fill="#FAC775"/>
  <text x="565" y="48" text-anchor="middle" font-size="12" font-weight="bold" fill="#412402">PriceSetting</text>
  <line x1="470" y1="56" x2="660" y2="56" stroke="#854F0B" stroke-width="0.5"/>
  <text x="482" y="72" font-size="10" fill="#412402">id: int</text>
  <text x="482" y="87" font-size="10" fill="#412402">jenis_sampah: string</text>
  <text x="482" y="102" font-size="10" fill="#412402">harga_per_kg: float</text>
  <text x="482" y="117" font-size="10" fill="#412402">persentase_insentif: float</text>
  <text x="482" y="132" font-size="10" fill="#412402">updated_by: int (FK)</text>
  <text x="482" y="147" font-size="10" fill="#412402">updated_at: datetime</text>
  <text x="482" y="162" font-size="10" fill="#412402">created_at: datetime</text>
  <rect x="20" y="280" width="190" height="165" rx="6" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.8"/>
  <rect x="20" y="280" width="190" height="26" rx="6" fill="#9FE1CB" stroke="#0F6E56" stroke-width="0.8"/>
  <rect x="20" y="294" width="190" height="12" fill="#9FE1CB"/>
  <text x="115" y="298" text-anchor="middle" font-size="12" font-weight="bold" fill="#04342C">PointTransaction</text>
  <line x1="20" y1="306" x2="210" y2="306" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="32" y="322" font-size="10" fill="#085041">id: int</text>
  <text x="32" y="337" font-size="10" fill="#085041">user_id: int (FK)</text>
  <text x="32" y="352" font-size="10" fill="#085041">deposit_id: int (FK)</text>
  <text x="32" y="367" font-size="10" fill="#085041">tipe: enum</text>
  <text x="32" y="382" font-size="10" fill="#085041">jumlah_poin: int</text>
  <text x="32" y="397" font-size="10" fill="#085041">ditambahkan_oleh: int (FK)</text>
  <text x="32" y="412" font-size="10" fill="#085041">created_at: datetime</text>
  <rect x="240" y="280" width="190" height="180" rx="6" fill="#FAECE7" stroke="#993C1D" stroke-width="0.8"/>
  <rect x="240" y="280" width="190" height="26" rx="6" fill="#F5C4B3" stroke="#993C1D" stroke-width="0.8"/>
  <rect x="240" y="294" width="190" height="12" fill="#F5C4B3"/>
  <text x="335" y="298" text-anchor="middle" font-size="12" font-weight="bold" fill="#4A1B0C">Redemption</text>
  <line x1="240" y1="306" x2="430" y2="306" stroke="#993C1D" stroke-width="0.5"/>
  <text x="252" y="322" font-size="10" fill="#4A1B0C">id: int</text>
  <text x="252" y="337" font-size="10" fill="#4A1B0C">user_id: int (FK)</text>
  <text x="252" y="352" font-size="10" fill="#4A1B0C">jumlah_poin: int</text>
  <text x="252" y="367" font-size="10" fill="#4A1B0C">metode: enum</text>
  <text x="252" y="382" font-size="10" fill="#4A1B0C">no_ewallet: string</text>
  <text x="252" y="397" font-size="10" fill="#4A1B0C">status: enum</text>
  <text x="252" y="412" font-size="10" fill="#4A1B0C">diproses_oleh: int (FK)</text>
  <text x="252" y="427" font-size="10" fill="#4A1B0C">created_at: datetime</text>
  <rect x="470" y="280" width="190" height="150" rx="6" fill="#EAF3DE" stroke="#3B6D11" stroke-width="0.8"/>
  <rect x="470" y="280" width="190" height="26" rx="6" fill="#C0DD97" stroke="#3B6D11" stroke-width="0.8"/>
  <rect x="470" y="294" width="190" height="12" fill="#C0DD97"/>
  <text x="565" y="298" text-anchor="middle" font-size="12" font-weight="bold" fill="#173404">InventoryItem</text>
  <line x1="470" y1="306" x2="660" y2="306" stroke="#3B6D11" stroke-width="0.5"/>
  <text x="482" y="322" font-size="10" fill="#173404">id: int</text>
  <text x="482" y="337" font-size="10" fill="#173404">jenis_sampah: string</text>
  <text x="482" y="352" font-size="10" fill="#173404">jumlah_kg: float</text>
  <text x="482" y="367" font-size="10" fill="#173404">status_stok: enum</text>
  <text x="482" y="382" font-size="10" fill="#173404">admin_id: int (FK)</text>
  <text x="482" y="397" font-size="10" fill="#173404">created_at: datetime</text>
  <rect x="470" y="490" width="190" height="165" rx="6" fill="#EAF3DE" stroke="#3B6D11" stroke-width="0.8"/>
  <rect x="470" y="490" width="190" height="26" rx="6" fill="#C0DD97" stroke="#3B6D11" stroke-width="0.8"/>
  <rect x="470" y="504" width="190" height="12" fill="#C0DD97"/>
  <text x="565" y="508" text-anchor="middle" font-size="12" font-weight="bold" fill="#173404">PartnerSale</text>
  <line x1="470" y1="516" x2="660" y2="516" stroke="#3B6D11" stroke-width="0.5"/>
  <text x="482" y="532" font-size="10" fill="#173404">id: int</text>
  <text x="482" y="547" font-size="10" fill="#173404">jenis_sampah: string</text>
  <text x="482" y="562" font-size="10" fill="#173404">jumlah_kg: float</text>
  <text x="482" y="577" font-size="10" fill="#173404">harga_jual_per_kg: float</text>
  <text x="482" y="592" font-size="10" fill="#173404">total_nilai: float</text>
  <text x="482" y="607" font-size="10" fill="#173404">mitra_tujuan: string</text>
  <text x="482" y="622" font-size="10" fill="#173404">admin_id: int (FK)</text>
  <text x="482" y="637" font-size="10" fill="#173404">tanggal_penjualan: date</text>
  <line x1="200" y1="130" x2="240" y2="130" stroke="#888" stroke-width="1" marker-end="url(#cd-arrow)"/>
  <text x="207" y="124" font-size="10" fill="#888">1</text><text x="230" y="124" font-size="10" fill="#888">N</text>
  <line x1="115" y1="215" x2="115" y2="280" stroke="#888" stroke-width="1" marker-end="url(#cd-arrow)"/>
  <text x="120" y="252" font-size="10" fill="#888">1..N</text>
  <line x1="175" y1="185" x2="260" y2="306" stroke="#888" stroke-width="1" marker-end="url(#cd-arrow)"/>
  <text x="195" y="246" font-size="10" fill="#888">1..N</text>
  <line x1="290" y1="230" x2="160" y2="286" stroke="#888" stroke-width="1" marker-end="url(#cd-arrow)"/>
  <text x="215" y="258" font-size="10" fill="#888">0..1</text>
  <line x1="470" y1="115" x2="430" y2="120" stroke="#888" stroke-width="1" marker-end="url(#cd-arrow)"/>
  <text x="440" y="110" font-size="10" fill="#888">1..N</text>
  <line x1="565" y1="430" x2="565" y2="490" stroke="#888" stroke-width="1" marker-end="url(#cd-arrow)"/>
  <text x="572" y="464" font-size="10" fill="#888">1..N</text>
</svg>

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
