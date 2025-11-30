# Render.com Deployment Guide

## Quick Deploy

1. **Sign up to Render**: https://render.com (gunakan GitHub)

2. **New Blueprint**:
   - Klik "New +" → "Blueprint"
   - Connect repository: `mrizkymxx/aureuserp`
   - Render akan otomatis detect `render.yaml`

3. **Set Environment Variables**:
   - `APP_KEY`: Generate dengan `php artisan key:generate --show`
   - `APP_URL`: Akan otomatis di-set ke `https://aureuserp.onrender.com`

4. **Deploy**:
   - Klik "Apply"
   - Tunggu 5-10 menit

## Features

- ✅ Free PostgreSQL database (256MB)
- ✅ 750 jam/bulan (cukup 24/7)
- ✅ Auto SSL certificate
- ✅ Auto deploy dari GitHub
- ⚠️ Sleep after 15 min inactive (free tier)

## Post-Deployment

### Generate APP_KEY
```bash
# Local
php artisan key:generate --show

# Copy output dan set di Render dashboard:
# Environment → Add Environment Variable
# Key: APP_KEY
# Value: base64:xxxxx
```

### Install ERP (via Render Shell)
```bash
# Di Render dashboard → Shell
php artisan erp:install
```

### Create Admin User
```bash
php artisan shield:super-admin --user=1
```

## Troubleshooting

### Logs
Dashboard → Logs (real-time)

### Shell Access
Dashboard → Shell (run commands)

### Database
Dashboard → aureuserp-db → Connect (untuk psql access)

## Free Tier Limits

- Web Service: 750 hours/month
- PostgreSQL: 256MB storage, 90 days retention
- Bandwidth: 100GB/month
- Sleep: After 15 min inactive

## Upgrade Path

Jika perlu upgrade (paid):
- $7/month: No sleep, always-on
- $25/month: 2GB RAM, faster builds
