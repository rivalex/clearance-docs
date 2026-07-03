# Blade कंपोनेंट्स

Clearance `clearance::` नेमस्पेस के तहत Blade components का एक छोटा सेट रजिस्टर करता है। सभी सीधे आपके अपने एप्लिकेशन views में फिर से उपयोग करने योग्य हैं, न कि केवल पैनल के भीतर।

## Components

### `x-clearance::message`

Flash/स्टेटस मैसेज डिस्प्ले।

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

Copy-to-clipboard बटन/wrapper — उदाहरण के लिए, route names या IDs को एक-क्लिक copy एक्शन के साथ दिखाने के लिए उपयोग किया जाता है।

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

मानक Laravel validation error सूची renderer।

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

एक छोटा बंडल किया गया आइकन सेट, जो पूरे पैनल में उपयोग होता है। Alpine के अलावा कोई बाहरी JS निर्भरता नहीं, जिसकी Livewire को वैसे भी आवश्यकता होती है।

```blade
<x-clearance::icon.check />
```

## Livewire Components

संदर्भ के लिए, ये पैकेज द्वारा रजिस्टर किए गए पूरे पेज/एम्बेड करने योग्य Livewire component नामों की सूची है, जो आपके अपने views में `<livewire:clearance::...>` एम्बेडिंग के साथ उपयोग करने योग्य हैं:

| Component नाम | विवरण |
|---|---|
| `dashboard` | पैनल डैशबोर्ड। |
| `permissions.permission-manager` | Permissions CRUD पैनल। |
| `roles.role-manager` | सीलिंग भूमिकाओं सहित Roles CRUD पैनल। |
| `guards.guard-manager` | Guards CRUD पैनल। |
| `settings.settings-manager` | Settings पैनल (डिस्प्ले मेटाडेटा, आइकन, रंग)। |
| `users.user-clearance-manager` | प्रति-उपयोगकर्ता भूमिका/अनुमति असाइनमेंट पैनल। `:userId` लेता है। |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

हर पैनल के लिए संबंधित फीचर पेज देखें: [डैशबोर्ड](/hi/features/dashboard.md), [अनुमतियाँ](/hi/features/permissions.md), [भूमिकाएं](/hi/features/roles.md), [गार्ड](/hi/features/guards.md), [सेटिंग्स](/hi/features/settings.md), [उपयोगकर्ता](/hi/features/users.md)।

::: tip
`config('clearance.ui.flux_pro')` key यह नियंत्रित करता है कि इन components में Pro-only Flux UI elements रेंडर होते हैं या नहीं। `null` (डिफ़ॉल्ट) `Flux::pro()` के माध्यम से Flux Pro को auto-detect करता है।
:::
