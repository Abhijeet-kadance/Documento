### NGINX DOCS
---
**# Redirecting http (80) to https (443)**

    server {
        listen 80;
        listen [::]:80;

        # Custom error pages
        error_page  400 401 404 403 405 /custom_404.html;
        location = /custom_404.html {
            server_tokens off;
            add_header Server "Webserver";
            root /usr/share/nginx/html;
            internal;
        }

        error_page 301 /custom_30x.html;
        location = /custom_30x.html {
            server_tokens off;
            add_header Server "Webserver";
            root /usr/share/nginx/html;
            internal;
        }

        error_page 500 502 503 504 /custom_50x.html;
        location = /custom_50x.html {
            server_tokens off;
            add_header Server "Webserver";
            root /usr/share/nginx/html;
            internal;
        }

        location / {
            try_files $uri @redirect_to_https;
        }

        location @redirect_to_https {
            return 301 https://$server_name$request_uri;
        }
    }

> This Logic is craeted for a usecase. That is when we redirect a request to https. Intruders can still hit 80 port to get access to the location mentioned. 

> In that scenario it gives 301 error page reveling the server name and some Data. In this case if you add custom pages logic too, it still can give nginx default error page. 

> This is due to the fact that nginx ask us to serve the custom error page before redirection. 

> Therefore we have created location / { try_files $uri @redirect_to_https; }, here in this we return 301 ro https (443) port.

---

**# X-Forwarded-For header Spoofing for a account lockout session with NGINX**

> The scenario of X-Forwaded-For spoofing can be vulnerable to functionalities as lockout functionality. If there is multiple login attempts then such scenario should be encounterd by adding throttling or lockout for a certain period of time. But this X-forwaded-For is set in a wrong way. Then an intruder can break this functionalty giving intruder to make multiple requests.

> To resolve this we have to set this header to be disabled.

> In the NGINX  conf file add this to the /location to not intrude on this.

    proxy_set_header X-Forwarded-For "";

> In this case we have disabled this header by adding above header.

---

