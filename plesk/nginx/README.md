# NGINX Configuration Snippets for Drupal 6 & 7 when running on Plesk's PHP-FPM integration
This package contains several configuration snippets that can be included in
the NGINX configuration for a particular site when that site has been configured
through Plesk's native NGINX and PHP-FPM support.

## Disclaimer
This package has been compiled and tested only with Plesk 12 and
CloudLinux Server release 6.6 (Leonid Kizim). It may work with other versions
but it has not been tested with them.

## Snippet Installation
1. Create a new system-wide folder location for the snippets. We recommend
   `/usr/local/share/plesk/nginx/templates`.
   
2. Copy all of the snippets in this folder to the folder you created in step #1.

3. Make sure that the folder and all of the snippet files are owned by root:root
   but readable by all users (i.e. folders should be 755 and files 644).
   
## Using the Snippets
When configuring a subscription in Plesk to use PHP-FPM, include references to
the appropriate scripts from this package in the "Additional nginx directives"
box of the "Web Server Settings" page for the site.

### Requiring SSL only
To force requests for the HTTP version of a site (i.e. on port 80) to be
redirected to the SSL-version (i.e. on port 443), place this in the
"Additional nginx directives" box:

    include "/usr/local/share/plesk/nginx/templates/ssl_redirect.conf";

### Drupal 6/7
Plesk's default configuration of NGINX is not compatible with friendly URLs.
As a result, Drupal will get served up via Apache instead of NGINX, causing it
not to perform as well.

To fix this for Drupal 6, place this in the "Additional nginx directives" box:

    include "/usr/local/share/plesk/nginx/templates/drupal6.conf";

To fix this for Drupal 7, place this in the "Additional nginx directives" box:

    include "/usr/local/share/plesk/nginx/templates/drupal7.conf";
