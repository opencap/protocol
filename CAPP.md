# CAPP - Crypto Alias Proxy Protocol

CAPP is a sub-protocol that allows us to use a CAPP compliant server as a host for a separate domain name. A good comparison would be when a company uses a service like gmail to handle email hosting, but points their own domain at the service. For instance, a company like Walmart uses gmail but has a email addresses ending in "@walmart.com"

For the purposes of this documentation we refer to the CAPP server that runs the API the "host server". The domain that is being pointed towards that host will be called the "proxy domain".

The proxy feature is the reason why the "domain" parameter is used by CAP and CAMP servers. These servers, even if they don't support acting as a proxy, all must at least be able to communicate that they are not acting as a proxy. To keept things simple, those aliases use a "domain" paramter that just points to the host server itself.

## 1. Verifying a domain on the host server

In order to use a host server for a proxy domain, the proxy user must have an account on the host server and be able to obtain a JWT via the /v1/auth endpoint. Once a jwt is obtained, the jwt is used to construct a SRV record. The SRV record is used to forward future requests to the proxy domain along to the host server.

### The SRV record

The SRV record must have the following format:

CAPP-{domain}:{port}-{jwt}

This must map to the exact domain where the actual OpenCAP server is running. Let's use Ogdolo as an example, and pretend we have a proxy domain "proxy.com" and we want to use Ogdolo as a CAPP host. We would go into "proxy.com" domain management and add a SRV record that says:

```
CAPP-opencap.ogdolo.com:443-89sfhdhf9.89df7f08dg7hd0s8g7hf08.8f0sd7fdf
```

Once the SRV record is added, the following endpoint is used to verify to the host server that a user actually owns the proxy domain. The jwt proves to the server because no one else would have been able to add the jwt to the SRV record. Keep in mind, the jwt only lasts for 20 minutes at a minumum so adding the jwt to the SRV record needs to happen relatively quickly.

## POST /domains

Authorization Bearer {jwt}

Associates a domain with the authenticated user

Body:
```javascript
{
    "domain": "proxy.com",
    // the master_jwt parameter is only used for adding addtional users to a 
    // proxy domain as described below, otherwise the empty string is passed.
    "master_jwt": ""  
}
```

Response:
```javascript
Status: 200
{}
```

Once the server recieves the request, the server will do a lookup to get the SRV record from proxy.com and ensure that the jwt in the SRV record matches that of the authenticated user. If it does, then the domain of the user on that server will be updated to "proxy.com"

Once one user has successfully verified a proxy domain, that first user's jwt can be used to add other aliases to the same proxy domain so that the SRV record doesn't need to be updated every time. The user to be added would use the POST /v1/domain endpoint and authenticate as themself. They would pass in the first user's jwt as the "master_jwt" to prove that they should be allowed to have an alias on the proxy domain as well.

Now the proxy domain is all set up. When a wallet wants to send money to user@proxy.com, first the wallet will look at proxy.com's SRV record and find that it points to host.com. Then the wallet knows that it should make the rest of it's requests to host.com using "proxy.com" as the domain and "user" as the username.