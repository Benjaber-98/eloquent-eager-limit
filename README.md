[![Build Status](https://travis-ci.org/staudenmeir/eloquent-eager-limit.svg?branch=master)](https://travis-ci.org/staudenmeir/eloquent-eager-limit)
[![Latest Stable Version](https://poser.pugx.org/staudenmeir/eloquent-eager-limit/v/stable)](https://packagist.org/packages/staudenmeir/eloquent-eager-limit)
[![Total Downloads](https://poser.pugx.org/staudenmeir/eloquent-eager-limit/downloads)](https://packagist.org/packages/staudenmeir/eloquent-eager-limit)
[![License](https://poser.pugx.org/staudenmeir/eloquent-eager-limit/license)](https://packagist.org/packages/staudenmeir/eloquent-eager-limit)

## Introduction
This Laravel Eloquent extension allows limiting the number of eager loading results per parent using [window functions](https://en.wikipedia.org/wiki/Select_(SQL)#Limiting_result_rows).    
Supports Laravel 5.5.29+.

## Compatibility

- **MySQL 5.5+**
- **MariaDB 10.2+**: Due to a [bug](https://jira.mariadb.org/projects/MDEV/issues/MDEV-17525) in MariaDB, the package only works with strict mode disabled.  
  In your `config/database.php` file, set `'strict' => false,` for the MariaDB connection.
- **PostgreSQL 9.3+**
- **SQLite 3.25+**: The limit is ignored on older versions of SQLite. This way, your application tests still work.
- **SQL Server 2008+**
 
## Installation

    composer require staudenmeir/eloquent-eager-limit

## Usage

Use the `HasEagerLimit` trait in both the parent and the related model and apply `limit()/take()` to your relationship. 

```php
class User extends Model
{
    use \Staudenmeir\EloquentEagerLimit\HasEagerLimit;

    public function posts()
    {
        return $this->hasMany('App\Post');
    }
}

class Post extends Model
{
    use \Staudenmeir\EloquentEagerLimit\HasEagerLimit;
}

$users = User::with(['posts' => function ($query) {
    $query->latest()->limit(10);
}])->get();
```

Improve the performance of `HasOne`/`MorphOne` relationships by applying `limit(1)`: 

```php
class User extends Model
{
    use \Staudenmeir\EloquentEagerLimit\HasEagerLimit;

    public function latestPost()
    {
        return $this->hasOne('App\Post')->latest()->limit(1);
    }
}

class Post extends Model
{
    use \Staudenmeir\EloquentEagerLimit\HasEagerLimit;
}

$users = User::with('latestPost')->get();
```