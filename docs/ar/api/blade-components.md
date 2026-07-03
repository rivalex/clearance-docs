# مكوّنات Blade

تُسجّل Clearance مجموعة صغيرة من مكوّنات Blade تحت مساحة الأسماء `clearance::`. جميعها قابلة لإعادة الاستخدام مباشرة في عروض تطبيقك الخاصة، وليس فقط داخل اللوحة.

## المكوّنات

### `x-clearance::message`

عرض رسالة فورية/حالة (flash/status).

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

زر/غلاف نسخ إلى الحافظة — يُستخدم، مثلاً، لعرض أسماء المسارات (routes) أو المعرّفات (IDs) مع إجراء نسخ بنقرة واحدة.

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

عارض قياسي لقائمة أخطاء التحقق (validation) في Laravel.

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

مجموعة أيقونات صغيرة مرفقة، تُستخدم في جميع أنحاء اللوحة. لا تعتمد على أي مكتبة JS خارجية بخلاف Alpine، التي يتطلبها Livewire أصلاً.

```blade
<x-clearance::icon.check />
```

## مكوّنات Livewire

للرجوع إليها، هذه هي أسماء مكوّنات Livewire ذات الصفحة الكاملة/القابلة للتضمين المُسجَّلة بواسطة الحزمة، القابلة للاستخدام مع تضمين `<livewire:clearance::...>` في عروضك الخاصة:

| اسم المكوّن | الوصف |
|---|---|
| `dashboard` | لوحة القيادة الخاصة بالوحة. |
| `permissions.permission-manager` | لوحة CRUD الصلاحيات. |
| `roles.role-manager` | لوحة CRUD الأدوار، بما في ذلك الأدوار السقفية. |
| `guards.guard-manager` | لوحة CRUD الحراس. |
| `settings.settings-manager` | لوحة الإعدادات (بيانات العرض الوصفية، الأيقونات، الألوان). |
| `users.user-clearance-manager` | لوحة تعيين الأدوار/الصلاحيات لكل مستخدم. تأخذ `:userId`. |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

انظر صفحات الميزات المقابلة لكل لوحة: [لوحة القيادة](/ar/features/dashboard.md)، [الصلاحيات](/ar/features/permissions.md)، [الأدوار](/ar/features/roles.md)، [الحراس](/ar/features/guards.md)، [الإعدادات](/ar/features/settings.md)، [المستخدمون](/ar/features/users.md).

::: tip
يتحكم مفتاح `config('clearance.ui.flux_pro')` فيما إذا كانت عناصر Flux UI المخصصة لـ Pro تُعرض في هذه المكوّنات. `null` (الافتراضي) يكتشف Flux Pro تلقائياً عبر `Flux::pro()`.
:::
