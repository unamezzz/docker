FROM centos:6
RUN rpm -Uvh http://repo.webtatic.com/yum/el6/latest.rpm
RUN yum install -y unzip httpd mysql-server php55w php55w-mysql php55w-gd
RUN sed -i 's/#ServerName www.example.com:80/ServerName localhost:80/g' /etc/httpd/conf/httpd.conf
RUN sed -i "/^<Directory \"\/var\/www\/html\">$/,/^<\/Directory>$/s/AllowOverride None/AllowOverride All/g" /etc/httpd/conf/httpd.conf
RUN sed -i 's/;date.timezone =/date.timezone = "Asia\/Shanghai"/g' /etc/php.ini
RUN rm -rf /var/www/html 
COPY www.zip /var/www/
RUN unzip /var/www/www.zip -d /var/www/
RUN mv /var/www/www.zip /var/www/html/
RUN chown apache /var/www/html -R && chgrp apache /var/www/html -R
COPY run.sh /root/
COPY setflag.sh /root/
COPY setmysqlrootpass.sh /root/
RUN chmod a+x /root/*.sh
RUN echo "Flag{***" > /flag
RUN chmod 444 /flag
CMD ["/root/run.sh"]