# Mongo-Crud

- Node.js: a JavaScript runtime environment, the lovely folks that created Node made it so that we can run JavaScript as server side-code. May God bless their souls.
- Express: a framework for building web applications using Node.js by making server creation a simpler process.
- MongoDB: nosql database system, this is where we will store the information for the website or app.
- CRUD: This is an acronym using for Create (POST), Read(GET), Update(PUT), and Delete(DELETE). Which are a set of operations we make the servers to execute.
- Nodemon: is a utility that will monitor for any changes in your source code and automatically restart your server. Perfect for development.
- Middleware: is computer software that provides services to software applications beyond those available from the operating system. It can be described as "software glue".
- Embedded JavaScript: EJS is a simple templating language that lets you generate HTML markup with plain JavaScript. No religiousness about how to organize things. No reinvention of iteration and control-flow. It's just plain JavaScript.

#   Create and start your project

Create your directory through the command line: `mkdir simple-crud` Initialize your project: `npm init`

#  Server and using express

First we got to install Express and include it in our dependencies `npm install express --save` Then create your server file. `touch server.js` Now let's put express in that file:

```
  const express = require('express');
  const app = express();
  app.listen(3000, function() {
  console.log('listening on 3000')
  })
```


#  CRUD - READ(GET)

Whenever we visit a website the browser sends a **GET** request to the server to perform a READ operation. This is why we are seeing the "cannot get/" error, it's trying to **GET** but we're not GIVING (not a real term) yet - because have nothing to send back at the moment. To handle **GET** requests we use the `get` method:

```
  app.get(path, callback)
```

`path` is anything that comes after your domain name, it's the path that **GET** is going to look at. In this case we're looking at localhost:3000, but the browser is actually looking for localhost:3000/, in this case the `path` is `/`. The callback function tells the server what to do when we find the path. It takes a request and a response object.

```
  app.get('/', function (request, response) {
  // do something here
  })
```

So what do we do inside the **GET**? we can just right "Hello World" for now to show it in the browser. To do this we need to use the `send` method. This method comes with a response and a request object. These are usually written as `res` and `req` respectively.

```
  app.get('/', (req, res) => {
  res.send('Hello World')
  })
```

You should restart your server and see it display our message. After this we want to change the app to serve an `index.html` page to the browser. Let's do this by using the `sendFile` method that's provided by the `res` object. For people that stumble upon this and don't know what `__dirname` is, it is the directory the directory where you are in.

```
  app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html')
  })
```

#  Nodemon

Basically, Nodemon restarts the server automatically whenever you save a file that the server uses. `npm install nodemon --save-dev` We're using `--save-dev` in this instance because this is a dependency exclusive for the development environment, so we save it as a devDependency. If you want to run nodemon through the command line you need to install it globally by adding the `-g` flag to the installation like so: `npm install nodemon -g` But if you don't install it globally, you can run it as a script if you add the following line to your `package.json` file:

```
  "scripts": {
    "dev": "nodemon server.js"
  }
```

And then start the server by running either: `nodemon server.js` If you install nodemon globally, or: `npm run dev` If you're running it with npm through a script in `package.json`.

#  CRUD - CREATE

**CREATE** is an operation performed only by the browser if a **POST** request is sent to the server. The **POST** request can be triggered with JavaScript or through a `<form>` tag. Lets create a `<form>` tag element and add it to your `index.html` file. There are 3 things necessary for this element:

- We need an `action` attribute
- We need a `method` attribute
- We need `name` attributes on all `<input>` elements within the form So we need something like this added to our `index.html`:

  ```
  <form action="/messages" method="POST">
  <input id="name" type="text" placeholder="name" name="name">
  <input type="text" placeholder="message" name="message">
  <button type="submit">Submit</button>
  </form>
  ```

  The `action` attribute communicates with the browser and tells him where to look in our Express app. We want to navigate to `/messages`. The`method` attribute tells the browser what to request to send, a **POST** request. On the server, we can handle this POST request with a `post` method. Provided - like **GET** - by Express and works similarly. We can add the following code to our `server.js` file:

  ```
  app.post('/messages', (req, res) => {
  console.log('What up BOI?!')
  })
  ```

  Save, nodemon will restart the server, refresh the browser, and then write something on the form and you should be able to see you message - in this case What up Boi?! - in the command line. Now we know that Express is handling the form correctly. Now something that we need to understand is that Express does not handle reading data from the `<form>` element by itself. We need to add another package called `body-parser` to do this. We add middlewares like `body-parser` to Express with the `use` method. Middlewares are plugins that change the request or response object before they get handled by our application. We need to place these before the CRUD handlers to make use of them. As with every import we declare it at the top and then use it right after the `express()` declaration, in our case:

  ```
  const express = require('express')
  const bodyParser= require('body-parser')
  const app = express()
  app.use(bodyParser.urlencoded({extended: true}))
  // Handlers here...
  ```


#  MongoDB

The first thing we need to do to start using MongoDB is to install it with npm to use it as our database. `npm install mongodb@2.2.33 --save` It's important to install that version to work with the current setup that we have. After this we can connect to MongoDB with the `connect` method that comes with `Mongo.Client`. We need to setup the following code in our `server.js` file: **Note:** this code goes before the handlers, we will also have to move our start server code, the `listen`, into inside the MongoDB connect method. But more on this later.

```
  const MongoClient = require('mongodb').MongoClient
  MongoClient.connect('mongodb-url', (err, database) => {
  // ... server code (app.listen)
  })
  // ... Handlers
```


