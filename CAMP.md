# CAMP - Crypto Alias Management Protocol

CAMP is a sub-protocol of OpenCap. CAMP is an optional protocol that allows a server to facilitate the creation, updating, and deletion of a user's alias resources. For a server to be CAMP compliant it must also be CAP compliant by serving aliases, CAMP is essentially a "tier 2" protocol built on top of CAP to add some more features.

The following endpoints are required:

## POST /v1/domains/{domain}/users

No Authorization (Public method)

Creates a new user. The new user is associated with the host's domain by default. This only changes if the endpoint described in the ([CAPP](/CAPP.md)) sub-protocol is used. The user<->domain relationship is one-to-one, although one server can have users with the same name as long as they are on different domains.

Request:

```javascript
HTTP/1.1
POST /v1/domains/domain.tld/users
Content-Type: application/json
{
    "username": "alice",
    "password": "mysecretpassword"
}
```

Response:

```javascript
HTTP/1.1
200 OK
```

## POST /v1/auth

No Authorization (Public method)

Authenticates a user to be able to use a JWT to authenticate at the other endpoints. The JWT should contain enough information to uniquely identify the user which would typically be the user's username and domain. The JWT must not expire for at least thirty minutes.

Request:

```javascript
HTTP/1.1
POST /v1/auth
Content-Type: application/json
{
    "username": "alice",
    "domain": "ogdolo.com",
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

## DELETE /v1/domains/{domain}/users

Authorization Bearer {jwt}

Deletes the authorized user and all associated addresses.  

Request:

```javascript
HTTP/1.1
DELETE /v1/domains/domain.tld/users HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE

```

Response:

```javascript
HTTP/1.1
200 OK
```

## PUT /v1/addresses

Authorization Bearer {jwt}

Adds or updates the address of the authenticated user for the given asset id.  

Request:

```javascript
HTTP/1.1
PUT /v1/addresses
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
{
    "type": 0,
    "asset": 1,
    "address": "XKdD14CTd6BNYerDzfkqFDilogkaqdbZpaYq6EqxuQ8="
}
```

Response:

```javascript
HTTP/1.1
200 OK
```

## DELETE /v1/addresses/{asset}

Authorization Bearer {jwt}

Deletes the address for authorized user of the given asset.

Request:

```javascript
HTTP/1.1
DELETE /v1/addresses/1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
```

Response:

```javascript
HTTP/1.1
200 OK
```