# Errors

The Weellu API uses a consistent error response format across all endpoints. When an error occurs, the response body follows this structure:

> Error Response Format

```json
{
  "statusCode": 400,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/endpoint",
  "data": "t.errorKey"
}
```

### Error Response Fields

Field | Type | Description
----- | ---- | -----------
statusCode | number | HTTP status code
timestamp | string | ISO 8601 timestamp of when the error occurred
path | string | The request URL path that caused the error
data | string | Error message prefixed with `t.` indicating an i18n translation key. Clients should strip the `t.` prefix and use the remaining key for localized error display

<aside class="notice">
The <code>data</code> field contains an i18n translation key prefixed with <code>t.</code>. For example, <code>t.userNotFound</code> means the client should look up the <code>userNotFound</code> key in its localization files to display a user-friendly message.
</aside>

## Validation Errors

When request body validation fails (via `class-validator`), the API returns the first validation error message as the `data` field.

> Validation Error Example

```json
{
  "statusCode": 400,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/auth/register",
  "data": "t.email must be an email"
}
```

## Error Codes

The Weellu API uses the following standard HTTP error codes:

Error Code | Meaning
---------- | -------
400 | **Bad Request** -- The request body is malformed, a required field is missing, or validation failed. Check the `data` field for the specific validation error.
401 | **Unauthorized** -- No valid Bearer token was provided, or the token has expired. Re-authenticate and retry with a fresh JWT.
403 | **Forbidden** -- The authenticated user does not have permission to perform this action. Common when accessing SuperAdmin endpoints without the required role.
404 | **Not Found** -- The requested resource does not exist. Verify the ID or path is correct.
405 | **Method Not Allowed** -- The HTTP method is not supported for this endpoint. Check the documentation for the correct method (GET, POST, PUT, PATCH, DELETE).
406 | **Not Acceptable** -- The request format is not supported. The API only returns JSON responses.
409 | **Conflict** -- The request conflicts with the current state of the resource. Common when trying to create a duplicate entry or when an operation cannot be performed due to the current state.
410 | **Gone** -- The requested resource has been permanently deleted and is no longer available.
422 | **Unprocessable Entity** -- The request was well-formed but contains semantic errors. For example, trying to ban a user who is already banned, or completing a multipart upload with missing parts.
429 | **Too Many Requests** -- Rate limit exceeded. The API uses throttling to protect against abuse. Wait before retrying.
500 | **Internal Server Error** -- An unexpected error occurred on the server. If this persists, report it via the report system endpoint.
503 | **Service Unavailable** -- The server is temporarily offline for maintenance or is overloaded. Retry after a short delay.

## Common Error Scenarios

> 401 Unauthorized - Invalid or expired token

```json
{
  "statusCode": 401,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/activity-notifications/feed",
  "data": "t.Unauthorized"
}
```

> 403 Forbidden - Insufficient permissions

```json
{
  "statusCode": 403,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/admin-panel/dashboard",
  "data": "t.Forbidden"
}
```

> 404 Not Found - Resource does not exist

```json
{
  "statusCode": 404,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/room-filters/660e5f6a7b8c9d0e1f2a3b4c",
  "data": "t.notFound"
}
```

> 409 Conflict - Duplicate or state conflict

```json
{
  "statusCode": 409,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/ban",
  "data": "t.userAlreadyBanned"
}
```

> 422 Unprocessable Entity - Semantic error

```json
{
  "statusCode": 422,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/multipart-upload/complete",
  "data": "t.missingParts"
}
```

> 429 Too Many Requests - Rate limited

```json
{
  "statusCode": 429,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/auth/login",
  "data": "t.ThrottlerException: Too Many Requests"
}
```

> 500 Internal Server Error

```json
{
  "statusCode": 500,
  "timestamp": "2026-03-18T10:30:00.000Z",
  "path": "/api/v1/market/home",
  "data": "t.Error: Internal server error"
}
```

## Error Handling Best Practices

1. **Always check the `statusCode` field** to determine the category of error.
2. **Parse the `data` field** by stripping the `t.` prefix to get the i18n translation key.
3. **Implement retry logic** for 429 and 503 errors with exponential backoff.
4. **Re-authenticate** when receiving 401 errors by refreshing the JWT token.
5. **Log 500 errors** and report them via `POST /api/v1/report-system` for investigation.
