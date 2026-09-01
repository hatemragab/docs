# Authentication

The Authentication API handles user registration, login, email verification, password resets, and session management. All endpoints are prefixed with `/api/v1/auth`.

## Protected Endpoint Authentication

Protected endpoints require exactly one `Authorization: Bearer <token>` header.
Missing, malformed, empty, `null`, or `undefined` credentials return HTTP `401`.
Invalid or expired JWTs also return HTTP `401`. Authentication responses and
server logs never echo the supplied access token.

```json
{
  "statusCode": 401,
  "timestamp": "2026-07-30T12:00:00.000Z",
  "path": "/api/v1/channel/v2",
  "data": "t.Authentication required"
}
```

## Login

`POST https://api.weellu.com/api/v1/auth/login`

Authenticates a user with email and password. Returns a JWT access token on success. If the user's email is not yet verified, the server sends a new OTP and returns code `2100` instead of `2000`.

### Parameters

| Parameter | Type   | Required | Description                                                                         |
| --------- | ------ | -------- | ----------------------------------------------------------------------------------- |
| email     | string | Yes      | User's email address. Must be a valid email format. Trimmed automatically.          |
| password  | string | Yes      | User's password. Max length: 255.                                                   |
| platform  | string | Yes      | Client platform. Enum: `Android`, `Ios`, `Web`, `Linux`, `Mac`, `Windows`, `Other`. |
| lang      | string | Yes      | Language/locale code (e.g. `en`, `ar`).                                             |
| mapInfo   | string | Yes      | JSON-encoded string containing device location/map metadata.                        |
| deviceId  | string | Yes      | Unique device identifier. Max length: 255. Trimmed automatically.                   |
| pushKey   | string | No       | Firebase Cloud Messaging push token. Trimmed automatically.                         |

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "securePass123",
    "platform": "Android",
    "lang": "en",
    "mapInfo": "{\"lat\":25.276987,\"lng\":55.296249}",
    "deviceId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
  }'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/auth/login", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    email: "john@example.com",
    password: "securePass123",
    platform: "Android",
    lang: "en",
    mapInfo: '{"lat":25.276987,"lng":55.296249}',
    deviceId: "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  }),
});
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/auth/login'),
  headers: {'Content-Type': 'application/json'},
  body: jsonEncode({
    'email': 'john@example.com',
    'password': 'securePass123',
    'platform': 'Android',
    'lang': 'en',
    'mapInfo': '{"lat":25.276987,"lng":55.296249}',
    'deviceId': 'a1b2c3d4-e5f6-7890-abcd-ef1234567890',
  }),
);
```

> Example Response (200 - Verified User)

```json
{
  "code": 2000,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  },
  "msg": null
}
```

> Example Response (200 - Unverified User, OTP Sent)

```json
{
  "code": 2100,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "message": "Verification code has been send to your email"
  },
  "msg": null
}
```

> Example Error Response (400)

```json
{
  "statusCode": 400,
  "message": "Invalid login credentials"
}
```

<aside class="notice">
If the user's email is unverified, the response code will be <code>2100</code> (not <code>2000</code>) and a verification OTP is sent to the email. The client should redirect to the email verification screen.
</aside>

<aside class="warning">
Accounts that are banned or have a registration status other than <code>accepted</code> cannot log in and will receive a 400 error.
</aside>

---

## Refresh Access Token

`POST https://api.weellu.com/api/v1/auth/refresh`

Issues a new access token for an active Android or iOS device session. The supplied access token may be expired, but its signature and payload must still be valid. Web sessions must authenticate again.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
token | string | Conditional | Valid or expired signed access token. Required in the JSON body when the Authorization header is omitted.

### Headers

Header | Required | Description
------ | -------- | -----------
Authorization | Conditional | `Bearer <accessToken>`. Required when the body `token` field is omitted.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/refresh" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/auth/refresh", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${accessToken}`,
  },
});
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/auth/refresh'),
  headers: {'Authorization': 'Bearer $accessToken'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  },
  "msg": null
}
```

