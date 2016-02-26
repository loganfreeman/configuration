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
factory
---
```php
	public static function factory($index, $driver = null)
	{
		if (null === $driver) {
			$driver = Config::get('search.default', 'zend');
		}
		
		switch ($driver) {
			case 'algolia':
				return new Index\Algolia($index, 'algolia');
				
			case 'elasticsearch':
				return new Index\Elasticsearch($index, 'elasticsearch');
				
			case 'zend':
			default:
				return new Index\Zend($index, 'zend');
		}
	}
```
compile template
---
```php
    /**
     * Compile a template with given data.
     *
     * @param $template
     * @param $data
     *
     * @return string
     */
    public function compile($template, $data)
    {
        foreach ($data as $key => $value) {
            $key = strtoupper($key);

            if(is_bool($value))
                $value = $value ? 'true' : 'false';

            $template = preg_replace("#\\$$key\\$#i", $value, $template);
        }

        return $template;
    }
```
closure
---
```php
    /**
     * Run the given task over SSH.
     *
     * @param  \Laravel\Envoy\Task  $task
     * @return void
     */
    public function run(Task $task, Closure $callback = null)
    {
        $processes = [];

        $callback = $callback ?: function () {};

        // Here we will gather all the process instances by host. We will build them in
        // an array so we can easily loop through them then start them up. We'll key
        // the array by the target name and set the value as the process instance.
        foreach ($task->hosts as $host) {
            $process = $this->getProcess($host, $task);

            $processes[$process[0]] = $process[1];
        }

        // Next we'll loop through the processes and run them sequentially while taking
        // the output and feeding it through the callback. This will in turn display
        // the output back out to the screen for the developer to inspect closely.
        foreach ($processes as $host => $process) {
            $process->run(function ($type, $output) use ($host, $callback) {
                $callback($type, $host, $output);
            });
        }

        return $this->gatherExitCodes($processes);
    }
```
