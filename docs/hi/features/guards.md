# गार्ड

गार्ड पैनल, authentication गार्ड्स को डेटाबेस रिकॉर्ड के रूप में प्रबंधित करता है, ताकि गार्ड्स को `config/auth.php` संपादित किए और दोबारा deploy किए बिना जोड़ा या बदला जा सके।

## पैनल क्या करता है

- हर गार्ड को एक `name`, `driver`, और `provider` के साथ **बनाना / संपादित करना / हटाना**।

<Screenshot src="/screenshots/guards-page.png" alt="Clearance गार्ड पैनल जो डेटाबेस-समर्थित गार्ड्स को सूचीबद्ध कर रहा है" caption="गार्ड पैनल हर डेटाबेस-समर्थित गार्ड को उसके driver और provider के साथ सूचीबद्ध करता है।" />

## गार्ड्स कैसे लागू होते हैं

पैनल से प्रबंधित गार्ड्स एप्लिकेशन के boot के समय `auth.guards` में इंजेक्ट किए जाते हैं, ताकि किसी भी request के चलने से पहले Laravel का auth सिस्टम उन्हें पहचान ले - `config/auth.php` में मैन्युअल संपादन की आवश्यकता नहीं।

## Allow-listed ड्राइवर

केवल allow-list में मौजूद ड्राइवर स्वीकार किए जाते हैं:

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning Unlisted ड्राइवर स्किप किए जाते हैं, ज़ोर से अस्वीकार नहीं किए जाते
जिस गार्ड का ड्राइवर `allowed_guard_drivers` में नहीं है, उसे auth config को corrupt करने के बजाय boot के समय (एक log warning के साथ) स्किप कर दिया जाता है। यदि आपके द्वारा बनाया गया कोई गार्ड प्रभावी नहीं हो रहा है, तो पहले ड्राइवर को allow-list के विरुद्ध जांचें।
:::

## एक्सेस नियंत्रण

पढ़ने की एक्सेस के लिए `clearance-access` आवश्यक है। किसी गार्ड को बनाना, संपादित करना, या हटाना `clearance-guards-write` की मांग करता है:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## रूट और एम्बेडेड कंपोनेंट

| URI | Route नाम | विवरण |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | गार्ड प्रबंधन (create/edit/delete) |

```blade
<livewire:clearance::guards.guard-manager />
```

## डेटाबेस

| टेबल | मुख्य कॉलम |
|---|---|
| `clr_guards` | `name` (unique), `driver`, `provider` |

## अगले कदम

- [कॉन्फ़िगरेशन संदर्भ](/hi/guide/configuration) - पूरी `allowed_guard_drivers` और `guards` config keys।
- [Facade संदर्भ](/hi/api/facade) - `Clearance::guards()` सभी प्रबंधित authentication गार्ड्स के नाम लौटाता है।
