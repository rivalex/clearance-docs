# الأدوار

لوحة الأدوار هي واجهة CRUD كاملة فوق نموذج `Role` الخاص بـ Spatie، ممتدّة بمفهومين خاصين بـ Clearance: **النطاق (scope)** (عام مقابل سياقي) و**السقف (ceiling)** (دور أب يحدّ من صلاحيات الدور الفرعي).

## ما تقوم به اللوحة

- **إنشاء / تعديل / حذف** الأدوار.
- **تعيين صلاحيات مقيّد بالحارس** - تعيين صلاحيات لدور، مُصفّاة حسب حارس الدور.
- اختيار **النطاق** - `global` أو `contextual`.
- إعداد **السقف** - تعيين أو إزالة دور أب.

<Screenshot src="/screenshots/roles-page.png" alt="لوحة أدوار Clearance تعرض قائمة الأدوار مع النطاق والسقف" caption="تعرض لوحة الأدوار كل دور مع نطاقه، وسقفه الأب إن وُجد." />

## النطاق: عام مقابل سياقي

يُعلن كل دور عن نطاقه وقت الإنشاء:

- **`global`** - يُعيَّن مباشرة لمستخدم عبر `$user->assignRole()`، وينطبق في كل مكان.
- **`contextual`** - مرتبط بنوع نموذج واحد أو أكثر (`context_types`)، ولا يمكن تعيينه إلا ضمن نسخة سياق محددة عبر `UserRoleContext`. انظر [التفويض السياقي](/ar/features/contextual-authorization) و[المستخدمون](/ar/features/users#contextual-roles-under-the-hood).

الأدوار التي لا تمتلك صف `RoleMeta` صريحاً تُعامَل افتراضياً كـ `global` للتوافق مع الإصدارات السابقة.

## الأدوار السقفية (Ceiling roles)

يمكن لدور ما أن يعلن عن سقف - دور أب تُشكّل مجموعة صلاحياته حداً أعلى:

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
```

<Screenshot src="/screenshots/role-form-ceiling.png" alt="نموذج الدور مع اختيار دور أب سقفي" caption="تعيين دور أب سقفي في نموذج الدور (RoleForm)." />

القواعد المفروضة تلقائياً عند كل حفظ:

- لا يمكن للدور الفرعي أن يحمل سوى صلاحيات يحملها الدور الأب أيضاً. يُقلَّم أي فائض بصمت عند `setParent()` وعند كل مزامنة صلاحيات لاحقة - دون استثناء، ودون رسالة خطأ، بل يُفرض بصمت فحسب.
- لا يمكن لدور يعمل بالفعل كدور أب أن يصبح دوراً فرعياً (`ClearanceScopeViolationException`).
- لا يمكن لدور هو بالفعل دور فرعي أن يعمل هو نفسه كدور أب (`ClearanceScopeViolationException`).
- إزالة صلاحيات من دور أب تتسلسل الإزالة إلى كل دور فرعي تلقائياً.

::: warning السقف يُقلّم فقط، ولا يمنح أبداً
تعيين دور أب متساهل لا يمنح الدور الفرعي أي شيء بحد ذاته. السقف هو حد أعلى بحت - لا يضيف أبداً صلاحيات للدور الفرعي، بل يمكنه فقط إزالتها.
:::

يُهيَّأ هذا لكل دور في لوحة الأدوار (`RoleForm`)، أو برمجياً عبر `RoleService::setParent()` / `RoleService::removeParent()`.

## التحكم بالوصول

يتطلب الوصول للقراءة صلاحية `clearance-access`. يتطلب إنشاء أو تعديل أو حذف دور - بما في ذلك تعييناته الخاصة بالصلاحيات، ونطاقه، وسقفه - صلاحية `clearance-roles-write`:

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

## المسار والمكوّن المُضمَّن

| URI | اسم المسار | الوصف |
|---|---|---|
| `/clearance/roles` | `clearance.roles` | CRUD الأدوار مع تعيين صلاحيات مقيّد بالحارس، والنطاق (عام/سياقي)، والسقف |

```blade
<livewire:clearance::roles.role-manager />
```

## قاعدة البيانات

تعيش بيانات الدور الوصفية (النطاق، السقف، حالة القفل) في `clr_role_meta`، وليس أبداً في جدول `roles` الخاص بـ Spatie:

| الجدول | الأعمدة الأساسية |
|---|---|
| `clr_role_meta` | `role_id` (فريد، FK -> `roles`) - `is_locked`، `scope` (`global`\|`contextual`)، `context_types` (json) - `parent_role_id` (FK اختياري -> `roles`، `ON DELETE SET NULL`) |

## الخطوات التالية

- [التفويض السياقي](/ar/features/contextual-authorization) - كيف تُفحص أدوار `scope=contextual` وقت التشغيل.
- [المستخدمون](/ar/features/users) - تعيين أدوار لمستخدم محدد، عالمياً أو حسب السياق.
- [مرجع الخدمات](/ar/api/services) - واجهة `RoleService` الكاملة.
