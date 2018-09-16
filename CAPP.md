# CAPP - Crypto Alias Proxy Protocol

CAPP is a sub-protocol that allows a CAPP compliant server to be used as a host for a separate domain name. A good comparison would be when a company uses a service like gmail to handle email hosting, but points their own domain at the service. For instance, a company like Walmart uses gmail but has its email addresses ending in "@walmart.com"

For the purposes of this documentation we refer to the CAPP server that runs the API the "host server". The domain that is being pointed towards that host will be called the "proxy domain".

## Pointing the proxy domain to the host server

The first step to setup a proxy domain is to add a SRV record that points to the host server so that when wallets query the proxy domain they can be redirected to the proper host.

The format of the SRV record is as follows:

```javascript
_service._proto.name. TTL class SRV priority weight port target.
```

Here is an example SRV record:

```javascript
_opencap._tcp.proxy.tld. 86400 IN SRV 0 5 443 opencap.host.tld.
```

Once the SRV record is added all incoming alias queries will be properly redirected to the host server.

For more information on SRV records view: https://en.wikipedia.org/wiki/SRV_record#Record_format

### Create an account on the host server

#### POST /v1/users

This endpoint is reserved to create a new user or proxy user on the host server. The payload struture is not specified by the CAMP protocol becasuse each server may have different user data requirements for signing up.

It is possible for two users to have the same username on the host server as long as they have different domains. In other words aliases must be unique, usernames don't need to be.

#### Verify that the new user is the owner of the proxy domain

Before activiting a new user that is using a proxy domain, a host server should check that this user has permission to have an alias using the proxy domain. There are many ways this can be done, here we descirbe one effective way that a host server can verify domain ownership. This method is not required by the CAPP protocol but is given as an example.

1. After a new proxy user account is created, the host server supplies the new user with a JWT that is associated with the new user and is signed by the server. This JWT could be similar (but not the same, for security purposes) to the JWT used for logging in.

2. The user is instructed to create a TXT record associated with their proxy domain that has the following format:

   ```javascript
   opencap-domain-verification={jwt}
   ```

   for example:

   ```javascript
   opencap-domain-verification=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFsaWNlIiwiZG9tYWluIjoiZG9tYWluLnRsZCIsImlhdCI6MTUxNjIzOTAyMn0.KxyelSGuiSzBv2s6JlqbFU3kxgODsg1fm7AgrRFDE;
   ```

3. After the TXT record has been added to the proxy domain, the user notifies the host server. The host server then checks to make sure that the TXT record exists with the proper JWT.

4. This proves that the user is the owner of the domain.

   Now this user can called a "domain owner". A domain owner is now allowed to create other users on the host server associated with the proxy domain
