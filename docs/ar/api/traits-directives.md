# الـ Traits والتوجيهات

مرجع لـ الـ traits، والـ concern، والعقد (contract)، وتوجيهات Blade التي تُشكّل الواجهة الموجّهة للمطوّرين في Clearance خارج نطاق الواجهة (facade) والخدمات.

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance` — أضِفه إلى نموذج `User` الخاص بك.

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

يتضمّن `HasRoles` الخاص بـ Spatie داخلياً، لذا يحل هذا الـ trait الواحد محل الاثنين معاً — لا تُضِف `Spatie\Permission\Traits\HasRoles` بشكل منفصل.

يضيف أساليب النسخة (instance methods) التالية:

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| الأسلوب | تُرجع | الوصف |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | فحص صلاحية مقيّد بنسخة سياق. |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | اسم بديل لـ `canIn()`. |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | فحص دور مقيّد بنسخة سياق. |

::: tip
بدون هذا الـ trait، تكون أساليب النسخة هذه غير متاحة — لكن توجيهات Blade و`ContextService` تستمر في العمل بغض النظر. لا يعتمد أي منهما على الـ trait.
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups` — استخدمه عندما تحتاج إلى نموذج `Permission` مخصص (مُهيَّأ عبر `config('permission.models.permission')`) بدلاً من نموذج `Rivalex\Clearance\Models\Permission` الخاص بـ Clearance.

طبّقه للحفاظ على عمل أدوات النفاذ (accessors) الخاصة بواجهة التجميع (grouped-UI) التي تدعم لوحة الصلاحيات على نموذجك المخصص:

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

يوفّر أدوات النفاذ (accessors) التالية:

| أداة النفاذ / الأسلوب | الوصف |
|---|---|
| `permission_group` | بادئة المجموعة قبل فاصل التسمية (مثال: `orders` في `orders-create`). |
| `group_string` | المجموعة كنص عرض. |
| `abilities()` | مجموعة القدرات/الإجراءات المُعرَّفة لمجموعة الصلاحية. |
| `colorForAbility()` | لون لقدرة الصلاحية، تستخدمه واجهة اللوحة. |

انظر [الصلاحيات](/ar/features/permissions.md) لمعرفة كيفية استخدام اصطلاح التسمية والتجميع في اللوحة.

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable` — نفّذه على نموذج سياق (مثال: نموذج `Store` الخاص بك) للتحكم في كيفية تصنيفه (labeling) في اللوحة.

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

إذا لم يُنفّذ نموذج السياق هذا العقد (contract)، تعود اللوحة إلى `contextual_models.<FQCN>.label_attribute` (الافتراضي `name`) من `config/clearance.php`. انظر [الإعداد](/ar/guide/configuration.md).

## توجيهات Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| التوجيه | الفحص المكافئ |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
تُحلَّل كلتا التوجيهين بأمان لمستخدم ضيف — تُرجعان `false` / تتخطّيان الكتلة (block) ولا تُطلقان استثناءً أبداً. كما أنهما لا تتطلبان trait من نوع `HasClearance`؛ تُحلَّلان عبر نفس `ContextService` الأساسي الذي تستخدمه الواجهة (facade).
:::

## انظر أيضاً

- [الواجهة (Facade)](/ar/api/facade.md) — المكافئ على نمط الاستدعاء الثابت (static-call) لأساليب النسخة هذه.
- [الخدمات](/ar/api/services.md) — تفاصيل `ContextService` الداخلية وترتيب أولوية التحليل ثلاثي المصادر.
