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

/*

$variable = \Namespace\ClassName::functionName()
								^^
								syntax for accessing a static field
*/

$start_date = \Utils\Params::getParam('start_date');
$end_date = \Utils\Params::getParam('end_date');


// ... something else ...
```

### Why do it this way?
Using `includes` every time you need to import some other code is really unsafe. It could potentially override any of the variables in the global scope. The entire included file is executed and that could execute unanticipated side effects

Adding code via namespaces has great intellisense. You get none when using include. You can Ctrl+Click on the name of the functions or classes and your IDE will take you right to the definition.

Using static