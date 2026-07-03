# الخدمات

تعيش جميع خدمات Clearance في `src/Services/` وتُحلّ من الحاوية (container)، مثال: `app(ContextService::class)`. هي الطبقة التي يُلجأ إليها من المتحكمات (controllers)، والسياسات (policies)، والمهام (jobs) — في أي مكان قد لا يستخدم فيه نموذج `User` trait من نوع `HasClearance`.

## `ContextService`

`Rivalex\Clearance\Services\ContextService` (‏`src/Services/ContextService.php`‏) هي المحرك الكامن وراء التفويض السياقي. تُفوّض كل من [واجهة `Clearance`](/ar/api/facade.md) وtrait من نوع `HasClearance` إليها.

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

تبني `resolveFor()` نتيجتها باستخدام أولوية ثلاثية المصادر:

1. منح الأدوار السياقية
2. منح الأدوار العامة
3. التجاوزات الخاصة بكل سياق (`forced_on` تضيف، `forced_off` تفوز دائماً)

انظر [التفويض السياقي](/ar/features/contextual-authorization.md) لشرح مفاهيمي لهذا الترتيب في الأولوية.

## `RoleService`

`Rivalex\Clearance\Services\RoleService` (‏`src/Services/RoleService.php`‏) تُدير الأدوار السقفية.

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| الأسلوب | الوصف |
|---|---|
| `setParent($childRole, $parentRole)` | يعيّن سقفاً: يُقلَّم بصمت أي فائض من الصلاحيات في `$childRole` يتجاوز مجموعة صلاحيات الأب. يُطلق `ClearanceScopeViolationException` إذا كان الدور الفرعي بالفعل دوراً أباً، أو كان الدور الأب بالفعل دوراً فرعياً. |
| `removeParent($childRole)` | يُزيل علاقة السقف. |

::: warning
السقف يُقلّم فقط — ولا يمنح أبداً. دور أب متساهل لا يمنح الدور الفرعي بحد ذاته أي صلاحيات. إزالة صلاحية من دور أب تتسلسل تلك الإزالة إلى كل دور فرعي تلقائياً.
:::

انظر [الأدوار](/ar/features/roles.md) لمفهوم الدور السقفي بتفصيل أكبر.

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService` (‏`src/Services/PermissionService.php`‏) تدعم CRUD لوحة الصلاحيات.

تفرض اصطلاح التسمية `group-action`، مُتحكَّم بها عبر مفاتيح الإعداد `enforce_naming_convention` و`naming_separator`، وتحمي مساحة أسماء صلاحيات `clearance-*` من الإنشاء أو إعادة التسمية أو الحذف خارج تدفق التثبيت.

## `GuardService`

`Rivalex\Clearance\Services\GuardService` (‏`src/Services/GuardService.php`‏) تدعم CRUD لوحة الحراس.

تحقن الحراس المخزّنة في قاعدة البيانات في `auth.guards` عند الإقلاع، مُصفّاة حسب مفتاح الإعداد `allowed_guard_drivers` — يُتجاهل المحرك غير المدرج مع تحذير في السجل بدلاً من إفساد إعداد المصادقة.

## `MetaService`

`Rivalex\Clearance\Services\MetaService` (‏`src/Services/MetaService.php`‏) تقرأ وتكتب `clr_meta` (اسم العرض، الوصف، أيقونة SVG معقّمة، اللون) لموضوع دور أو حارس. تدعم محرر بيانات العرض الوصفية الخاص بلوحة الإعدادات.

تمر أيقونات SVG عبر `Rivalex\Clearance\Support\SvgSanitizer` (مُعقِّم بقائمة سماح) قبل التخزين وعند العرض على حد سواء.

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService` (‏`src/Services/UserClearanceService.php`‏) تدعم لوحة وحدة المستخدمين:

- تعيين دور عام
- تعيين دور سياقي (عبر `UserRoleContext`)
- تجاوزات صلاحيات خاصة بكل سياق (عبر `UserContextPermissionOverride`، `forced_on` / `forced_off`)
- إزالة التعيينات، مما يتسلسل إلى حذف التجاوزات

تفرض سقفاً لمنع تصعيد الصلاحيات: لا يمكن لممثل (actor) غير `super_admin` تعديل تعييناته الخاصة، ولا يمكنه منح صلاحية لا يحملها هو نفسه. انظر [المشرف الفائق](/ar/features/super-admin.md) و[المستخدمون](/ar/features/users.md).

## الاستثناءات (Exceptions)

جميعها في `src/Exceptions/`:

| الاستثناء | يُطلق عندما |
|---|---|
| `ClearanceConfigException` | تُقرأ قيمة غير صالحة أو سيئة الإعداد في `config/clearance.php` (مثال: `naming_separator` سيئ). |
| `ClearanceNamingException` | يخالف اسم صلاحية اصطلاح التسمية `group-action` بينما الفرض مفعّل. |
| `ClearanceProtectedResourceException` | تُجرى محاولة لتعديل صلاحية `clearance-*` محمية، أو مورد مقفل، خارج تدفق التثبيت. |
| `ClearanceScopeViolationException` | علاقة دور سقفي غير صالحة (دور أب يحاول أن يصبح دوراً فرعياً، أو العكس)، أو عدم تطابق في النطاق. |

## انظر أيضاً

- [الواجهة (Facade)](/ar/api/facade.md) — الغلاف على نمط الاستدعاء الثابت (static-call) حول `ContextService`.
- [الـ Traits والتوجيهات](/ar/api/traits-directives.md) — المكافئات على مستوى أساليب النسخة وتوجيهات Blade.
- [أوامر Artisan](/ar/api/artisan-commands.md) — `clearance:install` و`clearance:backfill`، اللذان يزرعان البيانات التي تعمل عليها هذه الخدمات.
