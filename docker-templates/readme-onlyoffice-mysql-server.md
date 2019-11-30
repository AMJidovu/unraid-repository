# I will add info later on that I might miss to add right now.
# Also you can execute the lines one after another or you can make a bash script and launch it.
# This are files required to be created before any docker is started except the ones needed 
# for the SSL.

sudo mkdir -p "/mnt/user/appdata/onlyoffice/mysql/conf.d"; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/mysql/data"; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/mysql/initdb"; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/mysql/logs"; \
chown 999:999 /mnt/user/appdata/onlyoffice/mysql/logs; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/CommunityServer/data"; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/CommunityServer/logs"; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/DocumentServer/data"; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/DocumentServer/logs"; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/MailServer/data/certs"; \
sudo mkdir -p "/mnt/user/appdata/onlyoffice/MailServer/logs";

# This is when we create the onlyoffice network.

sudo docker network create --driver bridge onlyoffice

# This is when we create the the sql configs etc required before running the onlyoffice mysql docker

echo "[mysqld]
sql_mode = 'NO_ENGINE_SUBSTITUTION'
max_connections = 1000
max_allowed_packet = 1048576000
group_concat_max_len = 2048
log-error = /var/log/mysql/error.log" > /mnt/user/appdata/onlyoffice/mysql/conf.d/onlyoffice.cnf
sudo chmod 0644 /mnt/user/appdata/onlyoffice/mysql/conf.d/onlyoffice.cnf

# Replace 'onlyoffice_user' with your user name for the db and 'onlyoffice_pass' with the password
# Do the same with 'my-secret-pw' for the root user and 'mail_admin' and 'Isadmin123'
# for the mail database.

echo "CREATE USER 'onlyoffice_user'@'localhost' IDENTIFIED BY 'onlyoffice_pass';
CREATE USER 'mail_admin'@'localhost' IDENTIFIED BY 'Isadmin123';
GRANT ALL PRIVILEGES ON * . * TO 'root'@'%' IDENTIFIED BY 'my-secret-pw';
GRANT ALL PRIVILEGES ON * . * TO 'onlyoffice_user'@'%' IDENTIFIED BY 'onlyoffice_pass';
GRANT ALL PRIVILEGES ON * . * TO 'mail_admin'@'%' IDENTIFIED BY 'Isadmin123';
FLUSH PRIVILEGES;" > /mnt/user/appdata/onlyoffice/mysql/initdb/setup.sql

# The following lines are required if you are going to install and use SSL so you do not need
# to execute them before you launch any docker.

mkdir -p /mnt/user/appdata/onlyoffice/CommunityServer/data/certs
cp onlyoffice.key /mnt/user/appdata/onlyoffice/CommunityServer/data/certs/
cp onlyoffice.crt /mnt/user/appdata/onlyoffice/CommunityServer/data/certs/
cp dhparam.pem /mnt/user/appdata/onlyoffice/CommunityServer/data/certs/
chmod 400 /mnt/user/appdata/onlyoffice/CommunityServer/data/certs/onlyoffice.key


