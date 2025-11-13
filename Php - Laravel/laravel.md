````markdown
# Laravel Cheat Sheet

## 1. Installation

### Syntax Overview

- Install Laravel via Composer:
  ```bash
  composer create-project laravel/laravel transaction-app
  cd transaction-app
  php artisan serve  # Starts server at http://localhost:8000
````

* Initial setup:

  ```bash
  composer install
  composer dump-autolaod
  cp .env.example .env  # Copy environment file
  php artisan key:generate  # Generate app key
  ```

* Configure database in `.env`:

  ```env
  DB_CONNECTION=mysql
  DB_HOST=127.0.0.1
  DB_PORT=3306
  DB_DATABASE=transactions
  DB_USERNAME=root
  DB_PASSWORD=
  ```

* To create a controller in Laravel, you use the Artisan command-line tool:

```bash
 php artisan make:controller MyController
```

* Run migrations:

  ```bash
  php artisan migrate
  ```

### From Sample Code

The sample code assumes a transaction tracking app with a MySQL database named `transactions`. After setting up `.env`, run:

```bash
php artisan migrate  # Creates tables for transactions and users
```

### Tips

* Like your PHP CLI scripts, use `php artisan` for repetitive tasks.
* Keep `.env` secure (don’t commit to Git), similar to environment variables in React.
* Test the server with `php artisan serve` to ensure setup is correct.

---

## 2. Directory Structure

* `app/` → Application code (Models, Controllers, etc.)
* `routes/` → All routes (`web.php`, `api.php`)
* `database/` → Migrations, seeders, factories
* `resources/views/` → Blade templates
* `public/` → Public assets (CSS, JS, images)
* `config/` → Configuration files

---

## 2.1.1 — Migrations & modèles Eloquent

Prerequisites: avoir un projet Laravel 11 fonctionnel (ex. : blog-laravel) et une base MySQL configurée dans le fichier .env.

Glossary:

* PK (Primary Key) → identifiant unique d’une ligne dans une table.
* FK (Foreign Key) → clé faisant référence à une PK d’une autre table.
* Contrainte → règle d’intégrité garantissant la cohérence des données (unicité, non-null, cascade…).
* Migration → fichier versionné décrivant la structure d’une table.
* Modèle Eloquent → classe PHP représentant une table et permettant de la manipuler par le code.

### Objectif pédagogique

Créer un schéma relationnel complet pour le Blog Laravel :

* Définir les tables et leurs relations physiques (PK/FK)
* Appliquer les contraintes d’intégrité (unique, cascadeOnDelete)
* Générer les modèles Eloquent correspondants (Article, Tag)
* Vérifier la cohérence dans Tinker

### Étapes pratiques

**Étape 1 — Créer les migrations**

```bash
php artisan make:migration create_articles_table
php artisan make:migration create_tags_table
php artisan make:migration create_article_tag_table
```

**Étape 2 — Comprendre la table users**

```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->timestamp('email_verified_at')->nullable();
    $table->string('password');
    $table->rememberToken();
    $table->timestamps();
});
```

**Étape 3 — Créer la table articles**

```php
Schema::create('articles', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->cascadeOnDelete();
    $table->string('title', 180);
    $table->string('slug', 200)->unique();
    $table->text('excerpt')->nullable();
    $table->longText('content')->nullable();
    $table->timestamps();
});
```

**Étape 4 — Créer la table tags**

```php
Schema::create('tags', function (Blueprint $table) {
    $table->id();
    $table->string('name')->unique();
    $table->string('slug')->unique();
    $table->timestamps();
});
```

**Étape 5 — Créer la table pivot article_tag**

```php
Schema::create('article_tag', function (Blueprint $table) {
    $table->foreignId('article_id')->constrained()->cascadeOnDelete();
    $table->foreignId('tag_id')->constrained()->cascadeOnDelete();
    $table->primary(['article_id', 'tag_id']);
});
```

**Étape 6 — Exécuter les migrations**

```bash
php artisan migrate
```

**Étape 7 — Créer les modèles Eloquent**

```bash
php artisan make:model Article
php artisan make:model Tag
```

Exemple `app/Models/Article.php` :

```php
class Article extends Model
{
    use HasFactory;
    protected $fillable = ['user_id', 'title', 'slug', 'excerpt', 'content'];
}
```

Exemple `app/Models/Tag.php` :

```php
class Tag extends Model
{
    use HasFactory;
    protected $fillable = ['name', 'slug'];
}
```

**Étape 8 — Vérification rapide avec Tinker**

```bash
php artisan tinker
>>> App\Models\Tag::create(['name' => 'Laravel', 'slug' => 'laravel']);
>>> App\Models\Tag::all();
```

---

## 2.1.2 — Déclaration des relations Eloquent

Prerequisites: migrations et modèles User, Article et Tag doivent déjà être créés.

### Glossary

* Relation 1-n (hasMany / belongsTo)
* Relation n-n (belongsToMany)
* Pivot → table intermédiaire
* Eager loading → chargement anticipé

### Étapes pratiques

**Étape 1 — User → Article**

```php
public function articles()
{
    return $this->hasMany(Article::class);
}
```

**Étape 2 — Article → User**

```php
public function user()
{
    return $this->belongsTo(User::class);
}
```

**Étape 3 — Article ↔ Tag**

```php
public function tags()
{
    return $this->belongsToMany(Tag::class);
}
```

**Dans Tag.php**

```php
public function articles()
{
    return $this->belongsToMany(Article::class);
}
```

**Étape 4 — Eager Loading**

```php
$articles = App\Models\Article::with(['user', 'tags'])->get();
```

**Étape 5 — Compteur de relation**

```php
$articles = App\Models\Article::withCount('tags')->get();
```

**Étape 6 — Vérification dans Tinker**

```bash
php artisan tinker
>>> $u = App\Models\User::first();
>>> $u->articles;
>>> $a = App\Models\Article::first();
>>> $a->user;
>>> $a->tags;
```

---

## 2.1.3 — Seeders & Factories Eloquent

Prerequisites: modèles et relations User, Article et Tag doivent déjà être fonctionnels.

### Glossary

* Factory → structure des données fictives
* Seeder → script d’insertion
* Faker → générateur de données
* sync() → lie plusieurs entités

### Étapes pratiques

**Étape 1 — Créer les factories**

```bash
php artisan make:factory UserFactory --model=User
php artisan make:factory TagFactory --model=Tag
php artisan make:factory ArticleFactory --model=Article
```

**Étape 2 — Créer les seeders**

```bash
php artisan make:seeder UserSeeder
php artisan make:seeder TagSeeder
php artisan make:seeder ArticleSeeder
php artisan make:seeder PivotArticleTagSeeder
```

**Étape 3 — Orchestration**

```php
$this->call([
    UserSeeder::class,
    TagSeeder::class,
    ArticleSeeder::class,
    PivotArticleTagSeeder::class,
]);
```

**Étape 4 — Exécuter les seeders**

```bash
php artisan migrate:fresh --seed
```

**Étape 5 — Vérification**

```bash
php artisan tinker
>>> App\Models\User::count();
>>> App\Models\Article::first()->tags->pluck('name');
```

---

## 3. Routing

```php
Route::get('/', function() {
    return view('welcome');
});

