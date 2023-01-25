To further enhance the security of the app, it's recommended to use a package like dotenv-expand which allows for referencing other environment variables in the .env file. This allows for the use of a single source of truth for sensitive information, such as an environment variable that contains the database password.

Another way to secure the environment variables is to use a package like dotenv-webpack which allows for loading environment variables during the webpack build process. This way, the environment variables are never exposed in the JavaScript code and are only available during the build.

It's also important to note that environment variables are only accessible by the process that sets them and its child processes. To share environment variables between multiple processes, a process manager like PM2 can be used.

In summary, using environment variables is an effective way to manage different parameters depending on the environment while keeping sensitive information secure. It's important to never commit the .env file and to use packages like dotenv-expand and dotenv-webpack to further secure the environment variables.

In order to use different parameters in our app depending on the enviroment such as...

-   Server ports
-   Database credentials
-   API keys

...we can declare environment variables.

We do this by having a `.env` file to store the variables.

**NEVER COMMIT THIS FILE. PUT IT IN .gitignore!**

```
DBNAME=database
DBUSER=root
DBPASSWORD=root
```

Which we can read with the `dotenv` package like so.

```js
// config.js
const dotenv = require("dotenv").config({
    path: `${__dirname}/.env`,
});

const database = process.env.DBNAME;
const user = process.env.DBUSER;
const password = process.env.DBPASSWORD;

module.exports = {
    db: {
        server: "localhost",
        database: database,
        user: user,
        password: password,
    },
};
```

**NOTE:** `__dirname` is required because the variables are `undefined` when accessing the `.env` file outside the root folder.

We can then have one `.env` file for development in the local machine, and one for production on the live server. This way we can just set the variables and not touch the code.

**NEVER COMMIT THIS FILE. PUT IT IN .gitignore!**

We can also pass in the variables without a `.env` file and run the script.

```
DBNAME=database DBUSER=root DBPASSWORD=root node server.js
```
