# सुपर एडमिन

`super_admin` भूमिका `clearance:install` द्वारा स्वचालित रूप से बनाई जाती है और सभी `clearance-*` अनुमतियों के साथ sync रखी जाती है।

```bash
# Install and make user ID 1 a super admin
php artisan clearance:install --user=1
```

पूरी installer फ्लैग सूची के लिए देखें [इंस्टॉलेशन गाइड](/hi/guide/installation) और [Artisan कमांड संदर्भ](/hi/api/artisan-commands)।

## Gate bypass (वैकल्पिक)

`super_admin_gate_bypass` यह नियंत्रित करता है कि क्या `super_admin` भूमिका, `Gate::before()` hook के माध्यम से सभी Laravel `can()` / `Gate::allows()` / policy जांचों को globally bypass करती है। डिफ़ॉल्ट: `false`। bypass वैकल्पिक है। इसे सक्षम करने के लिए:

```php
// config/clearance.php
'super_admin_gate_bypass' => true,
```

::: danger एप्लिकेशन-व्यापी, पैनल तक सीमित नहीं
सक्षम होने पर, `super_admin` रखने वाला कोई भी उपयोगकर्ता एप्लिकेशन-व्यापी **सभी** Gate जांचों को स्किप कर देता है - न केवल Clearance पैनल की जांचों को। यह आपके एप्लिकेशन के हर `can()`, `Gate::allows()`, और policy चेक को प्रभावित करता है, न कि केवल उन्हें जो Clearance परिभाषित करता है। एक संकुचित दायरे के लिए, इसे बंद रखें और इसके बजाय स्पष्ट रूप से अनुमतियाँ प्रदान करें।
:::

## Alias detection

यदि आपके डेटाबेस में पहले से `super_admin`, `superadmin`, `super-admin`, `root`, या `owner` नाम की एक भूमिका मौजूद है, तो installer इसका पता लगाता है और आपको इसे प्रमोट करने या एक अलग `super_admin` बनाने के लिए इंटरैक्टिव रूप से प्रॉम्प्ट करता है। प्रॉम्प्ट को स्किप करने के लिए `--super-admin-role=NAME` का उपयोग करें:

```bash
php artisan clearance:install --super-admin-role=owner
```

::: tip Promotion non-destructive है
Promotion भूमिका का नाम यथावत बदल देता है - सभी मौजूदा `model_has_roles` असाइनमेंट `role_id` पर FK के माध्यम से सुरक्षित रहते हैं। promotion के दौरान किसी भी उपयोगकर्ता की मौजूदा भूमिका असाइनमेंट नहीं छूटती।
:::

## पैनल एक्सेस और सूक्ष्म-स्तरीय write अनुमतियाँ

इंस्टॉलेशन के बाद `/clearance` पर जाएं। पूरे पैनल तक पढ़ने की एक्सेस के लिए `clearance-access` अनुमति आवश्यक है, `can()` के माध्यम से जांची गई, `hasRole()` से कभी नहीं:

```php
$user->givePermissionTo('clearance-access');
```

प्रत्येक सेक्शन पर write ऑपरेशन एक समर्पित, सौंपी जा सकने वाली अनुमति द्वारा नियंत्रित होते हैं - सभी `clearance:install` द्वारा बनाई गई और `super_admin` को असाइन की गई:

| अनुमति | नियंत्रित करती है |
|---|---|
| `clearance-permissions-write` | अनुमतियों को बनाना / संपादित करना / हटाना |
| `clearance-roles-write` | भूमिकाओं और उनके अनुमति असाइनमेंट को बनाना / संपादित करना / हटाना |
| `clearance-guards-write` | गार्ड्स को बनाना / संपादित करना / हटाना |
| `clearance-settings-write` | डिस्प्ले मेटाडेटा, आइकन, रंग, डिफ़ॉल्ट भूमिका को संपादित करना |
| `clearance-users-write` | किसी विशिष्ट उपयोगकर्ता के लिए भूमिकाएं असाइन / हटाना |

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

::: tip संकुचित एक्सेस प्रदान करना
किसी उपयोगकर्ता (मान लीजिए, एक टीम लीड) को किसी एक पैनल सेक्शन - उदाहरण के लिए, roles - की write एक्सेस देने के लिए `clearance-access` को ठीक एक `clearance-{section}-write` अनुमति के साथ मिलाएं, बिना settings, guards, या user management को expose किए।
:::

यदि कोई सूक्ष्म-स्तरीय अनुमति row डेटाबेस में बिल्कुल मौजूद ही नहीं है (एक legacy pre-seeding स्थिति), तो जांच backwards compatibility के लिए `clearance-access` पर fall back हो जाती है। जिस भी instance पर `clearance:install` चल चुका है, वहां यह fallback कभी सक्रिय नहीं होता।

## सुरक्षित `clearance-*` अनुमतियाँ

`clearance-*` अनुमतियाँ आरक्षित हैं: इन्हें पैकेज के अपने install फ्लो के अलावा किसी भी तरह से बनाया, नाम-परिवर्तित, हटाया, या किसी भूमिका में जोड़ा नहीं जा सकता - यहां तक कि किसी `super_admin` एक्टर द्वारा भी नहीं, स्वयं `super_admin` भूमिका के लिए भी।

## अगले कदम

- [अनुमतियाँ](/hi/features/permissions) - सामान्य अनुमति CRUD पैनल (`clearance-*` को छोड़कर)।
- [भूमिकाएं](/hi/features/roles) - किसी कस्टम भूमिका को `clearance-*-write` अनुमतियाँ असाइन करना।
- [सुरक्षा](/hi/guide/security) - पूरा privilege-escalation और ceiling मॉडल।
