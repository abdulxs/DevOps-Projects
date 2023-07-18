TASK – Implement a Client Server Architecture using MySQL Database Management System (DBMS).
Steps

Spin up two Linux-based virtual servers (EC2 instances in AWS) and name them: mysql server and mysql client respectively.

Run ```sudo apt update -y``` for lastest updates on server.

<img width="1020" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/ff08d787-94ed-4709-a999-40df7bc67596">

Next, on mysql server install MySQL Server software: ```sudo apt install mysql-server -y```

<img width="1407" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/468e6dd0-5d7e-4470-9de7-d993a3d6e780">


On mysql client install MySQL Client software: ```sudo apt install mysql-client -y```

<img width="1402" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/4be60dbf-f4d2-42c1-a76e-57aa0f1b26e7">

Edit Inbound rule on mysql server to allow access to mysql client traffic. MySQL server uses TCP port 3306 by default. Specify inbound traffic from the IP of mysql cient for extra security.







