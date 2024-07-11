For static sites, a simple nginx directive can provide the health check endpoint. To configure your static site to respond to HTTP GET probes at the default `/health/ping` location, create an `nginx/health.conf` file in your repo with the following contents:

```
location /health/ping {
    add_header Content-Type text/plain;
    return 200 'ok';
}
```
