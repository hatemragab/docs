# Broadcasts

Broadcast channel endpoints allow you to create and manage one-to-many messaging channels. Only admins and super admins can send messages in broadcasts. All endpoints require authentication via Bearer token.

```
Authorization: Bearer TOKEN
```

## Create Broadcast

`POST https://api.weellu.com/api/v1/channel/broadcast`

Creates a new broadcast channel. Supports an optional image upload via multipart form data.

### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
broadcastName | string | Yes | Name of the broadcast channel
peerIds | string (JSON array) | Yes | JSON-encoded array of MongoDB ObjectIds of users to add
file | file | No | Optional broadcast image (JPEG, PNG, etc.)

<aside class="notice">
The <code>peerIds</code> field must be a JSON-encoded string array, e.g. <code>'["64a1b2c3d4e5f6a7b8c9d0e1","64a1b2c3d4e5f6a7b8c9d0e2"]'</code>. Each ID must be a valid MongoDB ObjectId.
</aside>

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/broadcast" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F 'broadcastName=Company Announcements' \
  -F 'peerIds=["64a1b2c3d4e5f6a7b8c9d0e2","64a1b2c3d4e5f6a7b8c9d0e3","64a1b2c3d4e5f6a7b8c9d0e4"]' \
  -F "file=@/path/to/broadcast-image.jpg"
```

```javascript
const formData = new FormData();
formData.append("broadcastName", "Company Announcements");
formData.append("peerIds", JSON.stringify([
  "64a1b2c3d4e5f6a7b8c9d0e2",
  "64a1b2c3d4e5f6a7b8c9d0e3",
  "64a1b2c3d4e5f6a7b8c9d0e4"
]));
formData.append("file", imageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/broadcast",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    },
    body: formData
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/channel/broadcast'),
);
request.headers['Authorization'] = 'Bearer YOUR_TOKEN';
request.fields['broadcastName'] = 'Company Announcements';
request.fields['peerIds'] = jsonEncode([
  '64a1b2c3d4e5f6a7b8c9d0e2',
  '64a1b2c3d4e5f6a7b8c9d0e3',
  '64a1b2c3d4e5f6a7b8c9d0e4',
]);
request.files.add(await http.MultipartFile.fromPath('file', imagePath));
final response = await request.send();
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "uId": "64a1b2c3d4e5f6a7b8c9d0e1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "rT": "b",
    "t": "Company Announcements",
    "tEn": "Company Announcements",
    "nTitle": null,
    "img": "https://s3.amazonaws.com/weellu/broadcasts/64f1a2b3.jpg",
    "isD": false,
    "isA": false,
    "isM": false,
    "isPin": false,
    "tTo": null,
    "chatWallpaper": null,
    "lastMsgId": null,
    "uC": 0,
    "mentionsCount": 0,
    "createdAt": "2026-03-18T12:00:00.000Z"
  },
  "msg": null
}
```

## Get Broadcast Info

`GET https://api.weellu.com/api/v1/channel/:roomId/broadcast/info`

Returns the broadcast settings and metadata (name, image, creator, linked groups and projects).

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/info" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/info",
  {
    method: "GET",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/info',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d2",
    "cId": "64a1b2c3d4e5f6a7b8c9d0e1",
    "bName": "Company Announcements",
    "bImg": "https://s3.amazonaws.com/weellu/broadcasts/64f1a2b3.jpg",
    "groups": [],
    "projects": []
  },
  "msg": null
}
```

## Get My Broadcast Info

`GET https://api.weellu.com/api/v1/channel/:roomId/broadcast/my-info`

Returns the authenticated user's room member record for the broadcast channel.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/my-info" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/my-info",
  {
    method: "GET",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/my-info',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "uId": "64a1b2c3d4e5f6a7b8c9d0e1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "rT": "b",
    "t": "Company Announcements",
    "tEn": "Company Announcements",
    "nTitle": null,
    "img": "https://s3.amazonaws.com/weellu/broadcasts/64f1a2b3.jpg",
    "isD": false,
    "isA": false,
    "isM": false,
    "isPin": false,
    "tTo": null,
    "chatWallpaper": null,
    "uC": 2,
    "mentionsCount": 0,
    "createdAt": "2026-03-01T08:00:00.000Z"
  },
  "msg": null
}
```

## List Broadcast Members

`GET https://api.weellu.com/api/v1/channel/:roomId/broadcast/members`

