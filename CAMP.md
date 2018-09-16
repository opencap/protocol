# CAMP - Crypto Alias Management Protocol

CAMP is a sub-protocol of OpenCap. CAMP is an optional protocol that allows a server to facilitate the creation, updating, and deletion of a user's alias resources. For a server to be CAMP compliant it must also be CAP compliant by serving aliases, CAMP is essentially a "tier 2" protocol built on top of CAP to add some more features.

The following endpoints are required:

## POST /v1/users

This endpoint is intended to create new users on the server. The payload struture is NOT specified by the CAMP protocol becasuse each server may have different user data requirements for signing up. The minimum is that each user be given a username and password. The username defines the first part of the alias, for example:

username$domain.tld

Here is what an example payload could look like, but typically this endpoint should be accessed via a client supplied by the CAMP server so there is no confusion. The idea is that users will typically create their account using their provider's website or client, then any OpenCAP wallet (client) can manage that account. The majority of the managerial duties are simply to login and update addresses periodically.

Request:

```javascript
HTTP/1.1
POST /v1/users
Content-Type: application/json
{
    "alias": "alice$domain.tld",
    "password": "mysecretpassword"
    // Additional info that is server specific
}
```

Response:

```javascript
HTTP/1.1
200 OK
```

## POST /v1/auth

No Authorization (Public method)

Authenticates a user to be able to use a JWT to authenticate at the other endpoints. The JWT must at least contain the alias of the user. The JWT must not expire for at least thirty minutes.

Request:

```javascript
HTTP/1.1
POST /v1/auth
Content-Type: application/json
{
    "alias": "alice$domain.tld",
    "password": "mysecretpassword"
}
```

Response:

```javascript
HTTP/1.1
200 OK
Content-Type: application/json
{
    "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE"
}
```

## DELETE /v1/users

Authorization Bearer {jwt}

Deletes the authorized user for the authorized domain and all associated addresses.

Request:

```javascript
HTTP/1.1
DELETE /v1/users HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
```

Response:

```javascript
HTTP/1.1
200 OK
```

## PUT /v1/addresses

Authorization Bearer {jwt}

Adds or updates the address of the authenticated user for the given address type.

Request:

```javascript
HTTP/1.1
PUT /v1/addresses
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
{
    "address_type": 100,
    "address": "1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2"
}
```

Response:

```javascript
HTTP/1.1
200 OK
```

## DELETE /v1/addresses/{address_type}

Authorization Bearer {jwt}

Deletes the address for authorized user of the given address_type.

Request:

```javascript
HTTP/1.1
DELETE /v1/addresses/100
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
```

Response:

```javascript
HTTP/1.1
200 OK
```
