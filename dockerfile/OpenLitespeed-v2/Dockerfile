# Use the official Ubuntu base image
FROM ubuntu:jammy

# Set timezone and make installation non-interactive
ENV TZ=Asia/Kolkata
ENV DEBIAN_FRONTEND=noninteractive

# Install necessary packages
RUN apt-get update && \
    apt-get install -y wget curl nano unzip wkhtmltopdf && \
    wget -O - https://repo.litespeed.sh | bash && \
    apt-get update && \
    apt-get install -y openlitespeed lsphp82 lsphp82-mysql lsphp82-common lsphp82-opcache \
    lsphp82-intl lsphp82-curl lsphp82-imap lsphp82-ldap lsphp82-redis lsphp82-memcached && \
    rm -r /var/lib/apt/lists/*

# Install Node.js 20
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get install -y nodejs

# Download and install ionCube Loader
RUN wget -O /tmp/ioncube_loaders.zip https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.zip && \
    unzip /tmp/ioncube_loaders.zip -d /tmp && \
    mkdir -p /usr/local/lsws/lsphp82/lib/php/20210902/ && \
    cp /tmp/ioncube/ioncube_loader_lin_8.2.so /usr/local/lsws/lsphp82/lib/php/20210902/ioncube_loader_lin_8.2.so && \
    echo "zend_extension = /usr/local/lsws/lsphp82/lib/php/20210902/ioncube_loader_lin_8.2.so" > /usr/local/lsws/lsphp82/etc/php/8.2/mods-available/00-ioncube.ini && \
    rm -rf /tmp/*

# Modify PHP configuration
RUN sed -i -e 's/^file_uploads =.*/file_uploads = On/' \
           -e 's/^allow_url_fopen =.*/allow_url_fopen = On/' \
           -e 's/^short_open_tag =.*/short_open_tag = On/' \
           -e 's/^memory_limit =.*/memory_limit = 256M/' \
           -e 's/^;cgi.fix_pathinfo=1.*/cgi.fix_pathinfo = 0/' \
           -e 's/^upload_max_filesize =.*/upload_max_filesize = 100M/' \
           -e 's/^post_max_size =.*/post_max_size = 100M/' \
           -e 's/^max_execution_time =.*/max_execution_time = 360/' \
           /usr/local/lsws/lsphp82/etc/php/8.2/litespeed/php.ini

RUN apt-get clean

# Create virtual host configuration directory
RUN mkdir -p /usr/local/lsws/conf/vhosts/faveo

# Copy virtual host configuration file
COPY httpd_config.conf /usr/local/lsws/conf/httpd_config.conf
COPY vhconf.conf /usr/local/lsws/conf/vhosts/faveo/vhconf.conf
COPY htpasswd /usr/local/lsws/admin/conf/htpasswd

# Create document root directory and set permissions
RUN mkdir -p /var/www/html
RUN chown -R www-data:www-data /var/www/html

WORKDIR /var/www/html

# Expose ports 80 and 443 for HTTP and HTTPS traffic
EXPOSE 80
EXPOSE 443
EXPOSE 7080

# Set entrypoint to OpenLiteSpeed service
COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]


