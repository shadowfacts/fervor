# Extensions
Since the Fervor API is designed to be a common foundation, it doesn't include all of the features that implementors might want. As such, it's designed to be extensible.

When extending the API, several factors should be taken into account to prevent naming conflicts.

## Extension Identifiers
Each extension should have its own unique identifier. It's recommended that these be reverse domain names (e.g. `com.example.fervor.tags`).

## Announcing Extensions
Implementations make their extensions known by including them in the [Extensions](./entities.md#extensions) sub-object in the [Instance](./entities.md#instance) object.

For each extension provided, there should be a field in the Extensions object with their identifier as the key and the URL of their specification/documentation as the value.

## Extended Entities
Extensions may include additional data in any of the [Entities](./entities.md). To avoid name conflicts, all extension data should be contained in a sub-object of the entity whose key is the extension identifier.

For example, an extended item object:

```json
{
	"id": 1,
	"feed_id": 1,
	"title": "Example",
	"url": "https://example.com",
	"com.example.fervor.tags": {
		"favorite": true,
		"tags": [1, 3]
	}
}
```

## Extended Endpoints
Extensions may also provide additional API endpoints that provide access to their specific data. To avoid name conflicts, all extension endpoints should be under `/api/<identifier>/`.

For example, the `com.example.fervor.tags` extension may provide a GET endpoint at `/api/com.example.fervor.tags/v1/tags` that returns an array of all tag objects.