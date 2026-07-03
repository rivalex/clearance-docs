# المستخدمون

وحدة المستخدمين هي لوحة خاصة بكل مستخدم لتعيين الأدوار - عالمياً أو مقيّدة بسياق محدد - ولمنح أو رفض صلاحيات فردية ضمن سياق. هي وحدة اختيارية ومعطّلة افتراضياً.

## تفعيل الوحدة

```php
// config/clearance.php
'modules' => ['users' => true],
```

## ما تقوم به اللوحة

- **تعيين دور عام** - تعيين أدوار من نوع `scope=global` مباشرة للمستخدم، على مستوى التطبيق بأكمله.
- **تعيين دور سياقي** - تعيين أدوار من نوع `scope=contextual` مقيّدة بنسخة نموذج محددة (متجر، فريق، أو مشروع مُعلَن في `contextual_models`).
- **تجاوزات صلاحيات خاصة بكل سياق** - لكل تعيين دور سياقي، يمكن فرض منح أو فرض رفض صلاحيات إضافية بشكل مستقل لكل نسخة سياق.
- **إزالة التعيين** - تُزيل دوراً من المستخدم (نطاق عام أو سياقي)، مع تسلسل حذف أي تجاوزات مرتبطة به.

<Screenshot src="/screenshots/user-panel-global.png" alt="لوحة المستخدم تعرض تعيين دور عام" caption="تعيين دور عام لمستخدم - ينطبق على مستوى التطبيق بأكمله." />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="لوحة المستخدم تعرض تعيين دور سياقي وتجاوزات صلاحيات خاصة بكل سياق" caption="تعيين دور سياقي وضبط تجاوزات صلاحيات خاصة بكل سياق لنسخة متجر محددة." />

::: warning سقف لمنع التصعيد عند التعديل الذاتي
يفرض كل من تعيين الأدوار ومنح الصلاحيات المباشر/التجاوزات سقفاً لمنع تصعيد الصلاحيات: لا يمكن لممثل (actor) غير `super_admin` تعديل تعييناته الخاصة، ولا يمكنه منح صلاحية لا يحملها هو نفسه.
:::

## التحكم بالوصول

يتطلب الوصول إلى `/clearance/user/{userId}` صلاحية `clearance-access` (للقراءة) وصلاحية `clearance-users-write` (للتعيين/الإزالة):

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## المسار والمكوّن المُضمَّن

| URI | اسم المسار | الوصف |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | تعيين أدوار لكل مستخدم + تجاوزات صلاحيات سياقية (يتطلب `modules.users = true`) |

```blade
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### الربط باللوحة من تطبيقك

```php
route('clearance.user', ['userId' => $user->id])
```

## الأدوار السياقية من الداخل

يؤدي تعيين دور سياقي لمستخدم إلى إنشاء صف `UserRoleContext`:

```php
use Rivalex\Clearance\Models\UserRoleContext;

// Samuele is "StoreStaff" only within Alessandro's Store
UserRoleContext::create([
    'user_id'      => $samuele->id,
    'role_id'      => $staffRole->id,
    'context_type' => Store::class,
    'context_id'   => $store->id,
]);
```

تحترم فحوصات التفويض السياق عبر `$user->canIn()`، و`@canin`، و`ContextService`. انظر [التفويض السياقي](/ar/features/contextual-authorization) لنموذج التحليل الكامل.

## قاعدة البيانات

| الجدول | الأعمدة الأساسية |
|---|---|
| `clr_role_ctx` | `user_id`، `role_id` (FK -> `roles`)، `context_type`، `context_id` - فريد لكل `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`، `role_id` (FK -> `roles`)، `permission_id` (FK -> `permissions`)، `context_type`، `context_id`، `type` (`forced_on`\|`forced_off`) |

## الخطوات التالية

- [التفويض السياقي](/ar/features/contextual-authorization) - ترتيب الأولوية الكامل للتحليل الخاص بـ `canIn()`.
- [الأدوار](/ar/features/roles) - إعداد نطاق دور و`context_types` الخاص به.
- [مرجع الإعدادات](/ar/guide/configuration) - `modules.users`، `contextual_models`.
