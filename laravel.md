# Laravel

## Progress Bars in Artisan Commands

Laravel includes a handy helper method to display progress bars in Artisan commands:

This will loop over every user and perform the callback and update the progress after every iteration.

```php
$users = $this->withProgressBar(User::all(), function ($user) {
    $this->performTask($user);
});
```

It’s also possible to manually update the progress:

```php
$bar = $this->output->createProgressBar(count($users));

$bar->start();

foreach ($users as $user) {
  // Do something...
  
  $bar->advance();
}
```

* [Laravel Docs](https://laravel.com/docs/8.x/artisan#progress-bars)
