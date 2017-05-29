# Set base image
FROM mysql:5.7

# Copy custom configuration file from the current directory
COPY conf.d/dockerstack.cnf /etc/mysql/conf.d/dockerstack.cnf

# Set the mandatory Mysql root password
ENV MYSQL_ROOT_PASSWORD rootpass
