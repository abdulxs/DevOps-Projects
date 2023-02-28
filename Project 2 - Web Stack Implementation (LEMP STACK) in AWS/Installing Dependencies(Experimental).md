WEB STACK IMPLEMENTATION (LEMP STACK)

STEP 1 – INSTALLING THE NGINX WEB SERVER

To start, update server’s package index. Afterwards, use apt install to get the Nginx installation going. 

To start the update run: Sudo apt update

<img width="568" alt="image" src="https://user-images.githubusercontent.com/18741380/221955841-a305a129-ad5d-46bf-8857-4cead82ceba6.png">


Next, install Nginx by running: sudo apt install nginx

<img width="569" alt="image" src="https://user-images.githubusercontent.com/18741380/221956146-f92cbd73-b90f-44b7-a9d7-c7c468602f01.png">


<img width="570" alt="image" src="https://user-images.githubusercontent.com/18741380/221956065-220b2d74-1e5c-40bc-b439-fcf90cc52630.png">


At the prompt, enter Y to confirm that you want to install Nginx. This would complete the installation process.

To confirm that nginx was successfully installed and is running as a service in Ubuntu, run: sudo systemctl status nginx

<img width="575" alt="image" src="https://user-images.githubusercontent.com/18741380/221956399-fdbac652-f352-4821-b3ff-43c84b4e641d.png">


eroor above shows it did not start successfully.  The error was due to another proces already using port 80, run the command below to get a list of all processes using port 80

<img width="568" alt="image" src="https://user-images.githubusercontent.com/18741380/221957371-e1eaca19-66a2-4983-88d0-06fcc042419f.png">


recall our apache server provisioned in the previous project.

To fix, try to stop the processes using the port 80 using: sudo fuser -k 80/tcp

<img width="405" alt="image" src="https://user-images.githubusercontent.com/18741380/221958076-c0bc12ce-c943-407e-a3c7-85f5782f8ec2.png">

if it still does not work, check the status of the config file using the below

<img width="601" alt="image" src="https://user-images.githubusercontent.com/18741380/221960935-fa430bad-bc22-4d61-a22a-7f75baa529d1.png">

if the file is fine, then

<img width="932" alt="image" src="https://user-images.githubusercontent.com/18741380/221961166-15aaadb7-517b-4f3b-ba7b-97ea545bb4f6.png">


finally, use the command below to kill off the process

<img width="349" alt="image" src="https://user-images.githubusercontent.com/18741380/221961358-9030aeff-7d0b-49fc-a88e-edf290f3938b.png">


restart the service and confirm it's running fine

<img width="575" alt="image" src="https://user-images.githubusercontent.com/18741380/221961664-3db914cc-3bcc-436f-b39f-86fd281e6aec.png">

Green indicator shows that the server was successfully installed and running.

The server is running and we can access it locally and from the Internet. 

But, to access it from the internet port 80 must be open to allow traffic from the internet in.

To test that the server can be accessed locally from the instance run the curl command: curl http://localhost:80 The output shows that the server is accessible from the local host.

<img width="564" alt="image" src="https://user-images.githubusercontent.com/18741380/221962122-4ba9bfc4-697c-4544-aaef-398272f186a4.png">

To test that the server can be accessed from the internet, open a browser and type the following url with the public IP of your Ubuntu instance; http://Public-IP-Address:80

<img width="1299" alt="image" src="https://user-images.githubusercontent.com/18741380/221962814-564e8c9b-7042-4156-8ec9-e9bda7fef014.png">

screenshot above shows the apache page is still being displayed

to fix this, we need to remove apache2 from our server completely using the below commands

<img width="572" alt="image" src="https://user-images.githubusercontent.com/18741380/221969260-f060012c-25fc-4f51-9c40-bc1cc55fe660.png">




