# Alias Management

Alias Management is an optional protocol that allows a wallet to facilitate the creation, updating, and deletion of a user's alias resources on his/her server.

If a server is to support "Alias Management" at all, it must support all of the following endpoints. In contrast, a wallet/client can choose which endpoints to support. The endpoints are served at the same location as designated by the [SRV Record](/API/AddressQuery.md#1-a-srv-record-on-the-aliass-domain) in the [Address Query](/API/AddressQuery.md) protocol.

All endpoints must be served over HTTPS on port 443.

## POST /v1/auth

No Authorization (Public method)

Returns a JWT to authenticate at the other endpoints. The JWT must contain the expiration datetime. The authorization token is submitted as a header in the other requests.

Request:

```javascript
POST /v1/auth
Content-Type: application/json
{
    "alias": "alice$domain.tld",
    "password": "mysecretpassword"
}
```

Response:

```javascript
200 OK
Content-Type: application/json
{
    "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE"
}
```

The decoded payload of the jwt must at least contain the expiration date. The format is a Unix Time Stamp in UTC.

```javascript
{
  "exp": 1541031979,
}
```

## PUT /v1/addresses

Authorization Bearer {jwt}

Adds or updates the address of the authenticated user for the given address type.

Request:

```javascript
PUT /v1/addresses
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
{
    "address_type": 100,
    "address": "1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2"
}
```

Response:

```javascript
200 OK
```

## DELETE /v1/addresses/{address_type}

Authorization Bearer {jwt}

Deletes the address for authorized user of the given address_type.

Request:

```javascript
DELETE /v1/addresses/100
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
```

Response:

```javascript
200 OK
```

## POST, DELETE, etc... /v1/users

This endpoint is reserved for user management. Payload strutures are not specified by the Alias Management protocol becasuse each server may have different user data requirements for signing up, deleteing, etc.

## Error Handling

Errors are handled the same way as "Address Query" seen [here](/API/AddressQuery.md#error-handling)
