# الإعداد (Configuration)

تأتي Clearance مع قيم افتراضية منطقية، لذا تعمل مباشرة بعد [التثبيت](/ar/guide/installation). انشر ملف الإعداد عندما تحتاج إلى تخصيص بادئة المسار (route prefix)، أو الـ middleware، أو النماذج السياقية، أو أي إعداد آخر.

```bash
php artisan vendor:publish --tag=clearance-config
```

هذا ينشر `config/clearance.php`.

## مرجع الإعدادات

هذا هو المرجع النهائي لكل مفتاح في `config/clearance.php`. الصفحات الأخرى في هذه الوثائق تُحيل إلى هنا بدلاً من تكرار الجدول.

| المفتاح | القيمة الافتراضية | الغرض |
|---|---|---|
| `route_prefix` | `'clearance'` | بادئة الـ URI لجميع مسارات اللوحة. |
| `middleware` | `['web', 'auth']` | تُطبَّق على جميع مسارات اللوحة قبل `clearance.access`. |
| `access_permission` | `'clearance-access'` | الصلاحية التي يتحقق منها middleware الوصول. |
| `user_model` | `null` | تُستخدم `auth.providers.users.model` افتراضياً. |
| `modules.users` | `false` | المفتاح الرئيسي لوحدة المستخدمين (المسارات + مكوّنات Livewire). |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']` - النماذج التي يمكن أن تعمل كسياقات مرتبطة بالأدوار. |
| `enforce_naming_convention` | `true` | فرض تسمية الصلاحيات بصيغة `group-action`. |
| `naming_separator` | `'-'` | حرف الفصل - `'-'` أو `'_'` فقط. |
| `guards` | `[]` | تجاوز الحراس المكتشَفين تلقائياً من `config/auth.php`. فارغ = اكتشاف الكل تلقائياً. |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | المحركات (drivers) المقبولة عند حقن حراس قاعدة البيانات في `auth.guards`. |
| `layout` | `null` | تخطيط Blade للمكوّنات ذات الصفحة الكاملة. `null` = الافتراضي الخاص بالتطبيق المضيف (`config('livewire.layout')`). |
| `auto_assign_default_role` | `false` | تعيين الدور الافتراضي المُهيّأ في الإعدادات تلقائياً عند حدث `Registered`. |
| `super_admin_gate_bypass` | `false` | اختياري: يتجاوز `super_admin` جميع فحوصات Gate على مستوى التطبيق بأكمله. |
| `ui.flux_pro` | `null` | `null` = اكتشاف تلقائي عبر `Flux::pro()`. |

## اصطلاح تسمية الصلاحيات

يجب أن تتبع الصلاحيات صيغة `group-action`:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

عطّل الفرض حسب البيئة عبر `enforce_naming_convention`:

```php
'enforce_naming_convention' => false,
```

::: warning يتم التحقق من الفاصل عند الإقلاع
قيمة `naming_separator` هي الجزء الوحيد من هذا الإعداد الذي يصل إلى جزء SQL خام (تجميع/فرز الصلاحيات)، لذا لا يُقبل سوى `-` أو `_`.
:::

## تخصيص الـ Middleware

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

تبقى أسماء المسارات (route names) ثابتة بغض النظر عن تغييرات البادئة أو الـ middleware - انظر [البدء السريع](/ar/guide/quick-start) للاطلاع على جدول المسارات الكامل.

## ذو صلة

- [البدء السريع](/ar/guide/quick-start) - تركيب اللوحة مع تطبيق هذه الإعدادات
- [نشر الأصول](/ar/guide/publishing) - نشر الترجمات وفهم مفتاح `layout`
- [الحراس](/ar/features/guards) - كيفية استخدام `guards` و`allowed_guard_drivers`
- [المشرف الفائق](/ar/features/super-admin) - كيف يعمل `super_admin_gate_bypass`
