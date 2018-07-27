# CAMP - Crypto Alias Management Protocol

CAMP is a sub-protocol of OpenCap. CAMP is an optional protocol that allows a server to facilitate the creation, updating, and deletion of a user's alias resources. For a server to be CAMP compliant it must also be CAP compliant by serving aliases, CAMP is essentially a "tier 2" protocol built on top of CAP to add some more features.

The following endpoints are required:

## POST /v1/domains/{domain}/users

No Authorization (Public method)

Creates a new user. The new user is associated with the host's domain by default. This only changes if the endpoint described in the ([CAPP](/CAPP.md)) sub-protocol is used. The user<->domain relationship is one-to-one, although one server can have users with the same name as long as they are on different domains.

Request:
```
POST /domains/{domain}/users HTTP/1.1

{
    "username": "alice",
    "password": "mysecretpassword"
}
```

Response:
```
HTTP/1.1 200 OK

```


## GET /v1/domains/{domain}/users/{username}/token

Basic Authentication (username + password)

Authenticates a user to be able to use a JWT to authenticate at the other endpoints. The JWT should contain enough information to uniquely identify the user which would typically be the user's username and domain. The JWT must not expire for at least thirty minutes.

Request:
```
GET /domains/domain.tld/users/alice/token HTTP/1.1
Authorization: Basic YWxpY2U6bXlzZWNyZXRwYXNzd29yZA==

```

Response:
```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE"
}
```

## DELETE /v1/domains/{domain}/users/{username}

Authorization Bearer {jwt}

Deletes the specified user and all associated addresses.  
Token username must match with username in path parameter.

Request:
```
DELETE /domains/domain.tld/users/alice HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE

```

Response:
```
HTTP/1.1 200 OK

```

## PUT /v1/domains/{domain}/users/{username}/assets/{asset_id}

Authorization Bearer {jwt}

Adds or updates the address of the authenticated user for the given asset id.  

Request:
```
PUT /domains/domain.tld/users/alice/assets/1 HTTP/1.1
Authorization: earer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE

{
    "asset": 1
    "type": 0,
    "address": "XKdD14CTd6BNYerDzfkqFDilogkaqdbZpaYq6EqxuQ8="
}
```

Response:
```
HTTP/1.1 200 OK

```

## DELETE /v1/domains/{domain}/users/{username}/assets/{asset_id}

Authorization Bearer {jwt}

Deletes the address for the user of the given asset.  
Token username must match with username in path parameter.

Request:
```
DELETE /domains/domain.tld/users/alice/assets/1 HTTP/1.1
Authorization: earer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE

```

Response:
```
HTTP/1.1 200 OK

```