# Access Control

Scenario: Allow users to sign-up, log-in, and be given access capabilities based on roles. Imagine a multi-story building with access control depending on rooms, floor, and purpose. For example, the first floor could be open to the general public, the second and third floor are open to all employees, and the top floor is only open to management. Each floor also has various rooms and spaces with levels of access. The cafeteria and bathrooms have no access requirements, the telecoms room is only accessible by IT, the fire equipment room by the fire department, etc...Like I mentioned, the building is open to the general public, this could be analogues to the homepage itself. Now, consider a user sign-up as an employee with a key card. This gives the person access to the second floor and the employee spaces, but *only* those spaces. The card can be upgraded accordingly to allow the employee to enter the more restricted spaces, and this is akin to access control with assigned roles. This README will describe the process and requirements to assign roles

## Business Requirements

1. Users can create an account, associated with a “role”
2. User Roles will be pre-defined and will each have a list of allowed capabilities
    * `admin` can read, create, update, delete
    * `editor` can read, create, update
    * `writer` can read, create
    user can read
3. Users can then login with a valid username and password
4. Alternatively, users can login using an OAuth provider such as Google or GitHub
    * In this case, users should be automatically assigned the role of `user`
5. Once logged in, Users can then access any route on the server, so long as they are permitted by the capabilities that match their role.
    * For example, a route that deletes records should only work if your user role is “admin”

## Technical Requirements

The application will be created with the following overall architecture and methodologies

1. Node.js
2. ES6 Classes and best practices
3. ExpressJS Web Server, built modularly
    * “Auth” routes for handling the login and authentication system
        * POST `/signup` to create an account
        * POST `/signin` to login with Basic Auth
        * GET `/oauth`
    * Middleware for handling 404 and 500 conditions
    * Middleware for handling each type of authentication
        * Basic (username + password) to be used on the `/signin` route only
            * i.e. `app.post('/signin', basicAuthentication, (req, res) => { ... })`
        * OAuth (3rd Party) to be used on the `/oauth` route only
            * i.e. `app.get('/oauth', OAuth, (req, res) => { ... })`
            * Handles the handshake process from a 3rd party OAuth system
        * Bearer (token) to be used on any other route in the server that requires a logged in user
            * i.e. `app.get('/secretstuff', tokenAuthentication, (req, res) => { ... })`
    * Middleware for handling authorization
        * To be run after Bearer Middleware on routes to be protected
        * Accepts a “capability” as a parameter
        * i.e. `app.delete('/secretstuff', tokenAuthRequired, capability('delete'), (req, res) => { ... })`
4. User Persistence using a Mongo Database (NoSQL)
    * Mongoose Schemas (data models) to define and model data
5. Test Driven Development, using Jest
    * Tests will be runnable locally
    * Tests will auto-execute (CI) in your repo using GitHub actions
    * Tests will use a 3rd party library called `supergoose` to:
        * “mock” the mongo running database
        * “mock” the running Express server
6. Deployment to Heroku

## Model/UML

![UML](/img/lab08UML.jpg)
