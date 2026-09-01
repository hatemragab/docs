# Group Chats

Group chat endpoints allow you to create and manage group conversations. All endpoints require authentication via Bearer token.

```
Authorization: Bearer TOKEN
```

## Create Group

`POST https://api.weellu.com/api/v1/channel/group`

Creates a new group chat room. Supports an optional group image upload via multipart form data.

### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
groupName | string | Yes | Name of the group
peerIds | string (JSON array) | Yes | JSON-encoded array of MongoDB ObjectIds of users to add
file | file | No | Optional group image (JPEG, PNG, etc.)

<aside class="notice">
The <code>peerIds</code> field must be a JSON-encoded string array, e.g. <code>'["64a1b2c3d4e5f6a7b8c9d0e1","64a1b2c3d4e5f6a7b8c9d0e2"]'</code>. Each ID must be a valid MongoDB ObjectId.
</aside>

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/group" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F 'groupName=Engineering Team' \
  -F 'peerIds=["64a1b2c3d4e5f6a7b8c9d0e2","64a1b2c3d4e5f6a7b8c9d0e3"]' \
  -F "file=@/path/to/group-image.jpg"
```

```javascript
const formData = new FormData();
formData.append("groupName", "Engineering Team");
formData.append("peerIds", JSON.stringify([
  "64a1b2c3d4e5f6a7b8c9d0e2",
  "64a1b2c3d4e5f6a7b8c9d0e3"
]));
formData.append("file", imageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/group",
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
  Uri.parse('https://api.weellu.com/api/v1/channel/group'),
);
request.headers['Authorization'] = 'Bearer YOUR_TOKEN';
request.fields['groupName'] = 'Engineering Team';
request.fields['peerIds'] = jsonEncode([
  '64a1b2c3d4e5f6a7b8c9d0e2',
  '64a1b2c3d4e5f6a7b8c9d0e3',
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
    "rT": "g",
    "t": "Engineering Team",
    "tEn": "Engineering Team",
    "nTitle": null,
    "img": "https://s3.amazonaws.com/weellu/groups/64f1a2b3.jpg",
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

## Get Group Info

`GET https://api.weellu.com/api/v1/channel/:roomId/group/info`

Returns the group settings and metadata (name, image, description, creator, pinned message).

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/info" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/info",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/info',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d2",
    "cId": "64a1b2c3d4e5f6a7b8c9d0e1",
    "gName": "Engineering Team",
    "gImg": "https://s3.amazonaws.com/weellu/groups/64f1a2b3.jpg",
    "desc": "Team discussions and standups",
    "pinMsg": null,
    "customMsg": null
  },
  "msg": null
}
```

## Get My Group Info

`GET https://api.weellu.com/api/v1/channel/:roomId/group/my-info`

Returns the authenticated user's room member record for the group, including their role, mute status, archive status, and other personal settings.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/my-info" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/my-info",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/my-info',
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
    "rT": "g",
    "t": "Engineering Team",
    "tEn": "Engineering Team",
    "nTitle": null,
    "img": "https://s3.amazonaws.com/weellu/groups/64f1a2b3.jpg",
    "isD": false,
    "isA": false,
    "isM": false,
    "isPin": false,
    "tTo": null,
    "chatWallpaper": null,
    "uC": 3,
    "mentionsCount": 1,
    "createdAt": "2026-03-01T08:00:00.000Z"
  },
  "msg": null
}
```

## List Group Members

`GET https://api.weellu.com/api/v1/channel/:roomId/group/members`

Returns a paginated list of group members. Supports searching by full name and cursor-based pagination.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number (default: 1)
limit | number | No | Items per page (default: 20)
lastId | string | No | Last member ID for cursor-based pagination
fullName | string | No | Search members by full name (partial match)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members?page=1&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members?page=1&limit=20",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members',
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
        "gR": "superAdmin",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e1",
          "fullName": "Alice Johnson",
          "img": "https://s3.amazonaws.com/weellu/avatars/alice.jpg",
          "bio": "Product Manager"
        },
        "createdAt": "2026-03-01T08:00:00.000Z"
      },
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0a2",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e2",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "gR": "admin",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e2",
          "fullName": "Bob Williams",
          "img": "https://s3.amazonaws.com/weellu/avatars/bob.jpg",
          "bio": "Senior Developer"
        },
        "createdAt": "2026-03-01T08:05:00.000Z"
      },
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0a3",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e3",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "gR": "member",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e3",
          "fullName": "Carol Davis",
          "img": "https://s3.amazonaws.com/weellu/avatars/carol.jpg",
          "bio": "Designer"
        },
        "createdAt": "2026-03-02T10:00:00.000Z"
      }
    ],
    "totalDocs": 15,
    "limit": 20,
    "page": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false
  },
  "msg": null
}
```

## List Group Members V2

`GET https://api.weellu.com/api/v1/channel/:roomId/group/members/v2`

