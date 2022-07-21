### MERN STACK IMPLEMENTATION

MERN stack is a web development framework. It consists of MongoDB, ExpressJS, ReactJS, and NodeJS as its working components

MongoDB: A document-based, No-SQL database used to store application data in a form of documents
ExpressJS: A server side Web Application framework for Node.js.
ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.


# STEP 1 – BACKEND CONFIGURATION

- Update and upgrade ubuntu

 `sudo apt update`

 `sudo apt upgrade`

- Lets get the location of Node.js software from Ubuntu repositories.

 `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

 ![Screenshot](./Images/Screenshot%201.png)

- Install Node.js on the server

 `sudo apt-get install -y nodejs`

 ![[Screenshot](./Images/screenshot%202.png)

The command above installs both nodejs and npm. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

- Verify the node installation with the command below

 `node -v`
 
- Verify the npm installation with the command below
 `npm -v`
 
- Create a new directory for your To-Do project:
 `mkdir Todo`

- Run the command below to verify that the Todo directory is created with ls command

 `ls`

- Change your current directory to the newly created one:

 `cd Todo`

Initialise project, so that a new file named package.json will be created. This file will normally contain information about your application and the dependencies that it needs to run. Follow the prompts after running the command. You can press Enter several times to accept default values, then accept to write out the package.json file by typing yes.

 `npm init`

 ![Screenshot](./Images/screenshot%203.png)

- Run the command ls to confirm that you have package.json file created.

 ![Screenshot](./Images/screenshot%204.pngimage.jpg)


# STEP 1.1 – INSTALL EXPRESSJS

Express is a framework for Node.js, it helps to define routes of your application based on HTTP methods and URLs.

- Install express using npm:

 `npm install express`

- Create a file index.js with the command below

 `touch index.js`

- Run ls to confirm that your index.js file is successfully created

- Install the dotenv module

 `npm install dotenv`

- Open the index.js file with the command below

 `vim index.js`

- Enter code below into index.js file

 `const express = require('express');
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
});`

We have specified to use port 5000 in the code. This will be required later when we go on the browser.

- Open your terminal in the same directory as your index.js file and type:

 `node index.js`

- You should see Server running on port 5000 in your terminal.

 ![Screenshot](./Images/screenshot%206.png)

 ![Screenshot](./Images/screenshot%205.png)

- Open up your browser and try to access your server’s Public IP or Public DNS name followed by port 5000:

![Screenshot](./Images/screenshot%207.png)

# STEP 1.2 – ROUTES

There are three actions that our To-Do application needs to be able to do:

1. Create a new task
2. Display list of all tasks
3. Delete a completed task

Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE. For each task, we need to create routes that will define various endpoints that the To-do app will depend on. 

- Create a folder routes
 `mkdir routes`

- Change directory to routes folder.

 `cd routes`

- Create a file api.js with the command below

 `touch api.js`

- Open the file with the command below

 `vim api.js`

- Paste below code in the file

`const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;`


# STEP 1.2 – MODELS

 install mongoose which is a Node.js package that allow us to communicate with our database.

- Change directory back Todo folder with cd and install Mongoose

 `npm install mongoose`

- Create a new folder models, then change directory into the newly created ‘models’ folder and create a file and name it todo.js:

 `mkdir models && cd models && touch todo.js`

- Open the file created with :

 `vim todo.js`  

- Add the code below into the todo.js file:
 `const mongoose = require('mongoose');
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

module.exports = Todo;`

- Update the routes from the file api.js in ‘routes’ directory to make use of the new model

- In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit

 `const express = require ('express');
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

module.exports = router;`