Returns a paginated list of broadcast members. Supports searching by name and cursor-based pagination.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number (default: 1)
limit | number | No | Items per page (default: 20)
lastId | string | No | Last member ID for cursor-based pagination
fullName | string | No | Search members by full name (partial match)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members?page=1&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members?page=1&limit=20",
  {
    method: "GET",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members',
  parameters: {'page': '1', 'limit': '20'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "docs": [
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0a1",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e1",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "bId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "role": "superAdmin",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e1",
          "fullName": "Alice Johnson",
          "img": "https://s3.amazonaws.com/weellu/avatars/alice.jpg",
          "bio": "CEO"
        },
        "createdAt": "2026-03-01T08:00:00.000Z"
      },
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0a2",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e2",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "bId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "role": "admin",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e2",
          "fullName": "Bob Williams",
          "img": "https://s3.amazonaws.com/weellu/avatars/bob.jpg",
          "bio": "VP Engineering"
        },
        "createdAt": "2026-03-01T08:05:00.000Z"
      },
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0a3",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e3",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "bId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "role": "member",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e3",
          "fullName": "Carol Davis",
          "img": "https://s3.amazonaws.com/weellu/avatars/carol.jpg",
          "bio": "Designer"
        },
        "createdAt": "2026-03-02T10:00:00.000Z"
      }
    ],
    "totalDocs": 50,
    "limit": 20,
    "page": 1,
    "totalPages": 3,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "msg": null
}
```

## List Broadcast Members V2

`GET https://api.weellu.com/api/v1/channel/:roomId/broadcast/members/v2`

Returns broadcast members using the v2 endpoint with enhanced query capabilities.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number (default: 1)
limit | number | No | Items per page (default: 20)
fullName | string | No | Search members by full name

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members/v2?page=1&limit=10" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members/v2?page=1&limit=10",
  {
    method: "GET",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members/v2',
  parameters: {'page': '1', 'limit': '10'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "docs": [
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0a1",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e1",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "bId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "role": "superAdmin",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e1",
          "fullName": "Alice Johnson",
          "img": "https://s3.amazonaws.com/weellu/avatars/alice.jpg",
          "bio": "CEO"
        },
        "createdAt": "2026-03-01T08:00:00.000Z"
      }
    ],
    "totalDocs": 50,
    "limit": 10,
    "page": 1,
    "totalPages": 5,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "msg": null
}
```

## Add Members to Broadcast

`POST https://api.weellu.com/api/v1/channel/:roomId/broadcast/members`

Adds one or more members to an existing broadcast channel.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
ids | array | Yes | Array of MongoDB ObjectIds of users to add

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ids": ["64a1b2c3d4e5f6a7b8c9d0e5", "64a1b2c3d4e5f6a7b8c9d0e6"]}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      ids: ["64a1b2c3d4e5f6a7b8c9d0e5", "64a1b2c3d4e5f6a7b8c9d0e6"]
    })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members',
  body: {
    'ids': ['64a1b2c3d4e5f6a7b8c9d0e5', '64a1b2c3d4e5f6a7b8c9d0e6'],
  },
);
```

> Example Response

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

## Kick Broadcast Member

`DELETE https://api.weellu.com/api/v1/channel/:roomId/broadcast/members/:peerId`

Removes a member from the broadcast channel. Only admins and super admins can remove members.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room
peerId | string | Yes | MongoDB ObjectId of the user to remove

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members/64a1b2c3d4e5f6a7b8c9d0e3" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members/64a1b2c3d4e5f6a7b8c9d0e3",
  {
    method: "DELETE",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/members/64a1b2c3d4e5f6a7b8c9d0e3',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

## Update Broadcast Title

`PATCH https://api.weellu.com/api/v1/channel/:roomId/broadcast/title`

Updates the display name of the broadcast channel. Only admins and super admins can update the title.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
title | string | Yes | The new broadcast title

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/title" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Product Updates & Releases"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/title",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ title: "Product Updates & Releases" })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/title',
  body: {'title': 'Product Updates & Releases'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d2",
    "bName": "Product Updates & Releases"
  },
  "msg": null
}
```

## Update Broadcast Image

`PATCH https://api.weellu.com/api/v1/channel/:roomId/broadcast/image`

