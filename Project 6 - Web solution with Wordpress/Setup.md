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

Type ```sudo vi /etc/fstab``` to open editor and update using the UUID you copied.










