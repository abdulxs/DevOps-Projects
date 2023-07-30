Part 1: Configure storage subsystem for Web and Database servers based on Linux OS.


Steps

Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server, each of 10 GiB.

Attach all three volumes one by one to your Web Server instance

Open up the Linux terminal to begin configuration of the instance. Use ```lsblk``` command to inspect what block devices are attached to the server. The 3 created block devices are names xvda, xvdb, xvdc

Use df -h command to see all mounts and free space on your server

<img width="808" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/21842a10-b52d-4700-9558-ba18ecdef3a8">


Use gdisk utility to create a single partition on each of the 3 disks ```sudo gdisk /dev/xvdf```

A prompt pops up, type ```n```, to create new partition. Enter the number of partition(in my case 1). Hex code is 8e00. Type ```p```, to view partition and finally ```w```, to save newly created partition.

Repeat this process for the other remaining block devices.

Type ```lsblk``` to view newly created partition.

<img width="810" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/fbbc6c79-428f-415c-b029-3ae852256b56">

Install lvm2 package by typing: ```sudo yum install lvm2```. Run ```sudo lvmdiskscan``` command to check for available partitions.

Create physical volume to be used by lvm by using the pvcreate command:

```
sudo pvcreate /dev/sda1
sudo pvcreate /dev/sdb1
sudo pvcreate /dev/sdc1
```

To check if the PV have been created type: ```sudo pvs```

<img width="1255" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/bb9c111a-2b02-467c-8c78-fc79a081ce73">

added an extra volume, attached and did all previous configurations done for other volumes

<img width="622" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/27c815c1-abb4-485e-8ba3-a2747b3a39bd">

Next, Create the volume group and name it webdata-vg: ```sudo vgcreate webdata-vg /dev/xvdf1 /dev/sdb1 /dev/sdc1```

View newly created volume group type: ```sudo vgs```

<img width="813" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/6232f751-7f94-4226-8d2e-5c4044c258d5">

Create 2 logical volumes using lvcreate utility. Name them: ```apps-lv``` for storing data for the Website and ```logs-lv``` for storing data for logs.

```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

<img width="860" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/22909b9d-fb66-45ec-a62b-ad9e26ba616d">

Verify Logical Volume has been created successfully by running: ```sudo lvs```
Next, format the logical volumes with ext4 filesystem:

```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```
<img width="868" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/32263458-d84d-4647-9c38-818d557ab473">

Next, create mount points for logical volumes. Create /var/www/html directory to store website files: ```sudo mkdir -p /var/www/html``` then mount /var/www/html on apps-lv logical volume : ```sudo mount /dev/webdata-vg/apps-lv /var/www/html/```

<img width="853" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/75d24f85-2d1b-429f-8847-91ab36fd2d70">

Create /home/recovery/logs to store backup of log data: ```sudo mkdir -p /home/recovery/logs```

Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (It is important to backup all data on the /var/log directory because all the data will be deleted during the mount process) Type the following command: ```sudo rsync -av /var/log/. /home/recovery/logs/```

Mount /var/log on logs-lv logical volume: ```sudo mount /dev/webdata-vg/logs-lv /var/log```

Finally, restore deleted log files back into /var/log directory: ```sudo rsync -av /home/recovery/logs/. /var/log```

Next, update /etc/fstab file so that the mount configuration will persist after restart of the server.

The UUID of the device will be used to update the /etc/fstab file to get the UUID type: ```sudo blkid``` and copy both the apps-vg and logs-vg UUID (Excluding the double quotes)

<img width="1429" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/167f055a-599a-4cd1-8da4-4e80ec09e31e">

logs-vg = be859168-ddab-408a-b8e8-bc1e44db7301
apps-vg = 747a4abb-c8d1-4d02-95d5-9b8677fcd058

Type ```sudo vi /etc/fstab``` to open editor and update using the UUID you copied.

<img width="1186" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/b711ce0f-e9d7-44a9-8209-93309041f0e5">

Test the configuration and reload the daemon:

```
sudo mount -a`
sudo systemctl daemon-reload
```

Verify your setup by running ```df -h```

<img width="1434" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/94379424-784c-4b47-9641-bd3b14fe3832">



Part 2 - Prepare the Database Server



Launch a second RedHat EC2 instance and name it DB Server

Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

<img width="1440" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/044a74a4-c120-4324-8c88-90027ee71ef8">

<img width="1440" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/044ae5eb-b6c6-4ddf-80c8-84733474f229">

<img width="1440" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/e56dcc2f-f9bd-41d7-b5d1-aa7ca24ae3f9">

<img width="1440" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/fa61aceb-ef54-4150-abc3-bffe5a80c834">

<img width="1440" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/aee870a0-c122-48d8-a223-d2b54730a139">

<img width="1440" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/2c6e348d-5e85-4cdc-ac18-32ed12520cb2">


logs-vg uuid = 7947ff40-7954-4245-9b46-dbb3f2cdba71
db-vg uuid = 93ae9603-ce03-4baa-8e39-03d6fed6c3a8

<img width="567" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/14a75c0a-b1c6-4fe6-a255-f7b418766b1a">

<img width="1440" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/d567414f-0f47-417c-b546-89271e4c9429">



Part 3 -Install WordPress and connect it to a remote MySQL database server.

Update the repository: ```sudo yum -y update```

Install wget, Apache and it’s dependencies: ```sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json```
Start Apache

```
sudo systemctl enable httpd
sudo systemctl start httpd
```

<img width="1440" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/a505c46f-b3e5-4d25-a9b8-4b0ea923cc29">

install PHP and it’s depemdencies:

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

Restart Apache: ```sudo systemctl restart httpd```
Download wordpress and copy wordpress to var/www/html

```
mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cp -R wordpress /var/www/html/
```