Updates the broadcast channel avatar image. Only admins and super admins can update the image.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
file | file | Yes | Image file to upload (JPEG, PNG, etc.)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/image" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@/path/to/new-broadcast-image.jpg"
```

```javascript
const formData = new FormData();
formData.append("file", imageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/image",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    },
    body: formData
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'PATCH',
  Uri.parse('https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/image'),
);
request.headers['Authorization'] = 'Bearer YOUR_TOKEN';
request.files.add(await http.MultipartFile.fromPath('file', imagePath));
final response = await request.send();
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d2",
    "bImg": "https://s3.amazonaws.com/weellu/broadcasts/64f1a2b3-new.jpg"
  },
  "msg": null
}
```

## Add Admin to Broadcast

`POST https://api.weellu.com/api/v1/channel/broadcast/:broadcastId/user/:peerId`

Promotes a broadcast member to admin role. Only the super admin can promote members.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
broadcastId | string | Yes | MongoDB ObjectId of the broadcast
peerId | string | Yes | MongoDB ObjectId of the user to promote

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/broadcast/64f1a2b3c4d5e6f7a8b9c0d2/user/64a1b2c3d4e5f6a7b8c9d0e3" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/broadcast/64f1a2b3c4d5e6f7a8b9c0d2/user/64a1b2c3d4e5f6a7b8c9d0e3",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/channel/broadcast/64f1a2b3c4d5e6f7a8b9c0d2/user/64a1b2c3d4e5f6a7b8c9d0e3',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

## Remove Admin from Broadcast

`DELETE https://api.weellu.com/api/v1/channel/broadcast/:broadcastId/user/:peerId`

Demotes a broadcast admin back to member role. Only the super admin can demote admins.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
broadcastId | string | Yes | MongoDB ObjectId of the broadcast
peerId | string | Yes | MongoDB ObjectId of the admin to demote

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/broadcast/64f1a2b3c4d5e6f7a8b9c0d2/user/64a1b2c3d4e5f6a7b8c9d0e3" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/broadcast/64f1a2b3c4d5e6f7a8b9c0d2/user/64a1b2c3d4e5f6a7b8c9d0e3",
  {
    method: "DELETE",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/channel/broadcast/64f1a2b3c4d5e6f7a8b9c0d2/user/64a1b2c3d4e5f6a7b8c9d0e3',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

## Get Broadcast Message Status

`GET https://api.weellu.com/api/v1/channel/:roomId/broadcast/message/:messageId/status/:type`

Returns the delivery or seen status of a specific message for all broadcast members. Supports pagination.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room
messageId | string | Yes | MongoDB ObjectId of the message
type | string | Yes | Status type: `seen` or `deliver`

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number (default: 1)
limit | number | No | Items per page (default: 20)

### MessageStatusType Enum

Value | Description
----- | -----------
`seen` | Members who have read the message
`deliver` | Members who have received (but not necessarily read) the message

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/message/64f1a2b3c4d5e6f7a8b9c0d3/status/seen?page=1&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/message/64f1a2b3c4d5e6f7a8b9c0d3/status/seen?page=1&limit=20",
  {
    method: "GET",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/message/64f1a2b3c4d5e6f7a8b9c0d3/status/seen',
  parameters: {'page': '1', 'limit': '20'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "docs": [
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0b1",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e2",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e2",
          "fullName": "Bob Williams",
          "img": "https://s3.amazonaws.com/weellu/avatars/bob.jpg"
        },
        "date": "2026-03-18T10:32:00.000Z"
      },
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0b2",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e3",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e3",
          "fullName": "Carol Davis",
          "img": "https://s3.amazonaws.com/weellu/avatars/carol.jpg"
        },
        "date": "2026-03-18T10:45:00.000Z"
      }
    ],
    "totalDocs": 35,
    "limit": 20,
    "page": 1,
    "totalPages": 2,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "msg": null
}
```

## Add Projects to Broadcast

`PATCH https://api.weellu.com/api/v1/channel/:roomId/broadcast/projects/add`

