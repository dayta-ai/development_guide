# Cyclops Response style guide

## Table of contents

- [Cyclops Response style guide](#cyclops-response-style-guide)
  - [Table of contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Guidelines](#guidelines)
    - [Error response](#error-response)
    - [Success Reponse](#success-reponse)

## Introduction

Response styles are returned responses from our cyclops backend microservices. The purpose of wrapping
responses, even on errors, are to differentiate network/infrastructural server errors from the actual errors
that were caught by our services.

This paradigm of design refers to Clean Architecture by uncle bob.

## Guidelines

### Error response

Error response is defined by the type below:
```go

type ErrorResponse struct {
	Status  int    `json:"status"`  // HTTP Status
	Code    int    `json:"code"`    // Internal debugging status
	Message string `json:"message"` // Error message
}
```

`Status`: status field represents the true HTTP status code returned by the server.
- Example: Resource not found - Status: 404

`Code`: code field represents the code required for debugging purposes. It follows the following convention:
- ABC where numerical value in the position each character represents the following values.
  - Numerical value in position A stands for the API route number.
  - Numerical value in position B stands for the HTTP verb used in that API in the order of (POST/GET/PATCH/PUT/DELETE)
    - POST: 0
    - GET: 1
    - PATCH: 2
    - PUT: 3
    - DELETE: 4
  - Numerical value in position C stands for the debug code.
- Example:
```go
/* User routes 0XX (route number 0) */
// route GET /user/:uid - user already exists error
return ErrorResponse {
    Status: http.StatusNotFound,
    Code: 000, // Route number 0, verb POST(0), Code 0
    Message: fmt.Sprintf("User (%v) already exists", id)
}
// route GET /user/:uid - not found error
return ErrorResponse {
    Status: http.StatusNotFound,
    Code: 010, // Route number 0, verb GET(1), Code 0
    Message: fmt.Sprintf("User (%v) not found", id)
}
```
`Message`: message field represents the specific error message associated with the error.
- Example: `User (abcd-efghijklm-nopqrs) not found`


### Success Reponse

Success response is defined by the type below:
```go

type SuccessResponse struct {
	Status int         `json:"status"` // HTTP Status
	Code   int         `json:"code"`   // Internal debugging status
	Data   interface{} `json:"data"`   // Data payload
}
```

`Status`: status field represents the true HTTP status code returned by the server, in most cases 200
for successful requests. For other responses that require specific behaviors, for example redirect,
the response will not be wrapped inside the response struct but will use the http response directly.
- Example: Response OK - 200

`Code`: code field represents the code required for debugging purposes. It follows the same convention as
the error response above.

`Data`: data field represents the data that will be successfully returned by the API. If the response payload
shall contain nested data, it will provide upto maximum 1 level of nesting.
- Example:
```go
type Workspace struct {
    Name string
    NestedAttrib *NestedAttrib
}
type User struct {
    Name string
    Workspace *Workspace
}

return SuccessResponse {
    Status: http.StatusOK,
    Code: 0,
    Data: User{ // returned value struct
        Name: "EXAMPLE_USER",
        Workspace: { // 1st level of nesting
            Name: "EXAMPLE_WORKSPACE",
            NestedAttrib: nil, // 2nd level of nesting, nil
        }
    }
}
```