# 20200805 horj: copy/paste van https://github.com/adamzammit/quexs-docker

FROM php:7.3-apache

# 20200805 horj: Heb aanpassingen gedaan in enkele bestanden van de oorspronkelijke code (zie github commits), 
# zodat de interface voor de bellers gelijk is aan lokaal (waar diezelfde aanpassingen destijds ook gedaan zijn)
# ENV DOWNLOAD_URL https://master.dl.sourceforge.net/project/quexs/quexs/quexs-1.16.5/quexs-1.16.5.zip
ENV DOWNLOAD_URL https://github.com/Joost1982/quexs/raw/master/quexs-1.16.5.zip

# install the PHP extensions we need
RUN apt-get update && apt-get install -y mariadb-client unzip libpng-dev libjpeg-dev libfreetype6-dev && rm -rf /var/lib/apt/lists/* \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-png-dir=/usr --with-jpeg-dir=/usr \
	&& docker-php-ext-install gd mysqli pdo_mysql opcache

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=2'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

RUN a2enmod rewrite expires

RUN set -x; \
	curl -SL "$DOWNLOAD_URL" -o /tmp/quexs.zip; \
    unzip /tmp/quexs.zip -d /tmp; \
    mv /tmp/quexs*/* /var/www/html/; \
    rm /tmp/quexs.zip; \
    rmdir /tmp/quexs*; \
    chown -R www-data:www-data /var/www/html

#use ADODB
RUN set -x \
	&& curl -o adodb.tar.gz -fSL "https://github.com/ADOdb/ADOdb/archive/master.tar.gz" \
	&& tar -xzf adodb.tar.gz -C /usr/src/ \
	&& rm adodb.tar.gz \
	&& mkdir /usr/share/php \
	&& mv /usr/src/ADOdb-master /usr/share/php/adodb

#Set PHP defaults for queXS (allow bigger uploads for sample files)
RUN { \
		echo 'memory_limit=256M'; \
		echo 'upload_max_filesize=128M'; \
		echo 'post_max_size=128M'; \
		echo 'max_execution_time=120'; \
        echo 'date.timezone=UTC'; \
	} > /usr/local/etc/php/conf.d/uploads.ini

#20200807 horj: download CACert for ssl connecttion to Azure database (see: https://docs.microsoft.com/en-us/azure/mysql/howto-configure-ssl)
RUN set -x \
	&& curl -o /var/www/html/BaltimoreCyberTrustRoot.crt.pem -fsL "https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem"
	

COPY docker-entrypoint.sh /usr/local/bin/
RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh # backwards compat

# ENTRYPOINT resets CMD
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
