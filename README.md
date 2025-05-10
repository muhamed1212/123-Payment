# Payment Integration Guide

هذا الدليل يوضح كيفية إضافة طرق دفع جديدة إلى المشروع، بالإضافة إلى كيفية جعلها قابلة للتفعيل أو التعطيل مثل الإضافات الأخرى.

---

## كيفية إضافة طريقة دفع جديدة

لإضافة طريقة دفع جديدة، قم باتباع الخطوات التالية:

### 1. **إضافة تعريف لطريقة الدفع في قاعدة البيانات**
- تأكد من أن الجدول `payment` يحتوي على الأعمدة التالية:
  - `user_id`: معرّف المستخدم الذي أنشأ طريقة الدفع.
  - `payment_name`: اسم طريقة الدفع.
  - `is_available`: لتحديد ما إذا كانت الطريقة متاحة.
  - `is_active`: لتحديد إذا كانت الطريقة مفعّلة أم لا.
  - `test_public_key` و `test_secret_key`: مفاتيح البيئة التجريبية.
  - `live_public_key` و `live_secret_key`: مفاتيح البيئة الحية.
  - `environment`: تحدد البيئة (تجريبية أو حية).

### 2. **تحديث نموذج `Payment`**
- يقع نموذج `Payment` في الملف `app/Models/Payment.php`. تأكد من أن الحقلين `is_available` و `is_active` مضافين في مصفوفة `fillable`.

#### مثال:
```php
protected $fillable = [
    'user_id',
    'payment_name',
    'is_available',
    'is_active',
    'test_public_key',
    'test_secret_key',
    'live_public_key',
    'live_secret_key',
    'environment'
];
```

### 3. **إدارة الإضافات عبر لوحة التحكم**
- قم بتحديث `PaymentController` لإضافة وظائف تفعيل أو تعطيل طرق الدفع.
- أضف واجهة إدارة في لوحة التحكم للسماح للمسؤول بتغيير حالة التفعيل (`is_active`).

#### مثال:
```php
public function toggleActivation(Request $request, $id)
{
    $payment = Payment::find($id);
    $payment->is_active = !$payment->is_active;
    $payment->save();

    return redirect()->back()->with('success', 'Payment method status updated successfully.');
}
```

### 4. **تكامل الواجهة الأمامية**
- قم بتحديث الواجهات الأمامية لإظهار طرق الدفع المتاحة فقط (`is_available = true` و `is_active = true`).

---

## شرح نموذج `Payment`

نموذج `Payment` يمثل طرق الدفع في النظام.

### هيكل النموذج
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Payment extends Model
{
    protected $table = 'payment';
    protected $fillable = [
        'user_id',
        'payment_name',
        'is_available',
        'is_active',
        'test_public_key',
        'test_secret_key',
        'live_public_key',
        'live_secret_key',
        'environment'
    ];
}
```

### الحقول الرئيسية
- **`is_active`**: يُحدد إذا ما كانت الطريقة مفعلة أم لا.
- **`is_available`**: يُحدد إذا ما كانت الطريقة متاحة للمستخدمين.

---

## شرح نموذج `AddonsGroup`

نموذج `AddonsGroup` يمثل مجموعات الإضافات التي يمكن ربطها بالكائنات الأخرى في التطبيق.

### هيكل النموذج
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class AddonsGroup extends Model
{
    protected $table = 'addons_group';
    protected $fillable = ['name', 'selection_type', 'selection_count', 'min_count', 'max_count'];

    public function category()
    {
        return $this->hasOne('App\Models\Category', 'id', 'cat_id');
    }

    public function item()
    {
        return $this->hasOne('App\Models\Item', 'id', 'item_id');
    }
}
```

### الحقول الرئيسية
- **`name`**: اسم مجموعة الإضافات.
- **`selection_type`**: نوع الاختيار (مثل فردي أو متعدد).
- **`selection_count`**: عدد الخيارات المسموح بها.
