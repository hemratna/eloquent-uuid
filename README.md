# Laravel Eloquent UUID

[![Packagist](https://img.shields.io/packagist/v/jamesmills/eloquent-uuid.svg?style=for-the-badge)](https://packagist.org/packages/jamesmills/eloquent-uuid)
![Packagist](https://img.shields.io/packagist/dt/jamesmills/eloquent-uuid.svg?style=for-the-badge)
![Packagist](https://img.shields.io/packagist/l/jamesmills/eloquent-uuid?style=for-the-badge)

A Laravel Eloquent Model trait for adding and using a uuid with models.

The trait listens to the `creating` event. It generates a new UUID and saves it in the uuid column on the model. 

Featured in [Laravel News](https://laravel-news.com/eloquent-uuid-package-for-laravel)

## Installation

```
composer require jamesmills/eloquent-uuid
```

## Use

In order to use this in your models, just put `use HasUuidTrait;`

```php
<?php

namespace App;
use JamesMills\Uuid\HasUuidTrait;

class User extends Eloquent
{
	use HasUuidTrait;
}
```

## Schema requirements

In order to use this trait, your **schema** must be something like:

```php
<?php
	// ...
	Schema::create('users', function (Blueprint $table) {
		$table->primary('id');
		$table->uuid('uuid')->unique(); // this will create a CHAR(36) field
		$table->string('username', 32);
		$table->string('password', 50);
		// ...
	});
```

## Querying your models

You may use the `findByUuidOrFail` method to try and fetch a model directly:

```php
<?php

Route::get('/user/{uuid}', function($uuid) {
    try {
        return App\User::findByUuidOrFail($uuid);
    } catch (Illuminate\Database\Eloquent\ModelNotFoundException $e) {
        abort(404);
    }
});
```

You may also use the `withUuid` and `withUuids` local query scopes with the query builder.

```php
<?php

Route::get('/user/{uuid}', function($uuid) {
    $user = App\User::withUuid($uuid)->first();
    if (! $user) {
        // Do something else...
    }
});
```
```php
<?php

Route::delete('/users', function(Request $request) {
    // Receive an array of UUIDs
    $uuids = $request->input('uuids');

    // Try to get the Users
    $users = App\User::withUuids($uuids)->all();
    
    // Handle the delete and return
    $users->delete();
});
```