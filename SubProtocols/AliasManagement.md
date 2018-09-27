# Alias Management

Alias Management is an optional protocol that allows a wallet to facilitate the creation, updating, and deletion of a user's alias resources on his/her server.

All following endpoints are required by the server to adhere to "Alias Management", while a wallet can choose which to support. The endpoints are served at the same location as designated by the [Address Query](/SubProtocols/AddressQuery.md) protocol. As such, the location must be found using a SRV lookup by the wallet.

## POST /v1/auth

No Authorization (Public method)

Authenticates a user to be able to use a JWT to authenticate at the other endpoints. The JWT must at least contain the expiration date.

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

## POST /v1/users

This endpoint is reserved to create new users on the server. The payload struture is not specified by the Alias Management protocol becasuse each server may have different user data requirements for signing up.
