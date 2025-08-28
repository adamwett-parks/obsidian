Our legacy codebase is littered with bad practices. My goal is to create an easy way to avoid relying on the following when making new features:
- Lots of `include` statements
- `extract` statements on `_REQUEST` or a query result


## Static Class Methods
PHP namespaces allow us to write reusable pure functions instead of reinventing the wheel. In this tutorial we will create a class to help us with 

A class can be created in the `_global` folder that is autoloaded in any file that includes `globalFunctions.php`. 

