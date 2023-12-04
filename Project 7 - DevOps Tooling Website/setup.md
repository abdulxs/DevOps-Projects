<img width="1016" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/8830f612-ed34-4484-a7c9-85cc76918730">

Prerequites
Provision 4 Red Hat Enterprise Linux 9. One will be the NFS server and the other as the Web servers.
Provision 1 Ubuntu 22.04 for the the databaes server.
<img width="1169" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/a40f9368-252d-44e7-8190-ee624518458a">

Step 1 - Prepare NFS server
To view all logical volumes, run the command ```lsblk``` 
The 3 newly created block devices are names xvdf, xvdh, xvdg respectively.

Use gdisk utility to create a single partition on each of the 3 disks ```sudo gdisk /dev/xvdf```

A prompt pops up, type ``n`` to create new partition, enter no of partition(1), hex code is 8300, ``p`` to view partition and ``w`` to save newly created partition.

Repeat this process for the other remaining block devices.

Type ``lsblk`` to view newly created partition.

Install lvm2 package by typing: ```sudo yum install lvm2``` then run ```sudo lvmdiskscan``` command to check for available partitions.

