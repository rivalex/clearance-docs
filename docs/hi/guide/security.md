# सुरक्षा

रिलीज़ से पहले Clearance एक समर्पित pre-publication सुरक्षा ऑडिट के साथ-साथ दो follow-up hardening पास से गुजरा। मुख्य बातें: हर अनुमति-प्रदान करने वाले पथ (प्रत्यक्ष अनुदान और role-level syncing दोनों) पर privilege-escalation सीलिंग, deny-प्राथमिकता वाले संदर्भ ओवरराइड, एक allow-list SVG sanitizer, एक सुरक्षित `clearance-*` अनुमति नेमस्पेस, और guest-safe Blade directives।

## पैनल एक्सेस और सूक्ष्म-स्तरीय write अनुमतियाँ

पूरे पैनल तक पढ़ने की एक्सेस के लिए `clearance-access` अनुमति आवश्यक है (`can()` के माध्यम से जांची गई, `hasRole()` से कभी नहीं)। प्रत्येक सेक्शन पर write ऑपरेशन एक समर्पित, सौंपी जा सकने वाली अनुमति द्वारा नियंत्रित होते हैं - सभी `clearance:install` द्वारा बनाई गई और `super_admin` को असाइन की गई:

| अनुमति | नियंत्रित करती है |
|---|---|
| `clearance-permissions-write` | अनुमतियों को बनाना / संपादित करना / हटाना |
| `clearance-roles-write` | भूमिकाओं और उनके अनुमति असाइनमेंट को बनाना / संपादित करना / हटाना |
| `clearance-guards-write` | गार्ड्स को बनाना / संपादित करना / हटाना |
| `clearance-settings-write` | डिस्प्ले मेटाडेटा, आइकन, रंग, डिफ़ॉल्ट भूमिका को संपादित करना |
| `clearance-users-write` | किसी विशिष्ट उपयोगकर्ता के लिए भूमिकाएं असाइन / हटाना |

यदि कोई सूक्ष्म-स्तरीय अनुमति row डेटाबेस में बिल्कुल मौजूद ही नहीं है (एक legacy pre-seeding स्थिति), तो जांच backwards compatibility के लिए `clearance-access` पर fall back हो जाती है।

## सुरक्षित `clearance-*` नेमस्पेस

`clearance-*` अनुमतियाँ आरक्षित हैं: इन्हें पैकेज के अपने install फ्लो के अलावा किसी भी तरह से बनाया, नाम-परिवर्तित, हटाया, या किसी भूमिका में जोड़ा नहीं जा सकता - यहां तक कि किसी `super_admin` एक्टर द्वारा भी नहीं, स्वयं `super_admin` भूमिका के लिए भी।

## Privilege-escalation सीलिंग

Clearance में हर अनुमति-प्रदान करने वाला पथ एक सीलिंग लागू करता है ताकि कोई एक्टर कभी भी उससे अधिक न दे सके जितना वह स्वयं रखता है:

- **सीलिंग भूमिकाएं** - एक भूमिका एक पैरेंट भूमिका घोषित कर सकती है जिसका अनुमति सेट एक ऊपरी सीमा के रूप में कार्य करता है। कोई भी अतिरिक्त अनुमति `RoleService::setParent()` पर और हर बाद के अनुमति sync पर चुपचाप trim कर दी जाती है - कोई exception नहीं, कोई error message नहीं। किसी पैरेंट से अनुमतियाँ हटाना स्वचालित रूप से हर चाइल्ड में यह हटाव cascade करता है। पूरे ceiling API के लिए देखें [भूमिकाएं](/hi/features/roles)।
- **Users मॉड्यूल** - भूमिका असाइनमेंट और direct/override अनुमति अनुदान दोनों एक privilege ceiling लागू करते हैं: कोई गैर-`super_admin` एक्टर अपने खुद के असाइनमेंट को संशोधित नहीं कर सकता, और ऐसी अनुमति नहीं दे सकता जो उसके पास स्वयं नहीं है। देखें [उपयोगकर्ता](/hi/features/users)।

## Deny-प्राथमिकता वाले संदर्भ ओवरराइड

प्रति-संदर्भ अनुमति ओवरराइड (`clr_ctx_overrides`) `forced_on` और `forced_off` का समर्थन करते हैं। `forced_off` हमेशा जीतता है - यहां तक कि किसी वैश्विक भूमिका द्वारा दी गई अनुमति पर भी। ऐसा कोई रास्ता नहीं है जिससे कम प्राथमिकता वाला अनुदान किसी स्पष्ट denial को override कर सके। पूरे resolution क्रम के लिए देखें [आर्किटेक्चर](/hi/guide/architecture#how-resolution-works)।

## SVG sanitization

हर उपयोगकर्ता-प्रदत्त आइकन (Settings पैनल में भूमिका/गार्ड डिस्प्ले मेटाडेटा) स्टोरेज से पहले और render के समय दोनों बार एक allow-list sanitizer से होकर गुजरता है - upload पर सिर्फ एक बार की जांच नहीं।

## Guest-safe Blade directives

`@canin` और `@hasrolein` दोनों किसी guest उपयोगकर्ता के लिए सुरक्षित रूप से resolve होते हैं (`false` return करते हैं) - ये कभी throw नहीं करते।

## Gate bypass एप्लिकेशन-व्यापी है

`super_admin_gate_bypass` (डिफ़ॉल्ट `false`) यह नियंत्रित करता है कि क्या `super_admin` भूमिका, `Gate::before()` hook के माध्यम से सभी Laravel `can()` / `Gate::allows()` / policy जांचों को globally bypass करती है।

::: warning पैनल तक सीमित नहीं
सक्षम होने पर, `super_admin` रखने वाला कोई भी उपयोगकर्ता एप्लिकेशन-व्यापी सभी Gate जांचों को स्किप कर देता है - न केवल Clearance पैनल की जांचों को। इसे सोच-समझकर सक्षम करें। alias डिटेक्शन और प्रमोशन व्यवहार के लिए [सुपर एडमिन](/hi/features/super-admin) देखें।
:::

## सुरक्षा-संबंधी सावधानियां

- `forced_off` हमेशा जीतता है - यहां तक कि किसी वैश्विक भूमिका द्वारा दी गई अनुमति पर भी।
- एक सीलिंग भूमिका केवल trim करती है, कभी अनुमति नहीं देती - एक permissive पैरेंट सेट करने से चाइल्ड को खुद-ब-खुद कुछ नहीं मिलता।
- Gate bypass (`super_admin_gate_bypass`) एप्लिकेशन-व्यापी है, पैनल तक सीमित नहीं।
- Users मॉड्यूल डिफ़ॉल्ट रूप से बंद है (`modules.users = false`)।
- `clearance-*` अनुमतियाँ install फ्लो के बाहर अछूती रहती हैं, यहां तक कि `super_admin` के लिए भी।
- किसी नए (fresh) सेटअप पर हमेशा `clearance:install` चलाएं, सादा `migrate` नहीं - देखें [इंस्टॉलेशन](/hi/guide/installation)।

## संबंधित

- [आर्किटेक्चर](/hi/guide/architecture) - resolution प्राथमिकता और `clr_*` schema
- [भूमिकाएं](/hi/features/roles) - सीलिंग भूमिकाओं का विस्तृत विवरण
- [उपयोगकर्ता](/hi/features/users) - भूमिका/अनुमति असाइनमेंट पर privilege ceiling
- [सुपर एडमिन](/hi/features/super-admin) - Gate bypass और alias detection