Now that our MongoDB is setted up let's dive in and create a `messages` collection to store our application messages. Go to the collections tab and click on `Add collection`. A collection is just where we will store data. You can create as many collections as you want. After you create your `messages` collection we call it by using MongoDB's `db.collection()` method. We can start saving our first entry into MongoDB with the `save` method. Once we save we will be redirected back to `/` which will cause their browser to reload. This is the `app.post` code we will use to save to our database.

```
  app.post('/messages', (req, res) => {
  db.collection('messages').save(req.body, (err, result) => {
   if (err) return console.log(err)
   console.log('saved to database')
   res.redirect('/')
  })
  })
```

Now if you enter something into our form and submit it, you will see an entry in your MongoDB collection. Now let's get cracking on how to display the messages to whoever visits the page.

#  Displaying Messages

Now to display the messages we need to do two things: Get the messages from MongoLab, and use a template engine to display the messages. First lets go ahead and get the messages by using the `find` method that's available in the `collection` method in junction with the `toArray` method that takes a callback function and allows us to actually use the information in the collection. Without `toArray` we would see a MongoDB object. Paste the following code under your `app.get` function after the res.sendFile() method:

```
  db.collection('messages').find().toArray(function(err, results) {
  console.log(results)
  // send HTML file populated with messages here
  })
```

If you reload your browser you will now see in your command line any messages you've sent to the collection. Now the next thing we want to do is to display it on the HTML. Since there's no way to add dynamic content to an HTML file we have to use a template engine. Let's use Embedded JavaScript (ejs) since it's beginner friendly. Let's install ejs: `npm install ejs --save` After installing we initialize it in our `server.js` file after our MongoDB connect method:

```
  \\ ... MongoDB connect method
  app.set('view engine', 'ejs')
  \\ ... Handlers and other methods
```

Now we can begin talking to HTML to display our messages. We call this process **rendering**. We call on the `render` object that is found on the `response` object to do so. It follows the following format: `res.render(view, locals)` The first parameter, `views`, is the name of the file we're rendering, the file must be placed within a folder called views. The second parameter, `locals`, is an object that passes data into the view. Lets first create an `index.ejs` file within the `views` folder to get this started.

```
  mkdir views
  touch views/index.ejs
```

Now we will put the following code in the index.ejs:

```
  <ul class="messages">
  <% for(var i=0; i<messages.length; i++) {%>
   <li class="message">
     <span><%= messages[i].name + " says: " %></span>
     <span><%= messages[i].message %></span>
   </li>
  <% } %>
  </ul>
```

```
  app.get('/', (req, res) => {
  db.collection('messages').find().toArray((err, result) => {
   if (err) return console.log(err)
   // renders index.ejs
   res.render('index.ejs', {messages: result})
  })
  })
```

Now we should see the messages showing up on out app! That's great! We're creating here! You will see as you submit more messages they will begin appearing one by one as you submit them! Great-great!

#  CRUD - UPDATE(PUT)

As the word says the **Update** operation is used when you want to update something, change it. This can be done with a **PUT** request. Like **POST**, the **PUT** request can either be triggered through JavaScript or a `<form>` element. Let's see how's triggered with JavaScript.

Let's first create a `button` in the `index.ejs` file:

```
<div>
  <button id="update">Update to Doggerino</button>
</div>
```

We also need to create a JavaScript file to execute the **PUT** request when the button is clicked. For this we will need to create a `public` folder as it's an Express convention.

We also need to add our `main.js` as a script in our `index.ejs` file:


So how're we going to trigger the **PUT** request when it's triggered? Well the easiest way is to use the Fetch API. It is only supported on Firefox, Chrome, and Opera so to use it in a real project we would want to use a polyfill.

We will send the following fetch request to the server. We will update our `update.addEventListener` method and add a variable to look for the name we want to update. Let's have a look at it and I will explain what we're seeing:

```
update.addEventListener('click', function () {
  var targetName = document.getElementById('name').value
  fetch('messages', {
    method: 'put',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({
      'targetName': targetName,
      'name': 'Doggerino',
      'message': 'Arf.'
    })
  })
})
```


We have also included the variable `targetName` that looks into our `name` input element and checks the value that we have there when we click our update button. We will send this value to our **PUT** method in `server.js` to search the message we will change.

In `headers` we've set `Content-Type` to `application/json`. We've also converted the message into JSON in the body with the `JSON.stringify` method. This is made because we need to send the message in JSON format to the server so that it can be resolved. However, our server does not read JSON data yet but thanks to `body-parser` it will!


# Updating a message in our Collection

Collections in MongoDB come with a method called `findOneAndUpdate` that allows us to change one item from the database.

`query` allows us to filter the collection through key-value pairs. We can filter our collection for messages by the name we specify by setting the `name` to whatever we write in the text box. And then sending the `targetName` parameter on our fetch method.

`update` tells MongoDB what to do with the update request. MongoDB has some update operators like `$set`, `$inc`, and `$push`. We will use the `$set` operator since we're changing names and messages.

`options` is an optional parameter that allows us to define additional things. Since we're looking for the last message that matches the name we input in the text box, we will set the `sort` option to `{_id: -1}`. This tells MongoDB to search from newest to oldest. However, if there are none, MongoBD by default does nothing. We can force it to create a new entry if we set the `upsert` option, which saves the parameters we entered if no entries are found, to true.


```
app.put('/messages', (req, res) => {
  db.collection('messages')
  .findOneAndUpdate({name: req.body.targetName}, {
    $set: {
      name: req.body.name,
      message: req.body.message
    }
  }, {
    sort: {_id: -1},
    upsert: true
  }, (err, result) => {
    if (err) return res.send(err)
    res.send(result)
  })
})
```

We also included `window.location.reload(true)` in the last `.then` to reload the browser so that we can see the changes when they happen.

And that's it for **CREATE** let's finish it off by doing the **DELETE** operation.
