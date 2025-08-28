Our legacy codebase is littered with bad practices. My goal is to create an easy way to avoid relying on the following when making new features:
- Lots of `include` statements
- `extract` statements on `_REQUEST` or a query result


## Static Class Methods
PHP namespaces allow us to write reusable pure functions instead of reinventing the wheel. In this tutorial we will create a class to help us with dealing with URL parameters 

A class can be created in the `_global` directory that is autoloaded in any file that includes `globalFunctions.php`. Since this class isn't specific to any application in particular, I'm putting it in the `_global/Utils` folder. Each subdirectory under `_global` is it's own namespace.

```php
// filename: _global/Utils/Params.php

<?php

namespace Utils;

class Params
{
  // todo
}
```

Now let's create a `public static function` called `getParam` to help us get a parameter from the `$_REQUEST` variable

```php
// filename: _global/Utils/Params.php

<?php

namespace Utils;

class Params
{
  public static function getParam($key, $fallback = null)
  {
	if (isset($_REQUEST[$key])) {
		return $_REQUEST[$key];
	}
	return $default;
  }
}

?>
```

Now anytime you need to consume a URL parameter you can using this syntax

```php

// ... something ...

$

// ... something else ...

```