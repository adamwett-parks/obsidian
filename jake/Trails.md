```
for feature in features {
	var associatedTrail = trails[feature.attributes.TRAILNAME]
	if (trails[feature.attributes.TRAILNAME] != undefined) {
		trails.create(feature)
	}
	associatedTrail.length += feature.attributes.LENGTH
	if (!associatedTrail.blaze.contains(feature.attributes.Blaze)
		&& feature.attributes.Blaze != " ") {
		if (!associatedTrail.blaze.isNullOrEmptyString)
			associatedTrail.blaze.append(", ")
		associatedTrail.blaze.append(feature.attributes.Blaze)
	}
	const uses = feature.attributes.USE_.split(" ")
	for use in uses {
		if (!associatedTrail.icons.includes(use))
			associatedTrail.icons.create(use)
	}
}
```