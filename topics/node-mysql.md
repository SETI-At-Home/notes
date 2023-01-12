Step 1: Import required modules

```js
const express = require("express");
const util = require("util");
```

Step 2: Create a databaseHandler function that takes in a pool object and returns a middleware function

```js
let databaseHandler = (pool) => {
    return (req, res, next) => {
        // let subodmain = req.subdomains[0];
        // req.subdomains is [] in deployment due to the reverse_proxy
        // req.decoded comes from authenticationHandler
        let database = req.decoded.database;
```

Step 3: Get a connection from the main pool in app.js

```js
pool.getConnection((err, conn) => {
            if (err) {
                console.log(err);
                return;
            }
```

Step 4: Change the database for the connection

```js
conn.changeUser({ database: database }, function (err) {
                if (err) {
                    console.log(err);
                    return;
                }
```

Step 5: Promisify for Node.js async/await and pass the modified query method down the chain as a property of req, available via next()

```js
const asyncQuery = util.promisify(conn.query).bind(conn);
req.asyncQuery = asyncQuery;
req.connection = conn;
req.tenant = database;
next();
```

Step 6: In the router, handle GET requests to the specified route

```js
router.get("/api/companies/:companyId", async (req, res, next) => {
    try {
        let companyId = req.params.companyId;
```

Step 7: Define the query and execute it using the asyncQuery method

```js
res.status(200).send(company);
```

Step 9: Release the connection

```js
finally {
    req.connection.release();
}
```