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

<img width="578" alt="image" src="https://user-images.githubusercontent.com/18741380/223221716-21736573-dfbd-42cd-90be-2978c0164b05.png">


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

<img width="571" alt="image" src="https://user-images.githubusercontent.com/18741380/223221959-df99fc28-6d7b-4468-bd0b-17a9925e45ba.png">

Move back to the client folder : ```cd ../..```

In the client folder, install Axios: ```npm install axios```

<img width="568" alt="image" src="https://user-images.githubusercontent.com/18741380/223222709-b2ec1a0b-7239-428a-a771-bd13a964de56.png">

Next, go to components directory: ```cd src/components```

Then open your ListTodo.js: ```vi ListTodo.js```

Paste the following code into the ListTodo.js file:

```

import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo

```

<img width="564" alt="image" src="https://user-images.githubusercontent.com/18741380/223223194-e3aa186d-b91d-4d8c-9318-808668ad036a.png">

In the Todo.js file you write the following code:

```
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;

```

<img width="567" alt="image" src="https://user-images.githubusercontent.com/18741380/223223546-7040bb53-d567-4f1e-a8e5-785d87c6f482.png">


Delete the logo and adjust our App.js. Navigate back to the src directory

In the src folder run: ```vi App.js```

Copy and paste the code below into it


```

import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;

```


Next, in the src directory open the App.css using: ```vi App.css```

Then paste the following code into App.css:

```
.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}

```
<img width="526" alt="image" src="https://user-images.githubusercontent.com/18741380/223225013-e0fc213f-151c-4457-ba45-55dda9b93b34.png">

Next, in the src directory open the index.css: ```vim index.css```

Copy and paste the code below:

```
body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}

```

<img width="565" alt="image" src="https://user-images.githubusercontent.com/18741380/223225376-ef73fe96-4ae7-48fb-9191-999a5eb7ddeb.png">

Go to the Todo directory: ```cd ../..```

When you are in the Todo directory run: ```npm run dev```

<img width="566" alt="image" src="https://user-images.githubusercontent.com/18741380/223225901-09d96256-eaae-4732-a1dd-f47bfa3bcc40.png">

Assuming no errors when saving all these files, our To-Do app should be ready and fully functional with all the functionality working: creating a task, deleting a task and viewing all your tasks.

<img width="1263" alt="image" src="https://user-images.githubusercontent.com/18741380/223226067-16629e93-8768-4e85-8f1d-ae6510b4c1fb.png">

<img width="1367" alt="image" src="https://user-images.githubusercontent.com/18741380/223226143-4c48ef7c-257c-4bb6-b62f-c585ad245130.png">









