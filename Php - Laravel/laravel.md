````markdown
# Laravel Cheat Sheet

## 1. Installation

### Syntax Overview

- Install Laravel via Composer:
  ```bash
  composer create-project laravel/laravel transaction-app
  cd transaction-app
  php artisan serve  # Starts server at http://localhost:8000
  ```

- Initial setup:
  ```bash
  cp .env.example .env  # Copy environment file
  php artisan key:generate  # Generate app key
  ```

- Configure database in `.env`:
  ```env
  DB_CONNECTION=mysql
  DB_HOST=127.0.0.1
  DB_PORT=3306
  DB_DATABASE=transactions
  DB_USERNAME=root
  DB_PASSWORD=
  ```

 - To create a controller in Laravel, you use the Artisan command-line tool:
 ```bash
  php artisan make:controller MyController
  ```

- Run migrations:
  ```bash
  php artisan migrate
  ```

### From Sample Code
The sample code assumes a transaction tracking app with a MySQL database named `transactions`. After setting up `.env`, run:
```bash
php artisan migrate  # Creates tables for transactions and users
```

### Tips
- Like your PHP CLI scripts, use `php artisan` for repetitive tasks.
- Keep `.env` secure (don’t commit to Git), similar to environment variables in React.
- Test the server with `php artisan serve` to ensure setup is correct.


````

---

## 2. Directory Structure

* `app/` → Application code (Models, Controllers, etc.)
* `routes/` → All routes (`web.php`, `api.php`)
* `database/` → Migrations, seeders, factories
* `resources/views/` → Blade templates
* `public/` → Public assets (CSS, JS, images)
* `config/` → Configuration files

---

## 3. Routing

### Web Routes

```php
// routes/web.php
Route::get('/', function() {
    return view('welcome');
});

Route::get('/about', function() {
    return 'About Page';
});

Route::post('/submit', [FormController::class, 'submit']);

Route::put('/update/{id}', [UserController::class, 'update']);

Route::delete('/delete/{id}', [UserController::class, 'destroy']);

// Route with name
Route::get('/home', [HomeController::class, 'index'])->name('home');

// Route with middleware
Route::get('/dashboard', [DashboardController::class, 'index'])->middleware('auth');

// Route Group
Route::middleware(['auth'])->group(function(){
    Route::get('/profile', [ProfileController::class, 'index']);
});

Route::get('/', [fileController::class, 'home'])->name('home');
// This defines a GET route for the root URL ('/')
// When a user visits the home page, Laravel will call the 'home' method of the fileController class
// The array [fileController::class, 'home'] tells Laravel which controller and method to use
// ->name('home') assigns a name to this route, allowing it to be referenced easily elsewhere (e.g., route('home'))
```

### API Routes

```php
// routes/api.php
Route::get('/users', [UserController::class, 'index']);
Route::post('/users', [UserController::class, 'store']);
```

---

## 4. Controllers

```php
// Generate controller
php artisan make:controller UserController

// app/Http/Controllers/UserController.php
namespace App\Http\Controllers;
// This line imports the fileController class from the "App\Http\Controllers" namespace
// It allows us to use the class name directly (fileController) instead of writing the full namespace path every time.

use Illuminate\Http\Request;
use App\Models\User;

class UserController extends Controller
{
    public function index() {
        $users = User::all();
        return view('users.index', compact('users'));
    }

    public function store(Request $request) {
        $data = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users'
        ]);

        User::create($data);
        return redirect()->back();
    }
}
```

---

## 5. Models

```php
// Generate model
php artisan make:model User -m   # -m creates migration too

// app/Models/User.php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    use HasFactory;

    protected $fillable = ['name', 'email', 'password'];
}
```

---

## 6. Migrations

```php
// Generate migration
php artisan make:migration create_users_table

// database/migrations/xxxx_create_users_table.php
public function up()
{
    Schema::create('users', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email')->unique();
        $table->string('password');
        $table->timestamps();
    });
}

// Run migrations
php artisan migrate

// Rollback
php artisan migrate:rollback
```

---

## 7. Blade Templates

```blade
<!-- resources/views/welcome.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Laravel Cheat Sheet</title>
</head>
<body>
    <h1>{{ $title }}</h1> <!-- Echo data -->

    {{-- Comment --}}

    @if($users->count() > 0)
        <ul>
        @foreach($users as $user)
            <li>{{ $user->name }}</li>
        @endforeach
        </ul>
    @else
        <p>No users found</p>
    @endif

    @include('partials.footer')
</body>
</html>
```

---

## 8. Requests & Validation

```php
$request->all();           // Get all input
$request->input('name');   // Single input
$request->has('email');    // Check existence
$request->validate([
    'name' => 'required|string|max:255',
    'email' => 'required|email|unique:users'
]);
```

---

## 9. Eloquent ORM

### CRUD

```php
// Create
$user = User::create([
    'name' => 'John',
    'email' => 'john@example.com',
    'password' => bcrypt('secret')
]);

// Read
$users = User::all();
$user = User::find(1);
$user = User::where('email', 'john@example.com')->first();

// Update
$user = User::find(1);
$user->name = 'Jane';
$user->save();

// Delete
$user = User::find(1);
$user->delete();
```

### Relationships

```php
// One-to-Many (User has many Posts)
class User extends Model {
    public function posts() {
        return $this->hasMany(Post::class);
    }
}

class Post extends Model {
    public function user() {
        return $this->belongsTo(User::class);
    }
}

// Many-to-Many
class User extends Model {
    public function roles() {
        return $this->belongsToMany(Role::class);
    }
}

// Accessing relationships
$user = User::find(1);
$user->posts;
$user->roles;
```

---

## 10. Middleware

```php
// Create middleware
php artisan make:middleware CheckAge

// app/Http/Middleware/CheckAge.php
public function handle($request, Closure $next) {
    if ($request->age < 18) {
        return redirect('home');
    }
    return $next($request);
}

// Register in app/Http/Kernel.php
protected $routeMiddleware = [
    'age' => \App\Http\Middleware\CheckAge::class,
];

// Apply middleware
Route::get('/restricted', function(){})
    ->middleware('age');
```

---

## 11. Authentication (Laravel Breeze)

```bash
composer require laravel/breeze --dev
php artisan breeze:install
npm install && npm run dev
php artisan migrate
```

* Login: `/login`
* Register: `/register`
* Logout: `Route::post('/logout')`

---

## 12. Artisan Commands

```bash
php artisan list          # List all commands
php artisan route:list    # Show routes
php artisan make:model ModelName -m   # Model with migration
php artisan make:controller ControllerName
php artisan make:middleware MiddlewareName
php artisan migrate
php artisan migrate:rollback
php artisan tinker        # Interactive shell
php artisan db:seed       # Run seeders
```

---

## 13. File Storage

```php
// Store uploaded file
$request->file('avatar')->store('avatars');

// Retrieve
Storage::url('avatars/file.jpg');

// Configuration: config/filesystems.php
```

---

## 14. Flash Messages

```php
// Set message
return redirect()->back()->with('success', 'Data saved successfully');

// Display in Blade
@if(session('success'))
    <div>{{ session('success') }}</div>
@endif
```

---

## 15. Helpers & Utilities

```php
route('home');          // Generate URL for named route
url('/about');          // Full URL
csrf_token();           // CSRF token
old('name');            // Old input value
asset('css/app.css');   // Public asset
bcrypt('password');     // Hash password
now();                  // Current datetime
```

---
