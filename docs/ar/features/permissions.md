# الصلاحيات

لوحة الصلاحيات هي واجهة CRUD كاملة فوق نموذج `Permission` الخاص بـ Spatie. لا تُقدّم Clearance أبداً جدول صلاحيات خاصاً بها - كل صلاحية تُنشئها هنا هي صلاحية Spatie عادية، قابلة للاستخدام في أي مكان تُستخدم فيه واجهة Spatie (`$user->can()`، `Gate::allows()`، السياسات (policies)، `@can`).

## ما تقوم به اللوحة

- **إنشاء** - إضافة صلاحية جديدة، يتم التحقق منها وفق [اصطلاح التسمية](#permission-naming-convention) (ما لم يكن معطّلاً) ومقيّدة بحارس.
- **تعديل** - إعادة تسمية صلاحية أو تغيير حارسها.
- **حذف** - إزالة صلاحية وتعييناتها الخاصة بالأدوار/المستخدمين (سلوك التسلسل القياسي لـ Spatie).

<Screenshot src="/screenshots/permissions-page.png" alt="لوحة صلاحيات Clearance تعرض قائمة الصلاحيات مجمّعة حسب بادئة التسمية" caption="تعرض لوحة الصلاحيات كل صلاحية في Spatie، مجمّعة حسب بادئة التسمية." />

<Screenshot src="/screenshots/permission-form-modal.png" alt="نموذج إنشاء/تعديل الصلاحية مع التحقق من اصطلاح التسمية" caption="يتحقق نموذج الصلاحية من اصطلاح التسمية بادئة-إجراء قبل الحفظ." />

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

بادئة المجموعة (`orders` في `orders-create`) هي ما تستخدمه [لوحة القيادة](/ar/features/dashboard) لحساب مجموعات الصلاحيات المميّزة.

::: tip تعطيل الفرض
تنتقل بعض الفرق من مخطط تسمية مختلف. عطّل الفحص حسب البيئة:

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## التحكم بالوصول

يتطلب الوصول للقراءة إلى اللوحة (عرض قائمة الصلاحيات) صلاحية `clearance-access`. يتطلب إنشاء أو تعديل أو حذف صلاحية بالإضافة إلى ذلك صلاحية `clearance-permissions-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning صلاحيات `clearance-*` محجوزة
لا يمكن إنشاء صلاحيات `clearance-*` نفسها (`clearance-access`، `clearance-permissions-write`، إلخ) أو إعادة تسميتها أو حذفها عبر هذه اللوحة - حتى بواسطة ممثل (actor) من نوع `super_admin`. تُدار حصرياً عبر تدفق التثبيت الخاص بالحزمة نفسها. انظر [المشرف الفائق](/ar/features/super-admin#reserved-clearance-permissions).
:::

## المسار والمكوّن المُضمَّن

| URI | اسم المسار | الوصف |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | CRUD الصلاحيات مع التحقق من تسمية البادئة-المجموعة |

```blade
<livewire:clearance::permissions.permission-manager />
```

## الخطوات التالية

- [الأدوار](/ar/features/roles) - تعيين صلاحيات لدور، مقيّدة بالحارس.
- [المشرف الفائق](/ar/features/super-admin) - جدول صلاحيات الكتابة الدقيق التحكم الكامل.
- [مرجع الإعدادات](/ar/guide/configuration) - `enforce_naming_convention`، `naming_separator`.
