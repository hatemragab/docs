# Admin Panel

<aside class="warning">
These endpoints require SuperAdmin authentication. All requests must include a valid JWT token belonging to a user with the SuperAdmin role. Unauthorized access will return a 403 Forbidden error.
</aside>

## Admin Login

`POST https://api.weellu.com/api/v1/admin-panel/login`

Authenticates an admin user and validates their SuperAdmin privileges. The Bearer token used must already belong to a SuperAdmin account.

### Parameters

No body parameters required. Authentication is performed via the Bearer token.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/admin-panel/login" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/login",
  {
    method: "POST",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/admin-panel/login',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Login successful",
    "role": "superAdmin"
  }
}
```

## Get Dashboard

`GET https://api.weellu.com/api/v1/admin-panel/dashboard`

Returns platform-wide dashboard statistics including user counts, message counts, and system health metrics.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/dashboard" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/dashboard",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/dashboard',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "totalUsers": 15420,
    "activeUsers": 8730,
    "totalRooms": 45200,
    "totalMessages": 1250000,
    "onlineUsers": 342,
    "registeredToday": 28
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
totalUsers | number | Total registered users on the platform
activeUsers | number | Users who have been active recently
totalRooms | number | Total chat rooms created
totalMessages | number | Total messages sent across the platform
onlineUsers | number | Currently connected users
registeredToday | number | Users who registered today

## Get Message Count

`GET https://api.weellu.com/api/v1/admin-panel/message-count`

Returns message count statistics filtered by a time period.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
filter | string | No | Time filter. One of the `DashMessagesCountEnum` values (e.g., `today`, `week`, `month`, `year`)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/message-count?filter=week" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/message-count?filter=week",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/message-count',
  parameters: {'filter': 'week'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "messages": 42500,
    "textMessages": 30000,
    "taskMessages": 1200,
    "imageMessages": 5000,
    "videoMessages": 2000,
    "voiceMessages": 1500,
    "fileMessages": 800,
    "infoMessages": 1000,
    "locationMessages": 100,
    "allDeletedMessages": 900,
    "series": [
      { "date": "2026-06-09", "text": 4200, "task": 150, "image": 700, "video": 300, "voice": 200, "file": 110, "info": 140, "location": 12 },
      { "date": "2026-06-10", "text": 3900, "task": 120, "image": 650, "video": 280, "voice": 190, "file": 95, "info": 130, "location": 9 }
    ]
  }
}
```

The top-level fields are the per-type **totals** over the selected period. `series` is a per-bucket time series for charting: one entry per bucket from the period start to now, with **zero-filled gaps** so the array is dense. Buckets are **daily** (`date` = `YYYY-MM-DD`) for every filter except `year`, which uses **monthly** buckets (`date` = `YYYY-MM`).

## Get App Config

`GET https://api.weellu.com/api/v1/admin-panel/config`

Returns the current application configuration.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/config" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/config",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/config',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "configVersion": 12,
    "clintVersion": "2.5.0",
    "backendVersion": "3.1.0",
    "enableAds": false,
    "approveMessage": "Your account has been approved!",
    "pendingMessage": "Your account is pending review.",
    "userIcon": "https://weellu-bucket.s3.amazonaws.com/defaults/user.png",
    "feedbackEmail": "support@weellu.com",
    "shareAppMessage": "Check out Weellu - the best communication platform!",
    "allowWebLogin": true,
    "allowMobileLogin": true,
    "allowCreateGroup": true,
    "allowCreateBroadcast": true,
    "allowDesktopLogin": true,
    "privacyUrl": "https://weellu.com/privacy",
    "googlePayUrl": "https://play.google.com/store/apps/details?id=com.weellu",
    "appleStoreUrl": "https://apps.apple.com/app/weellu/id123456789",
    "maxExpireEmailTime": 300,
    "userRegisterStatus": "approved",
    "callTimeout": 30000,
    "maxGroupMembers": 512,
    "maxBroadcastMembers": 1024,
    "maxChatMediaSize": 104857600,
    "allowCall": true,
    "groupIcon": "https://weellu-bucket.s3.amazonaws.com/defaults/group.png",
    "supportIcon": "https://weellu-bucket.s3.amazonaws.com/defaults/support.png",
    "broadcastIcon": "https://weellu-bucket.s3.amazonaws.com/defaults/broadcast.png"
  }
}
```

## Update App Config

`PATCH https://api.weellu.com/api/v1/admin-panel/config`

