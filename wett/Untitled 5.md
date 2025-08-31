Ahmed,

You are correct in your observations, but I will give you some lore to explain how the state of the schema got to where it is.

The redundant fields in `park` you mentioned were added before the `address` table was created. Originally, `address` was field on `park` and was a JSON string. This was a naive design decision, and this mistake