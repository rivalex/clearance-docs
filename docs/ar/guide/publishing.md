# النشر (Publishing)

تنشر Clearance ثلاثة أنواع من الأصول عبر وسوم `vendor:publish`: الإعداد، والترحيلات (migrations)، والترجمات. تُدار الإعدادات والترحيلات تلقائياً بواسطة أداة التثبيت (انظر [التثبيت](/ar/guide/installation))؛ تغطي هذه الصفحة نشرها يدوياً، ونشر الترجمات.

## الإعداد

```bash
php artisan vendor:publish --tag=clearance-config
```

ينشر `config/clearance.php` إلى تطبيقك. انظر [مرجع الإعدادات](/ar/guide/configuration) للاطلاع على كل مفتاح، وقيمته الافتراضية، وغرضه.

## الترجمات

```bash
php artisan vendor:publish --tag=clearance-translations
```

هذا ينسخ اللغات التسع المرفقة (`ar`، `en`، `es`، `fr`، `hi`، `it`، `pt`، `ru`، `zh`) إلى `lang/vendor/clearance/{locale}/ui.php` في تطبيقك. لإضافة لغة جديدة أو لتجاوز نصوص في لغة موجودة، أضف مجلد لغة هناك، مستخدماً `en/ui.php` كمرجع أساسي للمفاتيح. تعرض جميع اللغات مجموعة مفاتيح متطابقة، يتم التحقق منها باختبار مخصص عند كل تغيير.

## تجاوز تخطيط اللوحة

يتحكم مفتاح الإعداد `layout` في تخطيط Blade المستخدم للمكوّنات ذات الصفحة الكاملة:

```php
// config/clearance.php
'layout' => null, // host app's default (config('livewire.layout'))
```

اتركه `null` لاستخدام تخطيط Livewire الافتراضي لتطبيقك، أو عيّنه إلى عرض تخطيط محدد لعرض اللوحة داخل غلاف (shell) مخصص.

## الترحيلات (Migrations)

تُنشر ترحيلات Clearance وتُشغَّل تلقائياً بواسطة `php artisan clearance:install` - كما تنشر أداة التثبيت وتُشغّل ترحيلات Spatie أولاً إذا لم يكن جدول `roles` موجوداً بعد. انظر [التثبيت](/ar/guide/installation) للاطلاع على تدفق خطوة بخطوة الكامل، و[تنبيه الخطر](/ar/guide/installation#run-the-installer) حول عدم تشغيل `migrate` مباشرة أبداً على إعداد جديد.

## ذو صلة

- [مرجع الإعدادات](/ar/guide/configuration) - كل مفتاح إعداد
- [التثبيت](/ar/guide/installation) - تدفق أداة التثبيت الكامل
- [الترحيل](/ar/guide/migration) - `clearance:backfill` لتثبيتات Spatie الموجودة مسبقاً
