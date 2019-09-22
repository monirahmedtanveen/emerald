---
published: true
---
##### Introduction

This is an application which have laravel backend and jQuery frontend. In this application I solved the CORS(Cross Origin Resource Sharing) headers error while sharing data between frontend and backend. I used <a href="https://github.com/spatie/laravel-cors">spatie/laravel-cors</a> Package in backend app to solve the CORS headers issue.

##### Creating Backend Api

In this section I will create an api called "users" which will fetch all the user from the database. For creating this api at first install a freash laravel project into a folder called "laravel-cors".

```php
composer create-project --prefer-dist laravel/laravel backend
``` 

After installation, configure the .env file. Change the following lines of .env

```php
DB_DATABASE=your_database
DB_USERNAME=your_database_username
DB_PASSWORD=your_database_password
```

Change the database/migrations/2014_10_12_000000_create_users_table.php migration file

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password');
            $table->string('mobile')->nullable();
            $table->string('address')->nullable();
            $table->rememberToken();
            $table->integer('created_at');
            $table->integer('updated_at');
            $table->integer('deleted_at')->nullable();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('users');
    }
}
```

Then migrate this table using following command

```php
php artisan migrate
``` 