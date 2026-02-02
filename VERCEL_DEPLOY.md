# Panduan Deployment ke Vercel

Proyek ini adalah aplikasi **Next.js**, yang sangat mudah di-deploy ke Vercel. Namun, ada beberapa hal yang perlu diperhatikan agar aplikasi berjalan lancar.

## 1. Persiapan Deployment

### A. Repositori GitHub
Pastikan kode Anda sudah di-push ke GitHub.
```bash
git add .
git commit -m "Siap deploy ke Vercel"
git push
```

### B. Bersihkan File Lama (Sudah Dilakukan)
Saya telah membersihkan file konfigurasi Vite (`vite-env.d.ts`, `tsconfig.app.json`, dll) agar tidak membingungkan proses build Next.js.
Saya juga telah mengaktifkan rate limiting dengan mengubah `src/proxy.ts` menjadi `src/middleware.ts`.

## 2. Cara Deploy di Vercel

1. **Login ke Vercel** (https://vercel.com) dan klik **"Add New..."** > **"Project"**.
2. **Import Git Repository**: Pilih repositori GitHub proyek ini.
3. **Konfigurasi Project**:
   - **Framework Preset**: Next.js (seharusnya terdeteksi otomatis).
   - **Root Directory**: `./` (default).
   - **Build Command**: `next build` (default).

4. **Environment Variables**:
   Penting! Anda harus menambahkan variabel lingkungan berikut di bagian **Environment Variables**:

   | Key | Value (Contoh) | Deskripsi |
   |-----|---------------|-----------|
   | `NEXT_PUBLIC_API_BASE_URL` | `https://api.sansekai.my.id/api` | URL backend API yang digunakan. |
   | `NEXT_PUBLIC_CRYPTO_SECRET` | `Sansekai-SekaiDrama` | Kunci rahasia untuk enkripsi/dekripsi data. |

5. **Klik "Deploy"**.

## 3. Catatan Penting

### Proxy & Streaming
Aplikasi ini menggunakan API Route (`/api/proxy/video`) untuk mem-proxy video stream dan subtitle.
- **Limitasi**: Di plan **Hobby (Gratis)**, Vercel memiliki batas waktu eksekusi serverless function selama **10 detik** (kadang bisa sampai 60s jika dikonfigurasi, saya sudah menambahkan config `maxDuration = 60` di kode).
- Jika streaming video terputus-putus atau error 504 Gateway Timeout, itu mungkin karena limitasi Vercel Hobby. Solusinya adalah upgrade ke Pro atau memindahkan API proxy ke server VPS terpisah.

### Rate Limiting
Middleware untuk rate limiting sudah aktif (`src/middleware.ts`). Ingat bahwa pada Vercel, state rate limit disimpan di memori function yang bersifat *ephemeral* (sementara). Artinya, limit tidak akan seakurat jika menggunakan database (seperti Redis/KV), tapi cukup untuk basic protection.
