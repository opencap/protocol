# Tools - A list of resources to help developers use OpenCAP

## SRV lookup tools

Becuase OpenCAP is just a REST specification, most integrations should be striaghtforward and simple. The most challenging part will likely be the SRV record lookup. Most browsers are unable to do DNS lookups through javascript, so browser wallets should use a backend service to make DNS lookups. Native mobile wallets on the other hand are able to make queries, but we cannot gaurentee simplicity. Depending on your application's language/framework, varying amounts of tools are at your disposal. If it proves to be too complex, a native mobile app can also make backend queries of course to do its lookups. In the future we plan to offer an nslookup API.

### IOS SDK

https://developer.apple.com/library/archive/samplecode/SRVResolver/Introduction/Intro.html#//apple_ref/doc/uid/DTS40009625-Intro-DontLinkElementID_2

### Javascript

https://www.npmjs.com/package/cordova-plugin-nslookup

https://nodejs.org/api/dns.html#dns_dns_resolvesrv_hostname_callback

### Java

http://www.dnsjava.org/

https://github.com/ieugen/dnsjava

https://github.com/MiniDNS/minidns

### Python

http://www.dnspython.org/

### Ruby

https://github.com/javanthropus/resolv-srv

### Go

https://github.com/opencap/go-opencap

https://golang.org/pkg/net/#LookupSRV

### PHP

http://php.net/manual/en/function.dns-get-record.php
