# دليل إنشاء وتكامل الإضافات 

## مقدمة
يهدف هذا الدليل إلى تقديم شرح تفصيلي حول كيفية إنشاء إضافة جديدة وتكاملها مع المشروع، خصوصًا إضافات طرق الدفع. كما يوضح كيفية ظهورها تلقائيًا ضمن قائمة طرق الدفع في لوحة التحكم.

---

## إنشاء إضافة جديدة

### الخطوات الأساسية
1. **إنشاء ملف الإضافة**:
   - قم بإنشاء مجلد جديد داخل المسار `extensions/` (مثلاً: `extensions/YourAddonName`).
   - يحتوي المجلد على الملفات التالية:
     - ملف رئيسي (مثلاً: `YourAddonName.php`) لتعريف الإضافة.
     - ملفات الموارد والبنية (مثل: `views`، `assets`، و`config`).

2. **تعريف الإضافة**:
   - في الملف الرئيسي للإضافة، قم بتعريف الكود الأساسي للإضافة.
   - مثال:
     ```php
     namespace App\Extensions\YourAddonName;

     class YourAddonName {
         public function register() {
             // كود تسجيل الإضافة
         }
     }
     ```

3. **إضافة إعدادات الإضافة**:
   - أضف ملف إعدادات في المسار `config/extensions/YourAddonName.php` يحتوي على الإعدادات الافتراضية.

4. **تحميل الإضافة تلقائيًا**:
   - تأكد من تعديل ملف التحميل التلقائي للإضافات، مثل `extensions_loader.php`، لتضمين الإضافة الجديدة.

---

## تكامل الإضافة مع لوحة التحكم

### الخطوات
1. **إضافة الإعدادات إلى لوحة التحكم**:
   - قم بتحديث الملفات الموجودة داخل `resources/views/admin/` لإضافة إعدادات الإضافة.
   - مثال: تعديل الملف `resources/views/admin/plugin.php`.

2. **تسجيل الإضافة في قاعدة البيانات**:
   - أضف سجلًا جديدًا للإضافة في جدول الإضافات (على سبيل المثال: `addons`).

3. **عرض الإضافة في لوحة التحكم**:
   - قم بتحديث الواجهة باستخدام Blade (Laravel). مثال:
     ```php
     <div class="card">
         <div class="card-body">
             <h5 class="card-title">YourAddonName</h5>
             <p class="card-text">وصف الإضافة.</p>
         </div>
     </div>
     ```

---

## تكامل طرق الدفع

### الخطوات
1. **تعريف إضافة طريقة الدفع**:
   - قم بإنشاء ملف جديد داخل المسار `extensions/payment/YourPaymentMethod`.
   - استخدم ملفًا رئيسيًا لتعريف طريقة الدفع (مثلاً: `YourPaymentMethod.php`).

2. **إظهار طريقة الدفع في لوحة التحكم**:
   - قم بتعديل الملف `resources/views/admin/plugin.php` لإضافة طريقة الدفع الجديدة إلى القائمة.

3. **إضافة إعدادات طريقة الدفع**:
   - أضف إعدادات طريقة الدفع في لوحة التحكم ضمن قسم طرق الدفع.

4. **ربط طريقة الدفع بواجهة المستخدم**:
   - أضف الكود التالي في واجهة المستخدم:
     ```php
     <option value="YourPaymentMethod">Your Payment Method</option>
     ```

---

## أمثلة من المشروع

### مثال على الإضافات الحالية
- **Wallet**:
  - الملفات: `extensions/payment/Wallet/Wallet.php`.
  - إعدادات طريقة الدفع: تم تعريفها في الملف `resources/views/admin/plugin.php`.
