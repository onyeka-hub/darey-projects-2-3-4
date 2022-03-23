# SIMPLE TO-DO APPLICATION ON MERN WEB STACK

## Step 1 – Backend configuration

### spin up an ubuntu server in aws and Update it.
#### Lets get the location of Node.js software from Ubuntu repositories with the command below:

#### curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

#### Install Node.js on the server with the command below

#### sudo apt-get install -y nodejs

#### Note: The command above installs both nodejs and npm. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

#### Verify the node installation with the command below

#### node -v 

#### Verify the node installation with the command below

#### npm -v

### Application Code Setup

#### Create a new directory for your To-Do project:
#### mkdir Todo
#### Now change your current directory to the newly created one:
#### cd Todo
#### Next, you will use the command npm init to initialise your project, so that a new file named package.json will be created. This file will normally contain information about your application and the dependencies that it needs to run. Follow the prompts after running the command. You can press Enter several times to accept default values, then accept to write out the package.json file by typing yes.

#### npm init

![npm init](https://user-images.githubusercontent.com/83009045/159725768-8421e50e-aa79-49dc-b2d9-8475925b776d.JPG)

#### Run the command ls to confirm that you have package.json file created.

![package json file created](https://user-images.githubusercontent.com/83009045/159726109-7f7f0309-9021-4181-b78c-ed75cbce86ad.JPG)

## Step 2

### Install ExpressJS
#### To use express, install it using npm:

#### npm install express

#### Now create a file index.js with the command below

#### touch index.js
#### Install the dotenv module with this command

#### npm install dotenv

#### Open the index.js file with a text editor and paste the code below

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

#### Notice that we have specified to use port 5000 in the code. This will be required later when we go on the browser.
#### Now it is time to start our server to see if it works. Open your terminal in the same directory as your index.js file and type:

#### node index.js

#### If every thing goes well, you should see Server running on port 5000 in your terminal.

![server running on port 5000](https://user-images.githubusercontent.com/83009045/159731816-91e5c104-437d-4f09-9d93-1e044e107848.JPG)

![express on browser](https://user-images.githubusercontent.com/83009045/159732838-8119e885-cd22-4bd8-ad5f-a2dacb54b212.JPG)

## Step 3 

### Creating Routes
#### There are three actions that our To-Do application needs to be able to do:

1. Create a new task
2. Display list of all tasks
3. Delete a completed task

#### Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

#### For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes

#### mkdir routes

#### Change directory to routes folder.

#### cd routes

#### Now, create a file api.js with the command below

#### touch api.js

#### Open the file and populate with the code below

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

## Step 4

### Creating Models
#### Since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.
#### A model is at the heart of JavaScript based applications, and it is what makes it interactive.
#### We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document.
#### In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties
#### To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.
#### Change directory back Todo folder with cd .. and install Mongoose

#### npm install mongoose

#### Create a new folder models. Change directory into the newly created ‘models’ folder. Inside the models folder, create a file and name it todo.js with this command

#### mkdir models && cd models && touch todo.js

#### Open the file created with vim todo.js then paste the code below in the file:

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

#### Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model.

#### In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit

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

## Step 5

### Creating a MongoDB database
#### We need a database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS),
#### so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. Sign up here. Follow the sign up process,
#### select AWS as the cloud provider, and choose a region near you.
#### Create a MongoDB database and collection inside mLab
#### In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.

#### Create a file in your Todo directory and name it .env.
##### Add the connection string to access the database in it, just as below:
```
#### DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```

 ``` 
#### Ensure to update <username>, <password>, <network-address> and <database> according to your setup
```

#### Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.

#### Simply delete existing content in the file, and update it with the entire code below.

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
#### Start your server using the command:

#### node index.js



### Testing Backend Code without Frontend using RESTful API

#### So far we have written backend part of our To-Do application, and configured a database, but we do not have a frontend UI yet.
#### We need ReactJS code to achieve that. But during development, we will need a way to test our code using RESTfulL API. 
#### Therefore, we will need to make use of some API development client to test our code.

#### In this project, we will use Postman to test our API.

#### Download and Install Postman on your machine.

#### You should test all the API endpoints and make sure they are working. 
#### For the endpoints that require body, you should send JSON back with the necessary fields since it’s what we setup in our code.
#### Now open your Postman, create a POST request to the API



#### 
