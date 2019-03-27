# Fervor API
Fervor is a open standard for a self-hosted RSS aggregator API. It's primary goal is to replace the current de-facto standard, the [Fever API](https://feedafever.com/api).

The Fever API has a number of issues, including:

1. Using MD5 for authentication.
2. Poor design (e.g. `group` and `feeds_group` being separate entities.)
3. Poor documentation (the difference between `group` and `feeds_group` is never articulated.)
4. No longer being maintained.

The Fervor API is intended to be a common foundation for self-hosted RSS aggregators, not a catch-all solution. Different services will have their own specific needs from an API, so Fervor is designed to be only the component that is common to all of RSS aggregator implementations.

## Specification
The specification is divided into the following sections:

- [Authentication](https://github.com/shadowfacts/fervor/blob/master/authentication.md)
- [Endpoints](https://github.com/shadowfacts/fervor/blob/master/endpoints.md)
- [Entities](https://github.com/shadowfacts/fervor/blob/master/entities.md)
- [Extensions](https://github.com/shadowfacts/fervor/blob/master/extensions.md)
- [Pagination](https://github.com/shadowfacts/fervor/blob/master/pagination.md)

## Versioning
The Fervor API uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Minor fixes increment the patch number, minor additions increment the minor number, and any removals increment the major number.

The current Fervor version is 0.1.0. The major version 0 will remain until the specification is finished and no longer a draft.

## Known Implementations
### Clients
- 

### Servers
- 