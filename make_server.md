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
