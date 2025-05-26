Viz můj set-up pro bakalářskou práci:
- samotný server nespouštím přes `server:start` od Symfony, ale mám na to menší nginx
### docker-compose.yml
```
services:
  nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
    working_dir: /app
    container_name: thinklink-nginx
    volumes:
      - ./:/app
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - php
  php:
    build:
      args:
        - USER_ID=${UID:-1000}
        - GROUP_ID=${GID:-1000}
        - USER_NAME=${USER_NAME:-petr}
        - GROUP_NAME=${GROUP_NAME:-petr}
      context: ./docker/php
    working_dir: /app
    container_name: thinklink-app
    depends_on:
      - database
    volumes:
      - ./:/app
      - ./docker/php/php.ini:/usr/local/etc/php/conf.d/php.ini
  database:
    image: postgres:17
    environment:
      - POSTGRES_DB=devdb
      - POSTGRES_USER=dev
      - POSTGRES_PASSWORD=pass
    container_name: thinklink-postgres
    ports:
      - "5432:5432"
    volumes:
      - ./docker/postgres/data:/var/lib/postgresql/data:rw
  pgadmin:
    image: dpage/pgadmin4
    ports:
      - "5050:80"
    container_name: thinklink-pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@thinklink.com
      PGADMIN_DEFAULT_PASSWORD: admin
    depends_on:
      - database
```
### docker/nginx
default.conf
```
server {
    listen 80 default;
    server_name localhost;
    access_log /var/log/nginx/application.access.log;
    root /app/public;
    index index.php;
    if (!-e $request_filename) {
        rewrite ^.*$ /index.php last;
    }
    location ~ \.php$ {
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PHP_VALUE "error_log=/var/log/nginx/application_php_errors.log";
        fastcgi_buffers 16 16k;
        fastcgi_buffer_size 32k;
        include fastcgi_params;
    }
}
```
### docker/php
Dockerfile: 
```
FROM php:8.3-fpm
RUN apt-get update
RUN apt-get install -y zlib1g-dev libpq-dev git libicu-dev libxml2-dev libzip-dev libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql \
    && docker-php-ext-install zip xml \
    && docker-php-ext-install opcache \
    && docker-php-source delete \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && apt-get clean
RUN apt-get install -y libmagickwand-dev --no-install-recommends && rm -rf /var/lib/apt/lists/* \
    && printf "\n" | pecl install imagick \
    && docker-php-ext-enable imagick \
    && apt-get clean
RUN docker-php-source extract \
    && pecl install xdebug redis \
    && docker-php-ext-enable xdebug redis \
    && docker-php-source delete \
    && apt-get clean
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN curl -sS https://get.symfony.com/cli/installer | bash && \
    mv /root/.symfony*/bin/symfony /usr/local/bin/symfony
RUN rm -rf /tmp/*
ARG USER_ID  
ARG GROUP_ID  
ARG USER_NAME  
ARG GROUP_NAME  
    
RUN addgroup --gid $GROUP_ID $GROUP_NAME  
RUN adduser --disabled-password --gecos '' --uid $USER_ID --gid $GROUP_ID $USER_NAME  
USER $USER_NAME
CMD ["php-fpm", "-F"]
```
php.ini:
```
[PHP]
engine = On
short_open_tag = Off
precision = 14
output_buffering = Off
zlib.output_compression = Off
implicit_flush = Off
unserialize_callback_func =
serialize_precision = 100
disable_functions = dl
disable_classes =
zend.enable_gc = On
expose_php = Off
max_execution_time = 30
max_input_time = 60
memory_limit = 512M
error_reporting = E_ALL & ~E_NOTICE
display_errors = On
display_startup_errors = Off
log_errors = On
log_errors_max_len = 1024
ignore_repeated_errors = Off
ignore_repeated_source = Off
report_memleaks = On
track_errors = Off
html_errors = On
error_log = error_l
variables_order = "EGPCS"
request_order = "GP"
register_argc_argv = On
auto_globals_jit = On
post_max_size = 128M
auto_prepend_file =
auto_append_file =
default_mimetype = "text/html"
default_charset = "UTF-8"
include_path = ".:/opt/php72/lib/php"
doc_root =
user_dir =
enable_dl = Off
file_uploads = On
upload_max_filesize = 64M
max_file_uploads = 20
allow_url_fopen = On
default_socket_timeout
cli_server.color
date.timezone = Europe/Prague
pdo_mysql.cache_size = 2000
pdo_mysql.default_soc
sendmail_path = /usr/sbin/sendmail -t
mail.add_x_header
sql.safe_mode =
odbc.allow_persistent = Off
odbc.check_persistent = Off
odbc.max_persistent = -1
odbc.max_links = -1
odbc.defaultlrl = 4096
odbc.defaultbinmode
ibase.allow_persistent = 1
ibase.max_persistent = -1
ibase.max_links = -1
ibase.timestampformat = "%Y-%m-%d %H:%M:%S"
ibase.dateformat = "%Y-%m-%d"
ibase.timeformat = "%H:%M"
mysqli.max_persistent = -1
mysqli.allow_persistent = Off
mysqli.max_links = -1
mysqli.cache_size = 2000
mysqli.default_port = 3306
mysqli.default_socket =
mysqli.default_host =
mysqli.default_user =
mysqli.default_pw =
mysqli.reconnect =
mysqlnd.collect_statistics = Off
mysqlnd.collect_memory_statistics =
pgsql.allow_persistent = Off
pgsql.auto_reset_persistent = Off
pgsql.max_persistent = -1
pgsql.max_links = -1
pgsql.ignore_notice = 0
pgsql.log_notice
bcmath.scale
session.save_handler = files
session.save_path = "/tmp"
session.use_strict_mode = 0
session.use_cookies = 1
session.use_only_cookies = 1
session.name = PHPSESSID
session.auto_start = 0
session.cookie_lifetime = 0
session.cookie_path = /
session.cookie_domain =
session.cookie_httponly =
session.serialize_handler = php
session.gc_probability = 1
session.gc_divisor = 100
session.gc_maxlifetime = 1440
session.referer_check =
session.cache_limiter = nocache
session.cache_expire = 180
session.use_trans_sid = 0
session.hash_function = 0
session.hash_bits_per_character = 5
url_rewriter.tags = "a=href,area=href,frame=src,input=src,form=,fields"
zend.assertions
tidy.clean_output =
soap.wsdl_cache_enabled = 1
soap.wsdl_cache_dir = "/tmp"
soap.wsdl_cache_ttl = 86400
soap.wsdl_cache_limit
ldap.max_links
# zend_extension=opcache.so - already defined in /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini
opcache.enable=1
opcache.enable_cli=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.revalidate_freq=2
opcache.fast_shutdown=1
```