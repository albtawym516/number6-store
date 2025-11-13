# دليل النشر على الإنترنت

## المتطلبات
- Node.js 18+
- MongoDB (اختياري - يمكن استخدام البيانات الوهمية)
- نطاق (Domain)
- استضافة (VPS/Cloud)

## خطوات النشر

### 1. إعداد البيئة
```bash
# تحديث الـ environment variables للإنتاج
cp .env.production .env
```

### 2. تعديل ملف .env
```bash
# استبدل بالقيم الحقيقية
MONGO_URI=mongodb://localhost:27017/number6-store-production
EMAIL_USER=your-production-email@gmail.com
EMAIL_PASS=your-production-app-password
ADMIN_EMAIL=your-admin-email@gmail.com

# أضف عنوان IP الخاص بك
ALLOWED_ADMIN_IPS=YOUR_IP_ADDRESS

# مفتاح سري قوي
ADMIN_SECRET_KEY=your-very-secure-secret-key-here
```

### 3. البناء للإنتاج
```bash
# تثبيت الاعتماديات
npm install

# بناء الواجهة الأمامية
npm run build

# تشغيل في وضع الإنتاج
npm run start:prod
```

### 4. إعداد Reverse Proxy (Nginx)
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### 5. إعداد Process Manager (PM2)
```bash
# تثبيت PM2
npm install -g pm2

# تشغيل التطبيق مع PM2
pm2 start server/server.js --name "number6-store" --env production

# حفظ الـ configuration
pm2 save

# إعداد التشغيل التلقائي عند إعادة التشغيل
pm2 startup
```

### 6. الحصول على عنوان IP
```bash
# لمعرفة عنوان IP الحالي
curl ifconfig.me
```

## الأمان
- لوحة التحكم محمية بقائمة بيضاء لعناوين IP
- كلمة مرور لوحة التحكم: `01206155419`
- يُنصح بتغيير كلمة المرور في ملف `server/server.js`

## الوصول بعد النشر
- المتجر: `http://your-domain.com`
- لوحة التحكم: `http://your-domain.com/#/admin`

## المراقبة
```bash
# عرض حالة التطبيق
pm2 status

# عرض السجلات
pm2 logs number6-store

- إعادة تشغيل التطبيق
pm2 restart number6-store
```

## استكشاف الأخطاء
- تأكد من تشغيل MongoDB إذا استخدمته
- تحقق من صحة الـ environment variables
- تأكد من إضافة عنوان IP الخاص بك إلى القائمة البيضاء
