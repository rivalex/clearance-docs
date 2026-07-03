# पब्लिशिंग

Clearance `vendor:publish` tags के माध्यम से तीन प्रकार के एसेट publish करता है: config, migrations, और translations। Config और migrations installer द्वारा स्वचालित रूप से संभाले जाते हैं (देखें [इंस्टॉलेशन](/hi/guide/installation)); यह पेज इन्हें मैन्युअल रूप से publish करने, और translations को publish करने को कवर करता है।

## Config

```bash
php artisan vendor:publish --tag=clearance-config
```

`config/clearance.php` को आपके एप्लिकेशन में publish करता है। हर key, उसके डिफ़ॉल्ट, और उसके उद्देश्य के लिए देखें [कॉन्फ़िगरेशन संदर्भ](/hi/guide/configuration)।

## Translations

```bash
php artisan vendor:publish --tag=clearance-translations
```

यह 9 बंडल की गई भाषाओं (`ar`, `en`, `es`, `fr`, `hi`, `it`, `pt`, `ru`, `zh`) को आपके ऐप में `lang/vendor/clearance/{locale}/ui.php` में कॉपी करता है। किसी नई locale को जोड़ने या किसी मौजूदा locale में strings को override करने के लिए, वहां एक locale directory जोड़ें, canonical key संदर्भ के रूप में `en/ui.php` का उपयोग करते हुए। सभी locales एक समान key सेट expose करती हैं, जिसे हर बदलाव पर एक समर्पित टेस्ट द्वारा verify किया जाता है।

## पैनल Layout को override करना

`layout` config key यह नियंत्रित करता है कि पूरे पेज वाले components किस Blade layout का उपयोग करते हैं:

```php
// config/clearance.php
'layout' => null, // host app's default (config('livewire.layout'))
```

अपने एप्लिकेशन के डिफ़ॉल्ट Livewire layout का उपयोग करने के लिए इसे `null` छोड़ दें, या पैनल को किसी कस्टम shell के भीतर रेंडर करने के लिए इसे किसी विशिष्ट layout view पर सेट करें।

## Migrations

Clearance के migrations `php artisan clearance:install` द्वारा स्वचालित रूप से publish और run किए जाते हैं - यदि `roles` टेबल अभी तक मौजूद नहीं है तो installer पहले Spatie के migrations को भी publish और run करता है। पूरे चरण-दर-चरण फ्लो के लिए देखें [इंस्टॉलेशन](/hi/guide/installation), और किसी नए (fresh) सेटअप पर कभी सादा `migrate` न चलाने के बारे में [danger callout](/hi/guide/installation#run-the-installer) देखें।

## संबंधित

- [कॉन्फ़िगरेशन संदर्भ](/hi/guide/configuration) - हर config key
- [इंस्टॉलेशन](/hi/guide/installation) - पूरा installer फ्लो
- [माइग्रेशन](/hi/guide/migration) - मौजूदा Spatie installs के लिए `clearance:backfill`
