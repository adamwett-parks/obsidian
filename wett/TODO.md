-  michael's testing
- schema

Looking into this more it seems like Prisma has pretty solid naming conventions, and that deviating from them would add more boilerplate and complexity. Some of these would be impossible to follow using MariaDB. 

For example: `FK__collected_bucket_list_item__bucket_list_item_id` exceeds MariaDB's limit of 