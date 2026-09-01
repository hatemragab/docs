# API Tokens

The API Tokens endpoints allow authenticated users to request, view, and cancel personal API tokens. API tokens are used for programmatic access to the Weellu platform (e.g., integrations, bots). All endpoints require authentication and are prefixed with `/api/v1/user-api-token`.

Token requests go through an approval workflow: a newly created token starts in `pending` status and must be approved by an administrator before it becomes active.

### Token Statuses

Status | Description
------ | -----------
`pending` | Token has been requested and is awaiting admin approval.
`approved` | Token has been approved and is active for use.
`rejected` | Token request has been rejected by an admin.
`cancelled` | Token has been cancelled by the user.

---

## Request New API Token

`POST https://api.weellu.com/api/v1/user-api-token`

Requests a new API token for the authenticated user. Each user can only have one token at a time. The token is created with `pending` status and must be approved by an administrator.

### Headers

Header | Required | Description
------ | -------- | -----------
Authorization | Yes | Bearer token. Format: `Bearer <accessToken>`

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
requestData | string | No | Optional free-text field describing the intended use of the token (e.g., integration name, purpose).

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/user-api-token" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -d '{
    "requestData": "Bot integration for automated customer support"
  }'
```

```javascript
const response = await fetch('https://api.weellu.com/api/v1/user-api-token', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIs...'
  },
  body: JSON.stringify({
    requestData: 'Bot integration for automated customer support'
  })
});
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/user-api-token'),
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIs...',
  },
  body: jsonEncode({
    'requestData': 'Bot integration for automated customer support',
  }),
);
```

> Example Response (201)

```json
{
  "code": 2000,
  "data": {
    "_id": "660a1b2c3d4e5f6789012345",
    "userId": "65f9a8b7c6d5e4f3a2b10987",
    "token": "660a1b2c3d4e5f6789abcdef",
    "requestData": "Bot integration for automated customer support",
    "status": "pending",
    "createdAt": "2026-03-18T14:30:00.000Z"
  },
  "msg": null
}
```

> Example Error Response (400 - Token Already Exists)

```json
{
  "statusCode": 400,
  "message": "User already has a token"
}
```

> Example Error Response (401 - Unauthorized)

```json
{
  "statusCode": 401,
  "message": "Unauthorized"
}
```

<aside class="warning">
Each user can only have <strong>one</strong> API token at a time. If a token already exists (in any status), the request will be rejected with a 400 error. Cancel the existing token first if you need a new one.
</aside>

<aside class="notice">
The <code>token</code> value in the response is the actual API token string. Store it securely -- it will be needed for programmatic API access once approved.
</aside>

---

## Get My API Token Status

`GET https://api.weellu.com/api/v1/user-api-token`

Retrieves the current user's API token and its status. Returns `null` if no token exists.

### Headers

Header | Required | Description
------ | -------- | -----------
Authorization | Yes | Bearer token. Format: `Bearer <accessToken>`

### Parameters

None.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/user-api-token" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

```javascript
const response = await fetch('https://api.weellu.com/api/v1/user-api-token', {
  headers: {
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIs...'
  }
});
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/user-api-token'),
  headers: {
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIs...',
  },
);
```

> Example Response (200 - Token Exists)

```json
{
  "code": 2000,
  "data": {
    "_id": "660a1b2c3d4e5f6789012345",
    "userId": "65f9a8b7c6d5e4f3a2b10987",
    "token": "660a1b2c3d4e5f6789abcdef",
    "requestData": "Bot integration for automated customer support",
    "status": "approved",
    "createdAt": "2026-03-18T14:30:00.000Z"
  },
  "msg": null
}
```

> Example Response (200 - No Token)

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

> Example Error Response (401 - Unauthorized)

```json
{
  "statusCode": 401,
  "message": "Unauthorized"
}
```

<aside class="notice">
Use this endpoint to check the approval status of a pending token request. The <code>status</code> field indicates whether the token is <code>pending</code>, <code>approved</code>, <code>rejected</code>, or <code>cancelled</code>.
</aside>

---

## Cancel My API Token

`DELETE https://api.weellu.com/api/v1/user-api-token`

Permanently deletes the current user's API token. This works regardless of the token's current status (pending, approved, rejected). After cancellation, the user can request a new token.

### Headers

Header | Required | Description
------ | -------- | -----------
Authorization | Yes | Bearer token. Format: `Bearer <accessToken>`

### Parameters

None.

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/user-api-token" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

```javascript
const response = await fetch('https://api.weellu.com/api/v1/user-api-token', {
  method: 'DELETE',
  headers: {
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIs...'
  }
});
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/user-api-token'),
  headers: {
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIs...',
  },
);
```

> Example Response (200 - Token Deleted)

```json
{
  "code": 2000,
  "data": {
    "_id": "660a1b2c3d4e5f6789012345",
    "userId": "65f9a8b7c6d5e4f3a2b10987",
    "token": "660a1b2c3d4e5f6789abcdef",
    "requestData": "Bot integration for automated customer support",
    "status": "pending",
    "createdAt": "2026-03-18T14:30:00.000Z"
  },
  "msg": null
}
```

> Example Response (200 - No Token to Delete)

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

> Example Error Response (401 - Unauthorized)

```json
{
  "statusCode": 401,
  "message": "Unauthorized"
}
```

<aside class="warning">
This action is <strong>irreversible</strong>. Once cancelled, any integrations using the token will immediately lose access. The deleted token object is returned in the response for reference.
</aside>

<aside class="notice">
After cancelling a token, you can immediately request a new one via the <code>POST /user-api-token</code> endpoint. The new token will start in <code>pending</code> status.
</aside>
