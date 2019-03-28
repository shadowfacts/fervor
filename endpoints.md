# API Endpoints

## Note on Responses
All successful API calls must result in an HTTP 200 OK response whose body is the UTF-8 encoded JSON of the response data.

Error responses must result in non-200 response (it is recommended to use the appropriate HTTP error code when possible) whose body is a JSON object structured like so:

| Key                 | Description                                                                                   | Required |
| ------------------- | --------------------------------------------------------------------------------------------- | -------- |
| `error`             | String. The main error message. Should be fairly short and should be presentable to the user. | Yes      |
| `error_description` | String. A more verbose/technical description of the error.                                    | No       |

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

#### Query Parameters
| Key    | Description                                                                                                  | Required |
| ------ | ------------------------------------------------------------------------------------------------------------ | -------- |
| `only` | String. One of `read` or `unread`. Only the given type of items will be returned, or both types, if omitted. | No       |

### `POST /api/v1/groups/create`
Creates a new Group from the given parameters.

#### Parameters
Parameters should be sent as `application/x-www-form-urlencoded` in the POST body.

| Key        | Description                                                                                     | Required |
| ---------- | ----------------------------------------------------------------------------------------------- | -------- |
| `title`    | String. The title of this group.                                                                | Yes      |
| `feed_ids` | String of comma-delimited Integers. The IDs of any already existing feeds to add to this group. | No       |

#### Response
If group creation was successful, the server will return the new Group object.

**Note:** Specific server implementations may require additional information to be provided, so a request with the bare minimum parameters may not be successful.

### `POST /api/v1/groups/:id/delete`
Deletes the group with the given ID. In some server implementations, this request may also delete all feeds belonging to the deleted group.

No parameters.

If successful, the server will respond with an HTTP 410 Gone response.

## Feeds
### `GET /api/v1/feeds/`
Returns an array of all available Feed objects.

### `GET /api/v1/feeds/:id`
Returns the Feed object for the given ID.

### `GET /api/v1/feeds/:id/items`
[Paginated](./pagination.md).

Returns an array of the most recent Items (read or unread) from this feed.

#### Query Parameters
| Key    | Description                                                                                                  | Required |
| ------ | ------------------------------------------------------------------------------------------------------------ | -------- |
| `only` | String. One of `read` or `unread`. Only the given type of items will be returned, or both types, if omitted. | No       |

### `POST /api/v1/feeds/create`
Creates a new Feed from the given parameters.

#### Parameters
Parameters should be sent as `application/x-www-form-urlencoded` in the POST body.

| Key         | Description                                                                                                  | Required |
| ----------- | ------------------------------------------------------------------------------------------------------------ | -------- |
| `feed_url`  | URL. The URL of the RSS document for this feed.                                                              | Yes      |
| `group_ids` | String of comma-delimited Integers. The IDs of any already existing groups that this feed should be part of. | No       |

#### Response
If feed creation was successful, the server will return the new Feed object.

**Note:** Specific server implementations may require additional information to be provided, so a request with the bare minimum parameters may not be successful.

### `POST /api/v1/feeds/:id/delete`
Deletes the feed with the given ID.

No parameters.

If successful, the server will return respond with an HTTP 410 Gone response.

## Items
### `GET /api/v1/items`
[Paginated](./pagination.md).

Returns an array of all the Items that belong to the user.

#### Query Parameters
| Key    | Description                                                                                                  | Required |
| ------ | ------------------------------------------------------------------------------------------------------------ | -------- |
| `only` | String. One of `read` or `unread`. Only the given type of items will be returned, or both types, if omitted. | No       |

### `GET /api/v1/items/:id`
Returns the Item object for the given ID.

### `POST /api/v1/items/:id/read`
Marks the Item with the given ID as read.

### `POST /api/v1/items/:id/unread`
Marks the Item with the given ID as unread.

### `POST /api/v1/items/read`
Marks all Items with the given IDs as read.

Server implementations may optionally enforce a limit of how many posts may be marked as read with one API call.

#### Query Parameters
| Key   | Description                                                                   | Required |
| ----- | ----------------------------------------------------------------------------- | -------- |
| `ids` | String of comma delimited Integers. The IDs of all the posts to mark as read. | Yes      |

#### Response
Returns an array of all the item IDs that were _successfully_ marked as read.

### `POST /api/v1/items/unread`
Marks all Items with the given IDs as unread.

Server implementations may optionally enforce a limit of how many posts may be marked as unread with one API call.

#### Query Parameters
| Key   | Description                                                                     | Required |
| ----- | ------------------------------------------------------------------------------- | -------- |
| `ids` | String of comma delimited Integers. The IDs of all the posts to mark as unread. | Yes      |

#### Response
Returns an array of all the item IDs that were _successfully_ marked as unread.