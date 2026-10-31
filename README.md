# GALAK BOT — Web Dashboard

Dashboard web untuk bot WhatsApp pribadi GALAK BOT. Penghubungan perangkat tersedia lewat **QR** atau **kode tautan**. Setelah terhubung, perintah bot dapat digunakan di chat pribadi maupun grup.

> Dashboard ini adalah aplikasi yang harus di-deploy ke server Node.js. ZIP ini belum menjadi website online. Sediakan satu server yang tetap hidup dan penyimpanan persisten untuk folder sesi WhatsApp.

## Fitur

- Login dashboard dengan password.
- Hubungkan WhatsApp melalui QR yang tampil di website.
- Hubungkan WhatsApp melalui kode tautan 8 karakter.
- Status koneksi dan nomor perangkat tertaut.
- Putuskan perangkat dari dashboard.
- Bot pribadi untuk chat pribadi dan grup.
- Perintah: `!menu`, `!ping`, `!brat teks`, `!bratsticker teks`, `!sticker`, `!toimg`, `!qr teks/link`, `!reverse teks`, `!upper teks`, `!lower teks`.
- Sesi tersimpan di direktori `AUTH_DIR` dan dipulihkan otomatis setelah restart.

## Menjalankan lokal

Persyaratan: Node.js 20 atau lebih baru.

1. Ekstrak ZIP.
2. Salin `.env.example` menjadi `.env`.
3. Ubah `DASHBOARD_PASSWORD` menjadi password unik minimal 12 karakter. Ubah `SESSION_SECRET` menjadi string acak panjang.
4. Buka terminal di folder proyek, lalu jalankan:

   ```bash
   npm install
   npm start
   ```

5. Buka `http://localhost:3000`.
6. Login memakai `DASHBOARD_PASSWORD` dari `.env`.
7. Pilih **Scan QR** atau **Kode tautan**.

Untuk mode kode, masukkan nomor dengan kode negara, misalnya `6281234567890`. Setelah kode tampil, buka WhatsApp → Perangkat tertaut → Tautkan perangkat → Tautkan dengan nomor telepon, lalu masukkan kode tersebut.

## Deploy ke Render

Project menyertakan `render.yaml` yang menyiapkan Node web service dan persistent disk 1 GB di `/var/data`.

1. Buat repository GitHub pribadi dan unggah isi folder proyek ini. **Jangan unggah `.env`, `data/`, atau folder auth.** `.gitignore` sudah mengecualikannya.
2. Di Render, buat Blueprint baru dari repository tersebut.
3. Saat diminta, isi environment variable rahasia `DASHBOARD_PASSWORD` dengan password minimal 12 karakter.
4. Deploy service. Buka URL `onrender.com` yang diberikan Render dan login.
5. Pilih QR atau kode tautan untuk menghubungkan akun WhatsApp.

Blueprint memakai paket web service berbayar `starter` karena sesi WhatsApp harus tetap tersimpan di persistent disk. Filesystem service biasa bersifat sementara; tanpa disk persisten, sesi bisa hilang saat deploy/restart. Pantau biaya dan paket layanan di dashboard hosting sebelum membuat service.

## Environment variables

| Nama | Keterangan |
|---|---|
| `DASHBOARD_PASSWORD` | Password login dashboard, wajib, minimal 12 karakter |
| `SESSION_SECRET` | Rahasia cookie sesi; isi string acak panjang |
| `PORT` | Port web server, default `3000` |
| `AUTH_DIR` | Lokasi data sesi WhatsApp, default `./data/auth_info_baileys` |
| `NODE_ENV` | Gunakan `production` saat hosting |
| `TRUST_PROXY` | Set `1` bila memakai reverse proxy di depan server |

## Keamanan sesi

- Jangan bagikan URL dashboard, password, QR, kode tautan, atau isi direktori `AUTH_DIR`.
- Login dilindungi cookie `httpOnly`, `sameSite=strict`, dan HTTPS cookie saat production.
- Endpoint login dibatasi percobaannya.
- Dashboard tidak menyediakan fitur membaca arsip chat; bot hanya memproses perintah yang diawali `!`.
- Tombol **Putuskan perangkat** mengeluarkan perangkat tertaut dan menghapus file sesi tersimpan di server.

## Catatan teknis

Project memakai Baileys 6.7.24 agar kompatibel dengan handler bot v6 yang sudah ada. Baileys adalah library WebSocket tidak resmi untuk WhatsApp Web. Library dan protokol WhatsApp dapat berubah; jika koneksi gagal setelah perubahan WhatsApp, dependency mungkin perlu diperbarui dan diuji.
