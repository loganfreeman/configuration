platform
---
```php
    /**
     * Validate if the system is based on windows or not.
     *
     * @return bool
     */
    public function isWindows()
    {
        $keys = [
            'CYGWIN_NT-5.1',
            'WIN32',
            'WINNT',
            'Windows'
        ];

        return array_key_exists(PHP_OS, $keys);
    }
```
field 
---
```php
    /**
     * Converts dot notation to array notation.
     *
     * @param  string $name
     *
     * @return string
     */
    public function field($name)
    {
        $path = explode('.', $name);

        return array_shift($path) . ($path ? '[' . implode('][', $path) . ']' : '');
    }
```
phpinfo
---
```php
    /**
     * Renders phpinfo
     *
     * @return string The phpinfo() output
     */
    function phpinfo()
    {
        if (function_exists('phpinfo')) {
            ob_start();
            phpinfo();
            $pinfo = ob_get_contents();
            ob_end_clean();

            $pinfo = preg_replace('%^.*<body>(.*)</body>.*$%ms', '$1', $pinfo);
            return $pinfo;
        } else {
            return 'phpinfo() method is not available on this server.';
        }
    }
```
