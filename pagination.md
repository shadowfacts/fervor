# Pagination
Some API endpoints which are resource-intensive to handle are paginated. The following options are supported for paginated endpoints.

The `limit` query parameter may also be used with any of the following options (except Specific IDs) to specify the maximum number of resources to return. Default is 20.

## Max ID
The `max_id` query parameter may be used to request resources immediately older than the given ID.

## Min ID
The `min_id` query parameter may be used to request resources immediately newer than the given ID.

## Since ID
The `since_id` query parameter may be used to request resources newer than the given ID.

**Note:** The `since_id` parameter does not return resources _immediately_ newer, so using this may result in a gap. See the examples below for a comparison with the `min_id` parameter.

## Specific IDs
Clients may request paginated resources with specific IDs.

The `ids` query parameter must be a comma-delimited string containing the numerical IDs for all the desired resources.

Server implementations may limit this endpoint to a maximum number of resources that may be requested at once. As such, clients should check that they received objects for all the IDs requested and perform another request if they haven't.

## Examples
Given resources with IDs 1 through 50, all of which are accessible by the requesting user, these options will have the following results:

### `max_id=20`
Resources with IDs 21 through 40 will be returned.

### `max_id=50`
No resources will be returned.

### `min_id=30`
Resources with IDs 10 through 29 will be returned.

### `min_id=1`
No resources wil be returned.

### `since_id=30`
Resources with IDs 1 through 19 will be returned.

Note that with this response, there is a gap between the ID requested in the parameter and the maximum ID returned by the server. Compare this with the result of the `min_id=30` request above.

### `since_id=1`
No resources will be returned.