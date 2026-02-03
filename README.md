# Laravel Redis Stream

Manage Redis stream messages by firing handler classes per channel. You can assign multiple handler classes to the same channel.

## Requirements

- PHP ^7.4 | ^8.0
- Laravel / Lumen ^8.0 | ^9.0 | ^10.0 | ^11.0 | ^12.0
- Redis (phpredis driver recommended)

## Installation

Install the package via Composer:

```bash
composer require ostah/laravel-redis-stream
```

### Laravel

Publish the config file:

```bash
php artisan vendor:publish --tag=laravel-redis-stream-config
```

### Lumen

1. Register the service provider in `bootstrap/app.php`:

```php
$app->register(LaravelStream\Redis\StreamServiceProvider::class);
```

2. Ensure Redis is registered (add if not already present):

```php
$app->register(Illuminate\Redis\RedisServiceProvider::class);
```

3. Copy the config file from `vendor/ostah/laravel-redis-stream/config/streaming.php` to `config/streaming.php`.

> For more on Redis with Lumen, see the [Lumen cache documentation](https://lumen.laravel.com/docs/cache).

## Configuration

In `config/streaming.php`:

1. **Redis connection** – Set the connection name under the `redis.connection` key. This must match a connection defined in `config/database.php` (e.g. `default` or a custom `stream` connection).

2. **Channels and handlers** – Define channels and their handler classes:

```php
'channels' => [
    'channel-name' => [
        App\Channels\SomeClassChannel::class,
    ],
],
```

3. **Trim (optional)** – Limit stream length per channel:

```php
'trim' => [
    'channel-name' => 1000, // keep at most 1000 messages
],
```

We recommend using the **phpredis** driver for Redis.

## Creating a handler class

Generate a channel listener with Artisan (created in `app/Channels`):

```bash
php artisan make:channel-listener SomeClassChannel
```

## Running the channel listener

Listen to all channels:

```bash
php artisan stream:run
```

Listen to a specific channel:

```bash
php artisan stream:run --channel=channel-name
```

## Sending messages to a Redis stream

Use the `Stream` facade to publish messages (xADD):

```php
use LaravelStream\Redis\Facades\Stream;

$messageId = Stream::stream($channel, $data, $trim);
```

| Parameter  | Type    | Description |
|-----------|---------|-------------|
| `$channel` | string  | Channel name |
| `$data`    | mixed   | Message payload (will be JSON- or msgpack-encoded per config) |
| `$trim`    | int\|null | Max stream length for this channel. `null` = use config; `0` = no trimming |

## License

MIT