> Example Error Response (401)

```json
{
  "statusCode": 401,
  "message": "Invalid access token"
}
```

<aside class="notice">
Refresh verifies the original JWT signature while allowing token expiry, then confirms that the user and device session are still active. Invalid or forged tokens are rejected and never echoed in errors.
</aside>

<aside class="warning">
Web sessions do not support token refresh and must sign in again.
</aside>

---

## Register (Send OTP)

`POST https://api.weellu.com/api/v1/auth/send-otp-register`

Registers a new user account and sends an email verification OTP. Returns a JWT access token. If the email already exists but is unverified, a new OTP is sent instead. Supports an optional profile image upload via multipart form data.

### Parameters

| Parameter | Type   | Required | Description                                                                                                                               |
| --------- | ------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| email     | string | Yes      | User's email address. Must be valid email format. Trimmed automatically.                                                                  |
| fullName  | string | Yes      | User's display name. Max length: 50. Trimmed automatically.                                                                               |
| password  | string | Yes      | User's password. Min length: 6, max length: 255.                                                                                          |
| platform  | string | Yes      | Client platform. Enum: `Android`, `Ios`, `Web`, `Linux`, `Mac`, `Windows`, `Other`.                                                       |
| lang      | string | Yes      | Language/locale code (e.g. `en`, `ar`).                                                                                                   |
| mapInfo   | string | Yes      | JSON-encoded string containing device location/map metadata.                                                                              |
| deviceId  | string | Yes      | Unique device identifier. Max length: 255. Trimmed automatically.                                                                         |
| userName  | string | No       | Desired username. 3-20 characters, alphanumeric and underscores only (`[a-zA-Z0-9_]`). Validated only if provided. Trimmed automatically. |
| pushKey   | string | No       | Firebase Cloud Messaging push token. Trimmed automatically.                                                                               |
| image     | file   | No       | Profile image file upload (multipart/form-data).                                                                                          |

> Example Request (JSON)

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/send-otp-register" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "jane@example.com",
    "fullName": "Jane Smith",
    "password": "mySecure123",
    "platform": "Ios",
    "lang": "en",
    "mapInfo": "{\"lat\":40.712776,\"lng\":-74.005974}",
    "deviceId": "ios-device-uuid-12345"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/auth/send-otp-register",
  {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      email: "jane@example.com",
      fullName: "Jane Smith",
      password: "mySecure123",
      platform: "Ios",
      lang: "en",
      mapInfo: '{"lat":40.712776,"lng":-74.005974}',
      deviceId: "ios-device-uuid-12345",
    }),
  },
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/auth/send-otp-register'),
  headers: {'Content-Type': 'application/json'},
  body: jsonEncode({
    'email': 'jane@example.com',
    'fullName': 'Jane Smith',
    'password': 'mySecure123',
    'platform': 'Ios',
    'lang': 'en',
    'mapInfo': '{"lat":40.712776,"lng":-74.005974}',
    'deviceId': 'ios-device-uuid-12345',
  }),
);
```

> Example Request (Multipart with Image)

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/send-otp-register" \
  -F "email=jane@example.com" \
  -F "fullName=Jane Smith" \
  -F "password=mySecure123" \
  -F "platform=Ios" \
  -F "lang=en" \
  -F "mapInfo={\"lat\":40.712776,\"lng\":-74.005974}" \
  -F "deviceId=ios-device-uuid-12345" \
  -F "userName=janesmith" \
  -F "image=@/path/to/profile.jpg"
```

> Example Response (201)

```json
{
  "code": 2000,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "message": "Verification code has been send to your email"
  },
  "msg": null
}
```

> Example Error Response (400 - Email Exists)

```json
{
  "statusCode": 400,
  "message": "User already in data base and verified"
}
```

> Example Error Response (400 - Username Taken)

