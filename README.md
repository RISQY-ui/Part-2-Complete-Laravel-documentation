## Description for GitHub (Part 2):

**Short Description:**
> Complete documentation log of Model & Migration creation in Laravel 13, database schema structure, error handling (ParseError & typos), and migration execution to SQLite. A step-by-step guide for beginners with troubleshooting solutions.

---

# Comprehensive Documentation Log: Faris's Laravel Project - Part 2

## 📋 Table of Contents

- [1. Project Basics & Setup](#1-project-basics--setup)
- [2. Models & Database (Technical Steps)](#2-models--database-technical-steps)
- [3. Troubleshooting & Success](#3-troubleshooting--success)
- [4. Key Concepts Explained](#4-key-concepts-explained)
- [5. Best Practices](#5-best-practices)

---

## 1. Project Basics & Setup

### Environment Setup

| Component | Details |
|-----------|---------|
| **Operating System** | Linux |
| **PHP Version** | 8.3 |
| **Composer Version** | 2.7.1 |
| **Framework** | Laravel 13 |
| **Database** | SQLite (default) |

### Project Initialization

```bash
# Create new Laravel project
composer create-project laravel/laravel proyek-pertama-laravel

# Navigate to project
cd proyek-pertama-laravel

# Start development server
php artisan serve
```

**Result:** Application running successfully at `http://127.0.0.1:8000`

### Project Structure

```
proyek-pertama-laravel/
├── app/
│   ├── Models/              # Where Model files are stored
│   ├── Http/
│   │   └── Controllers/     # API and web controllers
│   └── ...
├── database/
│   ├── migrations/          # Migration files (schema changes)
│   ├── factories/           # Data factories for testing
│   ├── seeders/             # Database seeders
│   └── database.sqlite      # SQLite database file
├── routes/
│   ├── api.php              # API routes
│   └── web.php              # Web routes
├── .env                     # Environment configuration
├── composer.json            # Project dependencies
└── artisan                  # Laravel CLI
```

---

## 2. Models & Database (Technical Steps)

### What are Models?

A **Model** represents a single database table and provides an interface to interact with it.

| Concept | Explanation |
|---------|-------------|
| **Model** | PHP class representing a database table |
| **Migration** | PHP file that defines database schema |
| **ORM** | Object-Relational Mapping (Eloquent in Laravel) |

### Creating Models with Migrations

**Command:**
```bash
php artisan make:model NamaModel -m
```

**Command Breakdown:**

| Part | Function |
|------|----------|
| `php artisan` | Laravel command-line interface |
| `make:model` | Generate a new Model class |
| `NamaModel` | Name of the model (PascalCase) |
| `-m` | Flag to automatically create migration file |

### Example: Creating a Post Model

```bash
php artisan make:model Post -m
```

**Created Files:**
- `app/Models/Post.php` - Model class
- `database/migrations/2026_06_15_120000_create_posts_table.php` - Migration file

### Understanding Migration Structure

**File Location:** `database/migrations/2026_06_15_120000_create_posts_table.php`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();                          // Primary key (auto-increment)
            $table->string('title');               // String column (max 255 chars)
            $table->text('content');               // Long text column
            $table->string('slug')->unique();      // Unique string for URL
            $table->unsignedBigInteger('author_id'); // Foreign key
            $table->boolean('is_published')->default(false);  // Boolean with default
            $table->integer('views')->default(0);  // Integer counter
            $table->timestamps();                  // created_at & updated_at
            $table->softDeletes();                 // deleted_at (for soft delete)
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

### Migration Column Types

| Type | Purpose | Example |
|------|---------|---------|
| `id()` | Auto-incrementing primary key | `$table->id();` |
| `string()` | Short text (max 255) | `$table->string('name');` |
| `text()` | Long text content | `$table->text('description');` |
| `integer()` | Whole numbers | `$table->integer('age');` |
| `bigInteger()` | Large whole numbers | `$table->bigInteger('views');` |
| `decimal()` | Decimal numbers | `$table->decimal('price', 8, 2);` |
| `boolean()` | True/False | `$table->boolean('is_active');` |
| `dateTime()` | Date and time | `$table->dateTime('published_at');` |
| `date()` | Date only | `$table->date('birth_date');` |
| `timestamps()` | created_at & updated_at | `$table->timestamps();` |
| `softDeletes()` | Soft delete support | `$table->softDeletes();` |
| `foreignId()` | Foreign key reference | `$table->foreignId('user_id');` |
| `enum()` | Predefined options | `$table->enum('status', ['draft', 'published']);` |
| `json()` | JSON data storage | `$table->json('metadata');` |

### Column Modifiers

```php
Schema::create('posts', function (Blueprint $table) {
    // Nullable column
    $table->string('middle_name')->nullable();
    
    // Default value
    $table->boolean('is_active')->default(true);
    
    // Unique column
    $table->string('email')->unique();
    
    // Unsigned (positive numbers only)
    $table->unsignedInteger('quantity');
    
    // Index for faster queries
    $table->string('category')->index();
    
    // Full-text search
    $table->string('title')->fullText();
    
    // Comment (documentation)
    $table->string('status')->comment('Status of the post');
});
```

### The Model Class

**File:** `app/Models/Post.php`

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use HasFactory, SoftDeletes;

    /**
     * The table associated with the model.
     */
    protected $table = 'posts';

    /**
     * The attributes that are mass assignable.
     */
    protected $fillable = [
        'title',
        'content',
        'slug',
        'author_id',
        'is_published'
    ];

    /**
     * The attributes that should be hidden for serialization.
     */
    protected $hidden = [
        'password',
    ];

    /**
     * The attributes that should be cast.
     */
    protected $casts = [
        'is_published' => 'boolean',
        'published_at' => 'datetime',
    ];

    /**
     * Define relationship with User (Author)
     */
    public function author()
    {
        return $this->belongsTo(User::class);
    }

    /**
     * Define relationship with Comments
     */
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }

    /**
     * Scope for published posts
     */
    public function scopePublished($query)
    {
        return $query->where('is_published', true);
    }

    /**
     * Get post URL slug
     */
    public function getUrlAttribute()
    {
        return "/posts/{$this->slug}";
    }
}
```

---

## 3. Troubleshooting & Success

### Common Errors and Solutions

#### Error 1: ParseError - Missing Closing Brace

**Error Message:**
```
ParseError: syntax error, unexpected end of file in migration file
```

**Cause:** Missing closing brace `}` in function

**Example of Wrong Code:**
```php
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        // Missing closing brace here
    // Missing closing brace here
}
```

**Correct Code:**
```php
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title');
    });  // ← Closing brace for Schema::create
}    // ← Closing brace for function
```

**Prevention:**
- Always match opening and closing braces
- Use IDE with brace matching highlighting
- Format code with `php-cs-fixer`

#### Error 2: Variable Typo ($tabel vs $table)

**Error Message:**
```
Undefined variable: $tabel
```

**Cause:** Typo in variable name

**Example of Wrong Code:**
```php
Schema::create('posts', function (Blueprint $table) {
    $tabel->id();  // ← Typo: should be $table
    $tabel->string('title');  // ← Typo again
});
```

**Correct Code:**
```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
});
```

**Prevention:**
- Use IDE with auto-completion
- Install PHP_CodeSniffer
- Use static analysis tools like PHPStan

#### Error 3: Column Already Exists

**Error Message:**
```
SQLSTATE[HY000]: General error: 1 table posts already exists
```

**Cause:** Attempting to create table that already exists

**Solution:**
```bash
# Rollback last migration
php artisan migrate:rollback

