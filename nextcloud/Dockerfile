FROM nextcloud:apache

RUN set -ex; \
    \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        ffmpeg \
        ghostscript \
        libmagickcore-6.q16-6-extra \
        procps \
        smbclient \
        supervisor \
#       libreoffice \
    ; \
    rm -rf /var/lib/apt/lists/*

RUN set -ex; \
    \
    savedAptMark="$(apt-mark showmanual)"; \
    \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        libbz2-dev \
        libc-client-dev \
        libkrb5-dev \
        libsmbclient-dev \
    ; \
    \
    docker-php-ext-configure imap --with-kerberos --with-imap-ssl; \
    docker-php-ext-install \
        bz2 \
        imap \
    ; \
    pecl install smbclient; \
    docker-php-ext-enable smbclient; \
    \
# reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
    apt-mark auto '.*' > /dev/null; \
    apt-mark manual $savedAptMark; \
    ldd "$(php -r 'echo ini_get("extension_dir");')"/*.so \
        | awk '/=>/ { so = $(NF-1); if (index(so, "/usr/local/") == 1) { next }; gsub("^/(usr/)?", "", so); print so }' \
        | sort -u \
        | xargs -r dpkg-query --search \
        | cut -d: -f1 \
        | sort -u \
        | xargs -rt apt-mark manual; \
    \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
    rm -rf /var/lib/apt/lists/*

RUN mkdir -p \
    /var/log/supervisord \
    /var/run/supervisord \
;

# Install additional dependencies
RUN set -ex; \
    \
    apt-get update && \
    \
    apt-get install -y build-essential wget cmake libx11-dev libopenblas-dev git 

# Install dlib
RUN set -ex; \
    git clone https://github.com/davisking/dlib.git && \
    cd dlib/dlib && \
    mkdir build && \
    cd build && \
    cmake -DBUILD_SHARED_LIBS=ON .. && \
    make && \
    make install

# Install pdlib
RUN set -ex; \
    git clone https://github.com/goodspb/pdlib.git && \
    cd pdlib && \
    phpize && \
    ./configure --enable-debug && \
    make && \
    make install

# Enable pdlib in PHP ###zzz ensures it will load last and override any other settings
RUN echo 'extension="pdlib.so"' >> /usr/local/etc/php/conf.d/zzz-php.ini \
# Explicitly Increase memory limit in PHP
    echo 'memory_limit = 9600M' >> /usr/local/etc/php/conf.d/zzz-php.ini

COPY supervisord.conf /

COPY mycronfile /var/spool/cron/crontabs/www-data

ENV NEXTCLOUD_UPDATE=1

CMD ["/usr/bin/supervisord", "-c", "/supervisord.conf"]