```json
{
  "statusCode": 400,
  "message": "userName janesmith already exists please choose another one"
}
```

<aside class="notice">
After registration, the user must verify their email using the <code>/auth/validate-email</code> endpoint before they can fully use the platform.
</aside>

<aside class="notice">
When uploading a profile image, use <code>multipart/form-data</code> content type instead of JSON. The image field name must be <code>image</code>.
</aside>

---

## Verify Email

`POST https://api.weellu.com/api/v1/auth/validate-email`

Verifies a user's email address using the OTP code sent during registration. The code expires after 10 minutes.

### Parameters

| Parameter | Type   | Required | Description                                                                               |
| --------- | ------ | -------- | ----------------------------------------------------------------------------------------- |
| email     | string | Yes      | User's email address. Must be valid email format. Max length: 200. Trimmed automatically. |
| code      | number | Yes      | The numeric OTP code received via email.                                                  |

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/validate-email" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "jane@example.com",
    "code": 482910
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/auth/validate-email",
  {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      email: "jane@example.com",
      code: 482910,
    }),
  },
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/auth/validate-email'),
  headers: {'Content-Type': 'application/json'},
  body: jsonEncode({
    'email': 'jane@example.com',
    'code': 482910,
  }),
);
```

> Example Response (200)

```json
{
  "code": 2000,
  "data": "Email has been verified successfully",
  "msg": null
}
```

> Example Error Response (400 - Already Verified)

```json
{
  "statusCode": 400,
  "message": "User already verified"
}
```

> Example Error Response (400 - Expired Code)

```json
{
  "statusCode": 400,
  "message": "Code has been expired"
}
```

> Example Error Response (400 - Invalid Code)

```json
{
  "statusCode": 400,
  "message": "Invalid code !"
}
```

<aside class="warning">
OTP codes expire after <strong>10 minutes</strong>. If the code has expired, the user must request a new one by calling the register or login endpoint again.
</aside>

---

## Check Username Availability

`GET https://api.weellu.com/api/v1/auth/check-user-name/:userName`

Checks whether a given username is already taken. Useful for real-time validation during registration.

### Parameters

| Parameter | Type   | Required | Description                                                                                                     |
| --------- | ------ | -------- | --------------------------------------------------------------------------------------------------------------- |
| userName  | string | Yes      | The username to check. URL path parameter. 3-20 characters, alphanumeric and underscores only (`[a-zA-Z0-9_]`). |

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/auth/check-user-name/janesmith"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/auth/check-user-name/janesmith",
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/auth/check-user-name/janesmith'),
);
```

> Example Response (200 - Available)

```json
{
  "code": 2000,
  "data": {
    "isExists": false
  },
  "msg": null
}
```

> Example Response (200 - Taken)

```json
{
  "code": 2000,
  "data": {
    "isExists": true
  },
  "msg": null
}
```

<aside class="success">
A username is available when <code>isExists</code> is <code>false</code>. The check is case-insensitive.
</aside>

---

## Request Password Reset (Send OTP)

`POST https://api.weellu.com/api/v1/auth/send-otp-reset-password`

Sends a password reset OTP to the specified email address. The user must have a registered account.

### Parameters

| Parameter | Type   | Required | Description                                                            |
| --------- | ------ | -------- | ---------------------------------------------------------------------- |
| email     | string | Yes      | The email address of the account to reset. Must be a registered email. |

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/send-otp-reset-password" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/auth/send-otp-reset-password",
  {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      email: "john@example.com",
    }),
  },
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/auth/send-otp-reset-password'),
  headers: {'Content-Type': 'application/json'},
  body: jsonEncode({
    'email': 'john@example.com',
  }),
);
```

> Example Response (201)

```json
{
  "code": 2000,
  "data": "Password reset code has been send to your email",
  "msg": null
}
```

> Example Error Response (400)

```json
{
  "statusCode": 400,
  "message": "Email is required"
}
```

<aside class="notice">
The reset OTP code expires after <strong>10 minutes</strong>. A new code can be requested at any time by calling this endpoint again.
</aside>

---

## Complete Password Reset

`POST https://api.weellu.com/api/v1/auth/verify-and-reset-password`

