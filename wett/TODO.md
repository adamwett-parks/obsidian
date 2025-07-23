-  michael's testing
- schema

I think we can adhere to these requirements except the following:

- prefixing table names with the name of the application
	- parks_passport is the name of the database so any external references to our tables would be redundant: `parks_passport.parks_passport_collected_bucket_list_items(bucket_list_item)`
- no unique id for many-to-many
	- a user can visit a park/trail multiple times and these should be separate rows
- foreign key constraint naming
	- prisma has sensible rules for this already which are automatically followed
	- deviating from these rules adds another layer of complexity

