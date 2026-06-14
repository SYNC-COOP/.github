<div align="center">
  <img src="sync_coop_app/assets/images/ic_launcher.png" width="150" alt="SYNC-COOP Logo">
  <h1>SYNC-COOP</h1>
  <p><strong>Platform Koperasi Peternakan Digital — Offline-First & Multi-Tenant</strong></p>

  <p>
    <img src="https://img.shields.io/badge/Flutter-3.x-02569B?logo=flutter" alt="Flutter">
    <img src="https://img.shields.io/badge/Go-1.24-00ADD8?logo=go" alt="Go">
    <img src="https://img.shields.io/badge/Next.js-16-000000?logo=next.js" alt="Next.js">
    <img src="https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql" alt="PostgreSQL">
  </p>
</div>

---

## Tentang SYNC-COOP

**SYNC-COOP** adalah platform manajemen koperasi peternakan modern yang dibangun dengan pendekatan **Offline-First** — setiap data dicatat secara instan ke perangkat lokal tanpa bergantung pada koneksi internet, lalu disinkronisasi secara otomatis ke server pusat saat jaringan kembali tersedia.

Platform ini terdiri dari **tiga komponen utama** yang saling terintegrasi:

| Komponen | Teknologi | Deskripsi |
|---|---|---|
| **Mobile App** | Flutter (Dart 3.x) | Aplikasi lapangan untuk petugas & peternak |
| **Backend API** | Go 1.24 + Gin + PostgreSQL | REST API untuk mobile sync dan web admin |
| **Admin Portal** | Next.js 16 + React 19 | Dashboard web untuk pengurus koperasi |

Sistem ini mendukung arsitektur **Multi-Tenant**, sehingga satu infrastruktur dapat melayani berbagai koperasi secara mandiri dan terisolasi — data antar koperasi tidak pernah bercampur.

---

## Preview Aplikasi

<div align="center">
  <img src="sync_coop_app/assets/images/preview1.png" width="1500" alt="Preview SYNC-COOP">
</div>

---

## Fitur Utama

### Pendekatan Offline-First
Petugas lapangan tidak pernah terhenti karena jaringan buruk.
- Setiap aksi langsung tersimpan ke memori lokal perangkat (zero-latency).
- Indikator status warna real-time: **Lokal** (🟠) → **Sinkronisasi** (🔵) → **Tersinkron** (🟢) → **Error** (🔴).
- Sinkronisasi latar belakang berjalan otomatis dan senyap saat terhubung ke internet.

### Manajemen Peternak & Kandang
- Data lengkap anggota peternak beserta peta persebaran kandang interaktif (Leaflet.js).
- Registrasi kandang baru dengan foto bukti dan deteksi lokasi GPS otomatis (*reverse geocoding*).
- Cetak kartu anggota fisik berformat CR80 lengkap dengan QR Code unik.

### Pencatatan Setoran & Kupon Pakan
- Catat setoran produksi harian (susu, dll.) berdasarkan kandang dan jenis ternak.
- Pemindaian kupon pakan QR secara luring — validasi kuota tetap berjalan tanpa internet.
- Monitoring stok pakan koperasi dan peringatan stok kritis di dasbor admin.

### Pencatatan Mortalitas (Terenkripsi)
- Catat kejadian kematian/sakit dengan validasi jumlah tidak melebihi populasi aktif.
- Lampiran foto bukti diunggah secara aman setelah sinkronisasi teks berhasil.

### Gaji & Pembukuan (Payroll)
- Rekonsiliasi otomatis: setoran produksi bulanan dikurangi bon pakan per anggota.
- Slip gaji siap cetak langsung dari portal admin.

### Smart Conflict Resolution
- Penyelesaian konflik data otomatis saat dua petugas mengubah data yang sama secara luring.

### Jejak Audit Terpusat
- Log aktivitas menyeluruh: penjualan, transaksi pakan, mortalitas, setoran, slip gaji, dan sinkronisasi perangkat.

