# /users/register Endpoint Documentation

## Description

The `/users/register` endpoint registers a new user. It validates the request payload, hashes the provided password, creates a new user record, and returns an authentication token along with the user details.

## HTTP Method

**POST**

## URL

`/users/register`

## Request Body

The request must be in JSON format with the following structure:

```json
{
  "fullname": {
    "firstname": "YourFirstName",
    "lastname": "YourLastName"  // Optional: if provided, must be at least 3 characters long.
  },
  "email": "user@example.com",
  "password": "yourPassword123"
}
```

fullname.firstname: Required; must be at least 3 characters long.  
fullname.lastname: Optional; if provided, it must be at least 3 characters long.  
email: Required; must be a valid email and at least 3 characters long.  
password: Required; must be at least 6 characters long.

## Success Response

```json
{
  "token": "generatedAuthToken",
  "user": {
    "fullname": {
      "firstname": "YourFirstName",
      "lastname": "YourLastName"
    },
    "email": "user@example.com"
    // additional user properties
  }
}
```

## Error Response

**Status Code:** 400 Bad Request

**Response Body:**

```json
{
  "errors": [
    {
      "msg": "Validation error message",
      "param": "field",
      "location": "body"
    }
  ]
}
```

## Additional Notes

- The endpoint uses express-validator to validate incoming data.
- The password is hashed using bcrypt (via userModel.hashPassword).
- A JSON Web Token (JWT) is generated for the newly created user.
- The service layer (userService.createUser) is responsible for creating and saving the user record.

Make sure to place this file in your `/d:/UBER/Backend` folder.