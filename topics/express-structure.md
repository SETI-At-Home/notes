# Simple
#The first piece of code is a simple Express.js router that handles a GET request to the route "/api/products/:productId". The router uses the req.params object to retrieve the productId from the URL, and then creates a connection pool using the mssql library. It then creates a query to select all data from the "product" table where the "id" column matches the productId passed in the URL. The result of the query is then passed to the response object and sent to the client. The code also includes a try-catch block to handle any errors that may occur and finally close the sql connection.

#The second piece of code is an example of a layered architecture for the same functionality. The router code has been refactored to use a separate products-service.js file, which handles the logic for retrieving the product by its id. This service file in turn uses a separate products-repository.js file, which contains the database query logic. This layered architecture makes the code more organized and easier to maintain, as well as easier to unit test.

#The service layer also uses a someBusinessLogic method, which is exported from a utils.js file. This method is used to perform any business logic that needs to be applied on the product data before returning it to the client.

#In summary, the provided code is an example of how to refactor the simple router code to use a layered architecture, making it more organized, maintainable and testable.

```js
router.get("/api/products/:productId", async (req, res) => {
    try {
        let { productId } = req.params;

        const pool = await poolPromise;
        const request = await pool.request();

        let query = `
            select *
            from product p
            where p.id = @productId
        `;

        request.input("productId", sql.Int, productId);

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

# Layered

This is useful because the functions are unit testable.

**/api/products.js**

```js
const productsService = require("../services/products-service");

const service = new productsService();

router.get("/api/products/:productId", async (req, res) => {
    try {
        let { productId } = req.params;
        let { product } = await service.getProductById(productId);

        res.send(product);
    } catch (err) {
        console.log("Error: " + err);
    } finally {
        sql.close();
    }
});
```

**/services/products-service.js**

```js
const ProductsRepository = require("../repository/products-repository")
const { someBusinessLogic } = require("../utils")

class ProductsService(){
    constructor(){
        this.repository = new ProductsRepository();
    }

    async getProductById(productId){
        try {
            let product = await this.repository.findProductById(productId);

            return someBusinessLogic(product);

        } catch (err) {
            throw new Error(err)
        }
    }

}
```

**/repository/products-repository.js**

```js
const ProductsRepository = require("mssql")

class ProductsRepository(){
    constructor(){
        this.repository = new ProductsRepository();
    }

    async findProductById(productId){
        try {
            const pool = await poolPromise;
            const request = await pool.request();

            let query = `
                select *
                from product p
                where p.id = @productId
            `;

            request.input("productId", sql.Int, productId);

            let result = await request.query(query);

            let product = result.recordset;

            return product;

        } catch (err) {
            throw new Error(err)
        }
    }

}
```
