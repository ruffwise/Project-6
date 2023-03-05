# Project-6

'lblk'
![inspect block devices](/Images/lblk.jpg)

### install lvm2 package

`sudo yum install lvm2`

### mark disk as physical volumes (PVs)

`sudo pvcreate /dev/xvdf1`

`sudo pvcreate /dev/xvdg1`

`sudo pvcreate /dev/xvdh1`
![pvcreate](/Images/pvcreate.jpg)

### Add the 3 PVs to a volume group

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`
![create volume group](/Images/volume%20group.jpg)

### create 2 logical volumes

`sudo lvcreate -n apps-lv -L 14G webdata-vg`

`sudo lvcreate -n logs-lv -L 14G webdata-vg`
![logical volume](/Images/logical%20volume.jpg)

### format logical volume

`sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`

`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`
![format logical volume](/Images/format.jpg)

### create directory and mount them on logical volume

`sudo mkdir -p /var/www/html`

`sudo mkdir -p /home/recovery/logs`

`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

#### backup logs files

`sudo rsync -av /var/log/. /home/recovery/logs/`

`sudo mount /dev/webdata-vg/logs-lv /var/log`

#### resotore back logs files

`sudo rsync -av /home/recovery/logs/. /var/log`

### update /etc/fstab file

![fstab](/Images/fstab.jpg)

`df -h`
![df output](/Images/df-output.png)

## prepare the database server

`sudo pvcreate /dev/xvdf1`

`sudo pvcreate /dev/xvdg1`

`sudo pvcreate /dev/xvdh1`

`sudo vgcreate database-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

### create logical volumes

`sudo lvcreate -n database-lv -L 14G database-vg`

`sudo lvcreate -n logs-lv -L 14G database-vg`

### format logical volume

`sudo mkfs -t ext4 /dev/database-vg/database-lv`
![database logical volume](/Images/database%20logical%20volume.png)

### update /etc/fstab file for database server

![fstab](/Images/fstab-database.png)

# Install word press

`sudo yum install update`

### Install wget, Apache and it’s dependencies

`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

### Start Apache

`sudo systemctl enable httpd`
`sudo systemctl start httpd`

# Install php and it's dependencies

`sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

`sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

`sudo yum module list php`

`sudo yum module reset php`

`sudo yum module enable php:remi-7.4`

`sudo yum install php php-opcache php-gd php-curl php-mysqlnd`

`sudo systemctl enable php-fpm`

`sudo setsebool -P httpd_execmem 1`

### restart apache

`sudo systemctl restart httpd`

### Download wordpress and copy wordpress to var/www/html

`mkdir wordpress`

`cd   wordpress`

`sudo wget http://wordpress.org/latest.tar.gz`

`sudo tar xzvf latest.tar.gz`

`sudo rm -rf latest.tar.gz`

`cp wordpress/wp-config-sample.php wordpress/wp-config.php`

`cp -R wordpress /var/www/html/`

### Configure SELinux Policies

`sudo chown -R apache:apache /var/www/html/wordpress`

`sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`

`sudo setsebool -P httpd_can_network_connect=1`

### Install mysql on database server

`sudo yum update`

`sudom yum install mysql-server`

### configure DB to work with wordpress

`sudo mysql
CREATE DATABASE wordpress;
CREATE USER `mysql_admin`@`172.31.1.130` IDENTIFIED BY 'welcome123';
GRANT ALL ON wordpress.\* TO 'mysql_admin'@'172.31.1.130';
FLUSH PRIVILEGES;
SHOW DATABASES;
![create wordpress database](/Images/wordpress-database.png)

### connect to database from webserver

![](/Images/connect-to-database-from-webserver.png)

### wordpress site

![](/Images/wordpress.jpg)
![](/Images/wordpress2.jpg)
