INSTALLING DEPENDENCIES

Installing Apache and updating the firewall

Steps
Install Apache using Ubuntu’s package manager ‘apt', Run the following commands: To update a list of packages in package manager: sudo apt update

<img width="575" alt="image" src="https://user-images.githubusercontent.com/18741380/221710153-4b69d245-5ff0-4e6c-ad7b-43642f9d296f.png">

To run apache2 package installation: sudo apt install apache2
<img width="565" alt="image" src="https://user-images.githubusercontent.com/18741380/221710602-bd7c1cc6-f28b-4b0f-a856-c44f9588598a.png">

<img width="577" alt="image" src="https://user-images.githubusercontent.com/18741380/221710652-267f5a5e-ca88-43d4-90d4-748123220980.png">


Next, verify that Apache2 is running as a service in the OS. run: sudo systemctl status apache2
<img width="572" alt="image" src="https://user-images.githubusercontent.com/18741380/221710766-671e112a-5eac-4dfa-8657-4ef59ea20d6a.png">


The green light indicates Apache2 is running.

Open port 80 on the Ubuntu instance to allow access from the internet.

To do this, add an inbound rule on the security group of the instance as below.

<img width="1147" alt="image" src="https://user-images.githubusercontent.com/18741380/221711902-7de59b70-a42a-46b1-9777-cb179df083fd.png">


Access the Apache2 service locally in our Ubuntu shell by running: curl http://localhost:80 or curl http://127.0.0.1:80 
<img width="567" alt="image" src="https://user-images.githubusercontent.com/18741380/221711544-50dd5340-af28-478e-8541-fe3df7f04ae2.png">

This command would output the Apache2 payload indicating that it is accessible locally in the Ubuntu shell.

Next, test that Apache HTTP server can respond to requests from the Internet. Open a browser and type the public IP of the Ubuntu instance: http://3.80.162.49:80 This outputs the Apache2 default page.


<img width="1364" alt="image" src="https://user-images.githubusercontent.com/18741380/221712109-df8bef83-afe8-4bb6-9f83-b2f8b7ea411d.png">


