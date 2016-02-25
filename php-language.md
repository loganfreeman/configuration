resolve nested variable
---
```php
    /**
     * Look for {$varname} patterns in the variable value.
     *
     * Replace with an existing environment variable.
     *
     * @param string $value
     *
     * @return mixed
     */
    protected static function resolveNestedVariables($value)
    {
        if (strpos($value, '$') !== false) {
            $value = preg_replace_callback(
                '/{\$([a-zA-Z0-9_]+)}/',
                function ($matchedPatterns) {
                    $nestedVariable = Dotenv::findEnvironmentVariable($matchedPatterns[1]);
                    if (is_null($nestedVariable)) {
                        return $matchedPatterns[0];
                    } else {
                        return  $nestedVariable;
                    }
                },
                $value
            );
        }

        return $value;
    }
```
