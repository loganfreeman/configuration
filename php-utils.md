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
normalize path
---
```php
$path = rtrim(str_replace('\\', '/', $path), '/');
// Remove prefix from filename.
$filename = '/' . trim(substr($file, strlen($prefix)), '\/');
```

Twig absolute url filter
---
`(?!(http|ftp))` Negative Lookahead - Assert that it is impossible to match the regex below
```php
    public function absoluteUrlFilter($string)
    {
        $url = $this->grav['uri']->base();
        $string = preg_replace('/((?:href|src) *= *[\'"](?!(http|ftp)))/i', "$1$url", $string);

        return $string;

    }
```

dynamic property
---
```php
    /**
     * @param array $field
     * @param string $property
     * @param array $call
     */
    protected function dynamicData(array &$field, $property, array &$call)
    {
        $params = $call['params'];

        if (is_array($params)) {
            $function = array_shift($params);
        } else {
            $function = $params;
            $params = [];
        }

        list($o, $f) = preg_split('/::/', $function, 2);
        if (!$f) {
            if (function_exists($o)) {
                $data = call_user_func_array($o, $params);
            }
        } else {
            if (method_exists($o, $f)) {
                $data = call_user_func_array(array($o, $f), $params);
            }
        }

        // If function returns a value,
        if (isset($data)) {
            if (isset($field[$property]) && is_array($field[$property]) && is_array($data)) {
                // Combine field and @data-field together.
                $field[$property] += $data;
            } else {
                // Or create/replace field with @data-field.
                $field[$property] = $data;
            }
        }
    }
```
