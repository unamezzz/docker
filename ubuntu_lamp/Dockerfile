FROM ubuntu
#my lamp
#Ubuntu 16.04.3 LTS 阿里云 20171127
#
WORKDIR /root
RUN apt-get update
#设置时区
RUN apt-get install -y tzdata
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

RUN apt-get install -y apache2 php libapache2-mod-php7.0

#设置apache2显示500错误详情
RUN sed -i 's/^display_errors = Off$/display_errors = On/g' /etc/php/7.0/apache2/php.ini

#无交互安装mysql
RUN echo "mysql-server mysql-server/root_password password 123456" | debconf-set-selections
RUN echo "mysql-server mysql-server/root_password_again password 123456" | debconf-set-selections
RUN apt-get install -y mysql-server

#个人习惯
RUN echo '127.0.0.1 a' >> /etc/hosts
RUN rm -rf /var/www/html/*
RUN echo '<?php echo "hello\\n";?>' > /var/www/html/index.php
RUN echo '<?php echo phpinfo();?>' > /var/www/html/phpinfo.php

#抄书的环境变量
RUN mkdir -p /var/lock/apache2 
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_SERVERADMIN zzz@localhost
ENV APACHE_SERVERNAME localhost
ENV APACHE_SERVERALIAS zzz.localhost
ENV APACHE_DOCUMENTROOT /var/www

#虽然违背docker思想，但我还是要安装一些开发工具
RUN apt-get install -y nmap curl net-tools iputils-ping vim

RUN echo '#!/bin/bash' > /root/run.sh
RUN echo 'service mysql start' >> /root/run.sh
RUN echo 'exec apache2 -D FOREGROUND' >> /root/run.sh
RUN chmod a+x /root/run.sh
CMD ["/root/run.sh"]