Ahmed,

You are correct in your observations, but I will give you some lore to explain how the state of the schema got to where it is.

The redundant fields in `park` you mentioned were added before the `address` table was created. Originally, `address` was field on `park` and was a JSON string. This was a naive design decision and the mistake became very apparent when it was time to implement the admin portal during last semester.

That being said, I think we are on the same page when it comes to the correct way the schema should be designed.

The GPS coordinates also serve as a backup if an `address` has a vague location or poor routing on Google Maps