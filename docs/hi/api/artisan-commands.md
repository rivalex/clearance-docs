# Artisan कमांड

Clearance दो Artisan कमांड के साथ आता है: पहली बार सेटअप के लिए `clearance:install`, और मौजूदा installs पर डिफ़ॉल्ट्स सीड करने के लिए `clearance:backfill`। पूरे end-to-end सेटअप walkthrough के लिए देखें [इंस्टॉलेशन गाइड](/hi/guide/installation.md)।

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| फ्लैग | विवरण |
|---|---|
| `--user=ID` | इंस्टॉल के बाद किसी उपयोगकर्ता को `super_admin` भूमिका असाइन करें। |
| `--role=NAME` | किसी भूमिका को `clearance-access` असाइन करें (यदि मौजूद नहीं है तो बनाई जाएगी)। |
| `--super-admin-role=NAME` | किसी मौजूदा भूमिका को `super_admin` में प्रमोट करें (इंटरैक्टिव प्रॉम्प्ट को स्किप करता है)। |
| `--force` | पहले से इंस्टॉल होने पर भी दोबारा चलाएं। |

### यह क्रम में क्या करता है

1. `config/clearance.php` को publish करता है।
2. Clearance के migrations को publish करता है।
3. Spatie Permission टेबल्स का पता लगाता है — यदि `roles` टेबल मौजूद नहीं है, तो पहले Spatie के migrations को स्वचालित रूप से publish और run करता है।
4. सभी लंबित migrations चलाता है।
5. `clearance-access` और सभी 5 सूक्ष्म-स्तरीय क्षमता अनुमतियाँ बनाता है: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`।
6. सभी `clearance-*` अनुमतियों के साथ `super_admin` भूमिका बनाता है। यह चरण additive है — दोबारा चलाने पर यह मौजूदा अनुमतियों को कभी नहीं हटाता।
7. Idempotency मार्कर के रूप में `storage/.clearance-installed` लिखता है।

### Alias detection

यदि डेटाबेस में पहले से `super_admin`, `superadmin`, `super-admin`, `root`, या `owner` नाम की एक भूमिका मौजूद है, तो installer इसका पता लगाता है और इसे या तो प्रमोट करने या एक अलग `super_admin` भूमिका बनाने के लिए इंटरैक्टिव रूप से प्रॉम्प्ट करता है। प्रॉम्प्ट को non-interactively स्किप करने के लिए `--super-admin-role=NAME` का उपयोग करें।

Promotion भूमिका का नाम यथावत बदल देता है — सभी `model_has_roles` असाइनमेंट `role_id` पर foreign key के माध्यम से सुरक्षित रहते हैं।

::: warning
`clearance-*` अनुमतियाँ install फ्लो के बाहर सुरक्षित हैं — देखें [`PermissionService`](/hi/api/services.md#permissionservice) और [Services](/hi/api/services.md#exceptions) में `ClearanceProtectedResourceException`।
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| फ्लैग | विवरण |
|---|---|
| `--only=meta\|roles\|guards` | केवल एक backfill सेक्शन चलाएं। |
| `--dry-run` | बिना लिखे बदलावों का पूर्वावलोकन करें। |

### सेक्शन

| सेक्शन | विवरण |
|---|---|
| `meta` | बिना meta वाली हर भूमिका के लिए `clr_meta` डिस्प्ले नाम (title-cased भूमिका नाम) सीड करता है। |
| `roles` | बिना meta वाली हर भूमिका के लिए `clr_role_meta` डिफ़ॉल्ट्स (`scope=global`, `is_locked=false`) सीड करता है। |
| `guards` | `config('auth.guards')` से गार्ड्स को `clr_guards` में import करता है, `allowed_guard_drivers` द्वारा फ़िल्टर किया गया। |

सभी सेक्शन idempotent हैं — `clearance:backfill` को बार-बार चलाना सुरक्षित है और यह केवल वही भरता है जो missing है।

::: tip
पहले से मौजूद Spatie installs के लिए: यदि `roles` टेबल पहले से मौजूद है तो `clearance:install` कभी भी Spatie के migrations नहीं चलाता, इसलिए मौजूदा भूमिकाएं, अनुमतियाँ, और असाइनमेंट स्वचालित रूप से सुरक्षित रहते हैं।
:::

## यह भी देखें

- [Services](/hi/api/services.md) — `GuardService` और `MetaService`, जो `guards` और `meta` backfill सेक्शन को समर्थन देते हैं।
- [Migration गाइड](/hi/guide/migration.md) — किसी मौजूदा Spatie Permission installation को Clearance में माइग्रेट करना।
