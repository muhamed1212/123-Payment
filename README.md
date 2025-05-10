# Project Documentation

## Creating a New Module: Blog Example

The system is designed to support modular development. Follow these steps to create a new "Blog" module:

### 1. Define the Blog Module
Create a directory for the module and include the necessary files:
- `app/Http/Controllers/BlogController.php`: Handles blog-related logic.
- `resources/views/blog`: Contains Blade templates for the blog.

Example `BlogController`:
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class BlogController extends Controller
{
    public function index()
    {
        // Fetch and return blog posts
        return view('blog.index', [
            'posts' => Blog::all()
        ]);
    }
}
```

### 2. Register Routes
Add routes for the Blog module in `routes/web.php`:
```php
Route::get('/blog', [BlogController::class, 'index'])->name('blog.index');
```

### 3. Add Views
Create the necessary views in `resources/views/blog`. Example `index.blade.php`:
```blade
@extends('layouts.app')

@section('content')
    <h1>Blog Posts</h1>
    <ul>
        @foreach($posts as $post)
            <li>{{ $post->title }}</li>
        @endforeach
    </ul>
@endsection
```

### 4. Register Module (Optional)
If needed, register the Blog module dynamically similar to other add-ons:
- Update the `SystemAddons` table with the module's `unique_identifier`, `version`, etc.
- Add a `config.json` file to describe the module's metadata.

---

## Adding a Payment Method Module

To add a new payment method (e.g., "PayPal") that dynamically appears in the admin panel's payment methods list, follow these steps:

### 1. Create the Payment Integration
Add the payment-specific logic in a new controller, e.g., `app/Http/Controllers/addons/PayPalController.php`:
```php
<?php

namespace App\Http\Controllers\addons;

use Illuminate\Http\Request;

class PayPalController extends Controller
{
    public function processPayment(Request $request)
    {
        // PayPal payment processing logic
    }
}
```

### 2. Update the Payment Model
Add the payment method to the `Payment` table in the database:
- Ensure `is_available` and `is_activate` flags are set to `1`.

### 3. Extend the Wallet Module
Modify the WalletController (or ensure it's dynamic):
- When fetching payment methods, the new method will automatically appear based on the `Payment` model query:
```php
$getpaymentmethods = Payment::select('id', 'payment_name', 'payment_type')
    ->where('is_available', 1)
    ->where('is_activate', 1)
    ->get();
```

### 4. Add Configuration
Include configurations for the payment method in `.env` or `config/payment.php`:
```php
PAYPAL_CLIENT_ID=your-client-id
PAYPAL_SECRET=your-secret
```

### 5. Test the Integration
Verify that the payment method appears in the admin panel and functions as expected.

---

## Conclusion

This guide outlines the steps to create new modules and integrate new payment methods. For further customization, refer to the `SystemAddons` model and existing modules such as the Wallet module.
