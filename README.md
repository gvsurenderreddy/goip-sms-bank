GOIP SMS bank web frontend
-----------------------------
Web application for managing multiple GOIP devices via UDP requests and
separate daemon-listener. Additional details to follow.
Based on [android-sms-hivemind](https://github.com/Xifax/android-sms-bank).

## About directory structure

        smsbank
        |
        |= fabfile          <-- Deployment scripts.
        |
        |= requirements     <-- Python virtualenv requirements.
        |
        |= etc              <-- Mostly front-end files.
        |   |= test         <-- Javascript unit tests (if any).
        |   |= app          <-- Project-wise static files (css, js).
        |   |= templates    <-- Project-wise Django templates (views).
        |   \= uploads      <-- User-uploaded content (MEDIA_ROOT)
        |
        \= smsbank          <-- Web application itself.
            |= spec         <-- Environment-specific settings.
            |= my           <-- Custom project information (for symlinking).
            |= common       <-- Project-wise functionalities & cli tools.
            |= apps         <-- Web modules, including hivemind.
            \= utils        <-- Additional utilities.

## Details of some directories

    smsbank/smsbank/my/

Should contain secret keys, local settings and so on.
These files are for soft-linking in `smsbank/spec`.

    etc/node_modules
    etc/bower_components

Contain node and css/js modules used in frontend. Generally excluded from
repository. Use `npm install`, then `bower install` to install those.

    etc/dist
    etc/static_collected

Both of those folders are created using build procedures.
`etc/dist` is produced when running `Grunt`, `etc/static_collected` is a
result of running `./manage.py collectstatic`, which collects files from
`etc/dist` and other static assets in one folder.  These folders are by
default excluded from git repository.

## Makefile to rule them all

This project uses `Makefile` for some generic tasks. E.g., you may initialize
development environment and run production/development tasks using `make`
command. The following tasks are available:

- **run** (default task): run Django development server with default params;
- **gunicorn**: run wsgi gunicorn server;
- **init**: initialize repository by install requirements, node modules
    and building frontend static files;
- **static**: compile and collect static files;
- **update**: update project and bower modules;
- **purge**: remove all project artifacts, such as virtualenvironment, node
    modules and so on.

## Development

First, initialize and source `python2` virtualenv:

    virtualenv venv
    souce venv/bin/activate

Then (you should have `npm`, `ruby` and `compass` in your path) run:

    make init

This task should install pip requirements, then install nodejs modules,
download bower components, run grunt build and collect resulting static files.

## Deployment

Application should be deployed in conjunction with nginx server and
supervisor. Nginx is used as reverse-proxy for gunicorn and also as static
assets server. Supervisor is used to monitor and restart (if necessary)
gunicorn server itself.

Suggested nginx config:
```
http {
    # may also use gzip_static
    gzip on;
    gzip_http_version 1.0;
    gzip_proxied any;

    server {

        listen 80;
        server_name smsbank.net;

        # serve static files
        location /static/ {
            alias /path/to/application/etc/static_collected/;
            location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
                expires 30d;
                log_not_found off;
            }
        }

        # reverse-proxy gunicorn server
        location / {
            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_connect_timeout 10;
            proxy_read_timeout 10;
            proxy_pass http://127.0.0.1:8000/;
        }

    }
}
```

Suggested supervisor config:
```
[program:smsbank]
directory=/path/to/app
command=/path/to/app/venv/bin/gunicorn smsbank.wsgi
user=user
umask=022
autostart=True
autorestart=True
redirect_stderr=True

[program:smsbank-daemon]
directory=/path/to/app
command=/path/to/app/venv/bin/python manage.py run_daemon
user=user
umask=022
autostart=True
autorestart=True
redirect_stderr=True
```

## Additional notes

TO DO.
