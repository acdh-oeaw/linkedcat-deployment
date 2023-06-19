FROM rocker/r-ver:3.5.3
LABEL maintainer="Dalibor Pancic dalibor.pancic@oeaw.ac.at"

ENV DEBIAN_FRONTEND=noninteractive \
    HEADSTART_LOGFILE=/var/log/apache2/okm.log \
    NODE_VERSION=10.19.0 \
    LINKEDCAT_SOLR_SCHEME="http" \
    LINKEDCAT_SOLR="solr" \
    LINKEDCAT_SOLR_PORT="8983" \
    LINKEDCAT_SOLR_PATH="solr/linkedcat2"

RUN apt update && apt install -y supervisor apache2 apache2-utils libapache2-mpm-itk \
                 links vim locales curl wget \
                 libapache2-mod-php php-cli php-pear php-pgsql php-zip \
                 php-mysql php-gd php-recode php-readline php-json \
                 php-curl php-gmp php-ldap php-odbc php-sqlite3 \
                 php-xmlrpc php-intl php-xsl php-mbstring php-apcu \
                 php-zip php-yaml php-imagick mariadb-client w3c-sgml-lib \
                 composer \
                 php-xml php-fileinfo
RUN apt install -y libxml2-dev libjpeg62-turbo-dev libz-dev \
                 libpoppler-cpp-dev \
                 libssl1.1 libcurl4-openssl-dev libssl-dev \
                 libasound2 libatk1.0 libatk-bridge2.0 libgtk-3-dev 
RUN apt install -y fonts-ipafont-gothic fonts-wqy-zenhei fonts-thai-tlwg fonts-kacst fonts-freefont-ttf \
                 g++ build-essential \
                 gconf-service libasound2 libatk1.0-0 libatk-bridge2.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget 
RUN apt clean 
RUN rm -f /etc/localtime && \
  ln -s /usr/share/zoneinfo/Europe/Vienna /etc/localtime && \
  dpkg-reconfigure --frontend noninteractive tzdata && \
  a2enmod rewrite && \
  a2enmod headers && \
  a2enmod proxy && \
  a2enmod proxy_http && \
  sed -i -e 's/StartServers.*/StartServers 1/g' /etc/apache2/mods-enabled/mpm_prefork.conf && \
  sed -i -e 's/MinSpareServers.*/MinSpareServers 1/g' /etc/apache2/mods-enabled/mpm_prefork.conf && \
  locale-gen en_US.UTF-8 && \
  mkdir -p /var/www/exec /var/www/config &&\
  chown www-data:www-data /var/www/exec /var/www/config &&\
  sed -i -e 's/upload_max_filesize.*/upload_max_filesize = 100M/' -e 's/post_max_size.*/post_max_size = 100M/' -e 's/memory_limit.*/memory_limit = 512M/' -e 's/max_input_time.*/max_input_time = 480/' -e 's/max_execution_time.*/max_execution_time = 240/' \ /etc/php/7.0/apache2/php.ini && \
  wget https://nodejs.org/dist/v10.19.0/node-v$NODE_VERSION-linux-x64.tar.gz && \
  tar -xvzf "node-v$NODE_VERSION-linux-x64.tar.gz" -C /usr/local --strip-components=1 --no-same-owner && \
  rm "node-v$NODE_VERSION-linux-x64.tar.gz" && \
  ln -s /usr/local/bin/node /usr/local/bin/nodejs && \
  # smoke tests
  node --version && \
  npm --version && \
  npm install -g puppeteer --unsafe-perm=true && \
  echo kernel.unprivileged_userns_clone=1 > /etc/sysctl.d/00-local-userns.conf && \
  service procps restart

# https://github.com/OpenKnowledgeMaps/Headstart/blob/master/doc/server_config.md
RUN R -e "install.packages('remotes')" && \
    R -e "remotes::install_version('curl', version = '3.3')" && \
    R -e "remotes::install_version('logging', version = '0.9-107')" && \
    R -e "install.packages('arsenal')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/proxy/proxy_0.4-20.tar.gz')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/caTools/caTools_1.17.1.3.tar.gz')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/GMD/GMD_0.3.3.tar.gz')"  && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/MASS/MASS_7.3-51.4.tar.gz')"
RUN R -e "remotes::install_version('ecodist', version = '2.0.1')" && \
    R -e "remotes::install_version('jsonlite', version = '1.6')" && \
    R -e "remotes::install_version('SnowballC', version = '0.6.0')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/rplos/rplos_0.8.4.tar.gz')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/parfossil/parfossil_0.2.0.tar.gz')" && \
    R -e "remotes::install_version('doParallel', version = '1.0.14')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/rentrez_1.2.2.tar.gz')" && \
    R -e "remotes::install_url('https://github.com/tidyverse/tibble/archive/v2.1.3.tar.gz')"
RUN R -e "remotes::install_version('data.table', version = '1.12.2')" && \
    R -e "remotes::install_version('stringi', version = '1.4.3')" && \
    R -e "remotes::install_version('stringdist', version = '0.9.5.1')" && \
    R -e "remotes::install_version('xml2', version = '1.2.0')" && \
    R -e "remotes::install_version('readr', version = '1.3.1')" && \
    R -e "remotes::install_version('plyr', version = '1.8.4')" && \
    R -e "remotes::install_version('textcat', version = '1.0-6')"
RUN R -e "remotes::install_version('onehot', version = '0.1.1')" && \
    R -e "remotes::install_version('jaod', version = '0.1.0')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/vegan_2.5-6.tar.gz')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/slam/slam_0.1-37.tar.gz')" && \
    R -e "remotes::install_version('tm', version = '0.6')" && \
    R -e "remotes::install_github('ropensci/rbace')"  && \
    R -e "remotes::install_github('sckott/ropenaire')"  && \
    R -e "remotes::install_github('ropensci/rAltmetric')"
RUN R -e "remotes::install_github('ropensci/rcrossref@async')" && \
    R -e "remotes::install_github('chreman/rcoreoa@description-filter')" && \
    R -e "remotes::install_github('chreman/solrium')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/dplyr/dplyr_0.8.1.tar.gz')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/data.table/data.table_1.12.2.tar.gz')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/tidyr/tidyr_0.8.3.tar.gz')" && \
    R -e "remotes::install_url('https://cran.r-project.org/src/contrib/Archive/forcats/forcats_0.4.0.tar.gz')"


WORKDIR /var/www

#@INJECT_USER@

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
CMD ["/usr/bin/supervisord"]
EXPOSE 80