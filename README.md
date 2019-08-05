## Config Vhost Project Laravel
** /etc/nginx/sites-available/test
server {
        listen 80;

        root /var/www/html/test1;
        index index.html index.htm index.php;

        server_name www.test1.local test1.local;

        #location / {
        #        try_files $uri $uri/ =404;
        #}

        location / {
            try_files $uri $uri/ /index.php$is_args$args;
        }

        # pass PHP scripts to FastCGI server
        #
        location ~ \.php$ {
            try_files $uri =404;
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            fastcgi_pass backend-server;
            fastcgi_index index.php;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param PATH_INFO $fastcgi_path_info;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        location ~ /\.ht {
               deny all;
        }
}
