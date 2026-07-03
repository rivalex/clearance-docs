# क्विक स्टार्ट

[इंस्टॉलेशन](/hi/guide/installation) के बाद, Clearance पैनल को expose करने के दो तरीके हैं: पहले से बने रूट्स, या अपने ऐप में कहीं भी अलग-अलग Livewire components को एम्बेड करना।

## विकल्प A - पहले से बने रूट्स

इंस्टॉलेशन के बाद पैनल `/clearance` (या कॉन्फ़िगर किए गए `route_prefix`, देखें [कॉन्फ़िगरेशन](/hi/guide/configuration)) पर उपलब्ध होता है। सभी रूट्स `clearance.access` middleware द्वारा सुरक्षित हैं और स्वचालित रूप से host ऐप के Livewire layout का उपयोग करते हैं।

| URI | Route नाम | विवरण |
|---|---|---|
| `/clearance` | `clearance.home` | डैशबोर्ड |
| `/clearance/guards` | `clearance.guards` | गार्ड प्रबंधन (create/edit/delete) |
| `/clearance/permissions` | `clearance.permissions` | group-prefix नामकरण validation के साथ Permission CRUD |
| `/clearance/roles` | `clearance.roles` | guard-scoped अनुमति असाइनमेंट, दायरा (global/contextual), और ceiling के साथ Role CRUD |
| `/clearance/settings` | `clearance.settings` | डिस्प्ले मेटाडेटा (आइकन, रंग), डिफ़ॉल्ट भूमिका, सामान्य सेटिंग्स |
| `/clearance/user/{userId}` | `clearance.user` | प्रति-उपयोगकर्ता भूमिका असाइनमेंट + प्रासंगिक अनुमति ओवरराइड (`modules.users = true` आवश्यक है) |

Prefix में बदलाव के बावजूद route names स्थिर रहते हैं। middleware को कस्टमाइज़ करने के लिए:

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## विकल्प B - एम्बेडेड Livewire Components

प्रत्येक पैनल सेक्शन को किसी भी Blade view, Filament page, या कस्टम route के अंदर अलग-अलग माउंट किया जा सकता है:

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## आगे कहां जाएं

- [कॉन्फ़िगरेशन संदर्भ](/hi/guide/configuration) - हर config key, डिफ़ॉल्ट और उद्देश्य
- [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization) - भूमिकाओं और अनुमतियों को किसी Store, Tenant, या Project तक सीमित करना
- [भूमिकाएं](/hi/features/roles) - दायरा और सीलिंग भूमिकाएं
- [डैशबोर्ड](/hi/features/dashboard) - डैशबोर्ड कंपोनेंट क्या दिखाता है
- [उपयोगकर्ता](/hi/features/users) - वैकल्पिक प्रति-उपयोगकर्ता पैनल