Updates the application configuration. All fields are optional; only provided fields are updated.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
configVersion | number | No | Configuration version number
clintVersion | string | No | Minimum required client version
backendVersion | string | No | Current backend version
enableAds | boolean | No | Whether advertisements are enabled
approveMessage | string | No | Message shown when user account is approved
pendingMessage | string | No | Message shown when user account is pending
userIcon | string | No | Default user avatar URL
feedbackEmail | string | No | Support/feedback email address
shareAppMessage | string | No | Message used when sharing the app
allowWebLogin | boolean | No | Whether web login is allowed
allowMobileLogin | boolean | No | Whether mobile login is allowed
allowCreateGroup | boolean | No | Whether users can create groups
allowCreateBroadcast | boolean | No | Whether users can create broadcasts
allowDesktopLogin | boolean | No | Whether desktop login is allowed
privacyUrl | string | No | Privacy policy URL
googlePayUrl | string | No | Google Play Store URL
appleStoreUrl | string | No | Apple App Store URL
maxExpireEmailTime | number | No | Email verification code expiry time in seconds
userRegisterStatus | string | No | Default registration status. One of: `approved`, `pending`, `blocked`
callTimeout | number | No | Call ring timeout in milliseconds
maxGroupMembers | number | No | Maximum members allowed in a group
maxBroadcastMembers | number | No | Maximum members allowed in a broadcast
maxChatMediaSize | number | No | Maximum media file size in bytes
allowCall | boolean | No | Whether voice/video calls are enabled
groupIcon | string | No | Default group avatar URL
supportIcon | string | No | Default support avatar URL
broadcastIcon | string | No | Default broadcast avatar URL

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/admin-panel/config" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "enableAds": true,
    "maxGroupMembers": 1024,
    "allowCall": true
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/config",
  {
    method: "PATCH",
    headers: {
      Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      enableAds: true,
      maxGroupMembers: 1024,
      allowCall: true,
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/api/v1/admin-panel/config',
  body: {
    'enableAds': true,
    'maxGroupMembers': 1024,
    'allowCall': true,
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "configVersion": 13,
    "enableAds": true,
    "maxGroupMembers": 1024,
    "allowCall": true
  }
}
```

## Get Users

`GET https://api.weellu.com/api/v1/admin-panel/users`

Returns a paginated list of all platform users with filtering options.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number
limit | number | No | Users per page
search | string | No | Search by name or email

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/users?page=1&limit=20" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/users?page=1&limit=20",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/users',
  parameters: {'page': 1, 'limit': 20},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "docs": [
      {
        "_id": "659f1a2b3c4d5e6f7a8b9c0d",
        "fullName": "John Doe",
        "email": "john@example.com",
        "registerStatus": "approved",
        "createdAt": "2026-01-15T08:00:00.000Z"
      }
    ],
    "totalDocs": 15420,
    "limit": 20,
    "page": 1,
    "totalPages": 771
  }
}
```

## Get User Info

`GET https://api.weellu.com/api/v1/admin-panel/user/info/:id`

Returns detailed information about a specific user.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The user ID (MongoDB ObjectId)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/user/info/659f1a2b3c4d5e6f7a8b9c0d" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/user/info/659f1a2b3c4d5e6f7a8b9c0d",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/user/info/659f1a2b3c4d5e6f7a8b9c0d',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "659f1a2b3c4d5e6f7a8b9c0d",
    "fullName": "John Doe",
    "email": "john@example.com",
    "registerStatus": "approved",
    "bio": "Flutter developer",
    "userImage": "https://weellu-bucket.s3.amazonaws.com/users/john.jpg",
    "lastSeenAt": "2026-03-18T10:00:00.000Z",
    "createdAt": "2026-01-15T08:00:00.000Z",
    "devices": [
      {
        "platform": "android",
        "lastActive": "2026-03-18T10:00:00.000Z"
      }
    ]
  }
}
```

## Update User Info

`PATCH https://api.weellu.com/api/v1/admin-panel/user/info/:id`

