# उपयोगकर्ता

Users मॉड्यूल एक प्रति-उपयोगकर्ता पैनल है जो भूमिकाओं को - वैश्विक रूप से या किसी विशिष्ट संदर्भ तक सीमित - असाइन करने के लिए, और किसी संदर्भ के भीतर व्यक्तिगत अनुमतियों को देने या अस्वीकार करने के लिए है। यह वैकल्पिक है और डिफ़ॉल्ट रूप से अक्षम है।

## मॉड्यूल को सक्षम करना

```php
// config/clearance.php
'modules' => ['users' => true],
```

## पैनल क्या करता है

- **वैश्विक भूमिका असाइनमेंट** - `scope=global` भूमिकाओं को सीधे उपयोगकर्ता को, एप्लिकेशन-व्यापी असाइन करें।
- **प्रासंगिक भूमिका असाइनमेंट** - `scope=contextual` भूमिकाओं को किसी विशिष्ट मॉडल इंस्टेंस (`contextual_models` में घोषित एक Store, Team, या Project) तक सीमित असाइन करें।
- **प्रति-संदर्भ अनुमति ओवरराइड** - हर प्रासंगिक भूमिका असाइनमेंट के लिए, अतिरिक्त अनुमतियों को प्रति संदर्भ इंस्टेंस स्वतंत्र रूप से force-grant या force-deny किया जा सकता है।
- **असाइनमेंट हटाना** - उपयोगकर्ता से किसी भूमिका को हटाता है (वैश्विक या प्रासंगिक दायरा), जिससे जुड़े किसी भी ओवरराइड की हटाव cascade हो जाती है।

<Screenshot src="/screenshots/user-panel-global.png" alt="वैश्विक भूमिका असाइनमेंट दिखाने वाला उपयोगकर्ता पैनल" caption="किसी उपयोगकर्ता को वैश्विक भूमिका असाइन करना - एप्लिकेशन-व्यापी लागू होता है।" />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="प्रासंगिक भूमिका असाइनमेंट और प्रति-संदर्भ अनुमति ओवरराइड दिखाने वाला उपयोगकर्ता पैनल" caption="एक प्रासंगिक भूमिका असाइन करना और किसी विशिष्ट Store इंस्टेंस के लिए प्रति-संदर्भ अनुमति ओवरराइड सेट करना।" />

::: warning स्व-परिवर्तन पर privilege ceiling
भूमिका असाइनमेंट और direct/override अनुमति अनुदान दोनों एक privilege ceiling लागू करते हैं: कोई गैर-`super_admin` एक्टर अपने खुद के असाइनमेंट को संशोधित नहीं कर सकता, और ऐसी अनुमति नहीं दे सकता जो उसके पास स्वयं नहीं है।
:::

## एक्सेस नियंत्रण

`/clearance/user/{userId}` तक एक्सेस के लिए `clearance-access` (पढ़ना) और `clearance-users-write` (असाइन/हटाना) आवश्यक है:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## रूट और एम्बेडेड कंपोनेंट

| URI | Route नाम | विवरण |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | प्रति-उपयोगकर्ता भूमिका असाइनमेंट + प्रासंगिक अनुमति ओवरराइड (`modules.users = true` आवश्यक है) |

```blade
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### अपने ऐप से पैनल को लिंक करना

```php
route('clearance.user', ['userId' => $user->id])
```

## पर्दे के पीछे प्रासंगिक भूमिकाएं

किसी उपयोगकर्ता को प्रासंगिक भूमिका असाइन करने से एक `UserRoleContext` row बनती है:

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

प्राधिकरण जांच `$user->canIn()`, `@canin`, और `ContextService` के माध्यम से संदर्भ का सम्मान करती है। पूरे resolution मॉडल के लिए देखें [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization)।

## डेटाबेस

| टेबल | मुख्य कॉलम |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - `(user, role, context_type, context_id)` के अनुसार unique |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## अगले कदम

- [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization) - `canIn()` के लिए पूरा resolution क्रम।
- [भूमिकाएं](/hi/features/roles) - किसी भूमिका का दायरा और `context_types` कॉन्फ़िगर करना।
- [कॉन्फ़िगरेशन संदर्भ](/hi/guide/configuration) - `modules.users`, `contextual_models`।
