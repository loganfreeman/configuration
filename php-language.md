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
prepare options
---
```php
    /**
     * Prepare a command's options for command line usage
     *
     * @param array $options
     *
     * @return string
     */
    public function prepareOptions(array $options)
    {
        $optionPieces = [];
        foreach ($options as $opt => $value) {
            //if it's an array of options, throw them in there as well
            if (is_array($value)) {
                foreach ($value as $optArrayValue) {
                    $optionPieces[] = '--'.$opt.'="'.addslashes($optArrayValue).'"';
                }
            } else {
                $option = null;

                //option exists with no value
                if (is_numeric($opt)) {
                    $option = $value;
                } elseif (!empty($value)) {
                    $option = $opt.'="'.addslashes($value).'"';
                }

                if (!is_null($option)) {
                    $optionPieces[] = '--'.$option;
                }
            }
        }

        return implode(' ', $optionPieces);
    }
```