Updates information for a specific user. Accepts any user fields as body parameters.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The user ID (MongoDB ObjectId)

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
(any user field) | any | No | Any valid user field to update (e.g., `fullName`, `registerStatus`, `bio`)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/admin-panel/user/info/659f1a2b3c4d5e6f7a8b9c0d" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "registerStatus": "blocked"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/user/info/659f1a2b3c4d5e6f7a8b9c0d",
  {
    method: "PATCH",
    headers: {
      Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      registerStatus: "blocked",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/api/v1/admin-panel/user/info/659f1a2b3c4d5e6f7a8b9c0d',
  body: {
    'registerStatus': 'blocked',
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "659f1a2b3c4d5e6f7a8b9c0d",
    "fullName": "John Doe",
    "registerStatus": "blocked"
  }
}
```

## Set New Version

`PATCH https://api.weellu.com/api/v1/admin-panel/versions`

Creates or updates a platform version entry. Used to manage app version requirements and update notifications.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
semVer | string | Yes | Semantic version string (e.g., `"2.5.0"`)
buildNumber | number | Yes | Build number (integer)
notify | boolean | Yes | Whether to send a push notification about this version
notes | string | No | Release notes
title | string | No | Version title
description | string | No | Version description
platform | string | Yes | Target platform. One of: `android`, `ios`, `web`, `desktop`
critical | boolean | Yes | Whether this is a critical/mandatory update
isForce | boolean | No | Whether to force users to update immediately

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/admin-panel/versions" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "semVer": "2.5.0",
    "buildNumber": 125,
    "notify": true,
    "title": "New Features Release",
    "description": "Added marketplace and improved performance",
    "notes": "Bug fixes and performance improvements",
    "platform": "android",
    "critical": false,
    "isForce": false
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/versions",
  {
    method: "PATCH",
    headers: {
      Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      semVer: "2.5.0",
      buildNumber: 125,
      notify: true,
      title: "New Features Release",
      description: "Added marketplace and improved performance",
      notes: "Bug fixes and performance improvements",
      platform: "android",
      critical: false,
      isForce: false,
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/api/v1/admin-panel/versions',
  body: {
    'semVer': '2.5.0',
    'buildNumber': 125,
    'notify': true,
    'title': 'New Features Release',
    'platform': 'android',
    'critical': false,
    'isForce': false,
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "66238c9d0e1f2a3b4c5d6e7f",
    "semVer": "2.5.0",
    "buildNumber": 125,
    "platform": "android",
    "critical": false,
    "isForce": false,
    "title": "New Features Release",
    "description": "Added marketplace and improved performance",
    "notes": "Bug fixes and performance improvements",
    "createdAt": "2026-03-18T12:00:00.000Z"
  }
}
```

## Get Version Dashboard

`GET https://api.weellu.com/api/v1/admin-panel/versions/:platform`

Returns version history for a specific platform.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
platform | string | Yes | The platform to get versions for. One of: `android`, `ios`, `web`, `desktop`

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/versions/android" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/versions/android",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/versions/android',
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "66238c9d0e1f2a3b4c5d6e7f",
      "semVer": "2.5.0",
      "buildNumber": 125,
      "platform": "android",
      "critical": false,
      "isForce": false,
      "title": "New Features Release",
      "createdAt": "2026-03-18T12:00:00.000Z"
    },
    {
      "_id": "66228c9d0e1f2a3b4c5d6e7e",
      "semVer": "2.4.2",
      "buildNumber": 122,
      "platform": "android",
      "critical": true,
      "isForce": true,
      "title": "Security Patch",
      "createdAt": "2026-03-10T09:00:00.000Z"
    }
  ]
}
```

## Delete Version

`DELETE https://api.weellu.com/api/v1/admin-panel/versions/:id`

Deletes a version entry.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The version entry ID (MongoDB ObjectId)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/admin-panel/versions/66238c9d0e1f2a3b4c5d6e7f" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/versions/66238c9d0e1f2a3b4c5d6e7f",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/admin-panel/versions/66238c9d0e1f2a3b4c5d6e7f',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Version deleted successfully"
  }
}
```

## Create Admin Notification

`POST https://api.weellu.com/api/v1/admin-panel/notifications`

Sends a push notification to all users or a targeted group. Accepts an optional image via multipart form data.

### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
title | string | Yes | Notification title
content | string | Yes | Notification body text
file | file | No | Optional image to include with the notification

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/admin-panel/notifications" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN" \
  -F "title=System Maintenance" \
  -F "content=We will perform scheduled maintenance on March 20th from 2AM-4AM UTC." \
  -F "file=@notification_banner.png"
```

```javascript
const formData = new FormData();
formData.append("title", "System Maintenance");
formData.append("content", "We will perform scheduled maintenance on March 20th from 2AM-4AM UTC.");
formData.append("file", imageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/notifications",
  {
    method: "POST",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/admin-panel/notifications'),
);
request.headers['Authorization'] = 'Bearer SUPER_ADMIN_JWT_TOKEN';
request.fields['title'] = 'System Maintenance';
request.fields['content'] = 'We will perform scheduled maintenance...';
request.files.add(await http.MultipartFile.fromPath('file', 'banner.png'));
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "66249d0e1f2a3b4c5d6e7f8a",
    "title": "System Maintenance",
    "content": "We will perform scheduled maintenance on March 20th from 2AM-4AM UTC.",
    "imageUrl": "https://weellu-bucket.s3.amazonaws.com/notifications/banner.png",
    "createdAt": "2026-03-18T12:30:00.000Z"
  }
}
```

## Get Admin Notifications

`GET https://api.weellu.com/api/v1/admin-panel/notifications`

Returns a list of all admin-sent notifications.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/notifications" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/notifications",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/notifications',
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "66249d0e1f2a3b4c5d6e7f8a",
      "title": "System Maintenance",
      "content": "We will perform scheduled maintenance on March 20th from 2AM-4AM UTC.",
      "imageUrl": "https://weellu-bucket.s3.amazonaws.com/notifications/banner.png",
      "createdAt": "2026-03-18T12:30:00.000Z"
    }
  ]
}
```

## Create Product Category

`POST https://api.weellu.com/api/v1/admin-panel/category/create`

Creates a new product category for the marketplace.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
name | string | Yes | Category display name
icon | string | No | Icon identifier or URL
iconColor | string | No | Hex color code for the icon
parentId | string | No | Parent category ID for creating subcategories (MongoDB ObjectId)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/admin-panel/category/create" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Electronics",
    "icon": "devices",
    "iconColor": "#2196F3"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/category/create",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      name: "Electronics",
      icon: "devices",
      iconColor: "#2196F3",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/admin-panel/category/create',
  body: {
    'name': 'Electronics',
    'icon': 'devices',
    'iconColor': '#2196F3',
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "6625ae1f2a3b4c5d6e7f8a9b",
    "name": "Electronics",
    "icon": "devices",
    "iconColor": "#2196F3",
    "parentId": null,
    "createdAt": "2026-03-18T13:00:00.000Z"
  }
}
```

## Update Product Category

`PATCH https://api.weellu.com/api/v1/admin-panel/category/:id/update`

Updates an existing product category.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The category ID (MongoDB ObjectId)

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
name | string | No | Updated category name
icon | string | No | Updated icon identifier
iconColor | string | No | Updated hex color code

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/admin-panel/category/6625ae1f2a3b4c5d6e7f8a9b/update" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Electronics & Gadgets",
    "iconColor": "#1565C0"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/category/6625ae1f2a3b4c5d6e7f8a9b/update",
  {
    method: "PATCH",
    headers: {
      Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      name: "Electronics & Gadgets",
      iconColor: "#1565C0",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/api/v1/admin-panel/category/6625ae1f2a3b4c5d6e7f8a9b/update',
  body: {
    'name': 'Electronics & Gadgets',
    'iconColor': '#1565C0',
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "6625ae1f2a3b4c5d6e7f8a9b",
    "name": "Electronics & Gadgets",
    "icon": "devices",
    "iconColor": "#1565C0"
  }
}
```

## Delete Product Category

`DELETE https://api.weellu.com/api/v1/admin-panel/category/:id/delete`

Deletes a product category. Categories with child subcategories should be handled with care.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The category ID (MongoDB ObjectId)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/admin-panel/category/6625ae1f2a3b4c5d6e7f8a9b/delete" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/category/6625ae1f2a3b4c5d6e7f8a9b/delete",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/admin-panel/category/6625ae1f2a3b4c5d6e7f8a9b/delete',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Category deleted successfully"
  }
}
```

## Get All Categories

`GET https://api.weellu.com/api/v1/admin-panel/category/all`

