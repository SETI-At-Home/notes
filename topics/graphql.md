GraphQL is an alternative to REST API. It's a typed query language.

It allows flexible querying from the front-end i.e. client.

It solves the problem of needing **just** a piece of data, without getting all the data from an end-point.  

REST API would handle this for a `GET /user` request with:
- `GET /user-slim` - Lots of routes and updating.
- `GET /user?data=slim` - The API becomes complex

GraphQL would handle this with `POST /user` by sending this query expression in the body.

```js
{
    query {           // operation type
        user {        // operation end-point
            name      // requested field
            age
        }
    }
}
```

GraphQL **always** uses `POST` because it sends the query expression.

| REST API | GraphQL |
| ------------- |-------------|
| GET | `query` operation type |
| POST, PUT, PATCH, DELETE | `mutation` operation type |
| Routes | Query definitions |
| Controllers | Resolvers |


#Example 1: Retrieve the name and age of a user

```js
// GraphQL query
{
    query {
        user {
            name
            age
        }
    }
}

// REST API request
GET /user-slim
```

#Example 2: Update the email of a user

```js
// GraphQL mutation
mutation {
    updateUser(id: 1, email: "newemail@example.com") {
        id
        email
    }
}

// REST API request
PATCH /user/1
{
    "email": "newemail@example.com"
}
```

#Example 3: Retrieve all products with their name and price

```js
// GraphQL query
{
    query {
        products {
            name
            price
        }
    }
}

// REST API request
GET /products
```

#Example 4: Create a new order

```js
// GraphQL mutation
mutation {
    createOrder(productId: 1, quantity: 2) {
        id
        totalPrice
    }
}

// REST API request
POST /orders
{
    "productId": 1,
    "quantity": 2
}
```
