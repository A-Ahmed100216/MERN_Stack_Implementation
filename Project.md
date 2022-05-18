# MERN Stack Implementation

## 1. Backend Configuration
* Install NodeJS. 
```bash
# Update and upgrade Ubuntu
sudo apt update
sudo apt upgrade
# Obtain NodeJS from Repo
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
# Install NodeJS
sudo apt-get install -y nodejs
```
* The last command installs both NodeJS and NPM, a package manager for Node. Confirm the installation of both Node and NPM. 
```bash
node -v
npm -v
```
![node](/images/node_npm_install.png)
* Prepare your application code. Create a directory and initialise the project using the command `npm init`. This creates a package.json file which contains information about your application and the dependencies that it needs to run.
```bash
# Create directory called Todo
mkdir Todo
# Navigate to directory
cd Todo
# Initialise project
npm init
```
![Initialise project](/images/init_project.png)

## 2. Install ExpressJS
* Express is a framework for NodeJS therefore simplifies development. Install via the command 
```bash
npm install express
```
![install express](/images/install_express.png)
* Next, create a index.js file. This file will be using a module called dotenv which will also need installing via npm. 
```bash
# Install dotenv
npm install dotenv
# Create index.js file
touch index.js
# Open file
vi index.js
```
![install dotenv](/images/install_dotenv.png)   
* Open the index.js file and paste the following code 
```js
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
* Start the server 
```bash 
node index.js
```
![start server](/images/start_server.png)
* The app uses port 5000 so our Security Group rules must be amended to open this port. 
![Security Group](/images/SG.png)
* Make sure the server is running and navigate to your browser. Use either the Public IP or DNS followed by port 5000 to display the following page:
![Express webpage](/images/express_webpage.png)
* The To-Do application will have 3 functions which are associated HTTP request methods:
    1. Create a new task (POST)
    2. Display all tasks (GET)
    3. Delete completed tasks (DELETE)   
* Each task requires routes which define various endpoints. Create a routes directory:
```bash
mkdir routes
cd routes
```
* Create a file called api.js.  
```bash
touch api.js
vi api.js
```

* Paste the following code.
```js
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

## 3. Models
* The app uses MongoDB which is a NoSQL database. This requires Models which allow JavaScript applications to be interactive. The Models will also define the database schema which is a blueprint of how the database should be constructed.  
* To create a schema and model, install mongoose. This makes it easier to work with mongodb. 
```bash
npm install mongoose
```
![install mongoose](/images/install_mongoose.png)
* Return back to Todo directory and create a directory called models.
```bash
mkdir models && cd models
```
* Within this directory, create a file called todo.js
```
touch todo.js
vi todo.js
```
* Paste the following
```js
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
* Update the route in api.js to make use of the new model. Navigate back to routes directory. Delete all the code in api.js and replace with the following. 
```js
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

## 4. MongoDB Database
* mLab is used as a Database as a Service solution. Create an account.
* Create a cluster and follow the steps set up your first database. 
![cluster](/images/cluster.png)
* In the Todo directory, create a .env file. Add the connection string to connect to your database. This can be found by clicking connect to your database.
```
touch .env
vi .env

DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```
* Update the index.js file to reflect the .env file. Delete the old content and replace with the following 
```js
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
* Start the server. The terminal should display 'database connected successfully'
```
node index.js
```
![Database Connected Successfully](/images/Database_connected.png)
* The database is now connected so the next step is to test the code. As the front-end is yet to be configured, make use of Postman, a REST API development client. 
### Testing 
* Create an account on Postman. You can either use the browser or download a desktop version. 
* The API endpoint is http://<PublicIP-or-PublicDNS>:5000/api/todos
1. POST - add an item to To-Do list. 
    * Select POST as request type.
    * Add the endpoint 
    * Navigate to the Headers tab and add a key 'Content-Type' with the corresponding value 'application/json'
    * Navigate to the Body tab, select 'raw', and paste the following 
    ```json
    {
    "action":"Finish project 3"
    }
    ```
    * Click 'Send'. The output should return with status 200
    ![POST](/images/POST.png)
* The item has been added to the list therefore should also be present in the database: 
![Item 1](/images/Item1.png)
* Also, the webpage should also include the item. As there is no front-end, it will not be formatted:
![Webpage before front-end](/images/webpage_before_frontend.png)

2. GET - Retrieve all items in list
    * Select GET as request type.
    * Add the endpoint 
    * Navigate to the Headers tab and add a key 'Content-Type' with the corresponding value 'application/json'
    * Navigate to the Body tab, select 'raw', and paste the following 
    ```json
    [
        {"_id":"628131a2942fc746ffe2203b","action":"Finish project 3"}
    ]
    ```
    * The output should be as follows:
    ![GET Output](/images/GET_output.png)
3. DELETE - Remove an item 
    * Select DELETE as request type
    * This request requires the id of the item. This can be obtained via the GET command 
    * Add the endpoint followed by the request id `http://<public_ip>:5000/api/todos/<item_id>`
    ![DELETE request](/images/DELETE.png)
    * To confirm, navigate back to the webpage and the list should be empty.
    ![Empty List](/images/empty_list.png)
    * Likewise, the database should also be empty:
    ![Empty DB](/images/empty_db.png)


