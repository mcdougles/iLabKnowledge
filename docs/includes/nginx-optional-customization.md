It is possible to provide Nginx configuration files for the default server block.
Static site pipelines check for a directory in the top-level of the repository named `nginx`.
Files inside the `nginx` directory matching the pattern `*.conf` will be copied into the image,
but the main Nginx configuration file `nginx.conf` cannot be replaced.

By default, Nginx cannot process environment variables in the nginx configuration files.
For example: proxy_pass directive `proxy_pass ${BACKEND_HOST};` placed in a configuration file cannot be processed by nginx,
it will cause an error at nginx startup.
The solution is to run the `envsubst` command before nginx starts in the container,
and `envsubst` will take care of replacing the environment variable placeholders with the value of the environment variables.

**`envsubst` command is part of the container startup procedure and behaves as follows:**

1. `envsubst` checks all configuration files (that you previously placed in the `nginx` directory)
and if a placeholder has a matching environment variable, it replaces the placeholder.
If there is no match, the placeholder is left untouched.
All environment variables are subject to substitution.
It is important to mention, that if there is an "official' nginx variable in a nginx configuration file, 
e.g: `$host` and there is an existing matching environment variable, `envsubst` will replace it.
Normally this should not happen,
but if this is the case then check the next option.
2. It is possible to manually control which environment variables are subject for substitution.
`NGINX_ENVSUBST_FILTER` can be used to specify which environment variables can only be substituted.
If there are more, you can separate them with a `|` pipe character.
For example: `NGINX_ENVSUBST_FILTER="BACKEND_HOST|FRONTEND_HOST|SERVICE_URL"` will tell `envsubst`
to replace only these 3 placeholders in all configuration files if there is a match.
Be careful not to include leading or trailing spaces and use only the pipe `|` character for separation. 
Only the specified environment variables are subject to substitution.

Since `envsubst` runs at runtime, use Cirrus secrets to create environment variables.

### **envsubst example:**

These are the existing environment variables:
```bash
BACKEND_HOST="http://backend:80"
PROXY_PATH="/api/v1"
COMMENT="This is a comment"
NGINX_ENVSUBST_FILTER="BACKEND_HOST|PROXY_PATH"
```
#### Before substitution
```nginx
# nginx/location.conf
location ${PROXY_PATH} {
    proxy_pass ${BACKEND_HOST}; # comment: ${COMMENT}
}
```
#### After substitution
```nginx
# nginx/location.conf
location /api/v1 {
    proxy_pass http://backend:80; # comment: ${COMMENT}
}
```