Route::get('/a-propos', [PageController::class, 'about'])->name('about');

Route::get('/about', function() {
    return 'About Page';
});

// Route with parameters
Route::get('/users/{id}', function ($id) {
    return 'User ' . $id;
});

// Route with multiple parameters
Route::get('/posts/{post}/comments/{comment}', function ($postId, $commentId) {
    return "Post {$postId}, Comment {$commentId}";
});

// Optional parameters
Route::get('/greeting/{name?}', function ($name = 'Guest') {
    return 'Hello ' . $name;
});

// Regular Expression Constraints
Route::get('/user/{name}', function ($name) {
    return $name;
})->where('name', '[A-Za-z]+');

// Named Routes
Route::get('/home', [HomeController::class, 'index'])->name('home');
Route::get('/dashboard', [DashboardController::class, 'index'])->middleware('auth')->name('dashboard');

// Route Groups
Route::middleware(['auth', 'admin'])->prefix('admin')->group(function () {
    Route::get('/users', [AdminUserController::class, 'index'])->name('admin.users');
    Route::get('/settings', [AdminSettingsController::class, 'index'])->name('admin.settings');
});

// Resource Routes
Route::resource('photos', PhotoController::class);
// This creates routes for: index, create, store, show, edit, update, destroy

// API Resource Routes (without create and edit)
Route::apiResource('products', ProductController::class);