---

## Arsitektur Sistem

```
┌─────────────────────┐        ┌──────────────────────┐
│   Mobile App        │        │   Admin Portal       │
│   (Flutter)         │        │   (Next.js 16)       │
│                     │        │                      │
│  - Offline SQLite   │        │  - Dashboard         │
│  - Background Sync  │        │  - Manajemen Data    │
│  - QR Scanner       │        │  - Peta Kandang      │
│  - GPS & Geocoding  │        │  - Payroll & Print   │
└────────┬────────────┘        └──────────┬───────────┘
         │  Mobile Sync API (port 8081)   │  Web Admin API (port 8080)
         └──────────────┬─────────────────┘
                        │
              ┌─────────▼──────────┐
              │   Backend Service  │
              │   (Go + Gin)       │
              │                    │
              │  - Multi-Tenant    │
              │  - Sync Engine     │
              │  - JWT Auth        │
              │  - Audit Log       │
              └─────────┬──────────┘
                        │
              ┌─────────▼──────────┐
              │   PostgreSQL       │
              │   (Bun ORM)        │
              └────────────────────┘
```

---

## Struktur Monorepo

```
hackathon/
├── sync_coop_app/          # Aplikasi Mobile Flutter
│   ├── lib/
│   ├── assets/
│   └── pubspec.yaml
│
├── sync-coop-backend/      # Backend Service Go
│   ├── internal/
│   │   ├── constants/      # Status, pesan error, konstanta
│   │   ├── controllers/    # Handler HTTP Gin
│   │   ├── domain/         # Model Bun ORM
│   │   ├── dto/            # Request validation & response format
│   │   ├── repository/     # Query PostgreSQL
│   │   ├── routes/         # Definisi rute & middleware
│   │   └── services/       # Business logic
│   ├── migrations/         # DDL SQL migration files
│   ├── pkg/
│   │   ├── authentication/ # JWT & enkripsi
│   │   ├── config/         # Konfigurasi environment
│   │   ├── database/       # Driver PostgreSQL Bun
│   │   ├── errors/         # Custom error handler
│   │   └── utils/          # Helper functions
│   └── main.go
│
└── sync-coop-nextjs/       # Admin Portal Next.js
    ├── app/
    │   ├── (auth)/         # Halaman login
    │   ├── (dashboard)/    # Dashboard utama + AuthGuard
    │   └── print/          # Route cetak slip & kartu
    ├── components/         # UI components (shadcn/ui)
    ├── features/           # Modul fitur mandiri
    │   ├── anggota/
    │   ├── audit-log/
    │   ├── dashboard/
    │   ├── kupon-pakan/
    │   ├── payroll/
    │   ├── stok-pakan/
    │   └── ternak/
    ├── hooks/
    ├── lib/
    ├── services/
    └── types/
```

---

## Stack Teknologi

### Mobile App (Flutter)
| Kategori | Library |
|---|---|
| UI & State | `provider` (SyncProvider, AuthProvider, LivestockProvider) |
| Database Lokal | `sqflite` |
| Networking | `dio` |
| Lokasi | `geolocator`, `geocoding` |
| QR Scanner | `mobile_scanner` |

### Backend (Go)
| Kategori | Library |
|---|---|
| Framework | `Gin Web Framework` |
| ORM / Database | `Bun ORM` + `PostgreSQL` |
| Autentikasi | `JWT (JSON Web Token)` |
| ID Generation | `KSUID` |

### Admin Portal (Next.js)
| Kategori | Library |
|---|---|
| Framework | `Next.js 16 (App Router)`, `React 19` |
| Bahasa | `TypeScript (Strict Mode)` |
| Styling | `Tailwind CSS v4`, `shadcn/ui`, `Radix UI` |
| Peta | `Leaflet.js` |
| HTTP Client | `Axios` |
| Notifikasi | `Sonner` |

---