Returns all product categories with optional filtering.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number
limit | number | No | Categories per page

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/category/all" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/category/all",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/category/all',
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "6625ae1f2a3b4c5d6e7f8a9b",
      "name": "Electronics",
      "icon": "devices",
      "iconColor": "#2196F3",
      "parentId": null
    },
    {
      "_id": "6625bf2a3b4c5d6e7f8a9b0c",
      "name": "Smartphones",
      "icon": "smartphone",
      "iconColor": "#4CAF50",
      "parentId": "6625ae1f2a3b4c5d6e7f8a9b"
    }
  ]
}
```

## Get User Reports

`GET https://api.weellu.com/api/v1/admin-panel/users/reports`

Returns user-submitted reports with optional filtering.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number
limit | number | No | Reports per page
type | string | No | Filter by report type

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/users/reports?page=1&limit=20" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/users/reports?page=1&limit=20",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/users/reports',
  parameters: {'page': 1, 'limit': 20},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "docs": [
      {
        "_id": "6626c03b4c5d6e7f8a9b0c1d",
        "content": "App crashes on video call",
        "stack": "Error: MediaStream initialization failed",
        "type": "crash",
        "platform": "android",
        "uId": "659f1a2b3c4d5e6f7a8b9c0d",
        "createdAt": "2026-03-18T10:30:00.000Z"
      }
    ],
    "totalDocs": 42,
    "limit": 20,
    "page": 1,
    "totalPages": 3
  }
}
```

## Delete Report

`DELETE https://api.weellu.com/api/v1/admin-panel/users/reports/:id`

Deletes a specific user report.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The report ID (MongoDB ObjectId)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/admin-panel/users/reports/6626c03b4c5d6e7f8a9b0c1d" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/users/reports/6626c03b4c5d6e7f8a9b0c1d",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/admin-panel/users/reports/6626c03b4c5d6e7f8a9b0c1d',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Report deleted successfully"
  }
}
```

## Get API Token Requests

`GET https://api.weellu.com/api/v1/admin-panel/users/api`

Returns a list of user API token requests with filtering options.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number
limit | number | No | Requests per page

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/users/api?page=1&limit=20" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/users/api?page=1&limit=20",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/users/api',
  parameters: {'page': 1, 'limit': 20},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "docs": [
      {
        "_id": "6627d14c5d6e7f8a9b0c1d2e",
        "userId": "659f1a2b3c4d5e6f7a8b9c0d",
        "status": "pending",
        "createdAt": "2026-03-18T10:00:00.000Z"
      }
    ],
    "totalDocs": 5,
    "limit": 20,
    "page": 1,
    "totalPages": 1
  }
}
```

## Update API Token Request

`PATCH https://api.weellu.com/api/v1/admin-panel/users/api/:id`

Updates the status of a user's API token request (approve, reject, etc.).

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The API token request ID (MongoDB ObjectId)

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
status | string | No | New status for the request. One of the `ApiTokenType` enum values

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/admin-panel/users/api/6627d14c5d6e7f8a9b0c1d2e?status=approved" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/users/api/6627d14c5d6e7f8a9b0c1d2e?status=approved",
  {
    method: "PATCH",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/api/v1/admin-panel/users/api/6627d14c5d6e7f8a9b0c1d2e',
  parameters: {'status': 'approved'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "6627d14c5d6e7f8a9b0c1d2e",
    "userId": "659f1a2b3c4d5e6f7a8b9c0d",
    "status": "approved"
  }
}
```

## Get Countries Info

`GET https://api.weellu.com/api/v1/admin-panel/countries`

Returns country distribution data for all registered users.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/countries" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/countries",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/countries',
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "country": "BR",
      "count": 5420
    },
    {
      "country": "US",
      "count": 3100
    },
    {
      "country": "PT",
      "count": 1850
    }
  ]
}
```

## Get Banners

`GET https://api.weellu.com/api/v1/admin-panel/banners`

