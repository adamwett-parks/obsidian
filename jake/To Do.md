## Short Term
- [x] Update Direct Deposit
- [ ] Update parking
- [x] Check travel reimbursement progress
- [ ] Blanket TA - done
- [x] [SDC Tasks](https://ncparks-dbteam.atlassian.net/browse/PA-1196)
	- Migrate, seed, migrate, edit, dump, rename dump to seed
	- [[Passport]]
- [ ] Update Figma template from Jira notes
	- [ ] Colors
- [ ] PDF Accessibility Video
- [ ] User Guides
	- [ ] Follow-up on SharePoint question for user guides
	- [ ] Transfer all PDF/Word user guides to SharePoint
	- [ ] Reach out to Aimee McGuiness
	- [ ] Reach out to Kris Anne's recommended contact
- [ ] [[Teams|Teams reorganization]]

## Longer Term
- [ ] [[Switching to Mac]]
- [ ] Reorganize SharePoint site
- [ ] [[M365|M365 Research]]



SELECT

p.abbreviation AS park_abbreviation,

p.name AS park_name,

SUBSTRING_INDEX(

REPLACE(REPLACE(p.coordinates, 'POINT (', ''), ')', ''),

' ',

-1

) AS park_latitude,

SUBSTRING_INDEX(

REPLACE(REPLACE(p.coordinates, 'POINT (', ''), ')', ''),

' ',

1

) AS park_longitude,

a.latitude AS address_latitude,

a.longitude AS address_longitude,

FROM park p

JOIN address a

ON p.address_id = a.id;