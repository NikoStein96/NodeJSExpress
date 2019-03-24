# Why would you consider a Scripting Language as JavaScript as your Backend Platform?
+ Javascript is one of the biggest scripting languages in the world. Using it as a backend language is preferable since you would be using the same language for frontend and backend. 
+ Also a big plus is that JavaScript has a large community, and people are always developing packages that can be used easily in a new project. 
+ You don't have to write a lot of code for an application to run.

# Explain Pros & Cons in using Node.js + Express to implement your Backend compared to a strategy using, for example, Java/JAX-RS/Tomcat
### Pros
+ Again it is very fast to get up and started with NodeJS and Express.
+ It's easy to add express middleware, which is a function that runs before the request. 
+ It's very easy adding some functionality thanks to the ability to import modules.
+ NodeJS is very good at handling many concurrent requests that you would see e.g. in a chat app.

### Cons
+ Despite JavaScript being able to run code asynchronous, it's not intended for CPU heavy application. It is much better choosing Java for CPU heavy apps. 
+ 500 errors will crash a NodeJS application, where in Java it won't.
+ Performance bottlenecks and design issues.

# Explain briefly how to deploy a Node/Express application including how to solve the following deployment problems:

### Ensure that you Node-process restarts after a (potential) exception that closed the application

You can install a dependency called forever which is used to keep the server alive by restarting it automatically when we encounter an error. These depencies like forever are called Process Manager, and there are many of them out there.

### Ensure that you Node-process restarts after a server (Ubuntu) restart

We can install the same dependecy as above.

### Ensure that you can run “many” node-applications on a single droplet on the same port (80)

Could be accomplished by using a reverse proxy like nginx

# Explain the difference between “Debug outputs” and application logging. What’s wrong with console.log(..) statements in our backend-code.

One of the biggest disadvantages is that you can’t toggle logging on and off, not out of the box at least. You could wrap console and extend it to do this, but this is code you’ll have to write, and likely code that will have to overwrite the built-in console functions.

You might want to turn off logging if you’re in a development environment vs a production environment. Or even if you’re just testing locally on your machine or VM, if you’ve got a ton of logging for debug purposes or otherwise, that can really clutter up your console and you might want to just test with logging disabled for a bit.

# Explain, using relevant examples, concepts related to testing a REST-API using Node/JavaScript + relevant packages 
It is possible to test code in JavaScript by using mocha with chai. Chai's intend is to make test code much more readable to let's say people that don't understand code. Expect is alot easier to understand than Assert and so on.
```javascript
const expect = require("chai").expect;
describe("Calculator API", function() {
    describe("Testing the basic Calc API", function() {
        it("9 / 3 should return 7", function() {
            expect(calc.divide(9, 3)).to.be.equal(3);
        });
        it("4 / 0 should throw error", function() {
            expect(() => calc.divide(4, 0)).to.throw(/Attempt to divide by zero/);
        });
    });
    describe("Testing the REST Calc API", function() {
        before(function(done) {
            calc.calcServer(PORT, function(s) {
                server = s;
                done();
            });
        });
        it("4 + 3 should return 7", async function() {
            const res = await fetch(URL + "add/4/3").then(r => r.text());
            expect(res).to.be.equal("7");
        });
        after(function() {
            server.close();
        });
    });
});
```

# Explain, using relevant examples, the Express concept; middleware.
Middleware is a function that is executed when we receive a request.

```javascript
var app = express()

app.use(function (req, res, next) {
  console.log('Time:', Date.now())
  next()
})
```
Let's say this function is placed in the App.js our root file. Then everytime we receive a request the function is going to run and print the Time: Date to the console. After it is done we let NodeJS know that the middleware function is done so we use the next() function in order to jump to the next middleware function that has to run.

```javascript
app.use('/user/:id', function (req, res, next) {
  console.log('Request Type:', req.method)
  next()
})
```
Middleware functions can also be route specific, so here every time we receive a request for a user, the middleware function is going to run before the actual endpoint gets called, so here we console.log the request method and move on by calling next();

# Compare the express strategy toward (server side) templating with the one you used with Java on second semester.

We can setup our Nodejs express server to handle static files. We just tell express where our static files are located.
In combination with the static files we can use a templating engine. like handlebars to allow our application to build dynamic web pages. So that would make our web app look like the one we had from 2. Sem. Where we used JSP as our templating engine.

JSP for java code
+ <% Pure Java code %>

HBS for adding some logic or variables is 
+ {{}}