# Then run migration again
php artisan migrate
```

#### Error 4: Foreign Key Constraint Error

**Error Message:**
```
SQLSTATE[HY000]: General error: 1 FOREIGN KEY constraint failed
```

**Cause:** Referenced table or column doesn't exist

**Solution:**
```php
// Ensure User table is created first
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->foreignId('user_id')
            ->constrained('users')  // Explicitly reference table
            ->onDelete('cascade');   // Action on user delete
    });
}
```

#### Error 5: Column Not Found

**Error Message:**
```
SQLSTATE[HY000]: General error: 1 no such column: posts.author_id
```

**Cause:** Column name mismatch between migration and query

**Solution:**
- Verify column name in migration
- Verify column name in query
- Ensure migration has been run with `php artisan migrate`

### Execution Steps for Successful Migration

#### Step 1: Verify Migration File

```bash
# List all pending migrations
php artisan migrate:status
```

#### Step 2: Run Migrations

```bash
# Execute all pending migrations
php artisan migrate

# Output should show:
# Migrating: 2014_10_12_000000_create_users_table
# Migrated:  2014_10_12_000000_create_users_table (50ms)
# Migrating: 2026_06_15_120000_create_posts_table
# Migrated:  2026_06_15_120000_create_posts_table (25ms)
```

#### Step 3: Verify Database Tables

```bash
# Using Laravel Tinker (interactive shell)
php artisan tinker

