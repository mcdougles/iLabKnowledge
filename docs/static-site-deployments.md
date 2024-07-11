# Static Site Deployments

Static site pipelines deploy an Application to Cirrus with an image running Nginx or Openresty.

Some of the static site pipelines we provide are:

{%include-markdown "./includes/nodejs-ui-container.md"%}
{%include-markdown "./includes/poetry-ui-container.md"%}

## Nginx

### Base Image

The image used to deploy static-sites using Nginx can be found at: [https://catalog.redhat.com/software/containers/rhel8/nginx-118/5f521a46e05bbcd88f128b64](https://catalog.redhat.com/software/containers/rhel8/nginx-118/5f521a46e05bbcd88f128b64)

### Pipeline file used

[https://github.ibm.com/cio-ci-cd/pipeline-catalog/blob/main/catalog/official/tasks/buildah/dockerfiles/nginx/nginx118.Dockerfile](https://github.ibm.com/cio-ci-cd/pipeline-catalog/blob/main/catalog/official/tasks/buildah/dockerfiles/nginx/nginx118.Dockerfile)
)

### Nginx Customization

{%
  include-markdown "./includes/nginx-optional-customization.md"
%}

### Example configuring a health check endpoint

{%
  include-markdown "./includes/nginx-health-checks.md"
%}

## OpenResty

{%
       include-markdown "./includes/community-supported.md"
%}

!!! info

    Dedicated Slack channel for OpenResty pipeline support: [#cio-cicd-openresty-pipeline](https://ibm-cio.slack.com/archives/C04L54FDZK9)

OpenResty is a scalable platform by extending [Nginx](https://nginx.org/) with [LuaJIT](https://luajit.org/). Through the implementation of the Node and OpenResty pipelines, developers are able to secure their Node front-end applications through custom Lua scripts.

### Base Image

A custom image based [ubi8](https://catalog.redhat.com/software/containers/ubi8/ubi/5c359854d70cc534b3a3784e) was created to install [Opresty](https://openresty.org/en/), the image can be found at: [https://github.ibm.com/cio-ci-cd/openresty-base-image](https://github.ibm.com/cio-ci-cd/openresty-base-image)

### Pipeline File Used

[https://github.ibm.com/cio-ci-cd/pipeline-catalog/blob/main/catalog/official/tasks/buildah/dockerfiles/openresty/openresty121.Dockerfile](https://github.ibm.com/cio-ci-cd/pipeline-catalog/blob/main/catalog/official/tasks/buildah/dockerfiles/openresty/openresty121.Dockerfile)

### Nginx Customization

OpenResty is configured similar to a regular Nginx server with some additional syntax to enable the lua authentication.

It is possible to provide Nginx configuration files for the default server block. Static site pipelines check for a directory in the top-level of the repository named `nginx`. The files inside `nginx` as well as those inside the `nginx/includes` directory are copied into the image.

For lua authentication, you should provide the `*.lua` scripts and place them inside the `lua` dirctory in the top-level of the repository to be copied into the image.

### Example Configuring, Lua Security and Health Check Endpoint

The following examples shows how to configure a minimal OpenResty server to listen for HTTP GET at the root endpoint `/` which is protected by lua, as well as for heatlh probe requests at endpoint `/health/ping`.

Note that your lua script requires secret environment variables to execute the authentication process, so you must create them as part of a secret in your Cirrus project, load the secret in your application's environment and load your variables in the main `nginx.conf` file to make them available in your lua script.

`nginx/nginx.conf`

```
env JWT_TOKEN;

http {
    lua_package_path /usr/local/openresty/lualib/?.lua;

    server {
        listen       8080;

        location / {
            access_by_lua '
                local security = require("nginx-security")
                security.validate()
            ';
            alias  /usr/local/openresty/nginx/site/;
            try_files $uri $uri/ /index.html =404;
        }

        error_page   401 500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

      include /usr/local/openresty/nginx/conf/includes/*.conf;
    }

}
```

`nginx/includes/health.conf`

```
location /health/ping {
    add_header Content-Type text/plain;
    return 200 'ok';
}
```

`lua/nginx-security.lua`

```
local jwt-token = os.getenv("JWT_TOKEN")
local M = {}

function M.unAuthorized()
    return ngx.exit(401)
end

function M.validate()
    if jwt-token == false then
        return M.unAuthorized()
    else
        return true
    end
end

return M

```

### Fixing "permission denined" errors

While working with the `nodejs-v<16 | 18>-npm-openresty-container-image` pipelines, you may face and error like the following: `[emerg] 1#1: mkdir() "/usr/local/openresty<some-new-directory>" failed (13: Permission denied)`. This is due to the non-root user under which the pod is running, does not have the hability to write on that directoy (and sub-directories), which is actully intentional, since the Openresty base directory: `/usr/local/openresty` - where your `nginx` ad `lua` configuration is copied to, is only intended for reading such configuration. Refer to the [Openresty Dockerfile](https://github.ibm.com/cio-ci-cd/pipeline-catalog/blob/main/catalog/official/tasks/buildah/dockerfiles/openresty/openresty121.Dockerfile) for more details.

If you face a similar issue, consider adding the following directives as needed in your `nginx/*.conf` file:


```
pid /tmp/nginx.pid;
client_body_temp_path /tmp/client_temp;
proxy_temp_path       /tmp/proxy_temp_path;
fastcgi_temp_path     /tmp/fastcgi_temp;
uwsgi_temp_path       /tmp/uwsgi_temp;
scgi_temp_path        /tmp/scgi_temp;
access_log            /dev/stdout;
```

Note, how the above directives tell nginx to write in a directory not own by root, you can find more details about the above directives and its usage in the [nginx official documentation](http://nginx.org/en/docs/dirindex.html), you can also refer to this [slack thread at #cio-cicd-openresty-pipeline] (https://ciosets.slack.com/archives/C04L54FDZK9/p1690221578052659) where the above issue was reported and troubleshooted.