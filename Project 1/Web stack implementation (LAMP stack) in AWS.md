WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS
AWS account setup and provisioning an Ubuntu Server

Steps

1. Sign up for an AWS account.

2. Log in as IAM user

3. In the AWS console, Create a Security Group

<img width="1421" alt="Screen Shot 2023-02-26 at 5 43 06 PM" src="https://user-images.githubusercontent.com/18741380/221424549-7adc0a09-a03b-42ac-842c-c2d48d347d2b.png">

4. launch an EC2 instance 

5. Select the Ubuntu free tier instance

6. Set the required configurations (Enabled public IP, security group, and key pair) and finally launch the instance.

<img width="1421" alt="image" src="https://user-images.githubusercontent.com/18741380/221426743-05926873-b600-4aba-8dfb-8f850b1dbb16.png">

Next, SSH into the instance using the Terminal

In the Terminal, type _cd Downloads_ to navigate to the location of the key-pair.

Inside the Downloads directory, connect to the EC2 instance using its Public DNS.

run the command
