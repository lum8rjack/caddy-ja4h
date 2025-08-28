# caddy-ja4h

Caddy v2 module to calculate the JA4H fingerprinter for each request and add it to the headers.

## Installation

You can build Caddy by yourself by installing [xcaddy](https://github.com/caddyserver/xcaddy) and running:
```bash
xcaddy build --with github.com/lum8rjack/caddy-ja4h
```

If you want to clone and make any changes, you can test locally with the following command:
```bash
# Specify the location of the local build
 xcaddy build --with github.com/lum8rjack/caddy-ja4h=./caddy-ja4h
```

### Caddyfile

Below is an example Caddyfile that will calculate the JA4H fingerprint for each request and add it as a new header before forwarding the request on.
```
{
  admin off
  order ja4h_header first
}

https://test.example.com {
  ja4h_header

  handle /* {
    reverse_proxy https://localhost:8080 {
      header_up Host {http.request.host}
      transport http {
        tls
        tls_insecure_skip_verify
      }
    }
  }
}
```

The request received on the backen will look similar to below with the newly added JA4H header.

```bash
GET / HTTP/1.1
Host: 127.0.0.1:9000
User-Agent: curl/7.81.0
Accept: */*
Ja4h: ge11nn020000_a00508f53a24_000000000000_000000000000
X-Forwarded-For: 127.0.0.1
X-Forwarded-Host: 127.0.0.1:9000
X-Forwarded-Proto: http
Accept-Encoding: gzip
```


## References

- [JA4+ Network Fingerprinting](https://blog.foxio.io/ja4+-network-fingerprinting)
- [ja4 GitHub](https://github.com/FoxIO-LLC/ja4)
- [go-ja4h](https://github.com/lum8rjack/go-ja4h)

# Licensing

The code in this repository is MIT licensed. However, JA4H itself is subject to additional restricitons. Please see the [NOTICE](NOTICE) file for further details.