# Inside Tinker:
>>> \DB::connection()->getDoctrineSchemaManager()->listTableNames()
# Output: ["users", "posts", "password_reset_tokens", ...]

>>> \DB::table('posts')->getColumns()
# Output: Shows all columns in posts table
```

#### Step 4: Test the Model

```bash
php artisan tinker

# Create a new post
>>> $post = new \App\Models\Post();
>>> $post->title = "My First Post";
>>> $post->content = "This is amazing!";
>>> $post->save();

# Retrieve posts
>>> \App\Models\Post::all();
>>> \App\Models\Post::where('title', 'My First Post')->first();

# Update post
>>> $post = \App\Models\Post::first();
>>> $post->update(['is_published' => true]);

# Delete post
>>> \App\Models\Post::first()->delete();
```

### Success Checklist

| Item | Status | Notes |
|------|--------|-------|
| PHP 8.3 installed | ✅ | Verified with `php -v` |
| Composer installed | ✅ | Verified with `composer --version` |
| Laravel project created | ✅ | `proyek-pertama-laravel` folder |
| Development server running | ✅ | http://127.0.0.1:8000 accessible |
| Model created | ✅ | `app/Models/Post.php` |
| Migration created | ✅ | Migration file in `database/migrations/` |
| Migration syntax correct | ✅ | No ParseError or typos |
| Migration executed | ✅ | `php artisan migrate` successful |
| Table created in SQLite | ✅ | Verified in `database/database.sqlite` |
| Model queries working | ✅ | Tested with Tinker |

---

## 4. Key Concepts Explained

### What is a Migration?

A **migration** is a version control system for your database. Each migration file represents a change to your database schema.

**Advantages:**
- Track all database changes
- Easy rollback to previous versions
- Team collaboration (everyone applies same changes)
- Automatic table creation

### What is a Model?

A **model** is the bridge between your PHP code and database table. It lets you interact with the database using object-oriented syntax.

**Benefits:**
- Type-safe database operations
- Built-in validation and relationships
- Automatic timestamps
- Query builder methods

### Eloquent ORM

**Eloquent** is Laravel's Object-Relational Mapper. It allows you to work with database records as objects.

```php
// Without Eloquent (Raw SQL)
$posts = DB::select("SELECT * FROM posts WHERE is_published = 1");

// With Eloquent (Cleaner)
$posts = Post::where('is_published', true)->get();

// Even more elegant
$posts = Post::published()->get();  // Using scope
```

### The Migration Lifecycle

```
1. Write Migration Code
   ↓
2. Run: php artisan migrate
   ↓
3. Database Schema Updated
   ↓
4. Commit to Git
   ↓
