## BookStore API Tests

[![BookStore API Tests](https://github.com/minalaz/DemoQa_BookStoreAPI/actions/workflows/newman.yml/badge.svg)](https://github.com/minalaz/DemoQa_BookStoreAPI/actions/workflows/newman.yml)

Automated API test suite for the DemoQA BookStore API, created and maintained in Postman.

The collection covers the main BookStore user journey: user registration, authentication, book management, authorization checks, and final cleanup of test data.

## Project Overview

This project validates the DemoQA BookStore API through end-to-end and negative API test scenarios.

The tests are organized around the following flows:

- User registration
- Login and token generation
- Authorization validation
- Retrieving available books
- Adding books to a user account
- Verifying books on the user profile
- Deleting books
- Unauthorized access checks
- Test user cleanup

The collection uses dynamic runtime data, so no static test user credentials or tokens are stored in the repository.

## Tech Stack

- Postman
- JavaScript test scripts
- Postman Collection Runner
- JSON Schema validation with AJV
- Git / GitHub
- Optional: Newman or Postman CLI for command-line execution

## Repository Structure

```text
.
├── .github
│   └── workflows/newman.yml
├── .gitignore
├── BookStore_API_Environment.postman_environment.json
├── BookStore_API_Tests.postman_collection.json
├── package.json
├── package-lock.json
└── README.md
```

## Environment

The collection requires one Postman environment:

```text
BookStore_API_Environment
```

Required variable:

| Variable  | Value                |
| --------- | -------------------- |
| `baseURL` | `https://demoqa.com` |

All requests use the `{{baseURL}}` environment variable.

## Test Coverage

### Register User

Validates the user registration endpoint:

```http
POST /account/v1/user
```

Covered scenarios:

- Successful registration with valid credentials
- Registration without username
- Registration without password
- Registration without any data
- Registration with a short or invalid password
- Registration with an already existing username

### BookStore Login

Validates authentication-related endpoints:

```http
POST /account/v1/generateToken
POST /account/v1/authorized
```

Covered scenarios:

- Successful login with valid credentials
- Token generation
- Authorization with a valid token
- Login without credentials
- Login with invalid credentials

Note: The DemoQA `/generateToken` endpoint may return HTTP 200 even when authentication fails. In that case, the actual login result is validated through the response body fields such as `status`, `result`, `token`, and `expires`.

### Add, Get, and Delete Books

Validates BookStore book management endpoints:

```http
GET    /bookStore/v1/books
POST   /bookStore/v1/books
GET    /account/v1/user/{{userId}}
DELETE /bookStore/v1/book
DELETE /bookStore/v1/books?UserId={{userId}}
```

Covered scenarios:

- Retrieve all available books
- Add one book to the user account
- Verify that the added book appears on the user profile
- Delete one book from the account
- Add two books to the account
- Delete all books from the account
- Attempt to add a book without authorization
- Attempt to delete books without authorization

### Cleanup

Final cleanup request:

```http
DELETE /account/v1/user/{{userId}}
```

The cleanup step deletes the dynamically created test user and clears collection variables after the run.

## Runtime Data Handling

This collection does not rely on hardcoded users, passwords, tokens, or book data.

Runtime values are generated or extracted during the collection run and stored as collection variables, including:

- `username`
- `password`
- `userId`
- `token`
- `usedUsername`
- `usedPassword`
- `bookISBN_0`
- `bookISBN_1`
- book metadata used for response validation

At the end of the collection run, collection variables are cleared using:

```javascript
pm.collectionVariables.clear();
```

This keeps the collection clean and prevents generated runtime data from being persisted after execution.

## Collection-Level Scripts

The collection includes shared scripts at collection level.

### Pre-request Script

The collection-level pre-request script defines reusable test data and expected messages, including:

- Negative test credentials
- Password validation message
- Duplicate user message
- Authorization error message
- Expected login success and failure messages
- JSON Schema definitions for response validation

### Test Script

The collection-level test script runs after each request and includes:

- Request and response logging in the Postman console
- Response time validation
- Content-Type validation
- Automatic schema validation based on request name

Global assertions include:

```javascript
pm.test("Response time is below 5000ms", function () {
  pm.expect(pm.response.responseTime).to.be.below(5000);
});
```

Schema validation is handled dynamically by mapping request names to predefined schemas.

## How to Run in Postman

1. Import the collection into Postman:
   - `BookStore_API_Tests.postman_collection.json`

2. Import the environment:
   - `BookStore_API_Environment.postman_environment.json`

3. Select the `BookStore_API_Environment` environment.

4. Run the full collection using the Postman Collection Runner.

5. Keep the request order unchanged, because later requests depend on runtime values created by earlier requests.

Recommended order:

```text
Register User
BookStore Login
Add, get, delete books
CleanUp
```

## Optional Command-Line Execution

The collection can also be executed from the command line using Newman.

Example:

```bash
newman run BookStore_API_Tests.postman_collection.json \
  -e BookStore_API_Environment.postman_environment.json
```

If Newman is not installed globally, it can be added as a dev dependency:

```bash
npm install --save-dev newman
```

Then add a script to `package.json`:

```json
{
  "scripts": {
    "test:api": "newman run BookStore_API_Tests.postman_collection.json -e BookStore_API_Environment.postman_environment.json"
  }
}
```

Run with:

```bash
npm run test:api
```

## Security and Repository Notes

This project is designed as a demo QA project and uses the public DemoQA API.

Security-related practices used in this collection:

- Only `baseURL` is stored in the environment file
- User credentials are generated dynamically during runtime
- Authentication token is stored only as a runtime collection variable
- Collection variables are cleared during cleanup

## Workflow

The collection is developed and validated in Postman, then exported manually into the repository.

The repository is managed through VS Code and Git, which keeps file changes, documentation, and commits under manual control.

## AI Assistance

Postman AI was used as an assistant to refine an existing manually created Postman collection.

The project repository is managed manually through VS Code and Git, and Postman AI does not have direct access to repository files.

All suggested changes were reviewed, adjusted, and validated manually before being committed.

## Author

Mina Lazičić
