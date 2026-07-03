# आर्किटेक्चर

## Resolution कैसे काम करता है

`resolveFor()`, Clearance के प्रासंगिक प्राधिकरण का मूल है - पूरे API के लिए देखें [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization)। यह तीन स्रोतों को इस प्राथमिकता क्रम में मिलाता है:

1. **प्रासंगिक भूमिका अनुदान** - उन भूमिकाओं से अनुमतियाँ जो उपयोगकर्ता विशेष रूप से इस संदर्भ के भीतर रखता है (`clr_role_ctx`)।
2. **वैश्विक भूमिका अनुदान** - उपयोगकर्ता की globally-असाइन की गई Spatie भूमिकाओं से अनुमतियाँ (ताकि `super_admin` और अन्य वैश्विक भूमिकाएं किसी भी संदर्भ के लिए `canIn()` को संतुष्ट करें)।
3. **प्रति-संदर्भ ओवरराइड** (`clr_ctx_overrides`) - `forced_on` उपरोक्त के ऊपर एक अनुमति जोड़ता है; `forced_off` इसे हटा देता है, भले ही वह अनुमति किसी वैश्विक भूमिका द्वारा दी गई हो। एक deny हमेशा जीतती है।

::: warning एक deny हमेशा जीतता है
`forced_off`, किसी वैश्विक भूमिका या प्रासंगिक भूमिका अनुदान द्वारा दी गई किसी भी अनुमति को override कर देता है। उसी user/permission/context के लिए `forced_off` ओवरराइड को हटाए बिना उसे "out-rank" करने का कोई तरीका नहीं है।
:::

## डेटाबेस

Clearance 6 टेबल्स का स्वामी है और कभी भी Spatie की मूल टेबल्स (`roles`, `permissions`, `model_has_roles`, `model_has_permissions`, `role_has_permissions`) में बदलाव नहीं करता। `roles.id`/`permissions.id` को संदर्भित करने वाले सभी FK, जब तक अन्यथा न बताया जाए, delete पर cascade करते हैं; Spatie टेबल्स में `clr_*` की ओर इशारा करने वाला कोई FK नहीं है।

| टेबल | मुख्य कॉलम |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (एक साथ unique) - `display_name`, `description`, `icon_svg`, `color` - किसी भूमिका या गार्ड के लिए डिस्प्ले मेटाडेटा |
| `clr_role_meta` | `role_id` (unique, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (nullable FK -> `roles`, `ON DELETE SET NULL`) - सीलिंग भूमिका |
| `clr_guards` | `name` (unique), `driver`, `provider` - पैनल के माध्यम से प्रबंधित गार्ड |
| `clr_settings` | `key` (unique), `value` - runtime key/value स्टोर (डिफ़ॉल्ट भूमिका, आदि) |
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - प्रासंगिक भूमिका असाइनमेंट, `(user, role, context_type, context_id)` के अनुसार unique |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) - प्रति-उपयोगकर्ता, प्रति-संदर्भ अनुमति ओवरराइड |

## Spatie की टेबल्स को कभी नहीं छूता

Clearance को `spatie/laravel-permission` को प्रतिस्थापित करने के बजाय उसके साथ रहने के लिए डिज़ाइन किया गया है। हर `Role` और `Permission` रिकॉर्ड जो एक Clearance पैनल बनाता या संपादित करता है, वह एक असली Spatie मॉडल है, जो Spatie की अपनी टेबल्स में संग्रहीत है। Clearance कभी भी ऐसा migration नहीं चलाता जो `roles`, `permissions`, `model_has_roles`, `model_has_permissions`, या `role_has_permissions` में से किसी में कॉलम जोड़े, या इनमें से किसी से FK लिखे।

Clearance का अपना सारा डेटा - डिस्प्ले मेटाडेटा, भूमिका दायरा और सीलिंग, प्रासंगिक असाइनमेंट, प्रति-संदर्भ ओवरराइड, गार्ड परिभाषाएं, और सेटिंग्स - विशेष रूप से ऊपर दी गई 6 `clr_*` टेबल्स में रहता है। इसका मतलब है:

- Clearance को uninstall करना आपके मौजूदा Spatie भूमिका/अनुमति डेटा को कभी corrupt होने का जोखिम नहीं देता।
- कोई भी कोड जो पहले से Spatie के मॉडल या टेबल्स को सीधे पढ़ता है, बिना किसी बदलाव के काम करता रहता है।
- Clearance की प्रासंगिक और सीलिंग सुविधाएं विशुद्ध रूप से additive परतें हैं जो query के समय resolve होती हैं (`resolveFor()` और `RoleService` के माध्यम से), Spatie के schema में built-in नहीं हैं।

## संबंधित

- [प्रासंगिक प्राधिकरण](/hi/features/contextual-authorization) - `canIn()`, `hasRoleIn()`, Blade directives, `ContextService`
- [भूमिकाएं](/hi/features/roles) - दायरा और सीलिंग भूमिकाएं, `RoleService::setParent()`
- [माइग्रेशन](/hi/guide/migration) - किसी मौजूदा Spatie install पर `clr_meta`/`clr_role_meta`/`clr_guards` को backfill करना
- [सुरक्षा](/hi/guide/security) - सीलिंग और ओवरराइड मॉडल privilege escalation को कैसे रोकता है
