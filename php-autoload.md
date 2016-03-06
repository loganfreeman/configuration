Ensure vendor libraries exist
---
```php
$autoload = __DIR__ . '/vendor/autoload.php';
if (!is_file($autoload)) {
    throw new \RuntimeException("Please run: <i>bin/grav install</i>");
}
// Register the auto-loader.
$loader = require_once $autoload;
```
