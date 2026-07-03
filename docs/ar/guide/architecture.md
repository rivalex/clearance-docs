# البنية المعمارية

## كيف تعمل عملية التحليل (Resolution)

`resolveFor()` هي جوهر التفويض السياقي في Clearance - انظر [التفويض السياقي](/ar/features/contextual-authorization) للاطلاع على واجهة الـ API الكاملة. تدمج ثلاثة مصادر، بترتيب الأولوية التالي:

1. **منح الأدوار السياقية** - الصلاحيات من الأدوار التي يحملها المستخدم تحديداً ضمن هذا السياق (`clr_role_ctx`).
2. **منح الأدوار العامة** - الصلاحيات من أدوار Spatie المُعيَّنة عالمياً للمستخدم (بحيث يُلبّي `super_admin` وأدوار عامة أخرى شرط `canIn()` لأي سياق).
3. **التجاوزات الخاصة بكل سياق** (`clr_ctx_overrides`) - `forced_on` تضيف صلاحية فوق ما سبق؛ `forced_off` تُزيلها، حتى لو كانت الصلاحية ممنوحة من دور عام. الرفض يفوز دائماً.

::: warning الرفض يفوز دائماً
تتجاوز `forced_off` أي صلاحية ممنوحة من دور عام أو من منح دور سياقي. لا توجد وسيلة "لتخطّي" تجاوز `forced_off` لنفس المستخدم/الصلاحية/السياق سوى إزالته.
:::

## قاعدة البيانات

تمتلك Clearance 6 جداول ولا تُعدّل أبداً جداول Spatie الأساسية (`roles`، `permissions`، `model_has_roles`، `model_has_permissions`، `role_has_permissions`). جميع المفاتيح الخارجية (FKs) المشيرة إلى `roles.id`/`permissions.id` تتسلسل عند الحذف (cascade on delete) ما لم يُذكر خلاف ذلك؛ ولا تحتوي جداول Spatie على أي مفاتيح خارجية تشير مرة أخرى إلى `clr_*`.

| الجدول | الأعمدة الأساسية |
|---|---|
| `clr_meta` | `subject_type`، `subject_key` (فريد معاً) - `display_name`، `description`، `icon_svg`، `color` - بيانات عرض وصفية لدور أو حارس |
| `clr_role_meta` | `role_id` (فريد، FK -> `roles`) - `is_locked`، `scope` (`global`\|`contextual`)، `context_types` (json) - `parent_role_id` (FK اختياري -> `roles`، `ON DELETE SET NULL`) - الدور السقفي |
| `clr_guards` | `name` (فريد)، `driver`، `provider` - الحراس المُدارة عبر اللوحة |
| `clr_settings` | `key` (فريد)، `value` - مخزن مفتاح/قيمة وقت التشغيل (الدور الافتراضي، إلخ) |
| `clr_role_ctx` | `user_id`، `role_id` (FK -> `roles`)، `context_type`، `context_id` - تعيينات أدوار سياقية، فريدة لكل `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`، `role_id` (FK -> `roles`)، `permission_id` (FK -> `permissions`)، `context_type`، `context_id`، `type` (`forced_on`\|`forced_off`) - تجاوزات صلاحيات خاصة بكل مستخدم وكل سياق |

## لا تمسّ جداول Spatie أبداً

صُمّمت Clearance لتعمل جنباً إلى جنب مع `spatie/laravel-permission`، لا لتحل محلها. كل سجل `Role` و`Permission` تُنشئه أو تُعدّله لوحة Clearance هو نموذج Spatie حقيقي، مخزّن في جداول Spatie الخاصة بها. لا تُشغّل Clearance أبداً ترحيلاً (migration) يضيف عموداً إلى، أو يكتب مفتاحاً خارجياً من، أي من `roles`، أو `permissions`، أو `model_has_roles`، أو `model_has_permissions`، أو `role_has_permissions`.

جميع بيانات Clearance الخاصة - بيانات العرض الوصفية، ونطاق الأدوار وأسقفها، والتعيينات السياقية، والتجاوزات الخاصة بكل سياق، وتعريفات الحراس، والإعدادات - تعيش حصرياً في جداول `clr_*` الستة أعلاه. هذا يعني:

- إلغاء تثبيت Clearance لا يُعرّض أبداً بيانات الأدوار/الصلاحيات الموجودة في Spatie للخطر.
- أي كود يقرأ بالفعل نماذج أو جداول Spatie مباشرة يستمر في العمل دون تعديل.
- ميزات Clearance السياقية والسقفية هي طبقات إضافية بحتة تُحلَّل وقت الاستعلام (عبر `resolveFor()` و`RoleService`)، وليست مدمجة في مخطط (schema) Spatie.

## ذو صلة

- [التفويض السياقي](/ar/features/contextual-authorization) - `canIn()`، `hasRoleIn()`، توجيهات Blade، `ContextService`
- [الأدوار](/ar/features/roles) - نطاق الأدوار وأسقفها، `RoleService::setParent()`
- [الترحيل](/ar/guide/migration) - ملء `clr_meta`/`clr_role_meta`/`clr_guards` على تثبيت Spatie موجود مسبقاً
- [الأمان](/ar/guide/security) - كيف يمنع نموذج السقف والتجاوز تصعيد الصلاحيات
