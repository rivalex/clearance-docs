# البدء السريع

بعد [التثبيت](/ar/guide/installation)، لديك طريقتان لعرض لوحة Clearance: المسارات (routes) الجاهزة، أو تضمين مكوّنات Livewire الفردية في أي مكان من تطبيقك.

## الخيار أ - المسارات الجاهزة

بعد التثبيت، تكون اللوحة متاحة على `/clearance` (أو بادئة المسار `route_prefix` المُهيّأة، انظر [الإعداد](/ar/guide/configuration)). جميع المسارات محمية بـ middleware من نوع `clearance.access` وتستخدم تخطيط Livewire الخاص بالتطبيق المضيف تلقائياً.

| URI | اسم المسار | الوصف |
|---|---|---|
| `/clearance` | `clearance.home` | لوحة القيادة |
| `/clearance/guards` | `clearance.guards` | إدارة الحراس (إنشاء/تعديل/حذف) |
| `/clearance/permissions` | `clearance.permissions` | CRUD الصلاحيات مع التحقق من تسمية البادئة-المجموعة |
| `/clearance/roles` | `clearance.roles` | CRUD الأدوار مع تعيين صلاحيات مقيّد بالحارس، والنطاق (عام/سياقي)، والسقف |
| `/clearance/settings` | `clearance.settings` | بيانات العرض الوصفية (الأيقونات، الألوان)، الدور الافتراضي، الإعدادات العامة |
| `/clearance/user/{userId}` | `clearance.user` | تعيين أدوار لكل مستخدم + تجاوزات صلاحيات سياقية (يتطلب `modules.users = true`) |

تبقى أسماء المسارات ثابتة بغض النظر عن تغييرات البادئة. لتخصيص الـ middleware:

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## الخيار ب - مكوّنات Livewire مُضمَّنة

يمكن تركيب كل قسم من أقسام اللوحة على حدة داخل أي عرض Blade، أو صفحة Filament، أو مسار مخصص:

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## إلى أين تذهب الآن

- [مرجع الإعدادات](/ar/guide/configuration) - كل مفتاح إعداد، وقيمته الافتراضية وغرضه
- [التفويض السياقي](/ar/features/contextual-authorization) - تقييد الأدوار والصلاحيات بمتجر أو مستأجر أو مشروع
- [الأدوار](/ar/features/roles) - نطاق الأدوار وأسقفها
- [لوحة القيادة](/ar/features/dashboard) - ما يعرضه مكوّن لوحة القيادة
- [المستخدمون](/ar/features/users) - اللوحة الاختيارية الخاصة بكل مستخدم
