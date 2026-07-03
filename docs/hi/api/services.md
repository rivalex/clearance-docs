# सर्विसेज़

Clearance की सभी services `src/Services/` में रहती हैं और container से resolve की जाती हैं, जैसे `app(ContextService::class)`। ये controllers, policies, और jobs से पहुंचने की परत हैं — कहीं भी जहां `User` मॉडल `HasClearance` trait का उपयोग नहीं कर रहा हो।

## `ContextService`

`Rivalex\Clearance\Services\ContextService` (`src/Services/ContextService.php`) प्रासंगिक प्राधिकरण के पीछे का engine है। [`Clearance` facade](/hi/api/facade.md) और `HasClearance` trait दोनों इसी को delegate करते हैं।

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

`resolveFor()` अपना परिणाम एक 3-स्रोत प्राथमिकता का उपयोग करके बनाता है:

1. प्रासंगिक भूमिका अनुदान
2. वैश्विक भूमिका अनुदान
3. प्रति-संदर्भ ओवरराइड (`forced_on` जोड़ता है, `forced_off` हमेशा जीतता है)

इस प्राथमिकता के concept walkthrough के लिए देखें [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization.md)।

## `RoleService`

`Rivalex\Clearance\Services\RoleService` (`src/Services/RoleService.php`) सीलिंग भूमिकाओं को प्रबंधित करता है।

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| मेथड | विवरण |
|---|---|
| `setParent($childRole, $parentRole)` | एक सीलिंग सेट करता है: पैरेंट के सेट से अधिक `$childRole` की अतिरिक्त अनुमतियाँ चुपचाप trim कर दी जाती हैं। यदि चाइल्ड पहले से ही एक पैरेंट है, या पैरेंट पहले से ही एक चाइल्ड है, तो `ClearanceScopeViolationException` throw करता है। |
| `removeParent($childRole)` | सीलिंग संबंध को हटाता है। |

::: warning
एक ceiling केवल trim करता है — यह कभी अनुमति प्रदान नहीं करता। एक permissive पैरेंट भूमिका खुद-ब-खुद चाइल्ड को कोई अनुमति नहीं देती। किसी पैरेंट भूमिका से अनुमति हटाने पर वह हटाव स्वचालित रूप से हर चाइल्ड भूमिका में cascade हो जाता है।
:::

सीलिंग-भूमिका अवधारणा के विस्तृत विवरण के लिए देखें [भूमिकाएं](/hi/features/roles.md)।

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService` (`src/Services/PermissionService.php`) Permissions पैनल के CRUD को समर्थन देता है।

यह `group-action` नामकरण परंपरा को लागू करता है, जो `enforce_naming_convention` और `naming_separator` config keys द्वारा नियंत्रित होती है, और install फ्लो के बाहर `clearance-*` अनुमति नेमस्पेस को बनाए जाने, नाम-परिवर्तित होने, या हटाए जाने से बचाता है।

## `GuardService`

`Rivalex\Clearance\Services\GuardService` (`src/Services/GuardService.php`) Guards पैनल के CRUD को समर्थन देता है।

यह DB-समर्थित गार्ड्स को boot के समय `auth.guards` में इंजेक्ट करता है, `allowed_guard_drivers` config key द्वारा फ़िल्टर किया गया — auth config को corrupt करने के बजाय एक unlisted ड्राइवर को log warning के साथ स्किप कर दिया जाता है।

## `MetaService`

`Rivalex\Clearance\Services\MetaService` (`src/Services/MetaService.php`) किसी भूमिका या गार्ड विषय के लिए `clr_meta` (डिस्प्ले नाम, विवरण, sanitized SVG आइकन, रंग) को पढ़ता और लिखता है। यह Settings पैनल के डिस्प्ले-मेटाडेटा editor को समर्थन देता है।

SVG आइकन `Rivalex\Clearance\Support\SvgSanitizer` (एक allow-list sanitizer) से स्टोरेज से पहले और render के समय दोनों बार होकर गुजरते हैं।

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService` (`src/Services/UserClearanceService.php`) Users मॉड्यूल पैनल को समर्थन देता है:

- वैश्विक भूमिका असाइनमेंट
- प्रासंगिक भूमिका असाइनमेंट (`UserRoleContext` के माध्यम से)
- प्रति-संदर्भ अनुमति ओवरराइड (`UserContextPermissionOverride` के माध्यम से, `forced_on` / `forced_off`)
- असाइनमेंट हटाना, जिससे ओवरराइड हटाव cascade होती है

यह एक privilege ceiling लागू करता है: कोई गैर-`super_admin` एक्टर अपने खुद के असाइनमेंट को संशोधित नहीं कर सकता, और ऐसी अनुमति नहीं दे सकता जो उसके पास स्वयं नहीं है। देखें [सुपर एडमिन](/hi/features/super-admin.md) और [उपयोगकर्ता](/hi/features/users.md)।

## Exceptions

सभी `src/Exceptions/` में:

| Exception | कब throw होता है |
|---|---|
| `ClearanceConfigException` | जब `config/clearance.php` का कोई अमान्य या misconfigured वैल्यू पढ़ा जाता है (जैसे एक गलत `naming_separator`)। |
| `ClearanceNamingException` | जब enforcement चालू होने पर कोई अनुमति नाम `group-action` नामकरण परंपरा का उल्लंघन करता है। |
| `ClearanceProtectedResourceException` | जब install फ्लो के बाहर किसी सुरक्षित `clearance-*` अनुमति, या किसी locked resource को संशोधित करने का प्रयास किया जाता है। |
| `ClearanceScopeViolationException` | जब एक अमान्य सीलिंग-भूमिका संबंध होता है (कोई पैरेंट चाइल्ड बनने की कोशिश करे, या इसका उल्टा), या कोई दायरा mismatch हो। |

## यह भी देखें

- [Facade](/hi/api/facade.md) — `ContextService` के चारों ओर static-call wrapper।
- [Traits & Directives](/hi/api/traits-directives.md) — instance-method और Blade-directive समकक्ष।
- [Artisan Commands](/hi/api/artisan-commands.md) — `clearance:install` और `clearance:backfill`, जो इन services द्वारा संचालित डेटा को सीड करते हैं।