// Fallback Routes
Route::fallback(function () {
    return 'Page Not Found';
});

// Redirect Routes
Route::redirect('/here', '/there', 301);

// View Routes
Route::view('/welcome', 'welcome', ['name' => 'Taylor']);

Route::post('/submit', [FormController::class, 'submit']);
Route::put('/update/{id}', [UserController::class, 'update']);
Route::delete('/delete/{id}', [UserController::class, 'destroy']);
```

---

## 4. Controllers

```php
php artisan make:controller UserController
```

```php
class UserController extends Controller
{
    // Display a listing of the resource.
    public function index() {
        $users = User::all();
        return view('users.index', compact('users'));
    }

    // Show the form for creating a new resource.
    public function create() {
        return view('users.create');
    }

    // Store a newly created resource in storage.
    public function store(Request $request) {
        $data = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users'
        ]);
        User::create($data);
        return redirect()->route('users.index')->with('success', 'User created successfully!');
    }

    // Display the specified resource.
    public function show(User $user) {
        return view('users.show', compact('user'));
    }

    // Show the form for editing the specified resource.
    public function edit(User $user) {
        return view('users.edit', compact('user'));
    }

    // Update the specified resource in storage.
    public function update(Request $request, User $user) {
        $data = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email,' . $user->id
        ]);
        $user->update($data);
        return redirect()->route('users.index')->with('success', 'User updated successfully!');
    }

    // Remove the specified resource from storage.
    public function destroy(User $user) {
        $user->delete();
        return redirect()->route('users.index')->with('success', 'User deleted successfully!');
    }
}
```

---

## 5. Models

```bash
php artisan make:model User -m
```

```php
class User extends Model
{
    use HasFactory;

    // Mass assignable attributes
    protected $fillable = [
        'name',
        'email',
        'password',
    ];

    // Hidden attributes
    protected $hidden = [
        'password',
        'remember_token',
    ];

    // Casts
    protected $casts = [
        'email_verified_at' => 'datetime',
    ];

    // Define a one-to-many relationship with Article
    public function articles()
    {
        return $this->hasMany(Article::class);
    }

    // Define a many-to-many relationship with Role
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}

class Article extends Model
{
    use HasFactory;
    protected $fillable = ['user_id', 'title', 'slug', 'excerpt', 'content'];

    // Define a many-to-one relationship with User
    public function user()
    {
        return $this->belongsTo(User::class);
    }

    // Define a many-to-many relationship with Tag
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
}

class Tag extends Model
{
    use HasFactory;
    protected $fillable = ['name', 'slug'];

    // Define a many-to-many relationship with Article
    public function articles()
    {
        return $this->belongsToMany(Article::class);
    }
}
```

---

## 6. Migrations

```bash
php artisan make:migration create_users_table --create=users
php artisan make:migration add_avatar_to_users_table --table=users
php artisan migrate
php artisan migrate:status
php artisan migrate:rollback
php artisan migrate:reset
php artisan migrate:refresh
php artisan migrate:fresh --seed
```

---

## 7. Blade Templates

```blade
<!DOCTYPE html>
<html>
<head>
    <title>Laravel Cheat Sheet</title>
    <link rel="stylesheet" href="{{ asset('css/app.css') }}">
</head>
<body>
    @include('partials.header')

    <div class="container">
        @yield('content')
    </div>

    @stack('scripts')

    <h1>{{ $title }}</h1>

    @if(session('success'))
        <div class="alert alert-success">
            {{ session('success') }}
        </div>
    @endif

    @isset($users)
        @if($users->count() > 0)
            <ul>
            @foreach($users as $user)
                <li>{{ $user->name }}</li>
            @endforeach
            </ul>
        @else
            <p>No users found</p>
        @endif
    @endisset

    {{-- This is a Blade comment --}}
    @verbatim
        <div class="foo">
            {{ name }}
        </div>
    @endverbatim
