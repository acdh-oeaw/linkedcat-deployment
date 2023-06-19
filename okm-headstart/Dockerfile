FROM acdhch/linkedcat-okm-headstart:stable

RUN cd /var && rm -fr /var/www && git clone --branch master --single-branch https://github.com/OpenKnowledgeMaps/Headstart.git www && cd www && git checkout -b stable 2732c3f9977f727eb0ce48ed17c152b012fd9972
RUN mkdir -p /var/www/exec /var/www/config &&\
    chown www-data:www-data /var/www