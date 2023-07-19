Part 1: Configure storage subsystem for Web and Database servers based on Linux OS.


Steps

Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server, each of 10 GiB.

Attach all three volumes one by one to your Web Server instance

Open up the Linux terminal to begin configuration of the instance. Use lsblk command to inspect what block devices are attached to the server. The 3 newly created block devices are names xvdf, xvdh, xvdg

Use df -h command to see all mounts and free space on your server
