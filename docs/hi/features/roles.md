# भूमिकाएं

भूमिकाएं पैनल, Spatie के `Role` मॉडल पर एक पूर्ण CRUD इंटरफेस है, जिसे दो Clearance-विशिष्ट अवधारणाओं के साथ विस्तारित किया गया है: **दायरा (scope)** (वैश्विक बनाम प्रासंगिक) और **सीलिंग (ceiling)** (एक पैरेंट भूमिका जो चाइल्ड की अनुमतियों को सीमित करती है)।

## पैनल क्या करता है

- भूमिकाओं को **बनाना / संपादित करना / हटाना**।
- **Guard-scoped अनुमति असाइनमेंट** - किसी भूमिका को अनुमतियाँ असाइन करें, भूमिका के गार्ड के अनुसार फ़िल्टर की गई।
- **दायरा (Scope)** चयन - `global` या `contextual`।
- **सीलिंग (Ceiling)** कॉन्फ़िगरेशन - एक पैरेंट भूमिका सेट या हटाएं।

<Screenshot src="/screenshots/roles-page.png" alt="Clearance भूमिकाएं पैनल जो दायरा और सीलिंग के साथ भूमिकाएं सूचीबद्ध कर रहा है" caption="भूमिकाएं पैनल हर भूमिका को उसके दायरे और, यदि सेट है, उसके सीलिंग पैरेंट के साथ सूचीबद्ध करता है।" />

## दायरा: वैश्विक बनाम प्रासंगिक

हर भूमिका बनाते समय एक दायरा घोषित करती है:

- **`global`** - `$user->assignRole()` के माध्यम से सीधे किसी उपयोगकर्ता को असाइन की जाती है, हर जगह लागू होती है।
- **`contextual`** - एक या अधिक मॉडल प्रकारों (`context_types`) से बंधी होती है, केवल `UserRoleContext` के माध्यम से किसी विशिष्ट संदर्भ इंस्टेंस के भीतर असाइन करने योग्य। देखें [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization) और [उपयोगकर्ता](/hi/features/users#contextual-roles-under-the-hood)।

बिना किसी स्पष्ट `RoleMeta` row वाली भूमिकाएं backwards compatibility के लिए डिफ़ॉल्ट रूप से `global` होती हैं।

## सीलिंग भूमिकाएं

एक भूमिका एक सीलिंग घोषित कर सकती है - एक पैरेंट भूमिका जिसका अनुमति सेट एक ऊपरी सीमा के रूप में कार्य करता है:

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
```

<Screenshot src="/screenshots/role-form-ceiling.png" alt="भूमिका फॉर्म जिसमें एक सीलिंग पैरेंट भूमिका चुनी गई है" caption="भूमिका फॉर्म (RoleForm) में एक सीलिंग पैरेंट सेट करना।" />

हर सेव पर स्वचालित रूप से लागू होने वाले नियम:

- चाइल्ड केवल वही अनुमतियाँ रख सकती है जो पैरेंट भी रखती है। कोई भी अतिरिक्त अनुमति `setParent()` पर और हर बाद के अनुमति sync पर चुपचाप trim कर दी जाती है - कोई exception नहीं, कोई error message नहीं, बस चुपचाप लागू किया जाता है।
- जो भूमिका पहले से ही एक पैरेंट के रूप में कार्य करती है वह चाइल्ड नहीं बन सकती (`ClearanceScopeViolationException`)।
- जो भूमिका पहले से ही एक चाइल्ड है वह स्वयं पैरेंट के रूप में कार्य नहीं कर सकती (`ClearanceScopeViolationException`)।
- किसी पैरेंट से अनुमतियाँ हटाना स्वचालित रूप से हर चाइल्ड में यह हटाव cascade करता है।

::: warning एक ceiling केवल trim करता है, यह कभी अनुमति प्रदान नहीं करता
एक permissive पैरेंट सेट करने से चाइल्ड को खुद-ब-खुद कुछ नहीं मिलता। ceiling सख्ती से एक ऊपरी सीमा है - यह कभी भी चाइल्ड में अनुमतियाँ नहीं जोड़ता, यह केवल उन्हें हटा सकता है।
:::

यह भूमिकाएं पैनल (`RoleForm`) में प्रति-भूमिका कॉन्फ़िगर किया जाता है, या प्रोग्रामेटिक रूप से `RoleService::setParent()` / `RoleService::removeParent()` के माध्यम से।

## एक्सेस नियंत्रण

पढ़ने की एक्सेस के लिए `clearance-access` आवश्यक है। किसी भूमिका को बनाना, संपादित करना, या हटाना - जिसमें उसके अनुमति असाइनमेंट, दायरा, और सीलिंग शामिल है - `clearance-roles-write` की मांग करता है:

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

## रूट और एम्बेडेड कंपोनेंट

| URI | Route नाम | विवरण |
|---|---|---|
| `/clearance/roles` | `clearance.roles` | guard-scoped अनुमति असाइनमेंट, दायरा (global/contextual), और ceiling के साथ Role CRUD |

```blade
<livewire:clearance::roles.role-manager />
```

## डेटाबेस

भूमिका मेटाडेटा (दायरा, सीलिंग, lock स्थिति) `clr_role_meta` में रहता है, Spatie की `roles` टेबल में कभी नहीं:

| टेबल | मुख्य कॉलम |
|---|---|
| `clr_role_meta` | `role_id` (unique, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (nullable FK -> `roles`, `ON DELETE SET NULL`) |

## अगले कदम

- [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization) - runtime पर `scope=contextual` भूमिकाओं की जांच कैसे होती है।
- [उपयोगकर्ता](/hi/features/users) - किसी विशिष्ट उपयोगकर्ता को, वैश्विक रूप से या प्रति संदर्भ, भूमिकाएं असाइन करना।
- [Services संदर्भ](/hi/api/services) - पूरा `RoleService` API।
