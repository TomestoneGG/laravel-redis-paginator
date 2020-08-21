# Laravel Redis Sorted Set Paginator

[![Latest Version on Packagist](https://img.shields.io/packagist/v/ge-tracker/laravel-redis-paginator.svg?style=flat-square)](https://packagist.org/packages/ge-tracker/laravel-redis-paginator)
[![GitHub Tests Action Status](https://img.shields.io/github/workflow/status/ge-tracker/laravel-redis-paginator/Tests?label=tests)](https://github.com/ge-tracker/laravel-redis-paginator/actions?query=workflow%3Arun-tests+branch%3Amaster)
[![Total Downloads](https://img.shields.io/packagist/dt/ge-tracker/laravel-redis-paginator.svg?style=flat-square)](https://packagist.org/packages/ge-tracker/laravel-redis-paginator)

Ever wanted to display paginated sorted sets at scale? A great example of this would be a leaderboard for a game, or for a website with a large userbase. This package will create a Laravel `LengthAwarePaginator` from a Redis sorted set. As a sorted set, by definition, is always sorted, it allows a large number of records to be paginated, with very little overhead.

## Installation

You can install the package via composer:

```bash
composer require ge-tracker/laravel-redis-paginator
```

## Usage

Initialise the paginator by using dependency injection or the provided `RedisPaginator` facade. The example below will interact with the `leaderboard` sorted set. We leverage Laravel's Redis interface, which will honour any prefixing and clustering options that you have configured on your application.

Here is an example of a paginated sorted set in action:

``` php
public function index(LaravelRedisPaginator $redisPaginator)
{
    $users = $redisPaginator->perPage(25)->paginate('leaderboard');

    return view('leaderboard', compact('users'));
}
```

### Sorting

The `sortAsc` and `sortDesc` methods allow you to choose the order of the returned results. The default sorting is in ascending order.

```php
$usersAsc = $redisPaginator->sortAsc()->paginate('leaderboard');

$usersDesc = $redisPaginator->sortDesc()->paginate('leaderboard');
```

### Get the rank and page for a user

You may want to display the user's rank in the sorted set, as well as a link to jump to the page that contains their name. This can be achieved by using the `rank()` method. A `MemberRank` object will be returned which contains the `score`, `rank`, and `page` properties:

```php
public function show(User $user, LaravelRedisPaginator $redisPaginator)
{
    $memberRank = $redisPaginator->rank('user:' . $user->id, 'leaderboard');

    dump($memberRank->score, $memberRank->rank, $memberRank->page);
}
```

### Using the facade

For those of you who prefer facades over dependency injection, that option is also available:

```php
public function index()
{
    $users = RedisPaginator::perPage(25)->paginate('leaderboard');

    return view('leaderboard', compact('users'));
}
```

### Selecting a page to view

The current page can be set by using the `page()` method, or by using the method parameters. Under the hood, the package uses Laravel's default `Paginator`'s page resolution, which means that the page can also be specified via the query string.

```php
// Using the fluent interface
$users = $redisPaginator->page(5)->paginate('leaderboard');

// Using method parameters
$users = $redisPaginator->paginate('leaderboard', 'page', 5);

// https://www.example.com/leaderboard?page=5
$users = $redisPaginator->paginate('leaderboard');
```

## Testing

``` bash
composer test
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security

If you discover any security related issues, please email james@ge-tracker.com instead of using the issue tracker.

## Credits

- [James Austen](https://github.com/gtjamesa)
- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
