GOIP SMS bank web frontend
-----------------------------
Web application for managing multiple GOIP devices via UDP requests and
separate daemon-listener. Additional details to follow.
Based on [android-sms-hivemind](https://github.com/Xifax/android-sms-bank).

## Makefile to rule them all

This project uses `Makefile` for some generic tasks. E.g., you may initialize
development environment and run production/development tasks using `make`
command.

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

## Deployment

TO DO.

## Additional notes

TO DO.
