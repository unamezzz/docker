FROM ubuntu
#Ubuntu 16.04.3 LTS 20171127
WORKDIR /root
RUN apt-get update
RUN apt-get install -y nginx php7.0 php7.0-dev php7.0-fpm wget rinetd
#选择性地安装一些传文件的软件
#RUN apt-get install -y curl lrzsz iputils-ping net-tools
#设置php-fpm监听/run/php/php7.0-fpm.sock
RUN mkdir /run/php/ -p
RUN touch /run/php/php7.0-fpm.sock
#配置nginx调用php-fpm
RUN sed -i "/^\t#location ~ \\\.php\$ {$/,/\t#}$/s/^\t#/\t/g" /etc/nginx/sites-available/default
RUN sed -i "/^\tlocation ~ \\\.php\$ {$/,/\t}$/s/\t\tfastcgi_pass 127.0.0.1:9000;/\t\t#fastcgi_pass 127.0.0.1:9000;/g" /etc/nginx/sites-available/default

#由于xdebug回连是根据xff头来设定remote_host的，这里配置fastcgi的xff头为127.0.0.1
RUN sed -i '/^\t\tinclude snippets\/fastcgi-php.conf;$/a\\t\tfastcgi_param HTTP_X_FORWARDED_FOR 127.0.0.1;' /etc/nginx/sites-available/default
#设置端口转发，把容器中访问127.0.0.1:9000的流量转发到宿主机的172.17.0.1:9000
RUN sed -i '/^# bindadress/a\127.0.0.1 9000 172.17.0.1 9000' /etc/rinetd.conf

#设置默认主页index.php
RUN sed -i 's/^\tindex index.html/\tindex index.php index.html/g' /etc/nginx/sites-available/default
RUN echo '<?php echo "hello";?>' > /var/www/html/index.php
RUN echo '<?php phpinfo();?>' > /var/www/html/phpinfo.php

#安装xdebug
RUN wget http://xdebug.org/files/xdebug-2.5.4.tgz
RUN tar -xzf xdebug-2.5.4.tgz
WORKDIR /root/xdebug-2.5.4
RUN phpize
RUN ./configure
RUN make
RUN cp modules/xdebug.so /usr/lib/php/20151012

#php.ini配置xdebug
#这里要写入fpm目录下的php.ini而不是cli目录下的php.ini
#/etc/php/7.0/cli/php.ini
#php -i|grep xdebug.remote查看的配置文件是cli目录下的，但实际是要解析加载fpm目录的配置文件
#写个phpinfo.php到根目录看就能发些写入cli和fpm两个目录下php.ini文件的区别
RUN echo '[xdebug]' >> /etc/php/7.0/fpm/php.ini
RUN echo 'zend_extension = /usr/lib/php/20151012/xdebug.so' >> /etc/php/7.0/fpm/php.ini
RUN echo 'xdebug.remote_enable=1' >> /etc/php/7.0/fpm/php.ini
#安全起见，不开remote_connect_back
#RUN echo 'xdebug.remote_connect_back=1' >> /etc/php/7.0/fpm/php.ini
#RUN echo 'xdebug.remote_handler=dbgp' >> /etc/php/7.0/fpm/php.ini
#RUN echo 'xdebug.remote_host=127.0.0.1' >> /etc/php/7.0/fpm/php.ini
#RUN echo 'xdebug.remote_port=9000' >> /etc/php/7.0/fpm/php.ini
#RUN echo 'xdebug.remote_autostart=1' >> /etc/php/7.0/fpm/php.ini

#启动php-fpm
#php-fpm7.0
#如果要重启php-fpm, kill掉然后重启即可
#ps aux | grep php-fpm
#kill xxxx
#php-fpm7.0

WORKDIR /root
RUN echo '#!/bin/bash' > /root/run.sh
RUN echo 'php-fpm7.0' >> /root/run.sh
RUN echo 'rinetd -c /etc/rinetd.conf' >> /root/run.sh
RUN echo 'nginx -g "daemon off;"' >> /root/run.sh
RUN chmod a+x /root/run.sh
CMD ["/root/run.sh"]