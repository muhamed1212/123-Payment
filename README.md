# Rege Payment Integration

## مقدمة
هذا المشروع يدعم إضافة وإدارة طرق دفع متعددة لتلبية احتياجات المستخدمين. يمكنك من خلال هذا الدليل التعرف على كيفية إضافة طرق دفع جديدة، الشروط اللازمة لتفعيلها، وكيفية عرضها في الواجهة.

---

## المتطلبات الأساسية
1. **إعدادات قاعدة البيانات**:
   - يجب أن تحتوي قاعدة البيانات على جدول `Payment` يتضمن الحقول التالية:
     - `id`: رقم تعريف فريد.
     - `payment_name`: اسم طريقة الدفع.
     - `is_activate`: إذا كانت طريقة الدفع مفعّلة (1) أو غير مفعّلة (0).
     - `is_available`: إذا كانت طريقة الدفع متاحة (1) أو غير متاحة (0).
     - `public_key` و`secret_key`: المفاتيح اللازمة للتكامل.
     - `currency`: العملة المدعومة.

2. **إعداد بيئة العمل**:
   - يجب التأكد من أن بيئة العمل (Environment) تدعم جميع المتطلبات التقنية مثل المفاتيح العامة والخاصة.

---

## المسارات الهامة

### 1. **`PaymentController.php`**
   - الموقع: `app/Http/Controllers/admin/PaymentController.php`
   - الوظيفة: إدارة طرق الدفع من لوحة التحكم.
   - الأكواد الهامة:
     ```php
     $getpaymentmethods = Payment::select('id', 'payment_name', 'is_activate', 'is_available')
         ->where('is_available', 1)
         ->where('is_activate', 1)
         ->get();
     ```

### 2. **`CheckoutController.php`**
   - الموقع: `app/Http/Controllers/front/CheckoutController.php`
   - الوظيفة: إدارة عملية الدفع أثناء الشراء.
   - الأكواد الهامة:
     ```php
     if (Session::get('payment_type') == "13") {
         $checkstatus = app('App\\Http\\Controllers\\addons\\MollieController')->checkpaymentstatus(Session::get('tran_ref'));
         if ($checkstatus == "A") {
             $paymentId = Session::get('tran_ref');
             $response = ['status' => 1, 'msg' => 'paid', 'paymentId' => $paymentId];
         }
     }
     ```

### 3. **`payment.blade.php`**
   - الموقع: `resources/views/admin/payment/payment.blade.php`
   - الوظيفة: عرض واجهة إدارة طرق الدفع في لوحة التحكم.
   - الأكواد الهامة:
     ```blade
     <label class="form-label">
         {{ trans('labels.payment_name') }} <span class="text-danger">*</span> 
     </label>
     <input type="text" class="form-control" name="name" placeholder="{{ trans('labels.payment_name') }}" required>
     ```

### 4. **`paymentmethodsview.blade.php`**
   - الموقع: `resources/views/web/paymentmethodsview.blade.php`
   - الوظيفة: عرض طرق الدفع في صفحة الواجهة الأمامية.
   - الأكواد الهامة:
     ```blade
     @foreach ($getpaymentmethods as $payment)
         <div class="payment-method">
             <img src="{{ $payment->image }}" alt="{{ $payment->payment_name }}">
             <p>{{ $payment->payment_name }}</p>
         </div>
     @endforeach
     ```

### 5. **`WalletController.php`**
   - الموقع: `app/Http/Controllers/front/WalletController.php`
   - الوظيفة: إدارة محفظة المستخدم وطرق الدفع المرتبطة بها.
   - الأكواد الهامة:
     ```php
     $checkstatus = app('App\\Http\\Controllers\\addons\\PayTabController')->checkpaymentstatus(Session::get('tran_ref'));
     if ($checkstatus == "A") {
         $paymentId = Session::get('tran_ref');
         $response = ['status' => '1', 'msg' => 'paid', 'transaction_id' => $paymentId];
     }
     ```

---

## كيفية إضافة طريقة دفع جديدة
1. **إضافة السجل إلى قاعدة البيانات**:
   - أدخل البيانات الأساسية لطريقة الدفع في جدول `Payment`.

2. **إعداد الكود في الخلفية (Backend)**:
   - افتح الملف `PaymentController.php` وأضف الكود التالي لجلب طريقة الدفع:
     ```php
     $getpaymentmethods = Payment::select('id', 'payment_name', 'is_activate', 'is_available')
         ->where('is_available', 1)
         ->where('is_activate', 1)
         ->get();
     ```

3. **عرض طريقة الدفع في الواجهة**:
   - استخدم الكود التالي في ملف Blade `paymentmethodsview.blade.php`:
     ```blade
     @foreach ($getpaymentmethods as $payment)
         <div class="payment-method">
             <img src="{{ $payment->image }}" alt="{{ $payment->payment_name }}">
             <p>{{ $payment->payment_name }}</p>
         </div>
     @endforeach
     ```

---

## الشروط لتفعيل طريقة الدفع
1. **أن تكون متاحة (`is_available = 1`)**:
   - يجب أن تحمل القيمة `1` في قاعدة البيانات.

2. **أن تكون مفعّلة (`is_activate = 1`)**:
   - يجب التأكد من أن طريقة الدفع مفعّلة.

3. **التحقق من العملة**:
   - إذا كنت تريد تحديد طريقة الدفع لعملة معينة، أضف هذا الشرط:
     ```php
     ->where('currency', 'USD')
     ```

---

## ترتيب طرق الدفع
- يتم ترتيب طرق الدفع باستخدام الحقل `reorder_id`. قم بتحديث هذا الحقل لتغيير ترتيب العرض.

---

## أمثلة على الشروط الإضافية
- **تحديد البيئة (اختبار/إنتاج)**:
  ```php
  ->where('environment', 'production')
  ```

- **استبعاد طرق دفع معينة**:
  ```php
  ->where('payment_name', '!=', 'Wallet')
  ```

---

## الأسئلة الشائعة
### 1. كيف أضيف طريقة دفع جديدة؟
اتبع الخطوات المذكورة أعلاه لإضافة السجل في قاعدة البيانات، وضمان إدراجها في الكود الخلفي والواجهة.

### 2. كيف أتحقق من تفعيل طريقة الدفع؟
تأكد من أن قيمتي `is_available` و `is_activate` تحملان القيمة `1` في قاعدة البيانات.

### 3. كيف أغير ترتيب طرق الدفع؟
قم بتحديث الحقل `reorder_id` للسجلات في جدول `Payment`.

---

## المساهمة
إذا كنت ترغب في المساهمة في المشروع أو تحسين الشيفرة، يرجى فتح طلب سحب (Pull Request) أو الإبلاغ عن المشاكل في قسم القضايا (Issues).
