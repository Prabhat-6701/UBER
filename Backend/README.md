# API Endpoints Documentation

---

## /users/register Endpoint Documentation

### Description

The `/users/register` endpoint registers a new user. It validates the request payload, hashes the provided password, creates a new user record, and returns an authentication token along with the user details.

### HTTP Method

**POST**

### URL

`/users/register`

### Request Body

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

- **fullname.firstname:** Required; must be at least 3 characters long.
- **fullname.lastname:** Optional; if provided, it must be at least 3 characters long.
- **email:** Required; must be a valid email and at least 3 characters long.
- **password:** Required; must be at least 6 characters long.

### Success Response

- **Status Code:** `201 Created`
- **Response Body:**

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

### Error Response

- **Status Code:** `400 Bad Request`
- **Response Body:**

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

### Additional Notes

- The endpoint uses `express-validator` to validate incoming data.
- The password is hashed using bcrypt (via `userModel.hashPassword`).
- A JSON Web Token (JWT) is generated for the newly created user.
- The service layer (`userService.createUser`) is responsible for creating and saving the user record.

---

## /users/login Endpoint Documentation

### Description

The `/users/login` endpoint logs in an existing user. It validates the request payload, verifies the user's credentials by comparing the provided password with the stored hashed password, and on success returns an authentication token along with the user details.

### HTTP Method

**POST**

### URL

`/users/login`

### Request Body

The request must be in JSON format with the following structure:

```json
{
  "email": "user@example.com",
  "password": "yourPassword123"
}
```

- **email:** Required; must be a valid email and at least 3 characters long.
- **password:** Required; must be at least 6 characters long.

### Success Response

- **Status Code:** `200 OK`
- **Response Body:**

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

### Error Response

- **Status Code:** `400 Bad Request` for validation errors.
- **Status Code:** `401 Unauthorized` if the credentials are invalid.
- **Response Body (Invalid Credentials):**

```json
{
  "message": "Invalid email or password" // Error message for invalid credentials.
}
```

### Additional Notes

- The endpoint uses `express-validator` to validate incoming data.
- The provided password is compared with the stored hashed password using the `user.comaparePassword` method.
- On a successful login, a JSON Web Token (JWT) is generated for the user.
- If the user is not found or the password does not match, the endpoint returns a 401 status code with an appropriate error message.

---

## /users/profile Endpoint Documentation

### Description

The `/users/profile` endpoint retrieves the authenticated user's profile information. This endpoint requires the user to be authenticated via a valid JWT.

### HTTP Method

**GET**

### URL

`/users/profile`

### Headers Required

```
Authorization: Bearer <token>
```

### Success Response

- **Status Code:** `200 OK`
- **Response Body:**

```json
{
  "fullname": {
    "firstname": "YourFirstName",
    "lastname": "YourLastName"
  },
  "email": "user@example.com"
  // additional user properties
}
```

### Error Response

- **Status Code:** `401 Unauthorized`
- **Response Body:**

```json
{
  "message": "Unauthorized"
}
```

### Additional Notes

- The endpoint uses an authentication middleware which verifies the JWT and appends the user to `req.user`.

---

## /users/logout Endpoint Documentation

### Description

The `/users/logout` endpoint logs out the current user. It clears the authentication cookie and adds the user's JWT to a blacklist (with a TTL of 24 hours) to prevent further use.

### HTTP Method

**GET**

### URL

`/users/logout`

### Headers Required

```
Authorization: Bearer <token>
```

or use the authentication cookie.

### Success Response

- **Status Code:** `200 OK`
- **Response Body:**

```json
{
  "message": "Logged out successfully"
}
```

### Error Response

- **Status Code:** `401 Unauthorized`
- **Response Body:**

```json
{
  "message": "Unauthorized"
}
```

### Additional Notes

- The endpoint uses the authentication middleware to verify the user.
- The current JWT token is cleared from the cookie and added to the blacklist so it cannot be used again.

---

## /captain/register Endpoint Documentation

### Description

The `/captains/register` endpoint registers a new captain. It validates the request payload and then creates a new captain record with both personal and vehicle information.

### HTTP Method

**POST**

### URL

`/captain/register`

### Request Body

The request must be in JSON format with the following structure:

```json
{
  "fullname": {
    "firstname": "YourFirstName", // Required; must be at least 3 characters long.
    "lastname": "YourLastName"      // Optional.
  },
  "email": "user@example.com",         // Required; valid email.
  "password": "yourPassword123"          // Required; at least 6 characters long.
  "vehicle": {
    "color": "Blue",                  // Required; at least 3 characters long.
    "plate": "XYZ123",                // Required; at least 3 characters long.
    "capacity": 4,                    // Required; integer value, minimum 1.
    "vehicleType": "car"              // Required; accepted values: "car", "motorcycle", "auto".
  }
}
```

- **fullname.firstname:** Required; must be at least 3 characters long.
- **fullname.lastname:** Optional.
- **email:** Required; must be a valid email address.
- **password:** Required; must be at least 6 characters long.
- **vehicle.color:** Required; must be at least 3 characters long.
- **vehicle.plate:** Required; must be at least 3 characters long.
- **vehicle.capacity:** Required; must be an integer of at least 1.
- **vehicle.vehicleType:** Required; must be one of `"car"`, `"motorcycle"`, or `"auto"`.

### Success Response

- **Status Code:** `201 Created`
- **Response Body:**

```json
{
  "captain": {
    "fullname": {
      "firstname": "YourFirstName",
      "lastname": "YourLastName"
    },
    "email": "user@example.com",
    "vehicle": {
      "color": "Blue",
      "plate": "XYZ123",
      "capacity": 4,
      "vehicleType": "car"
    }
    // additional captain properties if applicable
  }
}
```

### Error Response

- **Status Code:** `400 Bad Request`
- **Response Body:**

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

### Additional Notes

- The endpoint uses `express-validator` to validate incoming data.
- All fields are required; if any field is missing or does not meet validation rules, the endpoint will return a 400 error.
- The business logic for creating a captain is implemented in the `captainService.createCaptain` function.

/captains/login Endpoint Documentation
Description:
Logs in an existing captain by verifying provided credentials.

HTTP Method:
POST

URL:
/captains/login
Request Body Example:

{
    "email": "user@example.com",         // Required; valid email.
    "password": "yourPassword123"          // Required; at least 6 characters long.
}

Success Response:

Status Code: 200 OK
Response Body Example:
{
    "token": "generatedAuthToken",       // JWT for authentication.
    "captain": {
        "fullname": {
            "firstname": "YourFirstName",
            "lastname": "YourLastName"
        },
        "email": "user@example.com"
        // additional captain properties if applicable
    }
}

### Error Response

- **Status Code:** `400 Bad Request` for validation errors.
- **Status Code:** `401 Unauthorized` if the credentials are invalid.
- **Response Body (Invalid Credentials):**

```json
{
    "message": "Invalid email or password" // Error message for invalid credentials.
}
```

