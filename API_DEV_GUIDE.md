# API Development Guide

## API Designs

`POST /users`
`GET /workspaces/:workspace_id`
`GET /users/:uid/workspace_role/:wr_id`

1. API routes always start with the domain with an s, e.g. `/users`, `/workspaces`. To target a specific object, put the ID of the object after the domain (/workspaces/:workspace_id).
2. Using HTTP methods appropriately. `GET` for retrieving, `POST` for creating, `PUT` for object replacement, `PATCH` for updating specific attribute of the target object, and `DELETE` for deleting. We should avoid using `PUT` as there should be no scenarios we need an object replacement.
3. Response JSON objects will only be one layer deep in terms of nested objects. To query for deeper level, call the corresponding API to get the target objects.
<!-- 4. Response will be wrapped with a response wrapper, with fields `status`, `code`, `message` and `data`. Status is the HTTP status code returned for the API call, code refers to the type of error (for detail please refer to Error Code Dcoumentation), message represents the human-readable error message, and data contains the content of a successfuly API call. -->