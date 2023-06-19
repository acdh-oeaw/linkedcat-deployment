FROM ghcr.io/acdh-oeaw/linkedcat-deployment/okm-headstart:stable

RUN cd /var && rm -fr /var/www && git clone --branch master --single-branch https://github.com/OpenKnowledgeMaps/Headstart.git www && cd www && git reset --hard 2662bcc
RUN mkdir -p /var/www/exec /var/www/config &&\
    chown www-data:www-data /var/www