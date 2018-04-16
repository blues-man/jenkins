FROM registry.access.redhat.com/openshift3/jenkins-slave-base-rhel7

MAINTAINER Red Hat

# Labels consumed by Red Hat build service
LABEL com.redhat.component="jenkins-slave-php-rhel7-docker" \
      name="openshift3/jenkins-slave-php-rhel7" \
      version="3.7" \
      architecture="x86_64" \
      release="4" \
      io.k8s.display-name="Jenkins Slave PHP" \
      io.k8s.description="The jenkins slave PHP image has the PHP tools on top of the jenkins slave base image." \
      io.openshift.tags="openshift,jenkins,slave,php"

ENV HOME=/home/jenkins
ENV PHP_VERSION=7.1 \
    PHP_VER_SHORT=71 \
    NAME=php \
    PATH=$PATH:/opt/rh/rh-php71/root/usr/bin

############################################
##    install required software for PHP   ##
############################################
RUN yum install --disablerepo=rhel-7-server-htb-rpms -y yum-utils && \
    yum-config-manager --disable * && \
    yum-config-manager --enable rhel-server-rhscl-7-rpms && \
    yum-config-manager --enable rhel-7-server-rpms && \
    yum-config-manager --enable rhel-7-server-optional-rpms && \
    INSTALL_PKGS="rh-php71 rh-php71-php rh-php71-php-mysqlnd rh-php71-php-pgsql rh-php71-php-bcmath \
                  rh-php71-php-gd rh-php71-php-intl rh-php71-php-ldap rh-php71-php-mbstring rh-php71-php-pdo \
                  rh-php71-php-process rh-php71-php-soap rh-php71-php-opcache rh-php71-php-xml \
                  rh-php71-php-gmp rh-php71-php-pecl-apcu httpd24-mod_ssl" && \
    yum install --disablerepo=rhel-7-server-htb-rpms -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y

ENV PHP_CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/php/ \
    APP_DATA=${APP_ROOT}/src \
    PHP_DEFAULT_INCLUDE_PATH=/opt/rh/rh-php71/root/usr/share/pear \
    PHP_SYSCONF_PATH=/etc/opt/rh/rh-php71 \
    PHP_HTTPD_CONF_FILE=rh-php71-php.conf \
    HTTPD_CONFIGURATION_PATH=${APP_ROOT}/etc/conf.d \
    HTTPD_MAIN_CONF_PATH=/etc/httpd/conf \
    HTTPD_MAIN_CONF_D_PATH=/etc/httpd/conf.d \
    HTTPD_VAR_RUN=/var/run/httpd \
    HTTPD_DATA_PATH=/var/www \
    HTTPD_DATA_ORIG_PATH=/opt/rh/httpd24/root/var/www \
    HTTPD_VAR_PATH=/opt/rh/httpd24/root/var \
    SCL_ENABLED=rh-php71

##########################################
##    install additional PHP software   ##
##########################################
RUN curl -O https://phar.phpunit.de/phpunit.phar \
    && chmod +x phpunit.phar \
    && mv phpunit.phar /usr/local/bin/phpunit \
    && curl -O http://getcomposer.org/composer.phar \
    && chmod +x composer.phar \
    && mv composer.phar /usr/local/bin/composer \
    && git clone https://github.com/phacility/libphutil.git /opt/libphutil \
    && git clone https://github.com/phacility/arcanist.git /opt/arcanist

#####################
##    last steps   ##
#####################
# Copy extra files to the image.
COPY ./root/ /

# Reset permissions of filesystem to default values
# RUN /usr/libexec/container-setup && rpm-file-permissions

USER 1001
