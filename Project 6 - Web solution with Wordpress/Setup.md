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

Next, Create the volume group and name it webdata-vg: ```sudo vgcreate webdata-vg /dev/xvdf1 /dev/xsdb1 /dev/sda1```

View newly created volume group type: ```sudo vgs```









