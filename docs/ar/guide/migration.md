# الترحيل من تثبيت Spatie موجود مسبقاً

إذا كان تطبيقك يستخدم بالفعل `spatie/laravel-permission` مع أدوار وصلاحيات وتعيينات موجودة، فإن `clearance:install` تكتشفها وتحافظ عليها - فهي لا تُشغّل ترحيلات (migrations) Spatie أبداً إذا كان جدول `roles` موجوداً بالفعل. انظر [التثبيت](/ar/guide/installation) للاطلاع على تدفق التثبيت الكامل.

بعد التثبيت، استخدم `clearance:backfill` لتبنّي بيانات Clearance الوصفية الخاصة بها على أدوارك وحراسك الموجودة.

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## ما يقوم به كل قسم

| القسم | التأثير |
|---|---|
| `meta` | يُنشئ اسم عرض في `clr_meta` (اسم الدور بصيغة عناوين) لكل دور لا يمتلك واحداً. |
| `roles` | يُنشئ القيم الافتراضية لـ `clr_role_meta` (`scope=global`، `is_locked=false`) لكل دور لا يمتلك واحداً. |
| `guards` | يستورد الحراس من `config('auth.guards')` إلى `clr_guards`، مُصفّاة حسب `allowed_guard_drivers`. |

استخدم `--only=<section>` لتشغيل قسم واحد فقط بدلاً من الأقسام الثلاثة.

::: tip قابل للإعادة (Idempotent)
جميع الأقسام قابلة للإعادة - إعادة تشغيل `clearance:backfill` آمنة ولن تُكرّر أو تستبدل صفوف `clr_*` الموجودة.
:::

## الحالات الطرفية

يحتوي مستودع الحزمة على دليل ترحيل أعمق يغطي حالات طرفية لم يتم تلخيصها هنا - وعلى وجه الخصوص ما يحدث عندما تحتوي قاعدة بياناتك بالفعل على دور باسم `super_admin` (أو اسم بديل مثل `superadmin`، `root`، `owner`) يتعارض مع دور `super_admin` الخاص بـ Clearance نفسها، بالإضافة إلى ملاحظات حول الفئات الفرعية المخصصة لنماذج `Role`/`Permission` وسلوك استيراد الحراس. اقرأه قبل تشغيل الترحيل على قاعدة بيانات إنتاجية تحتوي على بيانات أدوار موجودة غير بسيطة. انظر أيضاً [المشرف الفائق](/ar/features/super-admin) لمعرفة سلوك كشف الأسماء البديلة أثناء `clearance:install`.

## ذو صلة

- [التثبيت](/ar/guide/installation) - تدفق `clearance:install` الكامل
- [أوامر Artisan](/ar/api/artisan-commands) - المرجع الكامل للأوامر
- [البنية المعمارية](/ar/guide/architecture) - جداول `clr_*` التي يملؤها الترحيل
