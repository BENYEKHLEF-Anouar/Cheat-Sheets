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

Route::get('/about', function() {
    return 'About Page';
});

Route::post('/submit', [FormController::class, 'submit']);
Route::put('/update/{id}', [UserController::class, 'update']);
Route::delete('/delete/{id}', [UserController::class, 'destroy']);
Route::get('/home', [HomeController::class, 'index'])->name('home');
Route::get('/dashboard', [DashboardController::class, 'index'])->middleware('auth');
```

---

## 4. Controllers

```php
php artisan make:controller UserController
```

```php
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

```bash
php artisan make:model User -m
```

```php
class User extends Model
{
    use HasFactory;
    protected $fillable = ['name', 'email', 'password'];
}
```

---

## 6. Migrations

```bash
php artisan make:migration create_users_table
php artisan migrate
php artisan migrate:rollback
```

---

## 7. Blade Templates

```blade
<!DOCTYPE html>
<html>
<head>
    <title>Laravel Cheat Sheet</title>
</head>
<body>
    <h1>{{ $title }}</h1>

    @if($users->count() > 0)
        <ul>
        @foreach($users as $user)
            <li>{{ $user->name }}</li>
        @endforeach
        </ul>
    @else
        <p>No users found</p>
    @endif
</body>
</html>
```

---

## 8. Requests & Validation

```php
$request->validate([
    'name' => 'required|string|max:255',
    'email' => 'required|email|unique:users'
]);
```

---

## 9. Eloquent ORM

### CRUD

```php
$user = User::create([...]);
$user = User::find(1);
$user->update([...]);
$user->delete();
```

### Relationships

```php
$user->posts;
$post->user;
$user->roles;
```

---

## 10. Middleware

```bash
php artisan make:middleware CheckAge
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
$request->file('avatar')->store('avatars');
Storage::url('avatars/file.jpg');
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
route('home');
url('/about');
csrf_token();
old('name');
asset('css/app.css');
bcrypt('password');
now();
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

