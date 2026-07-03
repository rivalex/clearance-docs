# प्रासंगिक प्राधिकरण

Clearance, Spatie के वैश्विक प्राधिकरण मॉडल को context-scoped जांचों के साथ विस्तारित करता है। किसी उपयोगकर्ता की भूमिकाएं किसी विशिष्ट संदर्भ (जैसे एक Store, Tenant, या Project) के भीतर असाइन की जा सकती हैं और उस दायरे के बाहर अदृश्य रहती हैं। यह [Users मॉड्यूल](/hi/features/users) और प्रासंगिक [भूमिकाओं](/hi/features/roles#scope-global-vs-contextual) के पीछे का मूल तंत्र है।

## User मॉडल पर

जब आपका `User` मॉडल `HasClearance` trait का उपयोग करता है, तब उपलब्ध (देखें [इंस्टॉलेशन](/hi/guide/installation#add-hasclearance-to-your-user-model)):

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

## Blade views में

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip डिज़ाइन के अनुसार guest-safe
दोनों directives किसी guest उपयोगकर्ता के लिए सुरक्षित रूप से resolve होते हैं (`false` return करते हैं) - ये कभी throw नहीं करते। आप authentication सुनिश्चित होने से पहले रेंडर होने वाले views में `@canin`/`@hasrolein` का उपयोग बिना उन्हें `@auth` जांच में लपेटे कर सकते हैं।
:::

## ContextService के माध्यम से

Controllers, policies, या jobs में उपयोग के लिए जहां `User` मॉडल में `HasClearance` trait न हो:

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## Clearance facade के माध्यम से

`Clearance` facade उसी service को delegate करता है - एक सुविधाजनक global entry point:

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

पूरी मेथड सूची के लिए देखें [Facade संदर्भ](/hi/api/facade)।

## Resolution कैसे काम करता है

`resolveFor()` तीन स्रोतों को इस प्राथमिकता क्रम में मिलाता है:

1. **प्रासंगिक भूमिका अनुदान** - उन भूमिकाओं से अनुमतियाँ जो उपयोगकर्ता विशेष रूप से इस संदर्भ के भीतर रखता है (`clr_role_ctx`)।
2. **वैश्विक भूमिका अनुदान** - उपयोगकर्ता की globally-असाइन की गई Spatie भूमिकाओं से अनुमतियाँ (ताकि `super_admin` और अन्य वैश्विक भूमिकाएं किसी भी संदर्भ के लिए `canIn()` को संतुष्ट करें)।
3. **प्रति-संदर्भ ओवरराइड** (`clr_ctx_overrides`) - `forced_on` उपरोक्त के ऊपर एक अनुमति जोड़ता है; `forced_off` इसे हटा देता है, भले ही वह अनुमति किसी वैश्विक भूमिका द्वारा दी गई हो।

::: danger एक deny हमेशा जीतता है
यदि किसी दिए गए संदर्भ में किसी उपयोगकर्ता के लिए कोई अनुमति `forced_off` है, तो वह denial हर दूसरे स्रोत को override कर देता है - जिसमें एक वैश्विक भूमिका अनुदान और एक प्रासंगिक भूमिका अनुदान भी शामिल है। उस संदर्भ के लिए `forced_off` अनुमति को override हटाने के अलावा दोबारा देने का कोई तरीका नहीं है। अपनी override नीति इसी को ध्यान में रखकर डिज़ाइन करें: `forced_off` सिस्टम में सबसे मजबूत संकेत है।
:::

## डेटाबेस

| टेबल | मुख्य कॉलम |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - `(user, role, context_type, context_id)` के अनुसार unique |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## अगले कदम

- [उपयोगकर्ता](/hi/features/users) - प्रासंगिक भूमिकाएं और ओवरराइड असाइन करने के लिए पैनल UI।
- [भूमिकाएं](/hi/features/roles#scope-global-vs-contextual) - किसी भूमिका को `contextual` घोषित करना और उसके अनुमत `context_types`।
- [Traits & Directives संदर्भ](/hi/api/traits-directives) - पूरा `HasClearance` trait और Blade directive signatures।
- [Services संदर्भ](/hi/api/services) - पूरा `ContextService` API।
