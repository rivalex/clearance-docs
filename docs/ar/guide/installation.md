# التثبيت

## المتطلبات

انظر [جدول المتطلبات](/ar/guide/introduction#requirements) الكامل في صفحة المقدمة - PHP ^8.3، Laravel ^11.0|^12.0|^13.0، spatie/laravel-permission ^6.0، livewire/livewire ^3.0|^4.0، livewire/flux ^2.14.

## تثبيت الحزمة

```bash
composer require rivalex/clearance
```

## تشغيل أداة التثبيت

```bash
php artisan clearance:install
```

::: danger شغّل دائماً clearance:install، ولا تُشغّل migrate مباشرة أبداً
تعتمد ترحيلات (migrations) Clearance الخاصة بها على وجود جداول `roles`/`permissions` الخاصة بـ Spatie أولاً؛ تنشر أداة التثبيت ترحيلات Spatie وتُشغّلها قبل ترحيلاتها الخاصة. إذا شغّلت `php artisan migrate` مباشرة على إعداد جديد ولم تكن Spatie معدّة بعد، ستحصل على رسالة خطأ عملية تُخبرك بتشغيل أداة التثبيت بدلاً من ذلك.
:::

### الخيارات الاختيارية

| الخيار | الوصف |
|---|---|
| `--user=ID` | تعيين دور `super_admin` لمستخدم بعد التثبيت |
| `--role=NAME` | تعيين `clearance-access` لدور (يُنشأ إذا لم يكن موجوداً) |
| `--super-admin-role=NAME` | ترقية دور موجود إلى `super_admin` (يتخطى المطالبة التفاعلية) |
| `--force` | إعادة التشغيل حتى لو كان مثبّتاً مسبقاً |

### ما تقوم به أداة التثبيت

1. تنشر `config/clearance.php`.
2. تنشر ترحيلات (migrations) Clearance.
3. تكتشف جداول Spatie Permission - إذا كان جدول `roles` غير موجود، تنشر وتُشغّل ترحيلات Spatie تلقائياً أولاً.
4. تُشغّل جميع الترحيلات المعلّقة.
5. تُنشئ `clearance-access` وجميع صلاحيات القدرات الخمس الدقيقة: `clearance-permissions-write`، و`clearance-roles-write`، و`clearance-guards-write`، و`clearance-settings-write`، و`clearance-users-write`.
6. تُنشئ دور `super_admin` مع تعيين جميع صلاحيات `clearance-*` له (إضافية - لا تُزيل أبداً الصلاحيات الموجودة عند إعادة التشغيل).
7. تكتب `storage/.clearance-installed` كعلامة على قابلية الإعادة (idempotency).

::: tip هل تُرحّل من تثبيت Spatie موجود مسبقاً؟
استخدم `php artisan clearance:backfill` بدلاً من البدء من الصفر، واقرأ [دليل الترحيل](/ar/guide/migration) أولاً - فهو يغطي تعارض `super_admin`، والفئات الفرعية المخصصة للنماذج، واستيراد الحراس.
:::

## أضف HasClearance إلى نموذج المستخدم لديك

لاستخدام أساليب التفويض السياقي (`$user->canIn()`، `$user->hasRoleIn()`)، أضف trait من نوع `HasClearance` إلى `App\Models\User`. وهو يتضمن بالفعل `HasRoles` الخاص بـ Spatie - سطر واحد يحل محل الاثنين:

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // includes HasRoles + all contextual authorization methods
}
```

::: tip هل تستخدم بالفعل `use HasRoles`؟
استبدله بـ `use HasClearance`، أو احتفظ بهما معاً - يُزيل PHP التكرار في تركيب الـ traits تلقائياً، دون أي تعارض.
:::

بدون هذا الـ trait، تكون `$user->canIn()` والأساليب المرتبطة بها غير متاحة، لكن توجيهات Blade (`@canin`، `@hasrolein`) و`ContextService` تستمر في العمل بغض النظر - فهي لا تعتمد على الـ trait. انظر [التفويض السياقي](/ar/features/contextual-authorization) للتفاصيل.

## الخطوات التالية

تابع إلى [البدء السريع](/ar/guide/quick-start) لتركيب اللوحة والبدء بإدارة الصلاحيات.
