FROM alpine:3.16

RUN apk update && apk upgrade && apk add --no-cache mariadb mariadb-client
RUN mkdir /var/run/mysqld; chmod 777 /var/run/mysqld && \
	sed -i "s|skip-networking|skip-networking=0|g" /etc/my.cnf.d/mariadb-server.cnf
COPY requirements/mariadb/conf/docker.cnf /etc/my.cnf.d/
RUN mysql_install_db --user=mysql --datadir=/var/lib/mysql

COPY requirements/mariadb/tools/createdb.sh /scripts/
RUN sh /scripts/createdb.sh && rm -f /scripts/createdb.sh

USER mysql
CMD ["/usr/bin/mariadbd-safe", "--skip-log-error"]
