# التفويض السياقي

توسّع Clearance نموذج التفويض العام الخاص بـ Spatie بفحوصات مقيّدة بسياق. يمكن تعيين أدوار المستخدم ضمن سياق محدد (مثل متجر، أو مستأجر، أو مشروع) وتكون غير مرئية خارج ذلك النطاق. هذه هي الآلية الأساسية خلف [وحدة المستخدمين](/ar/features/users) والأدوار [السياقية](/ar/features/roles#scope-global-vs-contextual).

## على نموذج المستخدم

متاحة عندما يستخدم نموذج `User` الخاص بك trait من نوع `HasClearance` (انظر [التثبيت](/ar/guide/installation#add-hasclearance-to-your-user-model)):

```php
// Does the user have this permission in this context?
$user->canIn('orders-create', $store);

// Alias - mirrors Spatie's hasPermissionTo() naming
$user->hasPermissionIn('orders-create', $store);

// Does the user hold this role in this context?
$user->hasRoleIn('manager', $store);

// Optionally filter by guard
$user->canIn('orders-create', $store, 'api');
$user->hasRoleIn('manager', $store, 'web');
```

## في عروض Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip آمنة للضيوف بالتصميم
تُحلَّل كلتا التوجيهين بأمان (تُرجعان `false`) لمستخدم ضيف - ولا تُطلقان استثناءً أبداً. يمكنك استخدام `@canin`/`@hasrolein` في عروض تُعرض قبل ضمان المصادقة دون تغليفها بفحص `@auth`.
:::

## عبر ContextService

للاستخدام في المتحكمات (controllers)، أو السياسات (policies)، أو المهام (jobs) حيث قد لا يمتلك نموذج `User` trait من نوع `HasClearance`:

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## عبر واجهة Clearance (Facade)

تُفوّض واجهة `Clearance` إلى نفس الخدمة - نقطة دخول عامة مريحة:

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

انظر [مرجع الواجهة (Facade)](/ar/api/facade) للاطلاع على قائمة الأساليب الكاملة.

## كيف تعمل عملية التحليل (Resolution)

تدمج `resolveFor()` ثلاثة مصادر، بترتيب الأولوية التالي:

1. **منح الأدوار السياقية** - الصلاحيات من الأدوار التي يحملها المستخدم تحديداً ضمن هذا السياق (`clr_role_ctx`).
2. **منح الأدوار العامة** - الصلاحيات من أدوار Spatie المُعيَّنة عالمياً للمستخدم (بحيث يُلبّي `super_admin` وأدوار عامة أخرى شرط `canIn()` لأي سياق).
3. **التجاوزات الخاصة بكل سياق** (`clr_ctx_overrides`) - `forced_on` تضيف صلاحية فوق ما سبق؛ `forced_off` تُزيلها، حتى لو كانت الصلاحية ممنوحة من دور عام.

::: danger الرفض يفوز دائماً
إذا كانت صلاحية ما مضبوطة على `forced_off` لمستخدم في سياق معيّن، فإن هذا الرفض يتجاوز كل مصدر آخر - بما في ذلك منح دور عام ومنح دور سياقي. لا توجد وسيلة لإعادة منح صلاحية `forced_off` لذلك السياق سوى إزالة التجاوز نفسه. صمّم سياسة التجاوز لديك مع أخذ هذا بعين الاعتبار: `forced_off` هي أقوى إشارة في النظام.
:::

## قاعدة البيانات

| الجدول | الأعمدة الأساسية |
|---|---|
| `clr_role_ctx` | `user_id`، `role_id` (FK -> `roles`)، `context_type`، `context_id` - فريد لكل `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`، `role_id` (FK -> `roles`)، `permission_id` (FK -> `permissions`)، `context_type`، `context_id`، `type` (`forced_on`\|`forced_off`) |

## الخطوات التالية

- [المستخدمون](/ar/features/users) - واجهة اللوحة لتعيين الأدوار والتجاوزات السياقية.
- [الأدوار](/ar/features/roles#scope-global-vs-contextual) - إعلان دور كـ `contextual` و`context_types` المسموح بها له.
- [مرجع الـ Traits والتوجيهات](/ar/api/traits-directives) - توقيعات trait من نوع `HasClearance` الكامل وتوجيهات Blade.
- [مرجع الخدمات](/ar/api/services) - واجهة `ContextService` الكاملة.
