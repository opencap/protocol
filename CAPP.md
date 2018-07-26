# CAPP - Crypto Alias Proxy Protocol

CAPP is a sub-protocol that allows us to use a CAPP compliant server as a host for a separate domain name. A good comparison would be when a company uses a service like gmail to handle hosting and SMTP, but points their own domain at the service. For instance, a company named "dixiechiro" uses gmail but has an address "lane@dixiechiro.com"

When we talk about this "proxy" feature we refer to the CAPP enabled server that runs the API the "host server". The domain that is being pointed towards that host will be called the "proxy domain".

The proxy feature is the reason why the "domain" parameter is required by all CAP and CAMP servers. These servers, even if they don't support acting as a proxy, all must at least be able to tell clients that they are not acting as a proxy so those aliases use a "domain" paramter that just points to the host server itself.

## 1. Adding a SRV record

SRV records are used so that the proxy domain can forward alias traffic along without needing a server of any kind.

The process for adding a SRV record to a domain is different depending on who you use for your domain name provider. What goes in the SRV record is the interesting part, and must follow the following format:

CAPP-{domain}:{port}

This must map to the exact domain where the actual OpenCAP server is running. Let's use Ogdolo as an example, and pretend we have a proxy domain "proxy.com" and we want to use Ogdolo as a CAPP host. We would go into "proxy.com" domain management and add a SRV record that says:

```
CAPP-opencap.ogdolo.com:443
```

It is considered "indsutry standard" to use "opencap" as the subdomain where the OpenCAP server runs, and Ogdolo adheres to that standard.

## 2. Creating the alias on a Host Server

Once the SRV record is correctly confirgured all that needs to happen is we create an alias on the host server and use our own domain. For instance, to create the alias "lane@dixiechiro.com" we make a POST request to https://opencap.ogdolo.com/v1/users

```javascript
{
    "username": "lane",
    "domain": "dixiechiro.com",
    "password": "mysecretpassword"
}
```

Now I have an account on ogdolo.com and Ogdolo knows that I'm using a proxy domain. I can create an address on Ogdolo while authenticated as "lane":

PUT https://opencap.ogdolo.com/v1/address

```javascript
{
    "asset": 0, // 0 = BTC
    "type": "segwit",
    "address": "bc1qvw0ytfntx6zs0lfsruem6xwj0mewng523ktatp"
}
```

Now to test that the proxy is working correctly I can follow the client (wallet) protocol using my alias lane@dixiechiro.com:

* Do a name server lookup to the SRV record on dixiechiro.com
* SRV record points to Ogdolo:

```javascript
opencap.ogdolo.com:443
```

* Make a GET request to Ogdolo
* https://opencap.ogdolo.com:443/0/lane/dixiechiro.com
* Ogdolo responds with the correct address:

```javascript
Code: 200
{
    "addresses":[
        "type": "segwit",
        "address": "bc1qvw0ytfntx6zs0lfsruem...",
        "extensions": {
            "name": "My segwit address",
            // any other asset specific info can be in here
        }
    }
    ]
}
```