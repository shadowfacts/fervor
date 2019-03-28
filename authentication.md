# Authentication
Fervor uses [OAuth 2](https://oauth.net/2/) for authentication so that clients don't need to store user credentials.

Basic steps for using OAuth 2 with Fervor:

1. Get the instance domain from the user.
2. Register your client with the Fervor API on that domain ([Client Registration](#client-registration)) to get the client ID and secret.
3. Navigate to the authorize endpoint to allow the user to log in ([Authorization Code Request](#authorization-code-request)) to generate the authorization code.
4. Request the access token from the server ([Access Token Request](#access-token-request)).

See the [Example](#example) below for a concrete example of the Fervor authentication flow.

**Notes**:

Servers may implement token expiration and refreshing, so clients should be capable of handling either possibility. See [Refreshing Access Tokens](#refreshing-access-tokens) for details.

Servers may implement password authentication for obtaining an access token, however, this should **only** be used for testing/in development. See [Password Authentication](#password-authentication) for details.

## Client Registration
To register your client with the server, make a POST request to `/api/v1/register`.

#### Parameters
Parameters should be sent as `application/x-www-form-urlencoded` in the POST body.

| Key            | Description                                                            | Required |
| -------------- | ---------------------------------------------------------------------- | -------- |
| `client_name`  | String. The name of your client. May be presented to the user.         | Yes      |
| `website`      | URL. The URL of your client or homepage. May be presented to the user. | No       |
| `redirect_uri` | URI. The URI the redirected to after a successful login.               | Yes      |

The redirect URI must not include a fragment, and may include query parameters. The redirect URI may be the special `urn:ietf:wg:oauth:2.0:oob` string to indicate that server should show the authorization code on the web page, instead of performing a redirect.

#### Response
An object with the client ID and secret to be used when retrieving an authorization code.

| Key             | Description                | Required |
| --------------- | -------------------------- | -------- |
| `client_id`     | String. The client ID.     | Yes      |
| `client_secret` | String. The client secret. | Yes      |

## Authorization Code Request
Now that you have a client ID and secret, the user needs to log in to the server to allow the client to access their account and a authorization code to be generated.

Navigate to `/oauth/authorize` in a web browser (either a browser embedded in a native application, or the user's web browser itself). The following query parameters should be included:

| Key             | Description                                                                                                         | Required |
| --------------- | ------------------------------------------------------------------------------------------------------------------- | -------- |
| `response_type` | String. Must be `code`.                                                                                             | Yes      |
| `client_id`     | String. The client ID received from the previous step.                                                              | Yes      |
| `redirect_uri`  | URI. The URI the user will be redirected to after a successful login. **Must** be the same as in the previous step. | Yes      |
| `state`         | Any. Session state/ID may be included here. If provided, the redirect will include the same query parameter value.  | No       |

The user will then be prompted to log in and approve your client. After this has happened, the user will be redirected (using a `302 Found` response with a `Location` header) back to the redirect URI you specified with the additional query parameter `code` containing the authorization code. If a `state` parameter was given to the request, the same value will be provided in the redirect's `state` query parameter.

## Access Token Request
### `POST /oauth/token`
Uses the client ID and secret to get an authorization code and 

#### Parameters
Parameters should be sent as `application/x-www-form-urlencoded` in the POST body.

| Key                  | Description                                                                     | Required |
| -------------------- | ------------------------------------------------------------------------------- | -------- |
| `grant_type`         | String. The grant type being used. See below for supported options.             | Yes      |
| `redirect_uri`       | URI. **Must** be the same as in the previous steps. Only used for verification. | Yes      |
| `client_id`          | String. The client ID received from the Client Registration step.               | Yes      |
| `client_secret`      | String. The client secret received from the Client Registration step.           | Yes      |

##### Supported Grant Types
1. `authorization_code`: Used to obtain an access token from an authorization code. The following parameters should also be included:
	- `authorization_code`: String. The authorization code received from the [Authorization Code Request](#authorization-code-request) step.
2. `refresh_token`: Used to refresh an existing access token that has expired. The following parameters should also be included:
	- `refresh_token`: String. The refresh token that was received from the previous Access Token response.
3. `password`: Used to obtain an access token from a users credentials. **Password grants should _never_ be used in production; they are available for development/testing purposes only.** The following parameters should also be included.
	- `username`: String. The user's username.
	- `password`: String. The user's password.

#### Successful Response
An object with information about the result of the access token request.

**Note for server implementers:** This response must include the `Cache-Control: no-store` and `Pragma: no-cache` headers to ensure that the response is not cached by the client.

| Key             | Description                                                                                                                     | Required    |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `access_token`  | String. The access token issued by the server.                                                                                  | Yes         |
| `token_type`    | String. The type of the issued token. Must be `bearer`.                                                                         | Yes         |
| `expires_in`    | Integer. The number of seconds the access token is valid for. If specified, `refresh_token` must also also be.                  | Recommended |
| `refresh_token` | String. The token that may be used to obtain a new access token when this one expires. If specified, `expires_in` must also be. | Recommended |

#### Unsuccessful Responses
Returned if the request is unsuccessful. See below for HTTP response codes and error types.

| Key                 | Description                                                   | Required |
| ------------------- | ------------------------------------------------------------- | -------- |
| `error`             | String. The type of the error. See below for possible values. | Yes      |
| `error_description` | String. A more detailed description of the error.             | Yes      |

##### 400 `invalid_request`
The request is missing parameters, has invalid values, or otherwise can't be processed.

##### 401 `invalid_client`
Authentication failed to an invalid client ID.

##### 400 `invalid_grant`
The given authorization code is invalid/expired or the `redirect_uri` did not match the originally specified.

##### 400 `unsupported_grant_type`
The `grant_type` used is not supported.

## Access Token Usage
All Fervor access tokens are Bearer tokens. To use them, include the `Authorization: Bearer <token>` header on all your requests (where `<token>` is your access token).

## Refreshing Access Tokens
Implementing access token expiration is optional, so clients must be able to handle either possibility.

If expiration/refreshing is implemented, the Access Token Response (see above) must include the `expires_in` and `refresh_token` parameters. After an access token expires, the refresh token will be used to obtain a new one. 

To obtain a new token, the [Access Token Request](#access-token-request) should be performed with the `refresh_token` grant type and parameter. A new access token will be received, and the new refresh token should be stored for future use.

## Password Authentication
Using password authentication is vastly less secure and defeats the purpose of OAuth. As such, it should **never** be used in production. Clients are **not** required to implement, however, they may do so for testing and development purposes.

To obtain an access token from user credentials, the client must first be registered as usual (see [Client Registration](#client-registration)) and then the [Access Token Request](#access-token-request) may be made with the `password` grant type and the `username`/`password` parameters.

## Example
This is an example of the complete authentication flow from beginning to end.

### Step 1: Get the server domain from the user
For this example, we'll use `fervor.example.com`

### Step 2: Register your client with the server.
For this example, we'll pretend we're building a native application which is already registered to handle the `fervorclient://` URL scheme. If you're building a web application, you'd use a normal URL for your application.

We'll send the following request:

```
POST /api/v1/register
Host: fervor.example.com
Content-Type: application/x-www-form-urlencoded

client_name=Example%20Client&redirect_uri=fervorclient://oauth
```

and get back the following response:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
	"client_id": "rH58aObIri1OTCSw2q0L",
	"client_secret": "5LfDsOyrDSmq6f4EHe3v"
}
```

### Step 3: Prompt the User to Log In
Next, we'll need to allow the user to log in to their account so that we can receive an authorization code.

To do this, we'll open the following URL in a web view inside our imaginary app:

```
https://fervor.example.com/oauth/authorize?response_type=code&client_id=rH58aObIri1OTCSw2q0L&redirect_uri=fervorclient://oauth
```

Once the user logs in and agrees to allow our app to interact with their account, the web view will be redirected to the following URL containing our authorization code.

```
fervorclient://oauth?code=ypqBbDdsOXUeYJFnlbT0
```

We can detect when we're redirected to `fervorclient://oauth` and close our web view, storing the authorization code from the `code` query parameter.

### Step 4: Exchange the Authorization Code for an Access Token
Now that we've got an authorization code, we can obtain an access token that can be used to interact with the Fervor API.

We'll make another request, this time to the token endpoint:

```
POST /oauth/token
Host: fervor.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&redirect_uri=fervorclient://oauth&client_id=rH58aObIri1OTCSw2q0L&client_secret=5LfDsOyrDSmq6f4EHe3v&authorization_code=ypqBbDdsOXUeYJFnlbT0
```

and we'll get back a response with an access token we can use:

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
	"access_token": "Bsltr6EAUIiAKCtw3ieg",
	"token_type": "bearer",
	"expires_in": 3600,
	"refresh_token": "Tr6xsiZN3dFKygaZQNlb"
}
```

We'll store the access token for use with API calls, and the refresh token for use in an hour when the current access token expires.

### Step 5: Making API Calls
We can now use our access token to make API calls:

```
GET /api/v1/instance
Host: fervor.example.com
Authorization: Bearer Bsltr6EAUIiAKCtw3ieg
```

and we'll get back an Instance object.

### Step 6: Refreshing the Token
After an hour has passed, our access token will have expired, so we'll need to generate a new one using the refresh token we received.

We'll once again make a request to the token endpoint, this time with the `refresh_token` grant type:

```
POST /oauth/token
Host: fervor.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&redirect_uri=fervorclient://oauth&client_id=rH58aObIri1OTCSw2q0L&client_secret=5LfDsOyrDSmq6f4EHe3v&refresh_token=Tr6xsiZN3dFKygaZQNlb
```

and we'll get back a new access token response:

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
	"access_token": "PbBBXlPNONhxhdkG6gUu",
	"token_type": "bearer",
	"expires_in": 3600,
	"refresh_token": "DyEC8hLOazgwLS7cUbBb"
}
```
