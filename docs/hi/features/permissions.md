# अनुमतियाँ

अनुमतियाँ पैनल, Spatie के `Permission` मॉडल पर एक पूर्ण CRUD इंटरफेस है। Clearance अपनी खुद की कोई अनुमति टेबल पेश नहीं करता - यहां आपके द्वारा बनाई गई हर अनुमति एक नियमित Spatie अनुमति है, जो कहीं भी Spatie के API के उपयोग होने पर इस्तेमाल की जा सकती है (`$user->can()`, `Gate::allows()`, policies, `@can`)।

## पैनल क्या करता है

- **बनाना (Create)** - एक नई अनुमति जोड़ें, [नामकरण परंपरा](#permission-naming-convention) के विरुद्ध validated (जब तक अक्षम न हो) और एक गार्ड तक सीमित।
- **संपादित करना (Edit)** - किसी अनुमति का नाम बदलें या उसका गार्ड बदलें।
- **हटाना (Delete)** - किसी अनुमति और उसके भूमिका/उपयोगकर्ता असाइनमेंट को हटाएं (मानक Spatie cascade व्यवहार)।

<Screenshot src="/screenshots/permissions-page.png" alt="Clearance अनुमतियाँ पैनल जो नामकरण prefix के अनुसार समूहीकृत अनुमतियाँ सूचीबद्ध कर रहा है" caption="अनुमतियाँ पैनल हर Spatie अनुमति को नामकरण prefix के अनुसार समूहीकृत करके सूचीबद्ध करता है।" />

<Screenshot src="/screenshots/permission-form-modal.png" alt="नामकरण परंपरा validation के साथ अनुमति बनाने/संपादित करने का फॉर्म" caption="अनुमति फॉर्म सेव करने से पहले group-action नामकरण परंपरा को validate करता है।" />

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

समूह prefix (`orders-create` में `orders`) वही है जिसका उपयोग [डैशबोर्ड](/hi/features/dashboard) अलग-अलग अनुमति समूहों की गिनती के लिए करता है।

::: tip Enforcement को अक्षम करना
कुछ टीमें किसी अलग नामकरण योजना से माइग्रेट करती हैं। प्रति environment इस जांच को अक्षम करें:

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## एक्सेस नियंत्रण

पैनल तक पढ़ने की एक्सेस (अनुमति सूची देखना) के लिए `clearance-access` आवश्यक है। किसी अनुमति को बनाना, संपादित करना, या हटाना अतिरिक्त रूप से `clearance-permissions-write` की मांग करता है:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning `clearance-*` अनुमतियाँ आरक्षित हैं
`clearance-*` अनुमतियाँ स्वयं (`clearance-access`, `clearance-permissions-write`, आदि) इस पैनल के माध्यम से बनाई, नाम-परिवर्तित या हटाई नहीं जा सकतीं - यहां तक कि किसी `super_admin` एक्टर द्वारा भी नहीं। इन्हें विशेष रूप से पैकेज के अपने install फ्लो द्वारा प्रबंधित किया जाता है। देखें [सुपर एडमिन](/hi/features/super-admin#reserved-clearance-permissions)।
:::

## रूट और एम्बेडेड कंपोनेंट

| URI | Route नाम | विवरण |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | group-prefix नामकरण validation के साथ Permission CRUD |

```blade
<livewire:clearance::permissions.permission-manager />
```

## अगले कदम

- [भूमिकाएं](/hi/features/roles) - किसी भूमिका को guard-scoped अनुमतियाँ असाइन करें।
- [सुपर एडमिन](/hi/features/super-admin) - पूरी सूक्ष्म-स्तरीय write-अनुमति टेबल।
- [कॉन्फ़िगरेशन संदर्भ](/hi/guide/configuration) - `enforce_naming_convention`, `naming_separator`।
