# Docker containers TLS termination and reverse proxy

Recently I had to configure TLS termination for a number of docker containerized applications. Each of these services listen on different ip/ports, and some require support for Websocket. 

The challenge was the ability to __dynamically map__ an external url to an internal one using regex expression captures. Showing how to configure `nginx` to perform this mapping is the goal of this article. 

## Prerequisites

For this article I will use the `example.com` [domain name](https://en.wikipedia.org/wiki/Domain_name). Your domain name and [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) may vary depending on your setup. I assume DNS resolution is working and configured correctly. I will not cover DNS configuration and resolution in this article. 

Nginx should be running and accessible both internally and exernally. On this article nginx is configured and accessible under `proxy.example.com`. It is also important that all the requests to `*.proxy.example.com` also resolve to the nginx host.

Each docker containerized application must be reachable __internally__ using a unique _FQDN_ and _port_. For example: `container1.example.com:8080` 

Finally, for TLS termination configuration both public and private certificate crt and key are needed. Supporting wildcard names under any common name that will resolve to the `nginx` proxy. For example: *.proxy.example.com or *.nginx.example.com

## How this works?

Mapping external requests comming to nginx to internal ones as follows:

- Request to: container1-8080.proxy.example.com
- Should map to: container1.example.com:8080

another example:

- Request to: redis2-6379.proxy.example.com
- Should map to: redis2.example.com:6379


Is important to highlight that the `container host` and `container port` are part of the initial request `FQDN`. In the above examples is possible to capture via regex expressions the conatiner names `container1`/`redis2` and ports `8080`/`6379`, and then use this information to construct the mapped internal FQDN.

## Implementation 

The idea behind this implementation is to extract the hostname and port from the server name url using regex expressions and forward the request using `proxy_pass`.

1. Configure TLS

TLS termination is straightforward. All you need is to have the certificate public and private keys pair.
```
server {
listen   443;
ssl    on;
ssl_certificate    /path/to/certficate/star_proxy_example_com.crt;
ssl_certificate_key  /path/to/certficate/star_proxy_example_com.hey;

`ssl_certificate` points to the public crt certificate file
`ssl_certificate_key` points to the private key certificate file
```

2. Extracting hostname and port using regex expressions

The [regular expressions used by nginx](http://nginx.org/en/docs/http/server_names.html#regex_names) are compatible with those used by the Perl programming language (PCRE)

Following code snippet captures both $containername and $containerport:
```
server_name ~^(?<containername>.+)-(?<containerport>\d+)\.proxy\.example\.com$;
```

3. Forward to correct internal server names

Use both $containername and $containerport along with resolver to forward the requests to the correct server:
```
location / {
            resolver x.x.x.x;
            proxy_pass http://${hostnamelab}.example.com:${portnamelab};
```

`resolver` must point to the IP address of the DNS server that can resolve the proxy_pass domain name to IP address. Required since we are using capture variables with `proxy_pass`

`proxy_pass` Use of `http` protocol and not `https`. Also notice the use of regex captures $containername and $containerport to correctly map to the right server name. 

4. Websocket support

Websocket support was easy. As described in [nginx documentation](http://nginx.org/en/docs/http/websocket.html) adding the `Upgrade` and `Connection` enabled websocket support.

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

## Solution

This configuration helps enable access to the required internal docker containers via secure TLS with support for websockets:

```
server {
       listen   443;
       ssl    on;
       ssl_certificate    /path/to/certficate/star_proxy_example_com.crt;
       ssl_certificate_key  /path/to/certficate/star_proxy_example_com.hey;

       server_name ~^(?<containername>.+)-(?<containerport>\d+)\.proxy\.example\.com$;

       location / {
            resolver 127.0.0.1; # point to correct DNS server
            proxy_pass http://${hostnamelab}.example.com:${portnamelab};
            proxy_set_header  Host $host;
            proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header  X-Forwarded-Proto $scheme;
            proxy_set_header  X-Forwarded-Ssl on; # Optional
            proxy_set_header  X-Forwarded-Port $server_port;
            proxy_set_header  X-Forwarded-Host $host;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
       }
   }
```

With similar approach is also possible to map external url to internal ip address. This would be possible if the ip address is part of the external url. For example: ip-192-168-1-1.proxy.example.com - and can be extracted using regex expressions. 

References:

- [1] Server name regular expressions - http://nginx.org/en/docs/http/server_names.html#regex_names
- [2] Websocket proxying - http://nginx.org/en/docs/http/websocket.html