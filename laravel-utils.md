write env
---
```php
    protected function writeEnv($key, $value)
    {
        $dir = app()->environmentPath();
        $file = app()->environmentFile();
        $path = "{$dir}/{$file}";

        try {
            (new Dotenv($dir, $file))->load();

            file_put_contents($path, str_replace(
                env(strtoupper($key)), $value, file_get_contents($path)
            ));
        } catch (InvalidPathException $e) {
            //
        }
    }
```
