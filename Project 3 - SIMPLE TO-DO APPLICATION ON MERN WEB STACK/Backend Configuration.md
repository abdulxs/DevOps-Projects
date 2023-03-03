To deploy a simple To-Do application that creates To-Do lists

BACKEND CONFIGURATION

Steps

Update ubuntu: ```sudo apt update```

<img width="564" alt="image" src="https://user-images.githubusercontent.com/18741380/222845730-a3510b2b-7280-43b5-a9f7-e806a04e2e7f.png">

Get the location of Node.js software from Ubuntu repositories. Run: ```curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -```

<img width="564" alt="image" src="https://user-images.githubusercontent.com/18741380/222846288-ff1a002d-b6c4-48ce-bb1f-488a4a159d30.png">

Install Node.js on the server with this command: ```sudo apt-get install -y nodejs```. This command installs both nodejs and npm.

NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

Verify the node installation with this command: node -v

Verify the npm installation with this command: npm -v

<img width="529" alt="image" src="https://user-images.githubusercontent.com/18741380/222847248-713d5f0d-6c43-44c7-a57a-ec60fa0abed7.png">



APPLICATION CODE SETUP

Create a new directory for your To-Do project: ```mkdir Todo```

Run the command below to verify that the Todo directory is created, run: ```ls```

Next, change the current directory to the newly created one: ```cd Todo```

Next, you will use the command ```npm init``` to initialise your project, so that a new file named package.json will be created. 

This file will normally contain information about your application and the dependencies that it needs to run. 

Run the command: ```npm init``` then follow the prompt and finally answer yes to write out the package file.

<img width="571" alt="image" src="https://user-images.githubusercontent.com/18741380/222849367-61e6644b-f9e5-40c9-b843-09d1ad7a767e.png">

Run the command ```ls``` to confirm that you have package.json file created.

<img width="567" alt="image" src="https://user-images.githubusercontent.com/18741380/222849590-2bfb5a19-545d-4596-b31c-2bb40e334461.png">



INSTALL EXPRESSJS

To use express, install it using npm: ```npm install express```

Next, create a file index.js with this command: ```touch index.js```

Run ```ls``` to confirm that your index.js file is successfully created.

<img width="571" alt="image" src="https://user-images.githubusercontent.com/18741380/222850342-cf555bee-7d8c-47aa-90ce-c05d4879c104.png">

Next step is to install the dotenv module. Run this code: ```npm install dotenv```

Then open the index.js file with this command: ```vim index.js```

Type the code below into it and save:

```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});

```

Use :w to save in vim and use :qa to exit vim

<img width="570" alt="image" src="https://user-images.githubusercontent.com/18741380/222851430-814b93d1-0ea3-4070-bddc-f333ca4ab046.png">

Test start the server to see if it works. Type: ```node index.js```

You should see Server running on port 5000 in the terminal.

<img width="568" alt="image" src="https://user-images.githubusercontent.com/18741380/222851613-ce9be641-0477-4b48-82bb-7fb0527f0b6e.png">

Next, open port 5000 in EC2 Security Groups and save changes.

<img width="1156" alt="image" src="https://user-images.githubusercontent.com/18741380/222852090-12b7bdce-3ef3-4e2d-b6d0-e2ecd891d196.png">

Open a browser and access the server’s Public IP or Public DNS name followed by port 5000: http://<PublicIP-or-PublicDNS>:5000

<img width="1314" alt="image" src="https://user-images.githubusercontent.com/18741380/222852286-37691286-116b-4d61-a3c4-4899af162649.png">

 

ROUTES
  
The To-Do application needs to be able to complete 3 actions:

Create a new task
Display list of all tasks
Delete a completed task

For each task, we need to create routes that will define various endpoints that the To-do app will depend on. 
  
Create a folder called routes with this command ```mkdir routes```

Change directory to routes folder and create a file api.js with the command: touch api.js


  

