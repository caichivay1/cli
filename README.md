##Debug phpstom in cli
                  
                  debug cli XDEBUG_CONFIG=idekey=phpstorm bin/magento
## Config fpm version , cli version
      /usr/local/etc/php/7.4/php-fpm.d/www.conf
      line listen:
      #the listen in www.conf is port in nginx.conf
            upstream fastcgi_backend {
                 server  unix:/run/php/php7.2-fpm.sock;
                 or server 127.0.0.1:9000 (on MAcos this is listen in /usr/local/etc/php/7.2/php-fpm.d/www.conf )
            }

## Config Vhost Project Laravel
### /etc/nginx/sites-available/laravel :

      server 
      {
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
## Config Vhost Project Magento
### /etc/nginx/sites-available/magento:
     server {
           listen 80;
           server_name  craft.local;
           set $MAGE_ROOT /var/www/html/craft;
            set $MAGE_MODE developer;
           include /var/www/html/craft/nginx.conf.sample;
            error_log /var/www/log/crafts/error.log;
       }
 ## nginx.conf.sample
 ## setup multi website/store
        #etc/nginx/sites-available/craft
            upstream fastcgi_backend {
           server  unix:/run/php/php7.2-fpm.sock;
           or server 127.0.0.1:9000 (on MAcos this is listen in /usr/local/etc/php/7.2/php-fpm.d/www.conf )
      }

      map $http_host $MAGE_RUN_CODE {
          #default '';
          specialistcrafts-ie2.staging.screencommerce.com ireland;
          specialistcrafts-ae2.staging.screencommerce.com uae;
          specialistcrafts-world2.staging.screencommerce.com world;
          sewingmachines2.staging.screencommerce.com sewing;
          homecrafts2.staging.screencommerce.com home;
          hearteducational2.staging.screencommerce.com heart;
      }
      server {

     listen 80;
     server_name specialistcrafts2.staging.screencommerce.com specialistcrafts-ae2.staging.screencommerce.com specialistcrafts-world2.staging.screencommerce.com specialistcrafts-ie2.staging.screencommerce.com sewingmachines2.staging.screencommerce.com homecrafts2.staging.screencommerce.com hearteducational2.staging.screencommerce.com;
     set $MAGE_ROOT /home/administrator/project/current;
     include /home/administrator/project/current/nginx.conf.sample;
     auth_basic "Private Property";
        set $MAGE_RUN_TYPE website; #or set $MAGE_RUN_TYPE store;
     auth_basic_user_file /home/administrator/.htpasswd;
}

#project/nginx.conf.sample

          `add 2 line below
            fastcgi_param  MAGE_RUN_TYPE $MAGE_RUN_TYPE;
            fastcgi_param  MAGE_RUN_CODE $MAGE_RUN_CODE;
            
                       location /media/downloadable/ {
                deny all;
            }

      location /media/import/ {
          deny all;
      }

      # PHP entry point for main application
      location ~ ^/(index|get|static|errors/report|errors/404|errors/503|health_check|elastic)\.php$ {
          try_files $uri =404;
          fastcgi_pass   fastcgi_backend;
          fastcgi_buffers 1024 4k;

          fastcgi_param  PHP_FLAG  "session.auto_start=off \n suhosin.session.cryptua=off";
          fastcgi_param  PHP_VALUE "memory_limit=756M \n max_execution_time=18000";
          fastcgi_read_timeout 600s;
          fastcgi_connect_timeout 600s;
      fastcgi_param  MAGE_RUN_TYPE $MAGE_RUN_TYPE;
      fastcgi_param  MAGE_RUN_CODE $MAGE_RUN_CODE;
          fastcgi_index  index.php;
          fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
          include        fastcgi_params;
      }

      gzip on;
      gzip_disable "msie6";

      gzip_comp_level 6;
      gzip_min_length 1100;
      gzip_buffers 16 8k;
      gzip_proxied any;
      gzip_types
          text/plain
          text/css
          text/js
          text/xml
          text/javascript
          application/javascript
          application/x-javascript
          application/json
          application/xml
          application/xml+rss
          image/svg+xml;
      gzip_vary on;

      # Banned locations (only reached if the earlier PHP entry point regexes don't match)
      location ~* (\.php$|\.htaccess$|\.git) {
          deny all;
      }
      try_files $uri $uri/ @magento;
      fastcgi_param SCRIPT_FILENAME $document_root/$fastcgi_script_name;
      
##Config and install magento 2

      php bin/magento setup:install \
        --admin-firstname=First \
        --admin-lastname=Last \
        --admin-email='email@example.com' \
        --admin-user=admin \
        --admin-password='ADMIN_PASSWORD' \
        --base-url='http://localhost' \
        --backend-frontname=admin \
        --db-host='DB_HOST' \
        --db-name='DB_NAME' \
        --db-user='DB_USER' \
        --db-password='DB_PASSWORD' \
        --language=en_US \
        --currency=USD \
        --timezone='America/Chicago' \
        --admin-use-security-key=0 \
        --session-save=files
      php bin/magento deploy:mode:set developer

 
