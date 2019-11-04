### basic express routing logic
```
const express = require('express');
const app = express();
const bodyParser = require('body-parser');
const path = require('path');

const authorRoutes = require('./routes/author');

// for body parser, we define it at top before out other routing middleware bc we want this middleware to run all the time
app.use (bodyParser.urlencoded({extended: false}));
app.use(express.static(path.join(__dirname, 'public'))); // built in middleware to serve static files

/**
 * below is our routing middleware. How should we handle reqs to different urls?
 * NOTE: we can clean up this file uing express Router()
 **/
app.use((req, res, next) => {
    console.log('first middlware that passed req to next middleware');
    next();
});

// we can define these same routes in a seperate file and import them for reuse
// we can filter paths so routing logic ONLY gets applied to requests that begin with /author
app.use('/author', authorRoutes);

// this express app.get() ONLY fires for incomming get requests with /add-product path!
app.get('/add-product' ,(req,res,next) => {
    console.log('add user middleware');
    res.send('<form action="add-product" method="POST"><input type="text" name="title"><button type="submit">Add prod</button></form>');
});

app.get('/html-please', (req, res, next) => {
    // __dirname holds the absolute path to our current directory so we can extract our html file we want to send
    res.sendFile(path.join(__dirname, '../', 'views', 'hml.html')); 
})

// we filter for incomming POST reqs with a /product path being used
// we're using the same path as before, but its a different method so its allowed
app.post('/add-product', (req, res, next) => {
    // extract what user sends us using the bodyParser middleware
    console.log(req.body);
    // redirect
    res.redirect('/');
});

app.use('/' ,(req,res,next) => {
    console.log('plain url');
    res.send('plain hp');
});

// using chaining, we add a 404 status to catch all unhandles reqs
app.use((req,res,next) => {
    // res.status(404).send('<h1>Page not found...</h1>')
    res.status(404).sendFile(path.join(__dirname, 'views', '404.html'));
});

app.listen(3000);
```