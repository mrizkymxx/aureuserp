# 🚀 Setup AureusERP di Railway - 10 Menit!

Panduan super cepat deploy ke Railway dengan PostgreSQL.

## ✅ Prerequisites

- Akun GitHub (gratis)
- Project sudah di GitHub
- Akun Railway (sign up gratis di [railway.app](https://railway.app))

---

## 📦 Step 1: Push Project ke GitHub (2 menit)

```bash
# Pastikan semua file sudah di-commit
git add .
git commit -m "Ready for Railway deployment"
git push origin master
```

---

## 🚂 Step 2: Deploy Laravel App di Railway (3 menit)

### 2.1 Create New Project
1. Buka [railway.app](https://railway.app)
2. Click **"Login"** → **Login with GitHub**
3. Click **"New Project"**
4. Pilih **"Deploy from GitHub repo"**
5. Authorize Railway untuk access GitHub
6. Pilih repository **`aureuserp`**

Railway akan mulai build (tunggu 3-5 menit untuk build pertama).

### 2.2 Monitoring Build
- Click **"Deployments"** untuk lihat progress
- Build success jika ada checkmark hijau ✅
- Jika gagal, check logs untuk debug

---

## 🗄️ Step 3: Add PostgreSQL Database (1 menit)

Masih di project yang sama:

1. Click tombol **"New"** (kanan atas)
2. Pilih **"Database"**
3. Click **"Add PostgreSQL"**
4. Railway akan create database (~30 detik)

Database otomatis terhubung ke Laravel app! 🎉

---

## ⚙️ Step 4: Configure Environment Variables (3 menit)

### 4.1 Access Variables
Click **service Laravel** (yang pertama dibuat) → tab **"Variables"**

### 4.2 Add Required Variables

Click **"New Variable"** dan tambahkan satu per satu:

```bash
APP_NAME=AureusERP
APP_ENV=production
APP_DEBUG=false
APP_KEY=                    # Kosongkan dulu, akan di-generate
```

### 4.3 Configure Database (Auto-Link)

Tambahkan variables untuk PostgreSQL:

```bash
DB_CONNECTION=pgsql
DB_HOST=${{Postgres.PGHOST}}
DB_PORT=${{Postgres.PGPORT}}
DB_DATABASE=${{Postgres.PGDATABASE}}
DB_USERNAME=${{Postgres.PGUSER}}
DB_PASSWORD=${{Postgres.PGPASSWORD}}
```

**Note:** `${{Postgres.XXXX}}` akan auto-replace dengan nilai dari PostgreSQL service!

### 4.4 Other Essential Variables

```bash
SESSION_DRIVER=database
CACHE_STORE=database
QUEUE_CONNECTION=sync
FILESYSTEM_DISK=public
MAIL_MAILER=log
LOG_LEVEL=error
```

### 4.5 Get APP_URL

Railway auto-generate URL:
1. Pergi ke **"Settings"** tab
2. Scroll ke **"Domains"**
3. Click **"Generate Domain"**
4. Copy URL (format: `https://aureuserp-production-xxxx.up.railway.app`)
5. Add variable:
   ```bash
   APP_URL=https://aureuserp-production-xxxx.up.railway.app
   ```

### 4.6 Redeploy
Click **"Deploy"** button (kanan atas) untuk apply changes.

---

## 🔑 Step 5: Generate APP_KEY (1 menit)

Setelah redeploy selesai:

### 5.1 Open Terminal
1. Click **"Deployments"**
2. Click deployment yang aktif (paling atas, ada dot hijau 🟢)
3. Scroll ke bawah, ada section **"Service"**
4. Click button dengan icon terminal atau **"Shell"**

### 5.2 Generate Key
Di terminal Railway, jalankan:

```bash
php artisan key:generate --show
```

Copy output (format: `base64:xxxxxxxxxxxxxxxx`)

### 5.3 Update Variable
1. Kembali ke tab **"Variables"**
2. Click variable `APP_KEY`
3. Paste value yang tadi di-copy
4. Railway akan auto-redeploy

---

## 🗃️ Step 6: Setup Database & Install ERP (2 menit)

Setelah redeploy selesai, buka terminal lagi:

### 6.1 Run Migrations
```bash
php artisan migrate --force
```

Tunggu sampai selesai (~1-2 menit). Akan create semua tables.

### 6.2 Install AureusERP
```bash
php artisan erp:install
```

Follow prompts:
- **Admin Name**: Masukkan nama admin
- **Admin Email**: Masukkan email admin
- **Password**: Masukkan password (min 8 karakter)
- **Confirm Password**: Konfirmasi password

Proses install akan:
- ✅ Seed default data
- ✅ Generate roles & permissions
- ✅ Create admin user

---

## 🎉 Step 7: Access Your ERP!

### 7.1 Get URL
Pergi ke **"Settings"** → **"Domains"**, copy URL Railway Anda.

### 7.2 Open in Browser
Buka: `https://your-app.up.railway.app`

### 7.3 Login
- **Email**: Yang tadi diinput saat `erp:install`
- **Password**: Password yang tadi dibuat

**Selamat! ERP Anda sudah live! 🚀**

---

## 📊 Monitoring & Management

### Check Status
- **Railway Dashboard**: Lihat uptime, memory, CPU usage
- **Logs**: Click "Deployments" → "View Logs"
- **Database**: Click PostgreSQL service → "Data" untuk query

### Monitor Free Credit
Railway dashboard show credit usage:
- Check di **"Usage"** tab
- ~$0.01-0.02/hour untuk small app
- $5/month = ~250-500 hours

### Pause When Not Using
Untuk save credit:
1. Click service Laravel
2. Click **"Settings"**
3. Scroll ke bawah
4. Click **"Delete Service"** atau **settings icon** → **"Sleep"** (if available)

Atau gunakan Railway CLI untuk cron-based pause/resume.

---

## 🔄 Update & Redeploy

Setiap kali ada perubahan code:

```bash
git add .
git commit -m "Your changes description"
git push origin master
```

Railway akan **auto-deploy** dalam 2-3 menit! ✨

---

## 🎯 Install Additional Plugins

Di Railway terminal:

```bash
# Install plugin products
php artisan products:install

# Install plugin invoices
php artisan invoices:install

# Install plugin contacts
php artisan contacts:install

# List available plugins
php artisan list | grep install
```

---

## 🐛 Troubleshooting

### Build Failed
**Check:**
- `composer.json` valid
- `package.json` valid
- `nixpacks.toml` exists
- Check build logs di Railway

**Fix:**
```bash
# Test build locally
composer install --no-dev
npm ci
npm run build
```

### Database Connection Error
**Check:**
1. PostgreSQL service running (green dot 🟢)
2. Variables menggunakan `${{Postgres.XXX}}` format
3. DB_CONNECTION=pgsql

**Fix di Railway terminal:**
```bash
php artisan config:clear
php artisan cache:clear

# Test connection
php artisan tinker
>>> DB::connection()->getPdo();
```

### 500 Internal Server Error
**Check Railway logs:**
1. Click "Deployments" → "View Logs"
2. Look for PHP errors

**Common fixes:**
```bash
# Clear all caches
php artisan optimize:clear

# Regenerate config cache
php artisan config:cache
```

### Migration Failed
**Error with MySQL syntax?**

Some migrations might have MySQL-specific syntax. Check error:
```bash
php artisan migrate --force 2>&1 | grep -i error
```

Edit migration file jika perlu, push, auto-redeploy.

### Out of Memory
**Railway default: 512MB RAM**

If error "Out of memory":
1. Check memory usage di Railway dashboard
2. Optimize queries
3. Atau upgrade Railway plan (masih affordable)

### "Class not found" Error
```bash
# Regenerate autoload
composer dump-autoload

# Clear compiled
php artisan clear-compiled
```

---

## 💡 Pro Tips

### 1. Use Railway CLI for Advanced Features
```bash
# Install Railway CLI
npm i -g @railway/cli

# Login
railway login

# Link project
railway link

# Check logs locally
railway logs

# Run commands remotely
railway run php artisan migrate
```

### 2. Environment-Specific Code
```php
// Check if running on Railway
if (env('RAILWAY_ENVIRONMENT')) {
    // Railway-specific config
}
```

### 3. Custom Domain (Optional)
1. Railway Settings → Domains
2. Click "Custom Domain"
3. Add your domain (e.g., `erp.yourdomain.com`)
4. Update DNS records (Railway akan provide instructions)
5. SSL auto-provision! 🔒

### 4. Database Backups
```bash
# In Railway terminal
pg_dump $DATABASE_URL > backup_$(date +%Y%m%d).sql

# Download using Railway CLI
railway run pg_dump $DATABASE_URL > local_backup.sql
```

### 5. Scheduled Tasks (Cron)
Laravel scheduler butuh cron. Di Railway:

**Option A:** Use external cron service (UptimeRobot, cron-job.org)
- Hit: `https://your-app.up.railway.app/schedule-run`

**Option B:** Railway Cron (paid feature)

---

## 📈 Scaling Tips

### When to Upgrade from Free Tier?
- Traffic > 500 hours/month
- Database > 500MB
- Need background workers
- Need custom domains

### Railway Paid Plans
- **Hobby**: $5/month per service (unlimited usage)
- **Pro**: $20/month (team features)
- **Enterprise**: Custom pricing

### Alternative: Optimize for Free Tier
```bash
# Use aggressive caching
CACHE_STORE=database

# Optimize images
php artisan optimize

# Use CDN for assets
```

---

## ✅ Checklist

- [ ] Project pushed to GitHub
- [ ] Railway account created
- [ ] Laravel service deployed
- [ ] PostgreSQL added
- [ ] Environment variables configured
- [ ] APP_KEY generated
- [ ] Database migrated
- [ ] ERP installed
- [ ] Admin login works
- [ ] Monitoring credit usage

---

## 🎓 Learning Resources

- **Railway Docs**: https://docs.railway.app
- **Laravel Deployment**: https://laravel.com/docs/deployment
- **AureusERP Docs**: https://github.com/aureuserp/aureuserp
- **Railway Discord**: https://discord.gg/railway (helpful community!)

---

## 🆘 Need Help?

1. **Check Railway logs** - 90% masalah ketahuan di sini
2. **Railway Discord** - Community sangat helpful
3. **Laravel Docs** - Untuk Laravel-specific issues
4. **GitHub Issues** - Untuk AureusERP-specific bugs

---

**Happy Coding! 🚀**

Railway makes deployment simple - perfect untuk belajar Laravel dan ERP systems!