5. Team runs migrate (same schema)
```

### Relationship Types

| Type | Example | Purpose |
|------|---------|---------|
| **One-to-Many** | Post has many Comments | One post, multiple comments |
| **Many-to-One** | Comment belongs to Post | Multiple comments, one post |
| **Many-to-Many** | Users have many Roles | Multiple users, multiple roles |
| **Has-One** | User has one Profile | One user, one profile |
| **Polymorphic** | Comments on Posts & Videos | Same model, different parents |

---

## 5. Best Practices

### Migration Best Practices

```php
// ✅ GOOD: Clear, descriptive migration
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title', 255)->comment('Post title');
        $table->text('content');
        $table->foreignId('user_id')
            ->constrained()
            ->onDelete('cascade');
        $table->timestamps();
        $table->softDeletes();
        
        // Add indexes
        $table->index('created_at');
        $table->fullText(['title', 'content']);
    });
}

// ❌ BAD: Unclear, missing constraints
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->string('user_id');  // Should be foreignId
        $table->timestamps();
    });
}
```

### Model Best Practices

```php
// ✅ GOOD: Explicit and well-documented
class Post extends Model
{
    use HasFactory;

    protected $fillable = ['title', 'content', 'user_id'];
    
    protected $casts = [
        'is_published' => 'boolean',
        'created_at' => 'datetime',
    ];

    public function user()
    {
        return $this->belongsTo(User::class);
    }

    public function scopePublished($query)
    {
        return $query->where('is_published', true);
    }
}

// ❌ BAD: Unsafe mass assignment, missing relationships
class Post extends Model
{
    // No $fillable - security risk!
    // No relationships defined
    // No scopes for common queries
}
```

### Naming Conventions

| Item | Convention | Example |
|------|-----------|---------|
| **Table** | Plural, snake_case | `blog_posts`, `user_comments` |
| **Model** | Singular, PascalCase | `Post`, `UserComment` |
| **Migration** | Descriptive, timestamp prefix | `2026_06_15_create_posts_table` |
| **Foreign Key** | Singular_id | `user_id`, `post_id` |
| **Pivot Table** | Alphabetical, snake_case | `role_user`, `post_tag` |

### Testing Your Migrations

```php
// tests/Feature/MigrationTest.php

class MigrationTest extends TestCase
{
    public function test_posts_table_has_required_columns()
    {
        $this->assertTrue(Schema::hasColumns('posts', [
            'id',
            'title',
            'content',
            'user_id',
            'is_published',
            'created_at',
            'updated_at',
        ]));
    }

    public function test_can_create_post()
    {
        $post = Post::create([
            'title' => 'Test Post',
            'content' => 'Test Content',
            'user_id' => 1,
        ]);

        $this->assertDatabaseHas('posts', [
            'title' => 'Test Post',
        ]);
    }
}
```

---

## 📊 Summary - Part 2

| Topic | Status | Notes |
|-------|--------|-------|
| **Environment Setup** | ✅ | PHP 8.3, Composer 2.7.1, Laravel 13 |
| **Project Initialization** | ✅ | Successfully created with Laravel CLI |
| **Model Creation** | ✅ | Using `php artisan make:model` |
| **Migration Creation** | ✅ | Generated with `-m` flag |
| **Schema Understanding** | ✅ | Learned all column types & modifiers |
| **Error 1: ParseError** | ✅ | Fixed missing braces |
| **Error 2: Variable Typo** | ✅ | Fixed `$tabel` → `$table` |
| **Migration Execution** | ✅ | Successfully ran `php artisan migrate` |
| **Database Verification** | ✅ | Tables created in SQLite |
| **Model Usage** | ✅ | Tested with Tinker REPL |

---

## 🚀 What's Next (Part 3)

- Controllers and routing
- Views and Blade templating
- Relationships between models
- API endpoints
- Validation and authorization
- Testing

---

**Last Updated:** June 2026

**Created by:** Faris

**Framework:** Laravel 13

**Database:** SQLite

**Status:** ✅ Completed & Verified

**Tags:** #laravel #laravel13 #migration #model #database #sqlite #php-artisan #eloquent #troubleshooting #beginner-guide

---

> This documentation serves as a complete reference for Laravel Model and Migration creation. Use this as a foundation for building more complex database operations in your Laravel applications. Great job completing Part 2! 🎉
