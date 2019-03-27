# Entities
All entities are UTF-8 encoded JSON objects.

## Notes
Object IDs are positive integers.

Date and DateTime fields are encoded as [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) strings.

## Instance
An Instance object provides information about the instance intself and the specific implementation of Fervor that is being used.

| Key                      | Description                                               | Required |
| ------------------------ | --------------------------------------------------------- | -------- |
| `name`                   | String. The user-friendly name of this instance.          | Yes      |
| `url`                    | URL. The user-friendly URL of this instance.              | Yes      |
| `version`                | String. The Fervor version this software uses.            | Yes      |
| `implementation_name`    | String. The name of the softwre this instance is running. | Yes      |
| `implementation_version` | String. The version of software this instance is running. | Yes      |
| `extensions`             | [Extensions object](#extensions).                         | No       |

### Extensions
The extensions object provides additional information about extension to the Fervor API this instance provides.

There should be a field in the object for each supported extension. The key should be a unique identifier for the extension (reverse domain name style is recommended) and the value should be a URL for the extension specification or API documentation.

See the [Extensions](./extensions.md) page for more information.

## Feed
An RSS feed.

| Key            | Description                                                   | Required |
| -------------- | ------------------------------------------------------------- | -------- |
| `id`           | Positive Integer. The ID of this feed.                        | Yes      |
| `title`        | String. The title/name of this feed.                          | Yes      |
| `url`          | URL. The URL of the website this feed belongs to.             | Yes      |
| `service_url`  | URL. The URL of this feed on the aggregation service.         | No       |
| `feed_url`     | URL. The URL of the RSS document itself.                      | Yes      |
| `last_updated` | DateTime. The last time this feed was updated by the service. | Yes      |
| `group_ids`    | Array of IDs. The IDs of the groups that contain this feed.   | Yes      |


## Item
An item is an individual item from an RSS feed.

| Key           | Description                                                        | Required |
| ------------- | ------------------------------------------------------------------ | -------- |
| `id`          | ID. The ID of this item.                                           | Yes      |
| `feed_id`     | ID. The ID of the feed to which this item belongs.                 | Yes      |
| `title`       | String. The title of this item.                                    | Yes      |
| `author`      | String. The author of this item.                                   | No       |
| `published`   | DateTime. The date this item was published.                        | No       |
| `created_at`  | DateTime. The date this item was added to the aggregation service. | No       |
| `content`     | HTML String. The content of this item.                             | No       |
| `summary`     | HTML String. The summar of this item.                              | No       |
| `url`         | URL. The original URL of this item.                                | Yes      |
| `service_url` | URL. The URL of this item on the aggregation service.              | No       |
| `read`        | Boolean. Whether this item has been marked as read..               | No       |

## Group
A group contains multiple feeds.

Feeds may or may not belong to multiple groups, depending on the service implementation.

| Key            | Description                                                   | Required |
| -------------- | ------------------------------------------------------------- | -------- |
| `id`           | ID. The ID of this group.                                     | Yes      |
| `title`        | String. The title/name of this group.                         | Yes      |
| `feed_ids`     | Array of IDs. The IDs of the feeds that this group contains.  | Yes      |