Frontend Creation

Having completed the functionality of the backend, it is time to create a user interface for a Web client (browser) to interact with the application via API.

In the same root directory as your backend code, which is the Todo directory, run: ```npx create-react-app``` client to create a new folder in your Todo directory called client

Running a React App

Before testing the react app, there are some dependencies that need to be installed:

Install concurrently, used to run more than one command simultaneously from the same terminal window. ```npm install concurrently --save-dev```

<img width="565" alt="image" src="https://user-images.githubusercontent.com/18741380/222926256-9c0ae16a-623f-4d78-8e56-40dd0c3b7989.png">

Install nodemon, used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
```npm install nodemon --save-dev```

<img width="500" alt="image" src="https://user-images.githubusercontent.com/18741380/222926322-e89ca460-b257-4268-875c-0eee7a85cdd0.png">


In Todo folder open the package.json file. make changes to the script replace with the code below:

```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},

```
<img width="563" alt="image" src="https://user-images.githubusercontent.com/18741380/222926656-1c3c68ee-1447-4eec-ad87-bf76f39d4267.png">


Configure Proxy in package.json

Change directory to client: ```cd client```

Open the package.json file: ```vi package.json```

Add the key value pair in the package.json file "proxy": "http://localhost:5000" 

The purpose of this is to ensure access to the application directly from the browser by simply calling the server url 

like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

<img width="566" alt="image" src="https://user-images.githubusercontent.com/18741380/222926771-8b92e830-390a-4fcc-b059-35171fb52cfd.png">

Navigate to the Todo directory and run: ```npm run dev``` The app should open and start running on localhost:3000

<img width="569" alt="image" src="https://user-images.githubusercontent.com/18741380/222926864-b60d4065-132e-42ce-a64c-933c910bbc05.png">

To access the application from the Internet you have to open TCP port 3000 on EC2 by adding a new Security Group rule.

<img width="1147" alt="image" src="https://user-images.githubusercontent.com/18741380/222927043-30b7cc3d-390d-4424-a9d0-b2c6f691ca06.png">

<img width="1337" alt="image" src="https://user-images.githubusercontent.com/18741380/222927098-8af76e75-2d5d-494c-927f-c0cf63491c54.png">


Creating your React Components

From your Todo directory run: ```cd client```

move to the src directory: ```cd src```

Inside your src folder create another folder called components: ```mkdir components```

Move into the components directory with: ```cd components```

Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js: ```touch Input.js ListTodo.js Todo.js```

Open Input.js file: ```vi Input.js```

Paste the following:

```
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input

```