## Panduan Instalasi & Menjalankan

### Prasyarat
- **Flutter SDK** (v3.x) — untuk Mobile App
- **Go 1.24+** — untuk Backend
- **Node.js 20+** dan **npm** — untuk Admin Portal
- **PostgreSQL 14+** — untuk database

---

### 1. Backend Service (Go)

```bash
cd sync-coop-backend

# Salin konfigurasi environment
cp .env.example pkg/config/files/env.yaml
```

Edit `pkg/config/files/env.yaml` dan sesuaikan kredensial database:
```yaml
database:
  host: localhost
  port: 5432
  user: postgres
  password: password_anda
  name: sync_coop_db
  sslmode: disable
  timezone: Asia/Jakarta
```

```bash
# Jalankan migrasi database secara berurutan
psql -U postgres -d sync_coop_db -f migrations/001_create_users_table.up.sql
psql -U postgres -d sync_coop_db -f migrations/002_create_authentications_table.up.sql
# ... Lanjutkan hingga file migrasi terakhir

# Instalasi dependensi
go mod tidy

# Jalankan server
go run main.go
```

Server aktif di:
- `http://localhost:8080` — Web Admin API
- `http://localhost:8081` — Mobile Sync API

---

### 2. Admin Portal (Next.js)

```bash
cd sync-coop-nextjs

# Salin konfigurasi environment
cp .env.example .env.local
```

Edit `.env.local`:
```env
NEXT_PUBLIC_API_URL=http://localhost:8080
```

```bash
# Instalasi dependensi
npm install

# Jalankan development server
npm run dev
```

Buka [http://localhost:3000](http://localhost:3000) di browser.

Untuk build produksi:
```bash
npm run build
```

---

### 3. Mobile App (Flutter)

```bash
cd sync_coop_app

# Verifikasi lingkungan Flutter
flutter doctor

# Instalasi dependensi
flutter pub get

# Hubungkan perangkat fisik atau jalankan emulator, lalu:
flutter run
```

> Gunakan akun demo `petugas@synccoop.com` dengan peran **Petugas/Pengurus Lapangan** untuk menjajal fitur secara penuh.

---

## Alur Sinkronisasi Data

```
[Petugas di Lapangan]
        │
        │  1. Catat data (offline)
        ▼
[SQLite Lokal — zero latency]
        │
        │  2. Internet tersedia → trigger background sync
        ▼
[Sync Push → Backend API]
        │
        │  3. Backend memproses & menyimpan ke PostgreSQL
        ▼
[PostgreSQL — data tersentralisasi]
        │
        │  4. Sync Pull → kirim delta perubahan master data
        ▼
[SQLite Lokal diperbarui]
        │
        │  5. Admin memantau via portal web
        ▼
[Next.js Admin Portal — realtime dashboard]
```

---

## Akun Demo

| Peran | Email | Keterangan |
|---|---|---|
| Petugas Lapangan | `petugas@synccoop.com` | Akses mobile app penuh |

---

## Lisensi

Proyek ini dikembangkan untuk keperluan kompetisi hackathon. Seluruh hak cipta dipegang oleh tim pengembang.

---

## Tertarik Melanjutkan atau Membeli Platform Ini?

Platform **SYNC-COOP** terbuka untuk kolaborasi, pengembangan lanjutan, maupun akuisisi komersial. Jika Anda adalah koperasi, dinas peternakan, investor, atau pengembang yang ingin membawa solusi ini ke level berikutnya, kami dengan senang hati berdiskusi.

**Hubungi kami di:**

📧 **dev.ngabroger@gmail.com**

> Kami siap mendiskusikan kebutuhan kustomisasi, deployment produksi, lisensi penggunaan, maupun potensi kerja sama pengembangan lebih lanjut.

---

<div align="center">
  <strong>SYNC-COOP</strong> — <em>Menghubungkan Peternakan, Menjamin Ketersediaan Data.</em>
</div>
