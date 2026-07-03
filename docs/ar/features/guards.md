# الحراس

تُدير لوحة الحراس حراس المصادقة كسجلات في قاعدة البيانات، بحيث يمكن إضافة الحراس أو تغييرها دون تعديل `config/auth.php` وإعادة النشر.

## ما تقوم به اللوحة

- **إنشاء / تعديل / حذف** الحراس، كل واحد منها بـ `name`، و`driver`، و`provider`.

<Screenshot src="/screenshots/guards-page.png" alt="لوحة حراس Clearance تعرض قائمة الحراس المخزّنة في قاعدة البيانات" caption="تعرض لوحة الحراس كل حارس مخزّن في قاعدة البيانات مع محركه ومزوّده." />

## كيف تُطبَّق الحراس

تُحقن الحراس المُدارة من اللوحة في `auth.guards` عند إقلاع التطبيق، بحيث يتعرّف عليها نظام مصادقة Laravel قبل تشغيل أي طلب - دون الحاجة لتعديل `config/auth.php` يدوياً.

## المحركات (Drivers) المسموح بها

لا تُقبل سوى المحركات الموجودة في قائمة السماح:

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning المحركات غير المدرجة تُتجاهل، ولا تُرفض بصخب
يُتجاهل الحارس الذي يحمل محركاً غير موجود في `allowed_guard_drivers` عند الإقلاع (مع تحذير في السجل) بدلاً من إفساد إعداد المصادقة. إذا كان حارس أنشأته لا يعمل، تحقق أولاً من المحرك مقابل قائمة السماح.
:::

## التحكم بالوصول

يتطلب الوصول للقراءة صلاحية `clearance-access`. يتطلب إنشاء أو تعديل أو حذف حارس صلاحية `clearance-guards-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## المسار والمكوّن المُضمَّن

| URI | اسم المسار | الوصف |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | إدارة الحراس (إنشاء/تعديل/حذف) |

```blade
<livewire:clearance::guards.guard-manager />
```

## قاعدة البيانات

| الجدول | الأعمدة الأساسية |
|---|---|
| `clr_guards` | `name` (فريد)، `driver`، `provider` |

## الخطوات التالية

- [مرجع الإعدادات](/ar/guide/configuration) - مفاتيح الإعداد الكاملة `allowed_guard_drivers` و`guards`.
- [مرجع الواجهة (Facade)](/ar/api/facade) - `Clearance::guards()` تُرجع أسماء جميع حراس المصادقة المُدارة.