</body>
</html>
```

---

## 8. Requests & Validation

```php
// Basic validation
$request->validate([
    'name' => 'required|string|max:255',
    'email' => 'required|email|unique:users',
    'password' => 'required|min:8|confirmed',
]);

// Custom error messages
$messages = [
    'name.required' => 'A name is required.',
    'email.unique' => 'This email is already taken.',
];
$request->validate([
    'name' => 'required',
    'email' => 'unique:users',
], $messages);

// Form Request Validation
// php artisan make:request StoreBlogPostRequest
// In StoreBlogPostRequest.php:
// public function authorize() { return true; }
// public function rules() { return ['title' => 'required|unique:posts|max:255']; }
// In Controller:
// public function store(StoreBlogPostRequest $request) { /* ... */ }

// Displaying validation errors in Blade
// @if ($errors->any())
//     <div class="alert alert-danger">
//         <ul>
//             @foreach ($errors->all() as $error)
//                 <li>{{ $error }}</li>
//             @endforeach
//         </ul>
//     </div>
// @endif

// Accessing validated data
$validated = $request->validated();
// $validated['name'], $validated['email']

// Conditional validation
$request->validate([
    'email' => 'required_if:mailable,true',
]);

// Custom validation rules
// php artisan make:rule Uppercase
// In Uppercase.php:
// public function passes($attribute, $value) { return strtoupper($value) === $value; }
// public function message() { return 'The :attribute must be uppercase.'; }
// In validation:
// 'name' => ['required', new Uppercase],
```

---

## 9. Eloquent ORM

### CRUD

```php
// Create
$user = User::create([
    'name' => 'John Doe',
    'email' => 'john@example.com',
    'password' => bcrypt('password'),
]);

// Read (Find by ID)
$user = User::find(1);
$user = User::findOrFail(1); // Throws exception if not found

// Read (Get all)
$users = User::all();

// Read (Query constraints)
$activeUsers = User::where('active', 1)->orderBy('name')->get();
$singleUser = User::where('email', 'john@example.com')->first();

// Update
$user = User::find(1);
$user->name = 'Jane Doe';
$user->save();

// Mass Update
User::where('active', 0)->update(['active' => 1]);

// Delete
$user = User::find(1);
$user->delete();

// Mass Delete
User::where('active', 0)->delete();

// Soft Deletes (add `use SoftDeletes;` to model and `->softDeletes()` to migration)
// $user->delete(); // Soft deletes the user
// User::withTrashed()->get(); // Retrieve all models including soft deleted
// User::onlyTrashed()->get(); // Retrieve only soft deleted models
// $user->restore(); // Restore a soft deleted model
// $user->forceDelete(); // Permanently delete a model

// Eager Loading (to avoid N+1 query problem)
$users = User::with('articles')->get();
foreach ($users as $user) {
    foreach ($user->articles as $article) {
        // ...
    }
}

// Lazy Eager Loading
$user = User::find(1);
$user->load('articles');

// Counting Related Models
$users = User::withCount('articles')->get();
foreach ($users as $user) {
    echo $user->articles_count;
}

// Polymorphic Relationships (e.g., Image model belongs to Post or User)
// class Image extends Model { public function imageable() { return $this->morphTo(); } }
// class Post extends Model { public function image() { return $this->morphOne(Image::class, 'imageable'); } }
// class User extends Model { public function image() { return $this->morphOne(Image::class, 'imageable'); } }

// Accessors & Mutators (in User model)
// public function getNameAttribute($value) { return ucfirst($value); } // Accessor
// public function setNameAttribute($value) { $this->attributes['name'] = strtolower($value); } // Mutator

// Customizing Table Names
// protected $table = 'my_users';

// Customizing Primary Keys
// protected $primaryKey = 'user_id';

