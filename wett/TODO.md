-  michael's testing
- schema

Looking into this more there are some conventions that can easily be followed and others that are more difficult to / counter productive to given the current state of our application and our databases.

We should definitely:
- prohibit spaces in names
- prohibit reserved words
- avoid abbreviations
- avoid acronyms
- 

These can be followed relatively easily:
- use underscores in table names
	- the model will still use `camelCase` however


I believe we should omit the following conventions:

- prefixing table names with the name of the application
	- parks_passport is the name of the database so any external references to our tables would be redundant: `parks_passport.parks_passport_collected_bucket_list_items(bucket_list_item)`
- no unique id for many-to-many
	- a user can visit a park/trail multiple times and these should be separate rows
- foreign key constraint naming
	- prisma has sensible rules for this already which are automatically followed
	- deviating from these rules will require manual boilerplate which requires maintainers to adhere to another requirement



