# KelasKita (PWA)

Aplikasi KelasKita untuk SMP Negeri 1 Bandar Lampung: presensi, tugas, nilai, rekap, dan cetak.
Database: Cloud Firestore + Firebase Authentication (admin). Hosting: Vercel (via GitHub).

## Struktur
```
.
├── public/                  <- seluruh file situs (di-deploy apa adanya)
│   ├── index.html
│   ├── pwa.js
│   ├── sw.js                <- naikkan VERSION setiap index.html berubah
│   ├── manifest.webmanifest
│   └── icons/
├── vercel.json              <- output directory + header keamanan/cache
├── package.json
├── .gitignore
└── PANDUAN.md
```
Tidak ada proses build. Vercel langsung menyajikan folder `public/`.

## Jalankan lokal
```bash
npm run dev      # membuka http://localhost:3000
```
Login admin lokal memerlukan `localhost` terdaftar di Firebase -> Authentication -> Authorized domains
(biasanya sudah ada secara default).

## Deploy
Lihat `PANDUAN.md`.