Verifies the password reset OTP and sets a new password. The code must have been requested via the `/auth/send-otp-reset-password` endpoint and must not be expired.

### Parameters

| Parameter   | Type   | Required | Description                                                                       |
| ----------- | ------ | -------- | --------------------------------------------------------------------------------- |
| email       | string | Yes      | The account email address. Must be valid email format. Max length: 200.           |
| code        | string | Yes      | The numeric OTP code received via email. Must be a numeric string. Max length: 6. |
| newPassword | string | Yes      | The new password to set.                                                          |

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/verify-and-reset-password" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "code": "482910",
    "newPassword": "myNewSecurePass456"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/auth/verify-and-reset-password",
  {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      email: "john@example.com",
      code: "482910",
      newPassword: "myNewSecurePass456",
    }),
  },
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/auth/verify-and-reset-password'),
  headers: {'Content-Type': 'application/json'},
  body: jsonEncode({
    'email': 'john@example.com',
    'code': '482910',
    'newPassword': 'myNewSecurePass456',
  }),
);
```

> Example Response (201)

```json
{
  "code": 2000,
  "data": "Password has been reset successfully",
  "msg": null
}
```

> Example Error Response (400 - Expired Code)

```json
{
  "statusCode": 400,
  "message": "Code has been expired"
}
```

> Example Error Response (400 - Invalid Code)

```json
{
  "statusCode": 400,
  "message": "Invalid code !"
}
```

<aside class="warning">
The OTP code expires after <strong>10 minutes</strong>. Each code can only be used once -- after a successful reset, the code is marked as expired.
</aside>

---

## Logout

`POST https://api.weellu.com/api/v1/auth/logout`

Logs out the current device or all devices. Requires authentication. When logging out from all devices, the user's password is required for security verification.

### Parameters

| Parameter     | Type    | Required    | Description                                                                            |
| ------------- | ------- | ----------- | -------------------------------------------------------------------------------------- |
| logoutFromAll | boolean | Yes         | If `true`, logs out from all devices. If `false`, logs out only the current device.    |
| password      | string  | Conditional | Required when `logoutFromAll` is `true`. The user's current password for verification. |

### Headers

| Header        | Required | Description                                  |
| ------------- | -------- | -------------------------------------------- |
| Authorization | Yes      | Bearer token. Format: `Bearer <accessToken>` |

> Example Request (Current Device)

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/logout" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -d '{
    "logoutFromAll": false
  }'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/auth/logout", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: "Bearer eyJhbGciOiJIUzI1NiIs...",
  },
  body: JSON.stringify({
    logoutFromAll: false,
  }),
});
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/auth/logout'),
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIs...',
  },
  body: jsonEncode({
    'logoutFromAll': false,
  }),
);
```

> Example Request (All Devices)

```shell
curl -X POST "https://api.weellu.com/api/v1/auth/logout" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -d '{
    "logoutFromAll": true,
    "password": "securePass123"
  }'
```

> Example Response (200 - Current Device)

```json
{
  "code": 2000,
  "data": "Device has been logout",
  "msg": null
}
```

> Example Response (200 - All Devices)

```json
{
  "code": 2000,
  "data": "Device has been logout from all devices",
  "msg": null
}
```

> Example Error Response (400 - Wrong Password)

```json
{
  "statusCode": 400,
  "message": "Invalid login credentials"
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
When <code>logoutFromAll</code> is <code>true</code>, the <code>password</code> field becomes mandatory. If the password does not match, the request is rejected with a 400 error. This prevents unauthorized mass logout.
</aside>

<aside class="notice">
Logging out from all devices deletes all device records for the user, which also invalidates all active JWT tokens associated with those devices.
</aside>