# Demonstrate a simple Server Side Rendering example using a technology of your own choice (pug, EJS, ..).

```javascript
app.engine("handlebars", exphbs({ defaultLayout: "main" }));
app.set("view engine", "handlebars");
app.use(express.static(__dirname + "/public"));

app.get("/", function(req, res) {
  res.render("index");
});
```
We told the app to use handlebars as a template enige, and we have told where our static files are located. Now with the get request we can render a specific handlebars/html page.

```handlebars
<div class="entry">
  <h1>{{title}}</h1>
  <div class="body">
    {{body}}
  </div>
</div>
```
Now the index.hbs would be rendered into the body of the main.hbs file, and if we had passed a title from the / endpoint it would also be rendered. 

# Explain, using relevant examples, your strategy for implementing a REST-API with Node/Express and show how you can "test" all the four CRUD operations programmatically using, for example, the Request package.

The REST API
```javascript
var express = require("express");
var router = express.Router();
var jokes = require("../model/jokes");

/* GET home page. */
router.get("/", function(req, res, next) {
    res.render("index", { title: "Express", userName: req.session.userName });
});

router.get("/login", function(req, res, next) {
    res.render("login", { title: "Login" });
});

router.post("/login", function(req, res, next) {
    res.render("index", { title: "Express" });
});

router.get("/joke", function(req, res, next) {
    let counter = req.session.jokeCounter;
    counter++;
    req.session.jokeCounter = counter;
    res.render("randomJoke", { title: "Joke", joke: jokes.getRandomJoke() });
});

router.get("/jokes", function(req, res, next) {
    let counter = req.session.jokesCounter;
    counter++;
    req.session.jokesCounter = counter;
    res.render("allJokes", { title: "Jokes", jokes: jokes.getAllJokes() });
});

router.get("/addjoke", function(req, res, next) {
    res.render("addJoke", { title: "Add Joke" });
});

router.post("/storejoke", function(req, res, next) {
    let counter = req.session.storeJokeCounter;
    counter++;
    req.session.storeJokeCounter = counter;

    const joke = req.body.joke;

    jokes.addJoke(joke);

    res.render("addJoke", { title: "Add Joke" });
});

module.exports = router;
```
Testing the rest API
```javscript
const expect = require("chai").expect;
const http = require('http');
const app = require('../app');
const fetch = require("node-fetch");
const TEST_PORT = 3344;
const URL = `http://localhost:${TEST_PORT}/api`;
const jokes = require("../model/jokes");
let server;
describe("Verify the Joke API", function() {
    before(function(done){
        server = http.createServer(app);
        server.listen(TEST_PORT,()=>{
            console.log("Server Started")
            done()
        })
    })
    after(function(done){
        server.close();
        done();
    })
    beforeEach(function(){
        jokes.setJokes(["aaa","bbb","ccc"])
    })
    it("Should add the joke 'ddd",async function(){
        var init = {
            method: "POST",
            headers : {"content-type": "application/json"},
            body : JSON.stringify({joke: "ddd"})
        }
        await fetch(URL+"/addjoke",init).then(r => r.json());
        //Verify result
        expect(jokes.getAllJokes()).lengthOf(4);
        expect(jokes.getAllJokes()).to.include("ddd")
    })
}
```

# Explain, using relevant examples, about testing JavaScript code, relevant packages (Mocha etc.) and how to test asynchronous code.
Using Mocha and chai we can make the testing more readable with the expect and description of each test.
```javscript
const expect = require("chai").expect;
const calc = require("../calc");
describe("Calculator API", function() {
	describe("Testing the basic Calc API", function() {
		it("4 + 3 should return 7", function() {
			const res = calc.add(4, 3);
			expect(res).to.be.equal(7);
		});
		it("4 - 3 should return 1", function() {
			const res = calc.subtract(4, 3);
			expect(res).to.be.equal(1);
		});
		it("4 * 3 should return 12", function() {
			const res = calc.muliply(4, 3);
			expect(res).to.be.equal(12);
		});
		it("9 / 3 should return 7", function() {
			const res = calc.divide(9, 3);
			expect(res).to.be.equal(3);
		});
		it("4 / 0 should throw error", function() {
			expect(() => calc.divide(4, 0)).to.throw(/Attempt to divide by zero/);
		});
    });
});
```
Asynch testing. 
```javascript
const expect = require("chai").expect;
const calc = require("../calc");
const fetch = require("node-fetch");
const PORT = 2345;
const URL = `http://localhost:${PORT}/api/calc/`;
let server;
describe("Testing the REST Calc API", function() {
    before(function(done) {
        calc.calcServer(PORT, function(s) {
            server = s;
            done();
        });
    });
    //testing asynchronous code
    it("4 + 3 should return 7", async function() {
        const res = await fetch(URL + "add/4/3").then(r => r.text());
        expect(res).to.be.equal("7");
    });
    after(function() {
        server.close();
    });
});
```

# NoSQL, MongoDB and Mongoose

# Explain, generally, what is meant by a NoSQL database.

NoSQL encompasses a wide variety of different database technologies that were developed in response to the demands presented in building modern applications:

+ Developers are working with applications that create massive volumes of new, rapidly changing data types — structured, semi-structured, unstructured and polymorphic data.

+ Long gone is the twelve-to-eighteen month waterfall development cycle. Now small teams work in agile sprints, iterating quick ly and pushing code every week or two, some even multiple times every day.

+ Applications that once served a finite audience are now delivered as services that must be always-on, accessible from many different devices and scaled globally to millions of users.

+ Organizations are now turning to scale-out architectures using open source software, commodity servers and cloud computing instead of large monolithic servers and storage infrastructure.

Relational databases were not designed to cope with the scale and agility challenges that face modern applications, nor were they built to take advantage of the commodity storage and processing power available today.

# Explain Pros & Cons in using a NoSQL database like MongoDB as your data store, compared to a traditional Relational SQL Database like MySQL.

NoSQL databases differ from older, relational technology in four main areas:

+ Data models: A NoSQL database lets you build an application without having to define the schema first unlike relational databases which make you define your schema before you can add any data to the system. No predefined schema makes NoSQL databases much easier to update as your data and requirements change.
+ Data structure: Relational databases were built in an era where data was fairly structured and clearly defined by their relationships. NoSQL databases are designed to handle unstructured data (e.g., texts, social media posts, video, email) which makes up much of the data that exists today.
+ Scaling: It’s much cheaper to scale a NoSQL database than a relational database because you can add capacity by scaling out over cheap, commodity servers. Relational databases, on the other hand, require a single server to host your entire database. To scale, you need to buy a bigger, more expensive server.
+ Development model: NoSQL databases are open source whereas relational databases typically are closed source with licensing fees baked into the use of their software. With NoSQL, you can get started on a project without any heavy investments in software fees upfront.

# Explain reasons to add a layer like Mongoose, on top on of a schema-less database like MongoDB

Mongoose is an object document modeling (ODM) layer that sits on top of Node's MongoDB driver. If your coming from SQL, it's similar to an ORM for a relational database. While it's not required to use Mongoose with the Mongo, here are four reasons why using Mongoose with MongoDB is generally a good idea.

1. Schemas
MongoDB is a denormalized NoSQL database. This makes it inherently schema-less as documents have varying sets of fields with different data types. While this provides your data model with flexibility as it evolves over time, it can be difficult to cope with coming from a SQL background. Mongoose defines a schema for your data models so your documents follow a specific structure with pre-defined data types.

2. Validation
Mongoose has built in validation for schema definitions. This saves you from writing a bunch of validation code that you have to otherwise write with the MongoDB driver. By simply including things like required:true in your schema definitions, Mongoose provides out-of-the-box validations for your collections (including data types).

3. Instance Methods
Mongoose provides optional pre and post save operations for data models. This makes it easy to define hooks and custom functionality on successful reads/writes etc. You can also define custom methods that act on a particular instance (or document). While you can achieve similar functionality with the native MongoDB driver, Mongoose makes it easier to define and organize such methods within your schema definition.

4. Returning results
Mongoose makes returning updated documents or query results easier. A prime example can be found with update queries. While the native driver returns an object with a success flag and the number of documents modified, Mongoose returns the updated object itself so you can easily work with the results.

# Demonstrate, using a REST-API you have designed, how to perform all CRUD operations on a MongoDB

```javascript
app.post("/tasks", (req, res) => {
  const task = new Task(req.body);

  task
    .save()
    .then(() => {
      res.send(task);
    })
    .catch(e => {
      res.status(400).send(e);
    });
});

app.get("/tasks", (req, res) => {
  Task.find()
    .then(tasks => {
      res.send(tasks);
    })
    .catch(e => {
      res.status(500).send();
    });
});
```
Some examples on the endpoints made from the root file app.js. We use the models that we've created in the models folder to query the database because the models are the one's using some of the mongoose functionality. 




