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

<img width="1080" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/65c19e0c-43ad-4395-9bfd-52f0ef96860c">

For mysql client to gain remote access to mysql server we need to create a database and a user on mysql server. To start with, run the mysql security script: ```sudo mysql_secure_installation ``` Follow the prompts and answer appropraitely to finish the process.

password set as 'Password12'

<img width="821" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/8f740c64-98a6-4e72-b07c-1955e6c77b45">

Run mysql command: ```sudo mysql``` This would take you to the mysql prompt (You may be required to input password if you opted for the validate password during the security script installation)

Next, create the remote user with this following command: ```CREATE USER 'remote_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';```

<img width="867" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/1a4d569c-ee2e-4ef4-af24-afc6860214ca">

Create database with: ```CREATE DATABASE test_db;```

Then grant privieges to remote_user: ```GRANT ALL ON test_db.* TO 'remote_user'@'%' WITH GRANT OPTION;```

. Finally, flush privileges and exit mysql : ```FLUSH PRIVILEGES;```

<img width="820" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/416824d1-c37f-4d4c-ab86-36d408806543">

Having created the user and database, configure MySQL server to allow connections from remote hosts. Use the following command: ```sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf```

In the text editor, replace the old Bind-address from ‘127.0.0.1’ to ‘0.0.0.0’ then save and exit.

<img width="908" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/c0b06a80-b500-44bb-86fb-7a903b322659">

Next, we restart mysql with: ```sudo systemctl restart mysql```

From mysql client connect remotely to mysql server Database Engine without using SSH. Using the mysql utility to perform this action type: ```sudo mysql -u remote_user -h 172.31.95.126 -p``` and enter password for the user password.

I get an error but then I check my inbound rule earlier set and change the source ip from the public to the private ip of the MySql server instance since my two instances share the same security group.

Now it works.

<img width="778" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/0cc27ee5-62ce-4e3b-aeab-a601f3ce297a">

This gives us access into the mysql server database engine.

Finally type: ```Show databases;``` to show the test_db database that was created.

<img width="808" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/3e34bfb6-bce6-4334-b1f3-8b2fb09fdfc7">








