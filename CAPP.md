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
_cap._tcp.proxy.com. 86400 IN SRV 0 5 443 opencap.host.com.
```

For more information on SRV records view: https://en.wikipedia.org/wiki/SRV_record#Record_format

## Verifying a domain on the host server

Once the SRV record is added all incoming alias queries will be properly redirected to the host server. Assuming we already have an account on our host server we now need to change the domain our account is associated with on the host server. There are two steps:

1. Add a TXT record to proxy.com that will prove we are the owner
2. Notify the host server to check the TXT record and validate our ownership

The format of the TXT record that we need to add to proxy.com is as follows:

```javascript
cap: {jwt}
```

for example:

```javascript
cap: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
```

The JWT is simply the the JWT that is obtained via the /v1/auth endpoint in the ([CAMP](/CAMP.md)) sub-protocol. The JWT shouldn't expire for at least 30 minutes, which means we have 30 minutes to use the following endpoint to verify our ownership:

## POST /v1/domains

Authorization Bearer {jwt}

Associates a domain with the authenticated user

```javascript
HTTP/1.1
PUT /v1/domains
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.Kxy-elSGuiSzBv2s6JlqbFU3kxgOD-sg1fm7AgrRFDE
{
    "domain": "proxy.com",
    "master_jwt": ""  
}
```

Response:

```javascript
HTTP/1.1
200 OK
```

Once the server recieves the request, the server will do a lookup to get the TXT and SRV records from proxy.com and ensure two things: 

1. The TXT record has a valid JWT to prove that we own proxy.com
2. The SRV record has the proper format to forward alias lookups back to host.com

### Additional Users aliases on the proxy domain using "master_jwt"

Once one user has successfully verified a proxy domain, that first user's jwt can be used to add other aliases to the same proxy domain so that the TXT record doesn't need to be validated every time.

The user to be added would use the POST /v1/domain endpoint and authenticate as themself. They would pass in the first user's jwt as the "master_jwt" parameter to prove that they should be allowed to have an alias on the proxy domain as well.