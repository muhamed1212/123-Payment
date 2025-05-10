# Rege Payment Integration Guide

This repository provides a framework for managing payment methods and addons groups in a Laravel application. Below, you'll find a guide to understanding and extending the payment methods functionality.

---

## How to Add a New Payment Method

To add a new payment method, follow these steps:

1. **Define the Payment in the Database**:
   - Add an entry to the `payment` table with the following fields:
     - `user_id`: ID of the user creating the payment method.
     - `payment_name`: Name of the payment method.
     - `is_available`: Whether this payment method is available.
     - `test_public_key`, `test_secret_key`: Keys for testing the payment gateway.
     - `live_public_key`, `live_secret_key`: Keys for live transactions.
     - `environment`: The current environment (`test` or `live`).

2. **Edit the `Payment` Model**:
   - The `Payment` model is defined in `app/Models/Payment.php`. Ensure the `fillable` array includes all necessary fields for your new payment method.

3. **Implement the Logic in the Controller**:
   - Update the `PaymentController` to handle CRUD operations for the new payment method. For example:
     - Define how to retrieve, update, and reorder the payments.
     - Specify logic for handling keys and environment configurations.

4. **Add Frontend Integration**:
   - Update the frontend views to display the new payment method and provide options for users to configure it.

5. **Test the Integration**:
   - Use the provided test keys to ensure the payment works correctly in test mode before switching to live mode.

---

## Payment Model

The `Payment` model is located at `app/Models/Payment.php`. It represents the payment methods in the application.

### Structure
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Payment extends Model
{
    protected $table = 'payment';
    protected $fillable = [
        'user_id',
        'payment_name',
        'is_available',
        'test_public_key',
        'test_secret_key',
        'live_public_key',
        'live_secret_key',
        'environment'
    ];
}
```

### Explanation
- `user_id`: The ID of the associated user.
- `payment_name`: Name of the payment method.
- `is_available`: Indicates if the payment method is active.
- `test_public_key`, `test_secret_key`: Keys for the test environment.
- `live_public_key`, `live_secret_key`: Keys for the live environment.
- `environment`: Specifies whether the method is in `test` or `live` mode.

---

## AddonsGroup Model

The `AddonsGroup` model is located at `app/Models/AddonsGroup.php`. It represents groups of addons that can be associated with other entities in the application.

### Structure
```php
<?php

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

### Explanation
- `name`: Name of the addons group.
- `selection_type`: Type of selection allowed (e.g., single or multiple).
- `selection_count`: Number of selections allowed.
- `min_count`, `max_count`: Minimum and maximum selections allowed.
- `category()`: Relationship to the `Category` model.
- `item()`: Relationship to the `Item` model.

---

## Contribution Guidelines

1. Fork and clone the repository.
2. Create a new branch for your feature or bugfix.
3. Commit your changes and push them to your fork.
4. Submit a pull request with a detailed explanation of your changes.

For more information, visit the [repository documentation](https://github.com/m-elesawy/rege).

---
