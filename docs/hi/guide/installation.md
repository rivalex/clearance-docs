# इंस्टॉलेशन

## आवश्यकताएं

Introduction पेज पर पूरी [आवश्यकताओं की टेबल](/hi/guide/introduction#requirements) देखें - PHP ^8.3, Laravel ^11.0|^12.0|^13.0, spatie/laravel-permission ^6.0, livewire/livewire ^3.0|^4.0, livewire/flux ^2.14।

## पैकेज इंस्टॉल करें

```bash
composer require rivalex/clearance
```

## इंस्टॉलर चलाएं

```bash
php artisan clearance:install
```

::: danger हमेशा clearance:install चलाएं, कभी सादा migrate न करें
Clearance के अपने migrations, Spatie की `roles`/`permissions` टेबल्स के पहले से मौजूद होने पर निर्भर करते हैं; installer अपने से पहले Spatie के migrations को publish और run करता है। यदि आप एक नए (fresh) सेटअप पर सीधे `php artisan migrate` चलाते हैं और Spatie अभी सेट अप नहीं है, तो आपको एक actionable एरर मैसेज मिलेगा जो आपको इसके बजाय installer चलाने के लिए कहेगा।
:::

### वैकल्पिक फ्लैग

| फ्लैग | विवरण |
|---|---|
| `--user=ID` | इंस्टॉल के बाद किसी उपयोगकर्ता को `super_admin` भूमिका असाइन करें |
| `--role=NAME` | किसी भूमिका को `clearance-access` असाइन करें (यदि मौजूद नहीं है तो बनाई जाएगी) |
| `--super-admin-role=NAME` | किसी मौजूदा भूमिका को `super_admin` में प्रमोट करें (इंटरैक्टिव प्रॉम्प्ट को स्किप करता है) |
| `--force` | पहले से इंस्टॉल होने पर भी दोबारा चलाएं |

### इंस्टॉलर क्या करता है

1. `config/clearance.php` को publish करता है।
2. Clearance के migrations को publish करता है।
3. Spatie Permission टेबल्स का पता लगाता है - यदि `roles` टेबल मौजूद नहीं है, तो पहले Spatie के migrations को स्वचालित रूप से publish और run करता है।
4. सभी लंबित migrations चलाता है।
5. `clearance-access` और सभी 5 सूक्ष्म-स्तरीय क्षमता अनुमतियाँ बनाता है: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`।
6. सभी `clearance-*` अनुमतियों के साथ `super_admin` भूमिका बनाता है (additive - दोबारा चलाने पर मौजूदा अनुमतियों को कभी नहीं हटाता)।
7. Idempotency मार्कर के रूप में `storage/.clearance-installed` लिखता है।

::: tip किसी मौजूदा Spatie install से माइग्रेट कर रहे हैं?
शुरुआत से करने के बजाय `php artisan clearance:backfill` का उपयोग करें, और पहले [माइग्रेशन गाइड](/hi/guide/migration) पढ़ें - यह `super_admin` collision, कस्टम मॉडल subclasses, और गार्ड import को कवर करता है।
:::

## अपने User मॉडल में HasClearance जोड़ें

प्रासंगिक प्राधिकरण मेथड्स (`$user->canIn()`, `$user->hasRoleIn()`) का उपयोग करने के लिए, `App\Models\User` में `HasClearance` trait जोड़ें। इसमें पहले से ही Spatie का `HasRoles` शामिल है - एक लाइन दोनों की जगह ले लेती है:

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // includes HasRoles + all contextual authorization methods
}
```

::: tip पहले से `use HasRoles` इस्तेमाल कर रहे हैं?
इसे `use HasClearance` से बदलें, या दोनों रखें - PHP trait composition को स्वचालित रूप से deduplicate करता है, कोई conflict नहीं होता।
:::

इस trait के बिना, `$user->canIn()` और संबंधित मेथड्स उपलब्ध नहीं होते, लेकिन Blade directives (`@canin`, `@hasrolein`) और `ContextService` फिर भी काम करते रहते हैं - वे trait पर निर्भर नहीं हैं। विवरण के लिए देखें [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization)।

## अगले कदम

पैनल माउंट करने और अनुमतियों को प्रबंधित करना शुरू करने के लिए [क्विक स्टार्ट](/hi/guide/quick-start) पर जारी रखें।
