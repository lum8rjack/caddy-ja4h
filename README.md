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

### Add JA4H to Caddy Logs

By default the newly created header is not added to the logs since the logging happens earlier than this module in the request lifecycle. You can append the Ja4h header by using `log_append`.
```
{
  admin off
  order ja4h_header first
}

http://localhost:8000 {
  ja4h_header
  log {
    output stdout
    format json
  }

  handle /* {
    # Append to logs
    log_append ja4h_header {ja4h}

    respond "Status ok" 200 
  }
}
```

Running curl will result in "Success" and the log file should look similar to the following:
```json
{
  "level": "info",
  "ts": 1768146425.651497,
  "logger": "http.log.access.log0",
  "msg": "handled request",
  "request": {
    "remote_ip": "::1",
    "remote_port": "50341",
    "client_ip": "::1",
    "proto": "HTTP/1.1",
    "method": "GET",
    "host": "localhost:8000",
    "uri": "/",
    "headers": {
      "User-Agent": [
        "curl/8.7.1"
      ],
      "Accept": [
        "*/*"
      ]
    }
  },
  "bytes_read": 0,
  "user_id": "",
  "duration": 0.0000415,
  "size": 7,
  "status": 200,
  "resp_headers": {
    "Server": [
      "Caddy"
    ],
    "Content-Type": [
      "text/plain; charset=utf-8"
    ]
  },
  "ja4h_header": "ge11nn020000_a00508f53a24_000000000000_000000000000"
}
```

## References

- [JA4+ Network Fingerprinting](https://blog.foxio.io/ja4+-network-fingerprinting)
- [ja4 GitHub](https://github.com/FoxIO-LLC/ja4)
- [go-ja4h](https://github.com/lum8rjack/go-ja4h)

# Licensing

The code in this repository is MIT licensed. However, JA4H itself is subject to additional restricitons. Please see the [NOTICE](NOTICE) file for further details.