Returns group members using the v2 endpoint with enhanced query capabilities.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number (default: 1)
limit | number | No | Items per page (default: 20)
fullName | string | No | Search members by full name

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/v2?page=1&limit=10" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/v2?page=1&limit=10",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/v2',
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
        "gR": "superAdmin",
        "userData": {
          "_id": "64a1b2c3d4e5f6a7b8c9d0e1",
          "fullName": "Alice Johnson",
          "img": "https://s3.amazonaws.com/weellu/avatars/alice.jpg",
          "bio": "Product Manager"
        },
        "createdAt": "2026-03-01T08:00:00.000Z"
      }
    ],
    "totalDocs": 15,
    "limit": 10,
    "page": 1,
    "totalPages": 2,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "msg": null
}
```

## Add Members to Group

`POST https://api.weellu.com/api/v1/channel/:roomId/group/members`

Adds one or more members to an existing group chat.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
ids | array | Yes | Array of MongoDB ObjectIds of users to add

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ids": ["64a1b2c3d4e5f6a7b8c9d0e4", "64a1b2c3d4e5f6a7b8c9d0e5"]}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      ids: ["64a1b2c3d4e5f6a7b8c9d0e4", "64a1b2c3d4e5f6a7b8c9d0e5"]
    })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members',
  body: {
    'ids': ['64a1b2c3d4e5f6a7b8c9d0e4', '64a1b2c3d4e5f6a7b8c9d0e5'],
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

## Kick Group Member

`DELETE https://api.weellu.com/api/v1/channel/:roomId/group/members/:peerId`

Removes a member from the group. Only admins and super admins can kick members.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room
peerId | string | Yes | MongoDB ObjectId of the user to remove

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/64a1b2c3d4e5f6a7b8c9d0e3" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/64a1b2c3d4e5f6a7b8c9d0e3",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/64a1b2c3d4e5f6a7b8c9d0e3',
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

## Update Member Role

`PATCH https://api.weellu.com/api/v1/channel/:roomId/group/members/:peerId/:role`

Updates the role of a group member. Only super admins can change member roles.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room
peerId | string | Yes | MongoDB ObjectId of the user whose role is being changed
role | string | Yes | New role. One of: `member`, `superAdmin`, `admin`, `broadcaster`

### GroupRoleType Enum

Value | Description
----- | -----------
`member` | Regular group member, can send and read messages
`superAdmin` | Group creator with full control
`admin` | Administrator with member management permissions
`broadcaster` | Can send messages but has limited admin rights

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/64a1b2c3d4e5f6a7b8c9d0e3/admin" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/64a1b2c3d4e5f6a7b8c9d0e3/admin",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/members/64a1b2c3d4e5f6a7b8c9d0e3/admin',
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

## Transfer Super Admin

`PATCH https://api.weellu.com/api/v1/channel/:roomId/group/transfer-super-admin/:peerId`

Transfers super admin ownership of the group to another member. The current super admin is demoted to admin.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room
peerId | string | Yes | MongoDB ObjectId of the user to become the new super admin

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/transfer-super-admin/64a1b2c3d4e5f6a7b8c9d0e2" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/transfer-super-admin/64a1b2c3d4e5f6a7b8c9d0e2",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/transfer-super-admin/64a1b2c3d4e5f6a7b8c9d0e2',
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

## Leave Group

`POST https://api.weellu.com/api/v1/channel/:roomId/group/leave`

The authenticated user leaves the group. If the user is the super admin, they must transfer ownership first.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/leave" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/leave",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/leave',
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

## Delete Group

`DELETE https://api.weellu.com/api/v1/channel/:roomId/group/destroy`

Permanently destroys a group chat. Only the super admin can perform this action. All messages and member data are deleted.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/destroy" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/destroy",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/destroy',
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

## Update Group Title

`PATCH https://api.weellu.com/api/v1/channel/:roomId/group/title`

Updates the display title/name of the group. Only admins and super admins can update the title.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
title | string | Yes | The new group title

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/title" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Backend Engineering"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/title",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ title: "Backend Engineering" })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/title',
  body: {'title': 'Backend Engineering'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d2",
    "gName": "Backend Engineering"
  },
  "msg": null
}
```

## Update Group Image

`PATCH https://api.weellu.com/api/v1/channel/:roomId/group/image`

Updates the group avatar image. Only admins and super admins can update the image.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room

### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
file | file | Yes | Image file to upload (JPEG, PNG, etc.)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/image" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@/path/to/new-group-image.jpg"
```

```javascript
const formData = new FormData();
formData.append("file", imageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/image",
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
  Uri.parse('https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/image'),
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
    "gImg": "https://s3.amazonaws.com/weellu/groups/64f1a2b3-new.jpg"
  },
  "msg": null
}
```

## Get Group Message Status

`GET https://api.weellu.com/api/v1/channel/:roomId/group/message/:messageId/status/:type`

Returns the delivery or seen status of a specific message for all group members. Useful for showing "read by" or "delivered to" lists.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the group room
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
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/message/64f1a2b3c4d5e6f7a8b9c0d3/status/seen?page=1&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/message/64f1a2b3c4d5e6f7a8b9c0d3/status/seen?page=1&limit=20",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/group/message/64f1a2b3c4d5e6f7a8b9c0d3/status/seen',
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
        "date": "2026-03-18T10:35:00.000Z"
      }
    ],
    "totalDocs": 10,
    "limit": 20,
    "page": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false
  },
  "msg": null
}
```
