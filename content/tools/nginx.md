---
title: Nginx
tags: 
- nginx
- http
---

[Nginx](https://www.nginx.com/) (pronounced "engine x" /ˌɛndʒɪnˈɛks/ EN-jin-EKS, stylized as NGINX) is a web server that can also be used as a reverse proxy, load balancer, mail proxy and HTTP cache. 
<!--more-->
The software was created by Igor Sysoev and publicly released in 2004. Nginx is free and open-source software.

## Troubleshooting

### Links have wrong hostname or port

While building a static site with [Hugo](hugo/hugo.md) which I was hosting with a Nginx Docker container I found that the 
relative links would be resolved by the browser to the wrong hostname or port.

After way too much investigation and searching I came across this [article](https://medium.com/localhost-run/fixing-nginx-links-that-have-the-wrong-hostname-or-port-a35e378a91a7)
which gave me the clue that I needed.

Looking at the Nginx logs I could see that when I used a relative `href` link such as `href="/blog"` Nginx 
was returning with a http code of 301 (Moved Permanently) and redirect the browser to `/blog/`.

This is expected behaviour except that Ngnix was also returning the hostname (and port) as well, which does not work when 
running in a container, or behind a load balancer, or a TLS termination point (such as an HTTPS Ingress in Kubernetes).

The fix is easy I just needed to add ``absolute_redirect off;`` to the http server config as 
per the example below:
```text
# In the /etc/nginx/conf.d/default.conf file
server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;
    # Make redirects issued by nginx relative.
    absolute_redirect off;
    ...
}
```

See the [Nginx config doco](https://nginx.org/en/docs/http/ngx_http_core_module.html#absolute_redirect) for more details.

