


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

<img width="577" alt="image" src="https://user-images.githubusercontent.com/18741380/222853028-d494f7c5-ee83-4a9f-8f31-6bd01f06003a.png">

Open the file with the command below vim api.js

Copy and save the code below into the file:
 
```
 const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
 
 ```
 
<img width="565" alt="image" src="https://user-images.githubusercontent.com/18741380/222853250-daf18f0c-941e-40a4-a51f-fc2f20b867d4.png">

 
 
 
 MODELS
 
To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier. 
 
Change directory back Todo folder with cd .. and install Mongoose with the following command: ```npm install mongoose```

Create a new folder models, then change directory into the newly created models folder. 
 
Inside the models folder, create a file and name it todo.js with the following command: ```mkdir models && cd models && touch todo.js```
 
<img width="570" alt="image" src="https://user-images.githubusercontent.com/18741380/222853733-cd95c57c-4c47-49db-838a-b1a080f4f003.png">
 
Open the file created with ```vim todo.js``` then paste the code below in the file:
 
```
 
 const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
 
 ```
 
 <img width="565" alt="image" src="https://user-images.githubusercontent.com/18741380/222854220-2650b66a-ceb1-40c5-94eb-00a17d34a27b.png">

 
 Next, we update our routes from the file api.js in ‘routes’ directory to make use of the new model. 
 
 In routes directory, open api.js with ```vim api.js```, delete the code inside with ```:%d``` command and paste the code below into it then save and exit
 
 ```
 
 const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
 
 ```

 <img width="566" alt="image" src="https://user-images.githubusercontent.com/18741380/222854558-59dcbffd-60f4-48da-a6ba-9a54a20ae37e.png">

 <img width="566" alt="image" src="https://user-images.githubusercontent.com/18741380/222854664-185f4d83-f442-4cc5-8c43-0e01a7f58c96.png">

 
 
 MONGODB DATABASE
 
A database is required where data will be stored. For this we will make use of mLab. 
 
Sign up for a shared clusters free account, Sign up on https://www.mongodb.com/atlas-signup-from-mlab. 
 
Follow the sign up process, select AWS as the cloud provider, and choose a region.

For the purposes of this project, allow access to the MongoDB database from anywhere.

Make sure you change the time of deleting the entry from 6 Hours to 1 Week

Create a MongoDB database and collection inside mLab
 
<img width="1436" alt="image" src="https://user-images.githubusercontent.com/18741380/222856613-6c2e9054-9f46-4bc0-b768-a4d28f199cae.png">

 Next, in the index.js file, we specified process.env to access environment variables, but we are yet to create the file. 
 
 Now, create a file in the Todo directory and name it .env To do this type:

 ```
touch .env
vi .env
 ```
 
 
Then add the connection string to access the database in it, just as below: ```DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'```

Update the index.js to reflect the use of .env so that Node.js can connect to the database. 
 
Delete existing content in the file, and update it with the following steps:
 
using vim, follow below steps: Open the file with vim index.js and enter. Type  ```:%d``` and enter. This will delete the entire content. 

 <img width="565" alt="image" src="https://user-images.githubusercontent.com/18741380/222857753-3c3296bf-82ca-4c70-a05b-9edab7441c23.png">
 
 Next, press i to enter the insert mode in vim. then, paste the entire code below in the file:
 
 
 ```
 
 const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
 
 ```
 
 <img width="572" alt="image" src="https://user-images.githubusercontent.com/18741380/222857846-1a753d81-b12f-4cc3-ad2a-61c57358bd39.png">

 Next, start the server using the command: ```node index.js```
 
 <img width="566" alt="image" src="https://user-images.githubusercontent.com/18741380/222858013-9b68e414-022a-4185-a77d-1790ae9667fa.png">

 
 I'm getting an error.
 
 This is due to an error made earlier, the routes folder should have been created inside the Todo directory rather than the home directory

 FIX
 
 run the command below to move the path top the Todo folder
 
 first run : ```cd Todo``` then ```mkdir routes``` to create the folder
 
 then run ``` cp -r ~/routes/api.js ~/Todo/routes/api.js ``` to copy the file topm the new directory
 
 <img width="566" alt="image" src="https://user-images.githubusercontent.com/18741380/222925187-7e910019-f0a2-4a1a-8127-81661fb6caf4.png">

  try to start the server again using the command: ```node index.js``` and it should be fine
 
 
 <img width="575" alt="image" src="https://user-images.githubusercontent.com/18741380/222925084-38a4964b-714e-417d-9535-992d1a6dbfc1.png">

 You shall see a message Database connected successfully, if so – we have our backend configured. Now we are going to test it.
 
 
 Testing Backend Code without Frontend using RESTful API
 
Because we do not have a frontend UI yet. We need ReactJS code to achieve that. 
 
But during development, we will need a way to test our code using RESTfulL API. 
 
Therefore, we will need to make use of some API development client to test our code. We will use Postman to test our API.

Create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos 
 
This request sends a new task to the To-Do list so the application could store it in the database. 
 
Also set header key Content-Type as application/json
  
<img width="997" alt="image" src="https://user-images.githubusercontent.com/18741380/222925791-baaa5112-5d72-4e9d-849c-03eaaf59e19b.png">

Create a GET request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos This request retrieves all existing records from the To-do application.
 
<img width="1008" alt="image" src="https://user-images.githubusercontent.com/18741380/222925905-2f9f3601-3c7d-4b1a-bed5-e351f436f035.png">



 
 
 