Returns all marketplace banners.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/banners" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/banners",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/banners',
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "6628e25d6e7f8a9b0c1d2e3f",
      "imageUrl": "https://weellu-bucket.s3.amazonaws.com/banners/spring_sale.jpg",
      "redirectUrl": "https://weellu.com/market/sale",
      "order": 0,
      "createdAt": "2026-03-15T09:00:00.000Z"
    },
    {
      "_id": "6628f36e7f8a9b0c1d2e3f4a",
      "imageUrl": "https://weellu-bucket.s3.amazonaws.com/banners/new_arrivals.jpg",
      "redirectUrl": "https://weellu.com/market/new",
      "order": 1,
      "createdAt": "2026-03-14T09:00:00.000Z"
    }
  ]
}
```

## Get Banner by ID

`GET https://api.weellu.com/api/v1/admin-panel/banners/:id`

Returns a single banner by its ID.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The banner ID (MongoDB ObjectId)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f",
  {
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "6628e25d6e7f8a9b0c1d2e3f",
    "imageUrl": "https://weellu-bucket.s3.amazonaws.com/banners/spring_sale.jpg",
    "redirectUrl": "https://weellu.com/market/sale",
    "order": 0,
    "createdAt": "2026-03-15T09:00:00.000Z"
  }
}
```

## Create Banner

`POST https://api.weellu.com/api/v1/admin-panel/banners`

Creates a new marketplace banner. Accepts an image via multipart form data.

### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
redirectUrl | string | Yes | URL to redirect to when the banner is tapped. Must be a valid URL
order | number | No | Display order (0-based). Lower numbers appear first. Default: 0
file | file | No | Banner image file

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/admin-panel/banners" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN" \
  -F "redirectUrl=https://weellu.com/market/flash-sale" \
  -F "order=0" \
  -F "file=@banner_image.jpg"
```

```javascript
const formData = new FormData();
formData.append("redirectUrl", "https://weellu.com/market/flash-sale");
formData.append("order", "0");
formData.append("file", bannerImageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/banners",
  {
    method: "POST",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/admin-panel/banners'),
);
request.headers['Authorization'] = 'Bearer SUPER_ADMIN_JWT_TOKEN';
request.fields['redirectUrl'] = 'https://weellu.com/market/flash-sale';
request.fields['order'] = '0';
request.files.add(await http.MultipartFile.fromPath('file', 'banner.jpg'));
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "6629a47f8a9b0c1d2e3f4a5b",
    "imageUrl": "https://weellu-bucket.s3.amazonaws.com/banners/flash_sale.jpg",
    "redirectUrl": "https://weellu.com/market/flash-sale",
    "order": 0,
    "createdAt": "2026-03-18T14:00:00.000Z"
  }
}
```

## Update Banner

`PATCH https://api.weellu.com/api/v1/admin-panel/banners/:id`

Updates an existing banner. Supports updating the redirect URL, display order, and image.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The banner ID (MongoDB ObjectId)

### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
redirectUrl | string | No | Updated redirect URL. Must be a valid URL
order | number | No | Updated display order
file | file | No | New banner image file

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN" \
  -F "redirectUrl=https://weellu.com/market/updated-sale" \
  -F "order=2"
```

```javascript
const formData = new FormData();
formData.append("redirectUrl", "https://weellu.com/market/updated-sale");
formData.append("order", "2");

const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f",
  {
    method: "PATCH",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'PATCH',
  Uri.parse('https://api.weellu.com/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f'),
);
request.headers['Authorization'] = 'Bearer SUPER_ADMIN_JWT_TOKEN';
request.fields['redirectUrl'] = 'https://weellu.com/market/updated-sale';
request.fields['order'] = '2';
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "6628e25d6e7f8a9b0c1d2e3f",
    "imageUrl": "https://weellu-bucket.s3.amazonaws.com/banners/spring_sale.jpg",
    "redirectUrl": "https://weellu.com/market/updated-sale",
    "order": 2
  }
}
```

## Delete Banner

`DELETE https://api.weellu.com/api/v1/admin-panel/banners/:id`

Deletes a marketplace banner.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The banner ID (MongoDB ObjectId)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f" \
  -H "Authorization: Bearer SUPER_ADMIN_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer SUPER_ADMIN_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/admin-panel/banners/6628e25d6e7f8a9b0c1d2e3f',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Banner deleted successfully"
  }
}
```
