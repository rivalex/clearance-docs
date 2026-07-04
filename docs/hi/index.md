---
layout: home

hero:
  name: "CLEARANCE"
  text: "Laravel के लिए अनुमतियाँ, भूमिकाएं और प्रासंगिक प्राधिकरण"
  tagline: spatie/laravel-permission के ऊपर एक drop-in Livewire 4 + Flux UI एडमिन पैनल। प्रासंगिक प्राधिकरण, ceiling भूमिकाएं, सूक्ष्म-स्तरीय delegation - एक artisan कमांड से इंस्टॉल करने योग्य, Spatie की टेबल्स में शून्य बदलाव के साथ।
  image:
    light: /clearance-mark.svg
    dark: /clearance-mark.svg
    alt: Clearance
  actions:
    - theme: brand
      text: शुरू करें
      link: /hi/guide/introduction
    - theme: alt
      text: GitHub पर देखें
      link: https://github.com/rivalex/clearance

features:
  - icon: 🖥️
    title: पूर्ण एडमिन पैनल
    details: अनुमतियाँ, भूमिकाएं, गार्ड, सेटिंग्स और एक डैशबोर्ड - पहले से बने रूट्स का उपयोग करें या अपने ऐप में कहीं भी प्रत्येक Livewire कंपोनेंट को अलग-अलग एम्बेड करें।

  - icon: 🎯
    title: प्रासंगिक प्राधिकरण
    details: "किसी विशिष्ट मॉडल इंस्टेंस - एक Store, एक Tenant, एक Project - तक सीमित भूमिका या अनुमति प्रदान करें, $user->canIn(), @canin/@hasrolein Blade directives, और एक ContextService के साथ।"

  - icon: 🏔️
    title: सीलिंग भूमिकाएं
    details: एक भूमिका एक पैरेंट भूमिका घोषित कर सकती है जिसका अनुमति सेट एक ऊपरी सीमा के रूप में कार्य करता है। एक चाइल्ड भूमिका अपने पैरेंट से कभी अधिक नहीं हो सकती, यह हर सेव पर स्वचालित रूप से लागू होता है।

  - icon: 🔑
    title: सूक्ष्म-स्तरीय write अनुमतियाँ
    details: प्रत्येक पैनल सेक्शन के लिए एक सौंपी जा सकने वाली clearance-{section}-write अनुमति, ताकि आप "manage roles" की अनुमति दे सकें बिना "manage settings" की अनुमति दिए।

  - icon: 👑
    title: सुपर एडमिन, वैकल्पिक
    details: एक वैकल्पिक ग्लोबल Gate bypass, मौजूदा एडमिन-जैसी भूमिकाओं के लिए alias डिटेक्शन, और किसी मौजूदा भूमिका का सुरक्षित non-destructive प्रमोशन।

  - icon: 🗄️
    title: Spatie की टेबल्स को कभी नहीं छूता
    details: Clearance के पास 6 समर्पित clr_* टेबल्स हैं और यह कभी भी roles, permissions, model_has_roles, model_has_permissions या role_has_permissions में बदलाव नहीं करता।

  - icon: 🛡️
    title: सुरक्षा-सुदृढ़
    details: हर अनुमति-प्रदान करने वाले पथ पर privilege-escalation सीलिंग, deny-प्राथमिकता वाले संदर्भ ओवरराइड, एक allow-list SVG sanitizer, और एक सुरक्षित clearance-* नेमस्पेस।

  - icon: 👥
    title: Users मॉड्यूल (वैकल्पिक)
    details: एक प्रति-उपयोगकर्ता पैनल से, भूमिकाएं वैश्विक रूप से या किसी विशिष्ट संदर्भ को असाइन करें, साथ ही प्रति-संदर्भ अनुमति ओवरराइड भी।

  - icon: 🌐
    title: 9 बंडल की गई भाषाएं
    details: "ar, en, es, fr, hi, it, pt, ru, zh - हर बदलाव पर key-parity टेस्ट किए गए।"

  - icon: 🧪
    title: 370 टेस्ट, 1054 assertions
    details: एक Pest टेस्ट सूट जो commands, services, Livewire कॉम्पोनेंट्स, contextual authorization, ceiling roles, और सभी 9 भाषाओं में i18n key-parity को कवर करता है।
---