// Disabling Timestamps
// public $timestamps = false;
```

### Relationships

```php
// One-to-One
// User hasOne Phone
// public function phone() { return $this->hasOne(Phone::class); }
// Phone belongsTo User
// public function user() { return $this->belongsTo(User::class); }

// One-to-Many
// User hasMany Posts
// public function posts() { return $this->hasMany(Post::class); }
// Post belongsTo User
// public function user() { return $this->belongsTo(User::class); }

// Many-to-Many
// User belongsToMany Roles
// public function roles() { return $this->belongsToMany(Role::class); }
// Role belongsToMany Users
// public function users() { return $this->belongsToMany(User::class); }

// Has Many Through
// Country hasMany Posts through Users
// public function posts() { return $this->hasManyThrough(Post::class, User::class); }

// Polymorphic One-to-One
// User morphOne Image
// public function image() { return $this->morphOne(Image::class, 'imageable'); }
// Post morphOne Image
// public function image() { return $this->morphOne(Image::class, 'imageable'); }
// Image morphTo imageable
// public function imageable() { return $this->morphTo(); }

// Polymorphic One-to-Many
// Post morphMany Comments
// public function comments() { return $this->morphMany(Comment::class, 'commentable'); }
// Video morphMany Comments
// public function comments() { return $this->morphMany(Comment::class, 'commentable'); }
// Comment morphTo commentable
// public function commentable() { return $this->morphTo(); }

// Polymorphic Many-to-Many
// Post morphToMany Tags
// public function tags() { return $this->morphToMany(Tag::class, 'taggable'); }
// Video morphToMany Tags
// public function tags() { return $this->morphToMany(Tag::class, 'taggable'); }
// Tag morphToMany Posts
// public function posts() { return $this->morphedByMany(Post::class, 'taggable'); }
// Tag morphToMany Videos
// public function videos() { return $this->morphedByMany(Video::class, 'taggable'); }
```

---

## 10. Middleware

```bash
php artisan make:middleware CheckAge
```

```php
// Registering Middleware
// In app/Http/Kernel.php
// protected $middleware = [ /* ... */ \App\Http\Middleware\CheckAge::class, ]; // Global Middleware
// protected $middlewareGroups = [ 'web' => [ /* ... */ ], 'api' => [ /* ... */ ], ]; // Middleware Groups
// protected $routeMiddleware = [ 'auth' => \App\Http\Middleware\Authenticate::class, 'age' => \App\Http\Middleware\CheckAge::class, ]; // Route Middleware

// Applying Middleware to Routes
// Route::get('/admin', function () { /* ... */ })->middleware('auth');
// Route::get('/admin', 'AdminController@index')->middleware('auth');
// Route::middleware(['auth', 'age'])->group(function () { /* ... */ });

// Middleware Parameters
// public function handle($request, Closure $next, $role) { /* ... */ }
// Route::put('post/{id}', function ($id) { /* ... */ })->middleware('role:editor');
```

---

## 11. Authentication (Laravel Breeze)

```bash
composer require laravel/breeze --dev
php artisan breeze:install
npm install && npm run dev
php artisan migrate
```

---

## 12. Artisan Commands

```bash
php artisan list
php artisan route:list
php artisan make:model ModelName -m
php artisan make:controller ControllerName
php artisan make:middleware MiddlewareName
php artisan migrate
php artisan tinker
php artisan db:seed
```

---

## 13. File Storage

```php
// Store a file on the default disk (usually 'local')
$path = $request->file('avatar')->store('avatars');
// $path will be something like 'avatars/random_filename.jpg'

// Store a file on a specific disk (e.g., 'public')
$path = $request->file('avatar')->store('avatars', 'public');
// To make it publicly accessible, you need to run `php artisan storage:link`

// Get the URL for a stored file
$url = Storage::url('avatars/file.jpg');

// Get the contents of a file
$contents = Storage::get('file.jpg');

// Check if a file exists
$exists = Storage::disk('public')->exists('avatars/file.jpg');

// Delete a file
Storage::delete('avatars/file.jpg');

// Uploading multiple files
// foreach ($request->file('photos') as $photo) {
//     $photo->store('photos');
// }
```

---

## 14. Flash Messages

```php
return redirect()->back()->with('success', 'Data saved successfully');
@if(session('success'))
    <div>{{ session('success') }}</div>
