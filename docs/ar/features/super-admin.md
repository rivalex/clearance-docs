# المشرف الفائق (Super Admin)

يُنشأ دور `super_admin` تلقائياً بواسطة `clearance:install` ويُبقى متزامناً مع جميع صلاحيات `clearance-*`.

```bash
# Install and make user ID 1 a super admin
php artisan clearance:install --user=1
```

انظر [دليل التثبيت](/ar/guide/installation) و[مرجع أوامر Artisan](/ar/api/artisan-commands) للاطلاع على قائمة خيارات أداة التثبيت الكاملة.

## تجاوز Gate (اختياري)

يتحكم `super_admin_gate_bypass` فيما إذا كان دور `super_admin` يتجاوز جميع فحوصات `can()` / `Gate::allows()` / السياسات (policies) الخاصة بـ Laravel على مستوى التطبيق بأكمله، عبر خطاف (hook) من نوع `Gate::before()`. الافتراضي: `false`. التجاوز اختياري. لتفعيله:

```php
// config/clearance.php
'super_admin_gate_bypass' => true,
```

::: danger على مستوى التطبيق بأكمله، وليس مقيّداً باللوحة
عند التفعيل، يتخطى أي مستخدم يحمل `super_admin` **جميع** فحوصات Gate على مستوى التطبيق بأكمله - وليس فقط فحوصات لوحة Clearance. يؤثر هذا على كل فحص `can()`، و`Gate::allows()`، وسياسة (policy) في تطبيقك، وليس فقط تلك التي تُعرّفها Clearance. لنطاق أضيق، اتركه معطّلاً وامنح الصلاحيات صراحةً بدلاً من ذلك.
:::

## كشف الأسماء البديلة

إذا كانت قاعدة بياناتك تحتوي بالفعل على دور باسم `super_admin`، أو `superadmin`، أو `super-admin`، أو `root`، أو `owner`، تكتشفه أداة التثبيت وتُطالبك تفاعلياً بترقيته أو إنشاء `super_admin` منفصل. استخدم `--super-admin-role=NAME` لتخطي المطالبة:

```bash
php artisan clearance:install --super-admin-role=owner
```

::: tip الترقية غير هدّامة
تُعيد الترقية تسمية الدور في مكانه - تُحفظ جميع تعيينات `model_has_roles` الموجودة عبر المفتاح الخارجي (FK) على `role_id`. لا يفقد أي مستخدم تعيين دوره الموجود أثناء الترقية.
:::

## الوصول إلى اللوحة وصلاحيات الكتابة الدقيقة التحكم

انتقل إلى `/clearance` بعد التثبيت. يتطلب الوصول للقراءة إلى اللوحة بأكملها صلاحية `clearance-access`، يُتحقق منها عبر `can()`، وليس أبداً عبر `hasRole()`:

```php
$user->givePermissionTo('clearance-access');
```

عمليات الكتابة على كل قسم مقيّدة بصلاحية مخصصة قابلة للتفويض - جميعها تُنشأ بواسطة `clearance:install` وتُعيَّن لـ `super_admin`:

| الصلاحية | تتحكم في |
|---|---|
| `clearance-permissions-write` | إنشاء / تعديل / حذف الصلاحيات |
| `clearance-roles-write` | إنشاء / تعديل / حذف الأدوار وتعييناتها الخاصة بالصلاحيات |
| `clearance-guards-write` | إنشاء / تعديل / حذف الحراس |
| `clearance-settings-write` | تعديل بيانات العرض الوصفية، والأيقونات، والألوان، والدور الافتراضي |
| `clearance-users-write` | تعيين / إزالة الأدوار لمستخدم محدد |

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

::: tip منح وصول ضيّق النطاق
اجمع بين `clearance-access` وصلاحية واحدة بالضبط من نوع `clearance-{section}-write` لمنح مستخدم (قائد فريق مثلاً) وصول كتابة إلى قسم واحد من أقسام اللوحة - الأدوار، على سبيل المثال - دون كشف الإعدادات، أو الحراس، أو إدارة المستخدمين.
:::

إذا لم يكن صف صلاحية دقيق التحكم موجوداً على الإطلاق في قاعدة البيانات (حالة قديمة سابقة للتزويد)، يعود الفحص إلى `clearance-access` للتوافق مع الإصدارات السابقة. على أي نسخة شغّلت `clearance:install`، لا يُفعَّل هذا الرجوع أبداً.

## صلاحيات `clearance-*` المحجوزة

صلاحيات `clearance-*` محجوزة: لا يمكن إنشاؤها، أو إعادة تسميتها، أو حذفها، أو إضافتها إلى أي دور إلا عبر تدفق التثبيت الخاص بالحزمة نفسها - حتى بواسطة ممثل (actor) من نوع `super_admin` لدور `super_admin` نفسه.

## الخطوات التالية

- [الصلاحيات](/ar/features/permissions) - لوحة CRUD الصلاحيات العامة (تستثني `clearance-*`).
- [الأدوار](/ar/features/roles) - تعيين صلاحيات `clearance-*-write` لدور مخصص.
- [الأمان](/ar/guide/security) - نموذج تصعيد الصلاحيات والسقف الكامل.
