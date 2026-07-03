# أوامر Artisan

تُقدّم Clearance أمرَي Artisan: `clearance:install` للإعداد لأول مرة، و`clearance:backfill` لزرع القيم الافتراضية على التثبيتات الموجودة. انظر [دليل التثبيت](/ar/guide/installation.md) للاطلاع على شرح الإعداد الكامل من البداية إلى النهاية.

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| الخيار | الوصف |
|---|---|
| `--user=ID` | تعيين دور `super_admin` لمستخدم بعد التثبيت. |
| `--role=NAME` | تعيين `clearance-access` لدور (يُنشأ إذا لم يكن موجوداً). |
| `--super-admin-role=NAME` | ترقية دور موجود إلى `super_admin` (يتخطى المطالبة التفاعلية). |
| `--force` | إعادة التشغيل حتى لو كان مثبّتاً مسبقاً. |

### ما يقوم به، بالترتيب

1. ينشر `config/clearance.php`.
2. ينشر ترحيلات (migrations) Clearance.
3. يكتشف جداول Spatie Permission — إذا كان جدول `roles` غير موجود، ينشر ويُشغّل ترحيلات Spatie تلقائياً أولاً.
4. يُشغّل جميع الترحيلات المعلّقة.
5. يُنشئ `clearance-access` وجميع صلاحيات القدرات الخمس الدقيقة: `clearance-permissions-write`، و`clearance-roles-write`، و`clearance-guards-write`، و`clearance-settings-write`، و`clearance-users-write`.
6. يُنشئ دور `super_admin` مع تعيين جميع صلاحيات `clearance-*` له. هذه الخطوة إضافية — لا تُزيل أبداً الصلاحيات الموجودة عند إعادة التشغيل.
7. يكتب `storage/.clearance-installed` كعلامة على قابلية الإعادة (idempotency).

### كشف الأسماء البديلة

إذا كانت قاعدة البيانات تحتوي بالفعل على دور باسم `super_admin`، أو `superadmin`، أو `super-admin`، أو `root`، أو `owner`، تكتشفه أداة التثبيت وتُطالب تفاعلياً إما بترقيته أو إنشاء دور `super_admin` منفصل. استخدم `--super-admin-role=NAME` لتخطي المطالبة بشكل غير تفاعلي.

تُعيد الترقية تسمية الدور في مكانه — تُحفظ جميع تعيينات `model_has_roles` عبر المفتاح الخارجي (foreign key) على `role_id`.

::: warning
صلاحيات `clearance-*` محمية خارج تدفق التثبيت — انظر [`PermissionService`](/ar/api/services.md#permissionservice) واستثناء `ClearanceProtectedResourceException` في [الخدمات](/ar/api/services.md#exceptions).
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| الخيار | الوصف |
|---|---|
| `--only=meta\|roles\|guards` | تشغيل قسم واحد فقط من أقسام الترحيل. |
| `--dry-run` | معاينة التغييرات دون كتابة. |

### الأقسام

| القسم | الوصف |
|---|---|
| `meta` | يُنشئ اسم عرض في `clr_meta` (اسم الدور بصيغة عناوين) لكل دور لا يمتلك واحداً. |
| `roles` | يُنشئ القيم الافتراضية لـ `clr_role_meta` (‏`scope=global`‏، `is_locked=false`‏) لكل دور لا يمتلك واحداً. |
| `guards` | يستورد الحراس من `config('auth.guards')` إلى `clr_guards`، مُصفّاة حسب `allowed_guard_drivers`. |

جميع الأقسام قابلة للإعادة (idempotent) — تشغيل `clearance:backfill` بشكل متكرر آمن ويملأ فقط ما هو مفقود.

::: tip
بالنسبة لتثبيتات Spatie الموجودة مسبقاً: لا تُشغّل `clearance:install` أبداً ترحيلات Spatie إذا كان جدول `roles` موجوداً بالفعل، لذا تُحفظ الأدوار والصلاحيات والتعيينات الموجودة تلقائياً.
:::

## انظر أيضاً

- [الخدمات](/ar/api/services.md) — `GuardService` و`MetaService`، اللتان تدعمان قسمي الترحيل `guards` و`meta`.
- [دليل الترحيل](/ar/guide/migration.md) — ترحيل تثبيت Spatie Permission موجود إلى Clearance.
