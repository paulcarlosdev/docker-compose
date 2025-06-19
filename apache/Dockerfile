FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install software-properties-common -y
RUN add-apt-repository ppa:ondrej/php

RUN apt-get update
RUN apt -y install php8.2 -y
RUN apt-get install -y php8.2-cli php8.2-common php8.2-mysql php8.2-zip php8.2-gd php8.2-mbstring php8.2-curl php8.2-xml php8.2-bcmath

RUN apt-get install openssl -y \
    nano \
    graphicsmagick \
    imagemagick \
    ghostscript \
    mysql-client \
    iputils-ping \
    locales \
    sqlite3 \
    ca-certificates \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install composer
RUN apt-get update
RUN apt-get install curl -y
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer
RUN chmod +x /usr/local/bin/composer

# Set locales
RUN locale-gen en_US.UTF-8 en_GB.UTF-8 de_DE.UTF-8 es_ES.UTF-8 fr_FR.UTF-8 it_IT.UTF-8 km_KH sv_SE.UTF-8 fi_FI.UTF-8

# Configure apache for TYPO3
RUN a2enmod rewrite expires
RUN echo "ServerName localhost" | tee /etc/apache2/conf-available/servername.conf
RUN a2enconf servername
# Configure vhost for TYPO3
#COPY typo3.conf /etc/apache2/sites-available/
#RUN a2dissite 000-default
#RUN a2ensite typo3.conf

# Configurar PHP para mejor uso de RAM y CPU
RUN sed -i 's/memory_limit = .*/memory_limit = 1024M/' /etc/php/8.2/apache2/php.ini && \
    sed -i 's/max_execution_time = .*/max_execution_time = 3000/' /etc/php/8.2/apache2/php.ini && \
    sed -i 's/upload_max_filesize = .*/upload_max_filesize = 1024M/' /etc/php/8.2/apache2/php.ini && \
    sed -i 's/post_max_size = .*/post_max_size = 512M/' /etc/php/8.2/apache2/php.ini

EXPOSE 80 8080 443

WORKDIR /var/www/html

#RUN rm index.html

HEALTHCHECK --interval=5s --timeout=3s --retries=3 CMD curl -f http://localhost || exit 1

CMD apachectl -D FOREGROUND 