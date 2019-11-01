### Makes a server with just NODE
```
const http = require('http');
const fs = require('fs');

/**
 * Creates a server in node where we accept the incoming req and provides the response
 */
const server = http.createServer((req, res) => {
    const url = req.url; // extract the url from the req object
    const method = req.method; // same as the method
    // we can return different things in response to different requests
    // for the home page below, we respond by sending a text that says welcome page
    if (url === '/') {
        res.write('welcome page');
        return res.end(); // to close off the response we just .end() the response
    }
    // We do two things here, if its a POST req to an endpoint of /messages, we listen for data
    // the data gets sent down the stream in chunks so once each chunk is processed, we push that value into an arr
    if (url === '/message' && method === 'POST') {
        const body = [];
        req.on('data', (chunk) => {
            body.push(chunk);
        });
        // when we end the req, we want to parse the buffer and create a file that contains the code
        // we then REDIRECT with a 302 and send them to the homepage. 
        // make sure we return the res.end() rather than just calling it so we can break out of our loop
        return req.on('end', () => {
            const parsedBody = Buffer.concat(body).toString();
            const message = parsedBody.split('=')[1];
            fs.writeFileSync('message.txt', message);
            res.statusCode = 302;
            res.setHeader('Location', '/');
            return res.end();
        });
    }
    res.setHeader('Content-type', 'text/html');
    res.write('youre done');
    res.end();
});

server.listen(3000);
```

### load slash route, see form, enter value, hit button, reach url on our server and log it, redirect it
```
const http = require('http');

const server = http.createServer((req,res) => {
    const url = req.url;

    // first route
    if (url === '/') {
        res.setHeader('Content-type', 'text/html');
        res.write('<html>')
        res.write('<head><title>assignment</title></head>');
        res.write('<body><form action="/create-user" method="POST"><input type="text" name="username"><button type="submit"></form></body>')
        res.write('</html>')
        return res.end();
    }

    // sedond route
    if (url === '/users') {
        res.setHeader('Content-type', 'text/html');
        res.write('<html>')
        res.write('<head><title>assignment</title></head>');
        res.write('<body><ul><li>cool</li><li>hot</li></ul></body>')
        res.write('</html>')
        return res.end();
    }

    if (url === '/create-user') {
        const body = [];
        req.on('data', (chunk) => {
            // we have an array of buffered chunks
            body.push(chunk);
        });
        // we want to read the data once all buffered chunks are available. Do that in end()
        // end is used when all data is read and has been pushed to the body
        // in this stage we can transform all the buffered data into readable string
        req.on('end', () => {
            const parsedBody = Buffer.concat(body).toString();
            // data is available as username=foo_bar_entry so we can split the data by equal sign and get the second el(which is the value that was set)
            console.log(parsedBody.split('=')[1]);
        });
        res.statusCode=302;
        res.setHeader('Location', '/');
        res.end();
    }
})

server.listen(3000);
```
