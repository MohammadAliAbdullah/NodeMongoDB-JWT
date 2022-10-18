Here is the explanation that should clear doubts on `express.json()` and `express.urlencoded()` and the use of body-parser.  It took me some time to figure this out.

 1. What is Middleware?  It is those methods/functions/operations that are called BETWEEN processing the Request and sending the Response in your application method.

 2. When talking about `express.json()` and `express.urlencoded()` think specifically about POST requests (i.e. the .post request object) and PUT Requests (i.e. the .put request object)

 3. You DO NOT NEED `express.json()` and `express.urlencoded()` for GET Requests or DELETE Requests.

 4. You NEED `express.json()` and `express.urlencoded()` for POST and PUT requests, because in both these requests you are **sending data** (in the form of some data object) to the server and you are asking the server to accept or store that data (object), which is enclosed in the body (i.e. `req.body`) of that (POST or PUT) Request 

 5. Express provides you with middleware to deal with the (incoming) data (object) in the body of the request.

  a. `express.json()` is a method inbuilt in express to recognize the incoming Request Object as a **JSON Object**.  This method is called as a middleware in your application using the code: `app.use(express.json());`

  b. `express.urlencoded()` is a method inbuilt in express to recognize the incoming Request Object as **strings or arrays**.  This method is called as a middleware in your application using the code: `app.use(express.urlencoded());`

 6. ALTERNATIVELY, I recommend using **body-parser** (it is an NPM package) to do the same thing.  It is developed by the same peeps who built express and is designed to work with express.  body-parser used to be part of express. Think of body-parser specifically for POST Requests (i.e. the .post request object) and/or PUT Requests (i.e. the .put request object).

 7. In body-parser you can do

        // calling body-parser to handle the Request Object from POST requests
        var bodyParser = require('body-parser');
        // parse application/json, basically parse incoming Request Object as a JSON Object 
        app.use(bodyParser.json());
        // parse application/x-www-form-urlencoded, basically can only parse incoming Request Object if strings or arrays
        app.use(bodyParser.urlencoded({ extended: false }));
        // combines the 2 above, then you can parse incoming Request Object if object, with nested objects, or generally any type.
        app.use(bodyParser.urlencoded({ extended: true }));

# Diff

If you ask me "what is the **difference** between `express.urlencoded({extended: false})` and `express.json()`", well, the difference is:

 - `express.json()`

If you use `express.json()` it will parse the body from post/fetch request **except** from html post form. It wont parse information from the html **post** form :

    <form action="/" method="POST">
        <input type="text" name="username">
        <button>Submit</button>
    </form>

For instance, if you fill the form with "dean" then submit it, Express wont have an idea what inside the body with this express code:

    const express = require('express')
    const app = express()
    
    app.use(express.json())
    // app.use(express.urlencoded({ extended: false }))
    app.use(express.static("public"))
    
    
    app.get("/", (req, res) => {
        res.sendFile("index.html")
    })
    
    app.post("/", (req, res) => {
        res.send(req.body)
    })
    
    
    const port = process.env.PORT || 3001
    app.listen(port, () => {
        console.log(`Server Up in Port ${port}`);
    })

It will send `{}` after you click submit. But if you uncommented `app.use(express.urlencoded({extended: false}))`,
then you will get `{"username": "dean"}`.

So the difference is `express.json()` is a body parser for post request **except** html post form and `express.urlencoded({extended: false})` is a body parser for html post form.




source: https://stackoverflow.com/questions/23259168/what-are-express-json-and-express-urlencoded