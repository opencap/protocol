# CAPP - Crypto Alias Proxy Protocol

CAPP is a sub-protocol that allows us to use a CAPP compliant server as a host for a separate domain name. A good comparison would be when a company uses a service like gmail to handle email hosting, but points their own domain at the service. For instance, a company like Walmart uses gmail but has a email addresses ending in "@walmart.com"

For the purposes of this documentation we refer to the CAPP server that runs the API the "host server". The domain that is being pointed towards that host will be called the "proxy domain".

## Pointing the proxy domain to the host server

The first step to setup a proxy domain is to add a SRV record that points to the host server so that when wallets query the proxy domain they can be redirected to the proper host.

The format of the SRV record is as follows:

```javascript
_service._proto.name. TTL class SRV priority weight port target.
```

Here is an example SRV record:

```javascript
_cap._tcp.proxy.tld. 86400 IN SRV 0 5 443 opencap.host.tld.
```

For more information on SRV records view: https://en.wikipedia.org/wiki/SRV_record#Record_format

## Verifying a domain on the host server

Once the SRV record is added all incoming alias queries will be properly redirected to the host server. Now we need to:

1. Add a TXT record to proxy.tld that will prove we are the owner
2. Create an account on the host server with an alias that uses our proxy domain
3. Validate to the host that we are the owner of the proxy domain

### Create an account on the host server

The following endpoint must be supported by CCAP servers, it allows users to create accounts associated with a proxy domain.

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

Because we created an account with a proxy domain, we will be unable to create addresses until we verify that we own the proxy.

### Validate to the server that we own the proxy

The format of the TXT record that we need to add to proxy.tld is as follows:

```javascript
cap: {jwt}
```

for example:

```javascript
cap: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
```

The JWT is simply the the JWT that is obtained via the /v1/auth endpoint in the [CAMP](/CAMP.md) sub-protocol. The JWT shouldn't expire for at least 30 minutes, which means we have 30 minutes to use the following endpoint to verify our ownership:

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

Once the server recieves the request, the server will do a lookup to get the TXT and SRV records from proxy.tld (which is specified in the user's alias in the JWT) and ensure two things:

1. The TXT record has a valid JWT to prove that the user owns proxy.tld
2. The SRV record has the proper format to forward alias lookups back to host.tld

### Adding additional users to the proxy domain

Once one user has successfully verified a proxy domain, that user is considered an owner of the proxy domain. An owner can use the same create proxy user endpoint to create additional users that are associated with the proxy.

These new users do not need to update the TXT record to validate ownership, they are automatically validated. In order to become a domain owner however, the new user would need to use the PUT /v1/users/proxy endpoint.

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