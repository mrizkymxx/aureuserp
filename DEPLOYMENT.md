# 🚀 Deployment Guide - Railway + Supabase (100% GRATIS)

Panduan lengkap deploy AureusERP untuk keperluan belajar dengan stack gratis.

## 📋 Stack yang Digunakan

- **Hosting**: Railway.app (Free $5/month credit)
- **Database**: Supabase PostgreSQL (Free 500MB)
- **Storage**: Cloudinary (Free 25GB) - opsional untuk file uploads
- **Cost**: **$0/bulan** untuk belajar!

---

## 🎯 Langkah 1: Setup Supabase Database

### 1.1 Buat Akun Supabase
1. Kunjungi [https://supabase.com](https://supabase.com)
2. Sign up dengan GitHub account (gratis)
3. Create new project:
   - **Project name**: `aureuserp-db` (atau nama lain)
   - **Database Password**: Simpan password ini! (butuh nanti)
   - **Region**: Pilih terdekat (Singapore recommended)
   - **Pricing Plan**: Free tier ✅

### 1.2 Dapatkan Database Credentials (Update 2025)
Setelah project dibuat (~2 menit):

**Cara Baru (Recommended):**
1. Pergi ke **Project Settings** (icon ⚙️ di sidebar kiri bawah)
2. Klik tab **Database**
3. Di bagian **Connection Info**, pilih **Session mode** (bukan Transaction mode)
4. Copy informasi berikut:
   ```
   Host: db.xxxxxxxxxxxxx.supabase.co
   Database name: postgres
   Port: 5432
   User: postgres.xxxxxxxxxxxxx
   Password: [password yang tadi dibuat]
   ```

**⚠️ PENTING - Connection Pooling:**
Supabase punya 2 mode:
- **Direct Connection** (Port 5432) - Max 60 connections
- **Connection Pooler** (Port 6543) - Untuk production dengan traffic tinggi

Untuk belajar, **gunakan Direct Connection (Port 5432)** saja.

**✅ Format untuk Railway Environment Variables:**
```bash
DB_CONNECTION=pgsql
DB_HOST=db.xxxxxxxxxxxxx.supabase.co
DB_PORT=5432
DB_DATABASE=postgres
DB_USERNAME=postgres.xxxxxxxxxxxxx  # ⚠️ Perhatikan format username!
DB_PASSWORD=your-password-here
```

**🔧 Troubleshooting Koneksi:**
Jika masih error "Connection refused" atau "SSL required":
1. Pastikan SSL mode enabled di config/database.php
2. Atau gunakan alternatif Railway PostgreSQL (lihat Opsi B di bawah)

---

## 🎯 Langkah 1B: Alternatif - Railway PostgreSQL (LEBIH MUDAH!)

**Jika Supabase sulit, gunakan Railway PostgreSQL - lebih simple!**

### Kenapa Railway PostgreSQL Lebih Mudah?
- ✅ Terintegrasi langsung dengan Railway
- ✅ Auto-configure environment variables
- ✅ Tidak perlu manual setup SSL
- ✅ Masih **GRATIS** (masuk di $5 credit yang sama)

### Setup Railway PostgreSQL:
1. Di Railway dashboard, klik **New** → **Database** → **PostgreSQL**
2. Railway akan auto-create database dan set semua env variables
3. **Done!** Tidak perlu manual config apapun

Environment variables akan otomatis:
- `DATABASE_URL` (Railway format)
- `PGHOST`, `PGPORT`, `PGDATABASE`, `PGUSER`, `PGPASSWORD`

**Yang perlu dilakukan:** Copy nilai `DATABASE_URL` atau gunakan individual variables di Laravel .env

---

## 🚂 Langkah 2: Setup Railway

### 2.1 Persiapan Repository
1. Push project ini ke GitHub:
   ```bash
   git add .
   git commit -m "Add Railway deployment config"
   git push origin master
   ```

### 2.2 Deploy ke Railway
1. Kunjungi [https://railway.app](https://railway.app)
2. **Sign in with GitHub**
3. Click **New Project**
4. Pilih **Deploy from GitHub repo**
5. Authorize Railway untuk access repository
6. Pilih repository `aureuserp`

### 2.3 Konfigurasi Environment Variables
Railway akan auto-detect Laravel project. Sekarang tambahkan environment variables:

1. Di Railway dashboard, klik project Anda
2. Pergi ke **Variables** tab
3. Tambahkan variables berikut:

#### **Required Variables:**
```bash
# Application
APP_NAME=AureusERP
APP_ENV=production
APP_KEY=                    # Akan di-generate otomatis
APP_DEBUG=false
APP_URL=https://your-app.railway.app  # Railway akan provide ini

# Database (dari Supabase)
DB_CONNECTION=pgsql
DB_HOST=db.xxx.supabase.co  # Dari Supabase
DB_PORT=5432
DB_DATABASE=postgres
DB_USERNAME=postgres
DB_PASSWORD=your-supabase-password  # Password Supabase

# Session & Cache
SESSION_DRIVER=database
CACHE_STORE=database
QUEUE_CONNECTION=database

# Mail (gunakan log dulu)
MAIL_MAILER=log

# File Storage (gunakan local dulu)
FILESYSTEM_DISK=public
```

### 2.4 Generate Application Key
Di Railway terminal (klik **Deployments** → pilih latest deployment → **View Logs**):
```bash
php artisan key:generate --show
```
Copy output-nya dan paste ke `APP_KEY` variable di Railway.

---

## 🔧 Langkah 3: Inisialisasi Database

### 3.1 Run Migrations via Railway
Railway punya terminal. Gunakan untuk run artisan commands:

1. Di Railway dashboard, click **Deployments**
2. Click active deployment
3. Click terminal icon atau **Connect**
4. Jalankan:

```bash
# Run migrations
php artisan migrate --force

# Install ERP (sesuai README)
php artisan erp:install
```

### 3.2 Troubleshooting Migrations
Jika ada error dengan migrations karena MySQL syntax:
- ERP ini mungkin punya beberapa MySQL-specific syntax
- Check error log di Railway
- Bisa edit migration files jika perlu

---

## 📦 Langkah 4: Konfigurasi File Storage (Opsional)

Untuk production, Railway tidak punya persistent storage. Untuk belajar, bisa:

### Option A: Cloudinary (Recommended - Free 25GB)
1. Sign up di [https://cloudinary.com](https://cloudinary.com)
2. Dapatkan credentials dari dashboard
3. Install package:
   ```bash
   composer require cloudinary-labs/cloudinary-laravel
   ```
4. Add ke Railway variables:
   ```
   CLOUDINARY_URL=cloudinary://key:secret@cloud_name
   FILESYSTEM_DISK=cloudinary
   ```

### Option B: Tetap Local (Untuk testing)
- File akan hilang setiap deploy
- Cukup untuk testing tanpa file upload

---

## ✅ Langkah 5: Testing Deployment

### 5.1 Access Application
1. Railway akan provide URL: `https://your-project.railway.app`
2. Buka di browser
3. Login dengan credentials yang dibuat saat `erp:install`

### 5.2 Check Logs
Di Railway dashboard → **Deployments** → **View Logs**

---

## 🎓 Tips untuk Belajar

### Free Tier Limitations:
- **Railway**: $5 credit/month (cukup untuk ~100-500 hours uptime)
- **Supabase**: 500MB database + 2GB bandwidth (cukup untuk development)
- **Cloudinary**: 25GB storage (lebih dari cukup)

### Optimasi untuk Stay Free:
1. **Matikan saat tidak dipakai**:
   ```bash
   # Di Railway, bisa pause service
   ```

2. **Monitor usage**:
   - Railway dashboard show credit usage
   - Supabase dashboard show database size

3. **Gunakan database seeder dengan bijak**:
   - Jangan seed terlalu banyak dummy data

---

## 🐛 Troubleshooting Common Issues

### Issue 1: Migration Error "Syntax error"
**Cause**: MySQL syntax di migrations (misal: `UNSIGNED`, `TINYINT`)

**Fix**: Edit migration file atau update di Railway terminal:
```bash
# Check specific error
php artisan migrate --force 2>&1 | grep -i error
```

### Issue 2: "No application encryption key"
**Fix**: Generate dan set di Railway variables:
```bash
php artisan key:generate --show
```

### Issue 3: 500 Error setelah deploy
**Fix**: 
1. Check logs di Railway
2. Pastikan `APP_DEBUG=false` di production
3. Run `php artisan config:clear` di terminal

### Issue 4: File upload error
**Fix**: Setup Cloudinary atau gunakan log untuk testing

### Issue 5: Queue jobs tidak jalan
**Fix**: Railway free tier tidak support background workers. Untuk belajar:
```bash
# Di .env Railway, gunakan:
QUEUE_CONNECTION=sync  # Process immediately, no background worker needed
```

---

## 📚 Dokumentasi Tambahan

- **Railway Docs**: https://docs.railway.app
- **Supabase Docs**: https://supabase.com/docs
- **Laravel Deployment**: https://laravel.com/docs/11.x/deployment
- **AureusERP**: https://github.com/aureuserp/aureuserp

---

## 🔄 Update & Redeploy

Setiap kali ada perubahan code:
```bash
git add .
git commit -m "Your changes"
git push origin master
```

Railway akan auto-deploy! 🎉

---

## 💰 Cost Estimate (Free Tier)

| Service | Free Tier | Cukup Untuk |
|---------|-----------|-------------|
| Railway | $5 credit/month | ~100-500 hours |
| Supabase | 500MB DB | Development |
| Cloudinary | 25GB storage | Ribuan files |
| **TOTAL** | **$0/bulan** | **Belajar & Testing** |

---

## ❓ Need Help?

Jika ada masalah saat deployment, check:
1. Railway logs
2. Supabase connection status
3. Laravel logs di `storage/logs/laravel.log`

Atau tanya saya! 😊
