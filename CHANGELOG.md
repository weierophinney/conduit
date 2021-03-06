# Changelog

All notable changes to this project will be documented in this file, in reverse chronological order by release..

## 0.3.0 - TBD

This release separates the HTTP functionality into its own package, [phly/http](https://github.com/phly/http). As such, the subnamespaces `Phly\Conduit\Http` and `PhlyTest\Conduit\Http` were removed, as they became part of that package. Additionally, the following changes were made:

- `Middleware::handle()` was renamed to `Middleware::__invoke()`, to be compatible with the `phly/http` server implementation.
- All signatures that referred to the former Http subnamespace now refer to the `phly/http` namespace (`Phly\Http`).
- Examples were rewritten to show instantiating a `Phly\Http\Server` instead of a `Phly\Conduit\Http\Server`.

### Added

- Nothing.

### Deprecated

- Nothing.

### Removed

- `Phly\Conduit\Http\*` were removed; this includes:

  - `AbstractMessage`
  - `Request`
  - `RequestFactory`
  - `Response`
  - `ResponseInterface`
  - `Stream`
  - `Uri`
  - `Server`
  
  Each of these are now part of the [phly/http](https://github.com/phly/http) package; install that package to use them.

### Fixed

- Nothing.

## 0.2.0 - 2014-08-21

Most importantly, this release changes the signature of `Phly\Conduit\Http\Server::createServer()`. Previously, the signature was:

```php
public static function createServer(
  Phly\Conduit\Middleware $middleware,
  Psr\Http\Message\RequestInterface $request = null,
  Phly\Conduit\Http\ResponseInterface $response = null
);
```

It is now:

```php
public static function createServer(
  Phly\Conduit\Middleware $middleware,
  array $server // usually $_SERVER
);
```

A new method, `createServerFromRequest()`, has the original arguments, albeit with the request argument required:

```php
public static function createServer(
  Phly\Conduit\Middleware $middleware,
  Psr\Http\Message\RequestInterface $request,
  Phly\Conduit\Http\ResponseInterface $response = null
);
```

This method will create a response for you if none is provided.

Finally, the constructor is now public, allowing you to instantiate directly if you have each of the middleware, request, and response objects prepared:

```php
public function __construct(
  Phly\Conduit\Middleware $middleware,
  Psr\Http\Message\RequestInterface $request,
  Phly\Conduit\Http\ResponseInterface $response
);
```

### Added

- `Phly\Conduit\Http\RequestFactory`, a static class for populating a `Psr\Http\Message\RequestInterface` instance based on `$_SERVER`. The primary entry method is `fromServer()`:

  ```php
  // Create a new request, based on $_SERVER:
  $request = Phly\Conduit\Http\RequestFactory::fromServer($_SERVER);

  // Populate an existing request, based on $_SERVER:
  $request = Phly\Conduit\Http\RequestFactory::fromServer($_SERVER, $request);
  ```

- `Phly\Conduit\Http\Server::__construct()`; see above.

- `Phly\Conduit\Http\Server::createServerFromRequest()`; see above.

### Deprecated

- Nothing.

### Removed

- `Phly\Conduit\Http\Server` removes all methods for marshaling a request object, and instead delegates to `Phly\Conduit\Http\RequestFactory::fromServer()` when the `createServer()` method is invoked.
- `Phly\Conduit\Next` no longer keeps track of a "slash added" status, as the `Phly\Conduit\Http\Uri` implementation obviates it.

### Fixed

- Used [scrutinizer](https://scrutinizer-ci.com) to refactor almost the entire code base to make it less complex, more stable, and easier to maintain. In many cases, extract method refactors were applied, in ways that keep the public API unchanged, but which remove complexity internally.
- `Phly\Conduit\Http\Server` now keeps track of the initial buffer level, and does not rewind beyond it when invoking `send()`.
- `Phly\Conduit\Http\Request::setUrl()` now throws an exception if neither a string or a `Phly\Conduit\Http\Uri` instance is provided.
- `Phly\Conduit\Http\Stream` now throws exceptions at instantiation if the provided stream is not a resource or a string capable of being a resource.
- `Phly\Conduit\Http\Stream` now detaches the resource when `close()` is called.
- `Phly\Conduit\Http\Stream` now returns false if the stream has been detached when calling `isSeekable()`.
- `Phly\Conduit\Http\Stream` now casts the return value of `fseek()` to the appropriate boolean during `seek()`.

## 0.1.0 - 2014-08-11

Initial release.
