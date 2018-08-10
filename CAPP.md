# CAPP - Crypto Alias Proxy Protocol

CAPP is a sub-protocol that allows us to use a CAPP compliant server as a host for a separate domain name. For example, lets pretend that alice has a business that owns a website hosted on "business.com". Alice wants to use ogdolo.com as the server to host her CAP/CAMP alias but she wants to use the alias alice@business.com instead of alice@ogdolo.com. If ogdolo.com is a CAPP server, she will be able to.

For the purposes of this documentation we refer to the CAPP server that runs the API the "host server". The domain that is being pointed towards that host will be called the "proxy domain".

## Pointing the proxy domain to the host server

The first step to setup a proxy domain is to add a CNAME record that points to the host server from the proxy domain. The CNAME record has the following format:

```javascript
opencap.proxy.tld.  CNAME  opencap.host.tld.
```

For more information on CNAME records view: https://en.wikipedia.org/wiki/CNAME_record

## Verifying a domain on the host server

Once the CNAME record is added all incoming alias queries will be properly redirected to the host server. The next steps are:

1. Add a TXT record to proxy.tld that will prove ownership of the user
2. Create an account on the host server with an alias that is associated with the proxy domain
3. Validate to the host that the user owns the domain by using a TXT record

### Create an account on the host server

#### POST /v1/users/proxy

CAPP servers must allow some way for users to be created with a separate domain, this endpoint is reserved for that purpose. The payload struture is NOT specified by the CAMP protocol becasuse each server may have different user data requirements for signing up. Typically this endpoint should be accessed via a client supplied by the CAMP server so there is no confusion.

```javascript
HTTP/1.1
POST /v1/users/proxy
Content-Type: application/json
{
    "username": "alice",
    "domain": "proxy.tld",
    "password": "mysecretpassword"
}
```

It is possible for two users to have the same username on the host server as long as they have different domains. In other words aliases must be unique, usernames don't need to be.

### Validate to the host server that the user owns the proxy domain

The format of the TXT record that is added to proxy.tld is as follows:

```javascript
opencap-jwt-verification={jwt}
```

for example:

```javascript
opencap-jwt-verification=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
```

The JWT is the same one that is obtained via the /v1/auth endpoint in the [CAMP](/CAMP.md) sub-protocol. The JWT shouldn't expire for at least 30 minutes, which means the user has at least 30 minutes to add the JWT to the TXT record and hit the following required endpoint:

#### PUT /v1/users/proxy

Authorization Bearer {jwt}

```javascript
HTTP/1.1
PUT /v1/users/proxy
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
{
    // No request body
}
```

Response:

```javascript
HTTP/1.1
200 OK
```

Once the server recieves the request, the server will do a lookup to get the TXT and CNAME records from proxy.tld (which is specified in the user's alias in the JWT) and ensure two things:

1. The TXT record has a valid JWT to prove that the user owns proxy.tld
2. The CNAME record has the proper format to forward alias lookups back to host.tld

### Adding additional users to the proxy domain

Once one user has successfully verified a proxy domain, that user is considered an owner of the proxy domain. An owner can use the same POST /v1/users/proxy endpoint to create additional users that are associated with the proxy. This is done by simply adding an Authorization header with the token of the owner.

These new users do not need to update the TXT record to validate ownership, they are automatically validated. In order to become a domain owner however, the new user would need to use the PUT /v1/users/proxy endpoint.

The following is an example of what the payload could look like. Again, this endpoint is required, but the payload is not specified because different servers will require different information at sign-up. This endpoint would typically be accessed via a client supplied by the host server.

Request:

```javascript
HTTP/1.1
POST /v1/users/proxy
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
Content-Type: application/json
{
    "username": "susan",
    "domain": "proxy.tld",
    "password": "susanpassword"
}
```

Response:

```javascript
HTTP/1.1
200 OK
```
