# किसी मौजूदा Spatie install से माइग्रेट करना

यदि आपका एप्लिकेशन पहले से `spatie/laravel-permission` का उपयोग मौजूदा भूमिकाओं, अनुमतियों और असाइनमेंट के साथ करता है, तो `clearance:install` इन्हें पहचानता है और सुरक्षित रखता है - यदि `roles` टेबल पहले से मौजूद है तो यह कभी भी Spatie के migrations नहीं चलाता। पूरे install फ्लो के लिए देखें [इंस्टॉलेशन](/hi/guide/installation)।

एक बार इंस्टॉल हो जाने पर, अपनी मौजूदा भूमिकाओं और गार्ड्स पर Clearance के अपने मेटाडेटा को अपनाने के लिए `clearance:backfill` का उपयोग करें।

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## हर सेक्शन क्या करता है

| सेक्शन | प्रभाव |
|---|---|
| `meta` | बिना meta वाली हर भूमिका के लिए `clr_meta` डिस्प्ले नाम (title-cased भूमिका नाम) सीड करता है। |
| `roles` | बिना meta वाली हर भूमिका के लिए `clr_role_meta` डिफ़ॉल्ट्स (`scope=global`, `is_locked=false`) सीड करता है। |
| `guards` | `config('auth.guards')` से गार्ड्स को `clr_guards` में import करता है, `allowed_guard_drivers` द्वारा फ़िल्टर किया गया। |

केवल एक सेक्शन चलाने के लिए, तीनों के बजाय `--only=<section>` का उपयोग करें।

::: tip Idempotent
सभी सेक्शन idempotent हैं - `clearance:backfill` को दोबारा चलाना सुरक्षित है और यह मौजूदा `clr_*` रो को डुप्लिकेट या ओवरराइट नहीं करेगा।
:::

## Edge cases

पैकेज रिपॉज़िटरी में एक अधिक विस्तृत माइग्रेशन गाइड है जो यहां न बताए गए edge cases को कवर करती है - विशेष रूप से तब क्या होता है जब आपके डेटाबेस में पहले से `super_admin` (या किसी alias जैसे `superadmin`, `root`, `owner`) नाम की एक भूमिका मौजूद हो जो Clearance की अपनी `super_admin` भूमिका से टकराती है, साथ ही कस्टम `Role`/`Permission` मॉडल subclasses और गार्ड import व्यवहार पर नोट्स। किसी production डेटाबेस को गैर-तुच्छ मौजूदा भूमिका डेटा के साथ backfill करने से पहले इसे पढ़ें। `clearance:install` के दौरान alias detection व्यवहार के लिए [सुपर एडमिन](/hi/features/super-admin) भी देखें।

## संबंधित

- [इंस्टॉलेशन](/hi/guide/installation) - पूरा `clearance:install` फ्लो
- [Artisan कमांड](/hi/api/artisan-commands) - पूरा कमांड संदर्भ
- [आर्किटेक्चर](/hi/guide/architecture) - वे `clr_*` टेबल्स जिन्हें backfill भरता है
