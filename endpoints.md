# API Endpoints

## Server Instance
### `GET /api/v1/instance`
Returns an Instance object containing information about this instance and this Fervor implementation.

**This endpoint does not require authentication.** It should be used by clients prior to completing the login/authentication flow to ensure that the server implementation is compatible with the client.

## Groups
### `GET /api/v1/groups/`
Returns an array of all available Group objects.

### `GET /api/v1/groups/:id`
Returns the Group object for the given ID.

### `GET /api/v1/groups/:id/feeds`
Returns an array of all Feed objects belonging to the group with the given ID.

Equivalent to retrieving the Group object and then looking up all the feeds specified by its `feed_ids`.

### `GET /api/v1/groups/:id/items`
[Paginated](./pagination.md).

Returns an array of the most recent Items (read or unread) from all the feeds in this group.

#### Parameters
| Key    | Description                                                                                                  | Required |
| ------ | ------------------------------------------------------------------------------------------------------------ | -------- |
| `only` | String. One of `read` or `unread`. Only the given type of items will be returned, or both types, if omitted. | No       |

## Feeds
### `GET /api/v1/feeds/`
Returns an array of all available Feed objects.

### `GET /api/v1/feeds/:id`
Returns the Feed object for the given ID.

### `GET /api/v1/feeds/:id/items`
[Paginated](./pagination.md).

Returns an array of the most recent Items (read or unread) from this feed.

#### Parameters
| Key    | Description                                                                                                  | Required |
| ------ | ------------------------------------------------------------------------------------------------------------ | -------- |
| `only` | String. One of `read` or `unread`. Only the given type of items will be returned, or both types, if omitted. | No       |

## Items
### `GET /api/v1/items/:id`
Returns the Item object for the given ID.

### `POST /api/v1/items/:id/read`
Marks the Item with the given ID as read.

### `POST /api/v1/items/:id/unread`
Marks the Item with the given ID as unread.