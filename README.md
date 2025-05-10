# دليل إعداد وإدارة طرق الدفع كموديولات مستقلة في المشروع

## مقدمة
يدعم هذا المشروع نظامًا مرنًا لإدارة طرق الدفع على هيئة إضافات (Modules). يمكن إضافة طريقة دفع جديدة بسهولة مع إمكانية تفعيلها أو تعطيلها من خلال لوحة التحكم. يهدف هذا الدليل إلى شرح الخطوات العملية لإنشاء إضافة جديدة لطرق الدفع وإعدادها لتكون قابلة للتفعيل أو الإيقاف.

---

## المتطلبات الأساسية
1. **إعداد قاعدة البيانات**:
   - يحتوي جدول `Payment على الحقول التالية:
     - `id`: رقم تعريف فريد للإضافة.
     - `payment_name`: اسم الإضافة (مثل: PayPal).
     - `unique_identifier`: معرف فريد للإضافة (مثل: `paypal`).
     - `is_activate`: إذا كانت الإضافة مفعّلة (1) أو معطلة (0).
     - `is_available`: إذا كانت الإضافة متاحة (1) أو غير متاحة (0).
     - `public_key` و`secret_key`: المفاتيح الخاصة بعملية الدفع.
     - `currency`: العملة المدعومة.

2. **إنشاء هيكل الموديولات**:
   - يجب أن يتم إنشاء كل إضافة جديدة داخل المسار `app/Http/Controllers/addons`.
   - يتم استخدام ملفات مخصصة لكل إضافة مثل `PayPalController.php`.

3. **إعداد واجهة المستخدم**:
   - يجب توفير واجهة لإعداد وتفعيل/تعطيل الإضافة في لوحة التحكم.

---

## خطوات إنشاء إضافة جديدة

### الخطوة 1: إعداد قاعدة البيانات
1. **إضافة سجل جديد في جدول `Payment`**:
   - استخدم SQL أو نموذجًا لتحديث السجل. مثال:
     ```sql
     INSERT INTO Payment (payment_name, unique_identifier, is_activate, is_available, public_key, secret_key, currency)
     VALUES ('PayPal', 'paypal', 1, 1, 'your_public_key', 'your_secret_key', 'USD');
     ```

2. **تحديث الحقول حسب احتياجات الإضافة**:
   - تأكد من أن الحقول `is_activate` و`is_available` تحمل القيم الصحيحة.

---

### الخطوة 2: إنشاء ملف التحكم (Controller)
1. **إنشاء ملف جديد داخل مسار الإضافات**:
   - المسار: `app/Http/Controllers/addons/PayPalController.php`.

2. **إعداد الكود الأساسي للإضافة**:
   ```php name=app/Http/Controllers/addons/PayPalController.php
   <?php

   namespace App\Http\Controllers\addons;

   use Illuminate\Http\Request;

   class PayPalController
   {
       public function processPayment(Request $request)
       {
           // منطق معالجة الدفع
           $response = $this->makePayment($request->all());

           if ($response['status'] === 'success') {
               return redirect()->route('payment.success');
           }

           return redirect()->route('payment.fail');
       }

       private function makePayment($data)
       {
           // قم بإضافة منطق التكامل مع PayPal API هنا
           return ['status' => 'success', 'transaction_id' => '12345'];
       }
   }
   ```

---

### الخطوة 3: إعداد واجهة لوحة التحكم
1. **إنشاء ملف Blade جديد للإعدادات**:
   - المسار: `resources/views/admin/payment/paypal.blade.php`.

2. **إعداد نموذج الإدخال**:
   ````blade name=resources/views/admin/payment/paypal.blade.php
   <form action="{{ route('admin.payment.update', ['id' => $paymentId]) }}" method="POST">
       @csrf
       <div class="form-group">
           <label for="public_key">Public Key</label>
           <input type="text" name="public_key" class="form-control" value="{{ $payment->public_key }}" required>
       </div>
       <div class="form-group">
           <label for="secret_key">Secret Key</label>
           <input type="text" name="secret_key" class="form-control" value="{{ $payment->secret_key }}" required>
       </div>
       <button type="submit" class="btn btn-primary">Save</button>
   </form>
