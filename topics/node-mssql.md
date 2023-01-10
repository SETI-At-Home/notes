This is a Node.js script that exports an object containing a connection pool to a Microsoft SQL (MSSQL) database, and a router function for an Express.js application. The script exports two objects: an instance of the sql object from the mssql package and a promise that resolves to a connection pool object.

1. The mssql package is imported at the top of the script, and an instance of the ConnectionPool class is created, passing in a config object from ./db.js.

2. The connect method is then called on the ConnectionPool instance, which returns a promise that resolves to the pool when a successful connection to the database has been established.

3. The catch method is called on the promise returned by connect to handle any error that might occur while connecting to the database, in which case the error message is logged to the console.

4. The poolPromise and sql objects are then exported.

5. The script then imports the poolPromise and sql objects

6. Defines a route for an Express.js application using 
    `router.get("/api/companies/:companyId",...)`,
    `async function` which take two parameters `req & res`
     - Within this function it tries to connect to the database pool by awaiting on `poolPromise`
     - Then it creates a new request object by awaiting on `pool.request()`
     - Then it defines the query it wants to run with the placeholder `@companyId`
     - Then it inputs the companyId it got from req.params to the request
     - Then it runs the query and awaits the result
     - Then it sends the final result in the form of recordset to the client
     - Finally it closes the connection.

7. This route function will handle a GET request to the /api/companies/:companyId endpoint, it will run the query to select all columns from the 'company' table where the id of the company matches the companyId path parameter passed in the request. The resulting recordset will be sent back to the client as a response.

```js
const sql = require("mssql");
const db = require("./db.js");

const poolPromise = new sql.ConnectionPool(db)
    .connect()
    .then((pool) => {
        // console.log("Connected to MSSQL");
        return pool;
    })
    .catch((err) => console.log("Database Connection Failed! Bad Config: ", err));

module.exports = {
    sql,
    poolPromise,
};
```

```js
const { sql, poolPromise } = require("../../pool");

router.get("/api/companies/:companyId", async (req, res) => {
    try {
        const pool = await poolPromise;
        const request = await pool.request();

        let query = `
            select * 
            from company c
            where c.id = @companyId
        `;

        request.input("companyId", sql.Int, companyId);

        let result = await request.query(query);

        let final = result.recordset;

        res.send(final);
    } catch (err) {
        console.log("Error: " + err);
    } finally {
        sql.close();
    }
});
```
