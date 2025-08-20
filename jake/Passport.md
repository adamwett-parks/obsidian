
To fix:
entire park detail page
missing PassportHeader
check the NCDPR Info page??
bucket list missing
sort and filter dialog

hover colors for rounded button
Icon Legend
Hiking Essentials
my notes - entire flow
trails page needs more styling updates

[Feldmann, Jacob C: Hey Jake, here is a link to some naming standards I proposed a ... | Chat | Microsoft Teams](https://teams.microsoft.com/l/message/19:3bf73de2-a095-42b4-8df3-5d0c2f6163c9_5b4e3b8e-b03c-447b-a54a-d49c1b0b41b1@unq.gbl.spaces/1752248180437?context=%7B%22contextType%22%3A%22chat%22%7D)

Hey Jake, here are some comments from a brief review. If we want to review further when I get back, could I get a copy of the requirements you guys wrote down for the project? Or could you let me know where they are?

I hope you have a great weekend! See you later!

## Schema Review  
Even though I worked on this project with you guys last semester, I am not as familiar with the requirements as you guys are. Therefore, I'd say you guys have better authority in the final schema design. However, here are some things to consider.

### Schema  
* If there's any way to get the ORM not to select everything, I do think it would be good to absorb the geojson tables into the parks and trails tables.  
* Can trail_geojson and park_geo_json become one table?  
* Can a user collect a stamp multiple times? If so, could the stamp_c table be combined with user_visits? I think a manual stamp collection can count as a manual visit too, if that's the hesitation.  
* Some of the created, updated, and deleted may be unnecessary. I'm mainly thinking for the visits, stamp collections, and bucket list item completions.  
* For bulis_c (and any other many-to-many linking table) we could make the primary key a composite key of the 2 foreign keys, rather than a new surrogate key. This reduces data but it's not a huge deal.

### Naming  
* for bulis_c, can we make the keys from users and bulis named users_id and bulis_id? (really I'd prefer users be called user and the foreign key on bulis_c called user_id) This would make it a little simpler to see the fields that connect them.  
* My preferences for naming are in that doc I sent. They aren't rules you have to follow though