@endif
```

---

## 15. Helpers & Utilities

```php
// Generate a URL to a named route
route('home');
route('profile', ['id' => 1]);

// Generate a full URL to a given path
url('/about');
url()->current(); // Get the current URL
url()->full(); // Get the full URL with query string

// Retrieve the CSRF token value
csrf_token();

// Retrieve old input from the session
old('name');
old('email', 'default@example.com');

// Generate a URL for an asset
asset('css/app.css');
asset('images/logo.png');

// Hash a password
bcrypt('password');
Hash::make('password');

// Get the current Carbon instance
now();
today();
Carbon::parse('2024-01-01');

// Dump variables (for debugging)
dd($variable); // Dump and Die
dump($variable); // Dump without stopping execution

// Environment variables
env('APP_NAME', 'Laravel');

// Configuration values
config('app.timezone');
config(['app.locale' => 'en']);

// String helpers
Str::slug('Laravel Framework', '-'); // laravel-framework
Str::snake('fooBar'); // foo_bar
Str::camel('foo_bar'); // fooBar

// Array helpers
Arr::first($array, function ($value, $key) {
    return $value >= 10;
});
Arr::pluck($array, 'name');

// Collections
collect([1, 2, 3])->map(function ($item) {
    return $item * 2;
})->all();

// Events
event(new UserRegistered($user));

// Jobs
dispatch(new ProcessPodcast($podcast));

// Cache
Cache::put('key', 'value', $minutes);
Cache::get('key');
Cache::remember('users', $minutes, function () {
    return User::all();
});
```

---

## Notions clés de Laravel

| Élément | Définition | Exemple / Remarques |
|---------|------------|-------------------|
| **Migration** | Classe PHP qui définit les changements de schéma de la base de données (création de tables, ajout de colonnes). C’est un script SQL versionné. | `php artisan make:migration create_users_table` |
| **Model** | Classe Eloquent représentant une table et permettant les opérations CRUD via l’ORM. | `User.php` |
| **Factory** | Classe générant des instances fictives d’un modèle pour tests ou seeders. | `UserFactory.php` |
| **Faker** | Librairie PHP intégrée (`fakerphp/faker`) qui produit des données réalistes (noms, emails, contenus). | `fake()->sentence()` |
| **Seeder** | Classe qui exécute les factories ou insère directement des données pour peupler la base. | `UserSeeder.php` |
| **DatabaseSeeder** | Coordonne l’exécution de tous les seeders dans le bon ordre. | `$this->call([UserSeeder::class, ...]);` |
| **Controller** | Classe qui gère la logique métier et les interactions entre modèles et vues. | `UserController.php` |
| **Routing** | Système qui relie les URL aux actions (closures ou méthodes de contrôleur). | `Route::get('/home', [HomeController::class, 'index']);` |
| **Middleware** | Filtre exécuté avant ou après une requête HTTP pour vérifier ou modifier la requête. | `CheckAge`, `auth` |
| **Blade** | Moteur de templates Laravel pour générer des vues HTML avec syntaxe simple. | `resources/views/users/index.blade.php` |
| **Eloquent ORM** | Système d’ORM pour interagir avec la base via les modèles et les relations. | `$user->articles;`, `$article->tags();` |
| **Request & Validation** | Permet de valider les données envoyées par l’utilisateur avant de les traiter. | `$request->validate([...]);` |
| **Authentication** | Système d’authentification utilisateur (Breeze, Jetstream, etc.) intégré. | `php artisan breeze:install` |
| **MVC (Model-View-Controller)** | Architecture : **Model** = données, **View** = affichage, **Controller** = logique métier et orchestration. | `$users = User::all(); return view('users.index', compact('users'));` |
| **sync()** | Méthode pour associer plusieurs entités dans une relation n-n via une table pivot. | `$article->tags()->sync([1,2,3]);` |
| **firstOrCreate()** | Crée une instance si elle n’existe pas encore (idempotent). | `Tag::firstOrCreate(['name' => 'Laravel']);` |

---

