FROM ghcr.io/acdh-oeaw/linkedcat-deployment/okm-headstart:stable

COPY custom /custom

RUN cd /var && rm -fr /var/www && git clone --branch master --single-branch https://github.com/OpenKnowledgeMaps/Headstart.git www && cd www && git reset --hard 2662bcc

RUN mkdir -p /var/www/html && \
    mv /var/www/examples/linkedcat /var/www/ && \
    cp -r /var/www/server/storage/headstart_test.sqlite /var/www/server/storage/db.sqlite && \
    cp -r /custom/config_local.ini /var/www/server/preprocessing/conf/config_local.ini && \
    rm -fR /var/www/config.js && \
    cp -r /custom/config.js /var/www/config.js 

RUN sed -i 's|"dist"|"linkedcat/dist"|g' /var/www/webpack.config.js 

RUN sed -i 's|scheme="https"|scheme="http"|g' $(find /var/www/server/preprocessing/other-scripts/ -type f) && \
    sed -i 's|port=NULL|port=8983|g' $(find /var/www/server/preprocessing/other-scripts/ -type f) && \
    sed -i 's|host=host|host="solr"|g' $(find /var/www/server/preprocessing/other-scripts/ -type f) && \
    sed -i 's|$base_url = "https://"|$base_url = "http://"|g' $(find /var/www/server/services/ -type f) && \
    sed -i 's|. "/solr/linkedcat2|. ":8983" . "/solr/linkedcat2|g' $(find /var/www/server/services/ -type f)

RUN cp /var/www/linkedcat/config_example.php /var/www/linkedcat/config.php && \
    sed -i 's|http://localhost/linkedcat|https://linkedcat.acdh.oeaw.ac.at|g' /var/www/linkedcat/config.php

RUN cd /var/www/linkedcat && \
    ln -s ../server server && \
    ln -s ../vis vis

RUN sed -i 's|../../../../js|../../../js|g' /var/www/server/services/snapshot/headstart_snapshot.php && \
    sed -i 's|../../../dist|dist|g' /var/www/server/services/snapshot/headstart_snapshot.php

RUN cp -r /custom/.htaccess /var/www/linkedcat/

RUN cd /var/www/ && npm install && npm run prod 

RUN rm -f /etc/apache2/sites-enabled/000-default.conf && \
    cp /custom/000-default.conf /etc/apache2/sites-enabled/000-default.conf

RUN mkdir -p /var/www/exec /var/www/config &&\
    chown -R www-data:www-data /var/www && \
    rm -fr /custom