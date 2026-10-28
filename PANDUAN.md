# Panduan: KelasKita (GitHub -> Vercel)

## 1. Unggah ke GitHub
1. Buat repositori baru di GitHub (mis. `kelaskita`), **Private** disarankan.
2. Ekstrak zip ini. Isi folder (`public/`, `vercel.json`, `package.json`, `.gitignore`, `README.md`, `PANDUAN.md`)
   harus berada di **root** repositori, bukan di dalam folder tambahan.
3. Unggah lewat GitHub web (Add file -> Upload files, seret semua isi folder) atau lewat Git:
   ```bash
   git init
   git add .
   git commit -m "KelasKita v5: backup & restore, siap Vercel"
   git branch -M main
   git remote add origin https://github.com/USERNAME/kelaskita.git
   git push -u origin main
   ```

## 2. Impor ke Vercel
1. Vercel -> Add New -> Project -> pilih repositori tadi -> Import.
2. Pengaturan dibaca otomatis dari `vercel.json`. Pastikan tampil:
   Framework Preset **Other**, Build Command kosong, Install Command kosong, Output Directory **public**.
3. Klik **Deploy**. Tidak ada environment variable yang diperlukan (konfigurasi Firebase web ada di `public/index.html`;
   itu konfigurasi publik, bukan kunci rahasia).
4. Setiap `git push` ke `main` otomatis men-deploy ulang.

## 3. WAJIB: daftarkan domain Vercel di Firebase (agar login admin berhasil)
1. console.firebase.google.com -> proyek `kelaskita-f7ca1`.
2. Authentication -> Settings -> **Authorized domains** -> **Add domain**.
3. Tambahkan domain Vercel Anda (mis. `kelaskita.vercel.app`) dan domain sekolah bila dipakai
   (mis. `kelaskita.smpn1-bandarlampung.sch.id`).
Tanpa langkah ini login admin gagal dengan pesan seperti "unauthorized-domain".
Jika API key Firebase Anda dibatasi per situs (HTTP referrer) di Google Cloud Console, tambahkan domain baru di sana juga.

## 4. Domain sekolah (opsional)
Vercel -> Project -> Settings -> Domains -> tambahkan subdomain baru (jangan `app`, itu dipakai Blogger),
lalu buat record DNS (CNAME) sesuai instruksi Vercel.

## 5. Tentang link lama di Blogger
Link Blogger lama tidak berubah dan tetap berfungsi. PWA memakai alamat Vercel yang baru;
pegawai dan siswa memasang aplikasi dari alamat baru itu. Data (Firestore) sama, jadi data lama tetap muncul.

## 6. Backup & Restore Database (menu Setting, khusus Admin)
**Unduh backup:** masuk sebagai Admin -> Setting -> *Backup & Restore Database* -> **Unduh Backup Sekarang**.
Hasilnya satu file `backup-kelaskita-TANGGAL-JAM.json` berisi koleksi: settings, siswa, guru, setGuru, bobot,
presensi, tugas, kumpul, nilai.

**Pulihkan:** pilih file backup -> aplikasi memeriksa file dan menampilkan jumlah data -> pilih mode:
- **Gabungkan (disarankan):** menambah data yang belum ada dan menimpa data dengan ID yang sama. Tidak menghapus apa pun.
- **Ganti total:** database dibuat sama persis dengan file backup; data yang tidak ada di backup dihapus permanen
  (pengaturan aplikasi tidak pernah dihapus).

Sebelum pemulihan dimulai, aplikasi otomatis mengunduh `pengaman-sebelum-restore-kelaskita-....json`
berisi kondisi database saat ini. Simpan file itu sampai Anda yakin hasilnya benar.

Catatan penting:
- File yang diunggah ke Google Drive (tugas/nilai) tidak ikut di-backup; yang tersimpan hanya tautan/datanya di Firestore.
- File backup berisi data pribadi dan password siswa/guru (disimpan sebagai teks biasa di Firestore sejak awal).
  Jangan di-commit ke GitHub (sudah dicegah `.gitignore`), jangan dibagikan, simpan di tempat aman.
- Backup membaca seluruh dokumen dan restore menulisnya satu per satu, sehingga memakai kuota baca/tulis Firestore.
  Pada data besar, proses bisa memakan waktu beberapa menit; jangan tutup halaman sampai selesai.
- Backup dan restore berjalan dengan akun Admin dan sudah diperiksa terhadap `firestore.rules` (versi `END`): admin boleh
  membaca dan menulis semua koleksi. Satu pengecualian dari rules ada pada koleksi `kumpul`: kiriman berstatus
  "Dikonfirmasi" tidak boleh ditimpa status lain. Saat restore, kiriman seperti itu otomatis dihapus lalu dibuat ulang
  dari backup, sehingga data kembali persis seperti saat backup. Jika rules Anda diubah, uji restore sekali pada data contoh.
- Backup bersifat manual. Biasakan mengunduh sebelum perubahan besar (awal semester, impor data, naik kelas, dll).

## 7. Memperbarui aplikasi
1. Ubah file di `public/`. Naikkan `VERSION` di `public/sw.js` (sekarang `kk-v5`, berikutnya `kk-v6`, dst.).
2. `git commit` lalu `git push`. Vercel otomatis deploy.
3. Pengguna membuka aplikasi dengan internet aktif; tutup lalu buka lagi bila tampilan belum berubah.

## 8. Apps Script (Code.gs) dan firestore.rules
Tidak perlu diubah. URL Apps Script yang tersimpan di Setting Admin tetap dipakai.
Upload tugas memanggil script.google.com langsung dan tidak pernah di-cache oleh aplikasi.

## 9. Tombol Back di HP
- Jika ada dialog terbuka, Back menutupnya (sama dengan Batal). Dialog progres unggah dan dialog wajib tanpa
  tombol Batal tidak ditutup paksa oleh Back.
- Jika tidak ada dialog, Back kembali ke menu sebelumnya.
- Di menu utama, Back pertama menampilkan "Tekan sekali lagi untuk keluar"; Back kedua (sekitar 2 detik) keluar.
- Di layar login Back langsung keluar. Setelah Logout, riwayat dibersihkan.

## 10. Catatan lain
- Di iPhone, aplikasi terpasang menyimpan data terpisah dari Safari: login sekali lagi setelah memasang.
- Ikon masih sementara; ganti 4 file di `public/icons/` (192, 512, maskable 512, apple-touch 180) dengan logo sekolah.
- Jika Vercel menampilkan 404, pastikan Output Directory = `public` dan `index.html` ada di dalamnya.
