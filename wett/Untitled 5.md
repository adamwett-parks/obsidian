Ahmed,

You are correct in your observations, but I will give you some lore to explain how the state of the schema got to where it is.

The redundant fields in `park` you mentioned were added before the `address` table was created. Originally, `address` was field on `park` and was a JSON string. This was a naive design decision and the mistake became very apparent when it was time to implement the admin portal during last semester.

I think we are on the same page when it comes to the correct way the schema should be designed. Feel free to remove the confusion. :)

Another intention behind the GPS coordinates is that they also serve as a backup if an `address` has a "bad street address". Think a trail head, a boat launch, something in the middle of the woods, etc. Originally, when we were just copying the info on the pages of the paper passport, only one was listed per park, and in my opinion were too vague to be useful. It was unclear what the coordinates of a park actually pointed to. We decided to add them to each address to make them actually useful.

I'd like to add that your team 