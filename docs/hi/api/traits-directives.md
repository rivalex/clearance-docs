# Traits & Directives

facade और services के अलावा Clearance की developer-facing सतह बनाने वाले traits, concern, contract, और Blade directives का संदर्भ।

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance` — अपने `User` मॉडल में जोड़ें।

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

इसमें Spatie का `HasRoles` internally शामिल है, इसलिए यह एक trait दोनों की जगह ले लेता है — `Spatie\Permission\Traits\HasRoles` को अलग से न जोड़ें।

यह निम्नलिखित instance मेथड्स जोड़ता है:

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| मेथड | लौटाता है | विवरण |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | किसी संदर्भ इंस्टेंस तक सीमित अनुमति जांच। |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | `canIn()` का alias। |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | किसी संदर्भ इंस्टेंस तक सीमित भूमिका जांच। |

::: tip
इस trait के बिना, ये instance methods उपलब्ध नहीं होते हैं — लेकिन Blade directives और `ContextService` फिर भी काम करते रहते हैं। इनमें से कोई भी trait पर निर्भर नहीं है।
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups` — तब उपयोग करें जब आपको Clearance के अपने `Rivalex\Clearance\Models\Permission` के बजाय एक कस्टम `Permission` मॉडल (`config('permission.models.permission')` के माध्यम से कॉन्फ़िगर किया गया) की आवश्यकता हो।

Permissions पैनल के पीछे मौजूद grouped-UI accessors को अपने कस्टम मॉडल पर काम करते रहने के लिए इसे लागू करें:

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

यह ये accessors प्रदान करता है:

| Accessor / मेथड | विवरण |
|---|---|
| `permission_group` | नामकरण separator से पहले का समूह prefix (जैसे `orders-create` में `orders`)। |
| `group_string` | समूह को एक डिस्प्ले स्ट्रिंग के रूप में। |
| `abilities()` | अनुमति के समूह के लिए परिभाषित क्षमताओं/actions का सेट। |
| `colorForAbility()` | अनुमति की क्षमता के लिए एक रंग, जो पैनल UI द्वारा उपयोग किया जाता है। |

नामकरण परंपरा और grouping का पैनल में उपयोग कैसे होता है, इसके लिए देखें [अनुमतियाँ](/hi/features/permissions.md)।

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable` — किसी संदर्भ मॉडल (जैसे आपका `Store` मॉडल) पर implement करें ताकि यह नियंत्रित हो सके कि इसे पैनल में कैसे लेबल किया जाता है।

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

यदि कोई संदर्भ मॉडल इस contract को implement नहीं करता है, तो पैनल `config/clearance.php` से `contextual_models.<FQCN>.label_attribute` (डिफ़ॉल्ट `name`) पर fall back हो जाता है। देखें [कॉन्फ़िगरेशन](/hi/guide/configuration.md)।

## Blade directives

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| Directive | समकक्ष जांच |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
दोनों directives किसी guest उपयोगकर्ता के लिए सुरक्षित रूप से resolve होते हैं — ये `false` return करते हैं / ब्लॉक को स्किप करते हैं और कभी throw नहीं करते। इन्हें `HasClearance` trait की भी आवश्यकता नहीं होती; ये facade द्वारा उपयोग किए जाने वाले उसी अंतर्निहित `ContextService` के माध्यम से resolve होते हैं।
:::

## यह भी देखें

- [Facade](/hi/api/facade.md) — इन instance मेथड्स के समकक्ष static-call।
- [Services](/hi/api/services.md) — `ContextService` internals और 3-स्रोत resolution प्राथमिकता।
