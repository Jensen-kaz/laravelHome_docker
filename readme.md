### Настройка mysql

Для подключения:
    - в контейнере mysql создать пользователя CREATE USER 'username'@'%' IDENTIFIED BY 'password';
    (CREATE USER 'root'@'%' IDENTIFIED BY '';).
    - назначить права GRANT ALL PRIVILEGES ON laravelHome.* TO 'username'@'%' IDENTIFIED BY 'password';
    
### Настройки env

БД: 
    DB_CONNECTION=mysql
    DB_HOST=laravelhome_mysql (необходимо указать контейнер mysql)
    DB_PORT=3306
    DB_DATABASE=laravelHome
    DB_USERNAME=root
    DB_PASSWORD=
    
### Настройки nginx 

nginx/conf.d/app.conf:
    server {
        listen 80;
    
        root /var/www/public;
    
        index index.html index.htm index.php;
    
        charset utf-8;
    
        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }
    
        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }
    
        access_log on;
        error_log  /var/log/nginx/laravelhome-error.log error;
    
        error_page 404 /index.php;
    
        sendfile off;
    
        location ~ ^.+\.(css|js)$ {
            expires 259200s; # 3 days
            access_log off;
            log_not_found off;
            add_header Pragma public;
            add_header Cache-Control "max-age=259200, public";
        }
    
        location ~ \.php$ {
            fastcgi_pass laravelhome_php:9000;
            fastcgi_send_timeout 300;
            fastcgi_read_timeout 300;
            fastcgi_split_path_info ^(.+\.php)(/.*)$;
            fastcgi_index index.php;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param PATH_INFO $fastcgi_path_info;
        }
    
        location ~ /\.ht {
            deny all;
        }
    }