Links project rooms to the broadcast. When a message is sent to the broadcast, it can also be forwarded to linked project rooms.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
ids | array | Yes | Array of MongoDB ObjectIds of project rooms to link

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects/add" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ids": ["64f1a2b3c4d5e6f7a8b9c1a1", "64f1a2b3c4d5e6f7a8b9c1a2"]}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects/add",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      ids: ["64f1a2b3c4d5e6f7a8b9c1a1", "64f1a2b3c4d5e6f7a8b9c1a2"]
    })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects/add',
  body: {
    'ids': ['64f1a2b3c4d5e6f7a8b9c1a1', '64f1a2b3c4d5e6f7a8b9c1a2'],
  },
);
```

> Example Response

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

## Remove Projects from Broadcast

`DELETE https://api.weellu.com/api/v1/channel/:roomId/broadcast/projects/remove`

Unlinks project rooms from the broadcast channel.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
ids | array | Yes | Array of MongoDB ObjectIds of project rooms to unlink

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects/remove" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ids": ["64f1a2b3c4d5e6f7a8b9c1a1"]}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects/remove",
  {
    method: "DELETE",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      ids: ["64f1a2b3c4d5e6f7a8b9c1a1"]
    })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects/remove',
  body: {
    'ids': ['64f1a2b3c4d5e6f7a8b9c1a1'],
  },
);
```

> Example Response

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

## List Broadcast Projects

`GET https://api.weellu.com/api/v1/channel/:roomId/broadcast/projects`

Returns the list of project rooms linked to the broadcast channel.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects",
  {
    method: "GET",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/projects',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": [
    {
      "_id": "64f1a2b3c4d5e6f7a8b9c1a1",
      "rT": "p",
      "t": "Mobile App Project",
      "img": "https://s3.amazonaws.com/weellu/projects/mobile.jpg"
    },
    {
      "_id": "64f1a2b3c4d5e6f7a8b9c1a2",
      "rT": "p",
      "t": "Backend API Project",
      "img": "https://s3.amazonaws.com/weellu/projects/backend.jpg"
    }
  ],
  "msg": null
}
```

## Add Groups to Broadcast

`PATCH https://api.weellu.com/api/v1/channel/:roomId/broadcast/groups/add`

Links group chat rooms to the broadcast. When a message is sent to the broadcast, it can also be forwarded to linked group rooms.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
ids | array | Yes | Array of MongoDB ObjectIds of group rooms to link

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups/add" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ids": ["64f1a2b3c4d5e6f7a8b9c2a1", "64f1a2b3c4d5e6f7a8b9c2a2"]}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups/add",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      ids: ["64f1a2b3c4d5e6f7a8b9c2a1", "64f1a2b3c4d5e6f7a8b9c2a2"]
    })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups/add',
  body: {
    'ids': ['64f1a2b3c4d5e6f7a8b9c2a1', '64f1a2b3c4d5e6f7a8b9c2a2'],
  },
);
```

> Example Response

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

## Remove Groups from Broadcast

`DELETE https://api.weellu.com/api/v1/channel/:roomId/broadcast/groups/remove`

Unlinks group chat rooms from the broadcast channel.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
ids | array | Yes | Array of MongoDB ObjectIds of group rooms to unlink

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups/remove" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ids": ["64f1a2b3c4d5e6f7a8b9c2a1"]}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups/remove",
  {
    method: "DELETE",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      ids: ["64f1a2b3c4d5e6f7a8b9c2a1"]
    })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups/remove',
  body: {
    'ids': ['64f1a2b3c4d5e6f7a8b9c2a1'],
  },
);
```

> Example Response

```json
{
  "code": 2000,
  "data": null,
  "msg": null
}
```

## List Broadcast Groups

`GET https://api.weellu.com/api/v1/channel/:roomId/broadcast/groups`

Returns the list of group chat rooms linked to the broadcast channel.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the broadcast room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups",
  {
    method: "GET",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/broadcast/groups',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": [
    {
      "_id": "64f1a2b3c4d5e6f7a8b9c2a1",
      "rT": "g",
      "t": "Engineering Team",
      "img": "https://s3.amazonaws.com/weellu/groups/eng-team.jpg"
    },
    {
      "_id": "64f1a2b3c4d5e6f7a8b9c2a2",
      "rT": "g",
      "t": "Design Team",
      "img": "https://s3.amazonaws.com/weellu/groups/design-team.jpg"
    }
  ],
  "msg": null
}
```
