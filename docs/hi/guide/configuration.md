# कॉन्फ़िगरेशन

Clearance उचित डिफ़ॉल्ट्स के साथ आता है, इसलिए [इंस्टॉलेशन](/hi/guide/installation) के बाद यह तुरंत काम करता है। जब आपको route prefix, middleware, प्रासंगिक मॉडल, या किसी अन्य सेटिंग को कस्टमाइज़ करना हो, तो config फाइल को publish करें।

```bash
php artisan vendor:publish --tag=clearance-config
```

यह `config/clearance.php` को publish करता है।

## कॉन्फ़िगरेशन संदर्भ

यह `config/clearance.php` में हर key के लिए निश्चित संदर्भ है। इन दस्तावेज़ों के अन्य पेज इस टेबल को दोहराने के बजाय यहीं लिंक करते हैं।

| Key | डिफ़ॉल्ट | उद्देश्य |
|---|---|---|
| `route_prefix` | `'clearance'` | सभी पैनल रूट्स के लिए URI prefix। |
| `middleware` | `['web', 'auth']` | `clearance.access` से पहले सभी पैनल रूट्स पर लागू होता है। |
| `access_permission` | `'clearance-access'` | access middleware द्वारा जांची जाने वाली अनुमति। |
| `user_model` | `null` | डिफ़ॉल्ट रूप से `auth.providers.users.model` का उपयोग करता है। |
| `modules.users` | `false` | Users मॉड्यूल के लिए मास्टर स्विच (routes + Livewire components)। |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']` - वे मॉडल जो भूमिका-बंधन संदर्भ के रूप में कार्य कर सकते हैं। |
| `enforce_naming_convention` | `true` | `group-action` अनुमति नामकरण लागू करता है। |
| `naming_separator` | `'-'` | विभाजक (separator) कैरेक्टर - केवल `'-'` या `'_'`। |
| `guards` | `[]` | `config/auth.php` से auto-detect किए गए गार्ड्स को ओवरराइड करता है। खाली = सभी को auto-detect करें। |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | DB गार्ड्स को `auth.guards` में इंजेक्ट करते समय स्वीकार किए जाने वाले ड्राइवर। |
| `layout` | `null` | पूरे पेज वाले components के लिए Blade layout। `null` = host ऐप का डिफ़ॉल्ट (`config('livewire.layout')`)। |
| `auto_assign_default_role` | `false` | `Registered` इवेंट पर Settings में कॉन्फ़िगर की गई डिफ़ॉल्ट भूमिका को स्वचालित रूप से असाइन करता है। |
| `super_admin_gate_bypass` | `false` | वैकल्पिक: `super_admin` एप्लिकेशन-व्यापी सभी Gate जांचों को bypass करता है। |
| `ui.flux_pro` | `null` | `null` = `Flux::pro()` के माध्यम से auto-detect। |

## अनुमति नामकरण परंपरा

अनुमतियों को `group-action` फॉर्मेट का पालन करना चाहिए:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

`enforce_naming_convention` के साथ प्रति environment इस enforcement को अक्षम करें:

```php
'enforce_naming_convention' => false,
```

::: warning Separator को boot के समय validate किया जाता है
`naming_separator` वैल्यू इस config का एकमात्र हिस्सा है जो एक raw SQL fragment (अनुमतियों को group/sort करने के लिए) तक पहुंचता है, इसलिए केवल `-` या `_` ही स्वीकार किए जाते हैं।
:::

## Middleware को कस्टमाइज़ करना

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

Route names, prefix या middleware में बदलाव के बावजूद स्थिर रहते हैं - पूरी routes टेबल के लिए [क्विक स्टार्ट](/hi/guide/quick-start) देखें।

## संबंधित

- [क्विक स्टार्ट](/hi/guide/quick-start) - इन सेटिंग्स के लागू होने पर पैनल को माउंट करना
- [एसेट्स पब्लिश करना](/hi/guide/publishing) - translations को publish करना और `layout` key को समझना
- [गार्ड](/hi/features/guards) - `guards` और `allowed_guard_drivers` का उपयोग कैसे होता है
- [सुपर एडमिन](/hi/features/super-admin) - `super_admin_gate_bypass` कैसे काम करता है
