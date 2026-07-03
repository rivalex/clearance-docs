# Facade

`Clearance` facade (`Rivalex\Clearance\Facades\Clearance`) पैकेज के प्रासंगिक प्राधिकरण API को static-style calls के रूप में expose करता है। यह `src/Clearance.php` द्वारा समर्थित है, जो हर call को [`ContextService`](/hi/api/services.md#contextservice) को delegate करता है।

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
Blade directives और `ContextService`, आपके `User` मॉडल में बिना किसी बदलाव के काम करते हैं। facade उसी engine के चारों ओर एक सुविधाजनक wrapper है — context-scoped जांचों के पीछे की अवधारणा के लिए [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization.md) देखें।
:::

## `canIn()`

जांचें कि क्या कोई उपयोगकर्ता किसी संदर्भ इंस्टेंस तक सीमित अनुमति रखता है।

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| पैरामीटर | प्रकार | विवरण |
|---|---|---|
| `$user` | `Authenticatable` | जांचा जाने वाला उपयोगकर्ता। |
| `string $permission` | `string` | अनुमति का नाम, जैसे `orders-create`। |
| `$context` | model instance | वह संदर्भ जिस तक अनुमति सीमित है, जैसे एक `Store`। |
| `?string $guard` | `string\|null` | वैकल्पिक गार्ड नाम फ़िल्टर। |

`bool` लौटाता है।

## `hasPermissionIn()`

[`canIn()`](#canin) का alias, जिसका नाम Spatie की `hasPermissionTo()` परंपरा को दर्शाने के लिए रखा गया है।

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

`bool` लौटाता है।

## `hasRoleIn()`

जांचें कि क्या कोई उपयोगकर्ता किसी संदर्भ इंस्टेंस तक सीमित भूमिका रखता है।

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

`bool` लौटाता है।

## `resolveFor()`

किसी दिए गए संदर्भ के भीतर उपयोगकर्ता की हर प्रभावी अनुमति के नाम को resolve करें।

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

अनुमति नामों का एक `Collection<string>` लौटाता है। इस सूची को बनाने में उपयोग होने वाले 3-स्रोत प्राथमिकता नियमों के लिए देखें [`ContextService::resolveFor()`](/hi/api/services.md#contextservice)।

## `guards()`

Clearance द्वारा प्रबंधित सभी authentication गार्ड्स के नाम सूचीबद्ध करें।

```php
Clearance::guards(); // Collection<string>
```

एक `Collection<string>` लौटाता है। [`GuardService`](/hi/api/services.md#guardservice) द्वारा समर्थित। गार्ड्स कैसे कॉन्फ़िगर किए जाते हैं और `auth.guards` में इंजेक्ट किए जाते हैं, इसके लिए देखें [गार्ड फीचर पेज](/hi/features/guards.md)।

## यह भी देखें

- [Traits & Directives](/hi/api/traits-directives.md) — `HasClearance` के माध्यम से समकक्ष instance मेथड्स, और guest-safe Blade directives।
- [Services](/hi/api/services.md) — अंतर्निहित `ContextService` और संबंधित services।
