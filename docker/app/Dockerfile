FROM php:7.4-apache

# 変数を宣言する
ARG DOCKER_UID=1000
ARG DOCKER_USER=docker
ARG DOCKER_PASSWORD=docker

# 設定ファイルをdockerコンテナ内のPHP、Apacheに読み込ませる
ADD php.ini /usr/local/etc/php/
ADD 000-default.conf /etc/apache2/sites-enabled/

# composerをインストールする
RUN curl -sS https://getcomposer.org/composer-2.phar -o /usr/local/bin/composer && chmod +x /usr/local/bin/composer

# パッケージをインストールする
RUN apt-get update \
  && apt-get install -y git zip unzip \
  && apt-get install -y sudo \
  && apt-get install -y vim \
  && apt-get install -y libicu-dev \
  && apt-get install -y libonig-dev \
  && docker-php-ext-install pdo_mysql intl

# Laravelで必要になるmodRewriteを有効化する
RUN mv /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled
RUN /bin/sh -c a2enmod rewrite

# ユーザを作成する
RUN useradd -m ${DOCKER_USER} -u ${DOCKER_UID}
RUN echo "${DOCKER_USER}:${DOCKER_PASSWORD}" | chpasswd

# 作成したユーザのサブグループにsudoを追加する
RUN usermod -aG sudo ${DOCKER_USER}

# 作成したユーザに切り替える
# USER ${DOCKER_USER}

# 作業ディレクトリを変更する
WORKDIR /var/www/html
