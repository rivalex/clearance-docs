# الواجهة (Facade)

تُقدّم واجهة `Clearance` (`Rivalex\Clearance\Facades\Clearance`) واجهة الـ API الخاصة بالتفويض السياقي في الحزمة كاستدعاءات على نمط ثابت (static). تعتمد على `src/Clearance.php`، الذي يُفوّض كل استدعاء إلى [`ContextService`](/ar/api/services.md#contextservice).

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
تعمل توجيهات Blade و`ContextService` دون أي تغيير على نموذج `User` الخاص بك. الواجهة (facade) هي غلاف مريح حول نفس المحرك — انظر [التفويض السياقي](/ar/features/contextual-authorization.md) للمفهوم الكامن وراء الفحوصات المقيّدة بسياق.
:::

## `canIn()`

يتحقق مما إذا كان المستخدم يحمل صلاحية مقيّدة بنسخة سياق.

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| المعامل | النوع | الوصف |
|---|---|---|
| `$user` | `Authenticatable` | المستخدم المراد فحصه. |
| `string $permission` | `string` | اسم الصلاحية، مثال: `orders-create`. |
| `$context` | نسخة نموذج | السياق الذي تُقيَّد به الصلاحية، مثال: `Store`. |
| `?string $guard` | `string\|null` | مرشِّح اسم حارس اختياري. |

تُرجع `bool`.

## `hasPermissionIn()`

اسم بديل لـ [`canIn()`](#canin)، سُمّي ليعكس اصطلاح `hasPermissionTo()` الخاص بـ Spatie.

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

تُرجع `bool`.

## `hasRoleIn()`

يتحقق مما إذا كان المستخدم يحمل دوراً مقيّداً بنسخة سياق.

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

تُرجع `bool`.

## `resolveFor()`

يحلّل كل اسم صلاحية فعّال يحمله المستخدم ضمن سياق معطى.

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

تُرجع `Collection<string>` من أسماء الصلاحيات. انظر [`ContextService::resolveFor()`](/ar/api/services.md#contextservice) لقواعد أولوية المصادر الثلاثة المستخدمة لبناء هذه القائمة.

## `guards()`

يسرد أسماء جميع حراس المصادقة التي تديرها Clearance.

```php
Clearance::guards(); // Collection<string>
```

تُرجع `Collection<string>`. مدعومة بـ [`GuardService`](/ar/api/services.md#guardservice). انظر [صفحة ميزة الحراس](/ar/features/guards.md) لمعرفة كيفية إعداد الحراس وحقنها في `auth.guards`.

## انظر أيضاً

- [الـ Traits والتوجيهات](/ar/api/traits-directives.md) — الأساليب المكافئة على مستوى النسخة (instance) عبر `HasClearance`، وتوجيهات Blade الآمنة للضيوف.
- [الخدمات](/ar/api/services.md) — `ContextService` والخدمات ذات الصلة.
