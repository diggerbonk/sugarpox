# Set up Apache2

Add a virtual host:

    Listen 80
    <VirtualHost *:80>
        DocumentRoot "/var/www/html/sugarpox"
        ServerName sugarpox.com

        # Other directives here
    RewriteEngine on
    RewriteCond %{SERVER_NAME} =sugarpox.com
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
    </VirtualHost>

Call certbot to create a certificate for the host:

    certbot --apache


Enable mod_include for server side includes:

    a2enmod include

    # vi mods-available/mime.conf, update:
    # see: https://stackoverflow.com/questions/58452272/enable-server-side-includes-in-apache2-for-selected-domains

    <IfModule mod_mime.c>
    <IfModule mod_include.c>
                    AddOutputFilter INCLUDES .shtml
    </IfModule>
    </IfModule>

Add the Directory entry in /etc/apache2/apache2.conf

    # Further relax access to the default document root:
    <Directory "/var/www/html">
        #
        # Possible values for the Options directive are "None", "All",
        # or any combination of:
        #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
        #
        # Note that "MultiViews" must be named *explicitly* --- "Options All"
        # doesn't give it to you.
        #
        # The Options directive is both complicated and important.  Please see
        # http://httpd.apache.org/docs/2.4/mod/core.html#options
        # for more information.
        #
        Options Indexes FollowSymLinks

        #
        # AllowOverride controls what directives may be placed in .htaccess files.
        # It can be "All", "None", or any combination of the keywords:
        #   Options FileInfo AuthConfig Limit
        #
        AllowOverride None

        #
        # Controls who can get stuff from this server.
        #
        Require all granted
        Options +IncludesNoExec
        AddType text/html .html
        AddOutputFilter INCLUDES .html
    </Directory>