## 5.Frond-End Configuration
* The next step is to create a UI (user interface) using React. 
* In the Todo directory, use the following command to create a scaffold (framework which provides minimal setup) for the app 
```bash
npx create-react-app client
```
* This creates a folder called client where the code will be stored. 
### Blocker 
* When attempting to run the above command, the following error was displayed:
![blocker](/images/blocker.png)
* The blocker indicated an issue with the version of node. To resolve, node needs upgrading. This can be done using the node version manager, nvm.
    1. Install nvm 
    ```bash
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
    ```
    ![install nvm](/images/install_nvm.png)
    2. Confirm installation (Bash may need restarting for changes to take effect)
    ```bash
    nvm --version
    ```
    3. Use nvm to install the desired version of node. To resolve this blocker, node must be version 14 or higher.
    ```bash
    # Install version 14.4
    nvm install 14.4.0
    ```
    ![Upgrade node](/images/upgrade_node.png)
* The command `npx create-react-app client` can now be run and should create a clients directory
![create-react-app](/images/create_react_app.png)
![create-react-app2](/images/react_app.png)


* Next, install dependencies. 
    * concurrently - run commands simultaneously 
    ```bash
    npm install concurrently --save-dev
    ```
    * nodemon - run and monitor server. If there are any changes, will restart server. 
    ```bash 
    npm install nodemon --save-dev
    ```
    ![install dependencies](/images/install_react_dep.png)
* In the Todo directory, open `package.json` and replace with the following (The scripts section has been changed from a default test script)
```json
{
  "name": "todo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "start-watch": "nodemon index.js",
    "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "dotenv": "^16.0.1",
    "express": "^4.18.1",
    "mongoose": "^6.3.3"
  },
  "devDependencies": {
    "concurrently": "^7.2.0",
    "nodemon": "^2.0.16"
  }
}
```
* Now, configure the proxy. Navigate to the client directory and open the `package.json` file. Add a key value pair for the proxy 
```bash
cd client
vi package.json

# Paste in file (don't forget the comma)
"proxy": "http://localhost:5000",
```
![Configure Proxy](/images/configure_proxy.png)
* This allows the app to be accessed by calling the url rather than the entire path i.e. http://localhost:5000/api/todos
* Return to Todo directory and run 
```
npm run dev
```
![NPM run dev](/images/npm_run_dev.png)
* Access the webpage via port 3000. (NB: SG rules must be amended to allow traffic from this port)
![React Webpage](/images/React_webpage.png)
* React uses components which allow for modularity and reusability. The app will consist of 2 stateful (holds some state - keeps track of changing data) components and 1 stateless(no state - prints out what is given or returns same output) component. 
* The components will be created in the directory `Todo/client/src`. Navigate to this location and create a new directory called components:
```bash
cd Todo/client/src
mkdir components
```
* Inside the components directory, create Input, ListTodo, and Todo files
```bash
cd components
touch Input.js ListTodo.js Todo.js
```
* Paste the following into the Input.js file
```js
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
* The script above uses axios (Promise-based HTTP client for nodejs and browser). Return to the clients folder and install axios 
```bash
cd ..
cd ..
npm install axios
```
![components and install axios](/images/components.png)

* Return to components directory and paste the following in ListTodo.js
```
vi src/components/ListTodo.js
```
```js
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
* Paste the following in Todo.js
```bash
vi src/components/Todo.js
```
```js
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
* The App.js file needs amending to make use of the components. Navigate to this file `vi src/App.js` and paste the following:
```js
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
* CSS is implemented for design purposes. Open the App.css file `vi src/App.css` and paste the following:
```css
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
* Open index.css `vi src/index.css` and paste the following:
```css
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
![css](/images/css.png)

* Return to the Todo directory and run the following command:
```
npm run dev
```
![npm run dev](/images/npm_run_dev2.png)
* Open the webpage and there should be a functioning To-Do app
![Final App](/images/final_app.png)
