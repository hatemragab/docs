# Channels

All channel endpoints require authentication via Bearer token.

```
Authorization: Bearer TOKEN
```

All responses are wrapped in:

```json
{
  "code": 2000,
  "data": { ... },
  "msg": null
}
```

## Enums Reference

### RoomType

Value | Description
----- | -----------
`s` | Single (1-to-1 direct message)
`g` | Group chat
`b` | Broadcast channel
`p` | Project room
`c` | Channel
`sy` | System

### GroupRoleType

Value | Description
----- | -----------
`member` | Regular member
`superAdmin` | Super administrator (creator)
`admin` | Administrator
`broadcaster` | Broadcaster (can send messages but limited admin rights)

### BroadcastRoleType

Value | Description
----- | -----------
`member` | Regular member
`superAdmin` | Super administrator (creator)
`admin` | Administrator

## Get Rooms List

`GET https://api.weellu.com/api/v1/channel/v2`

Returns a paginated list of rooms (channels) for the authenticated user. Supports filtering by room type and searching by title.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomType | string | No | Filter by room type: `s`, `g`, `b`, `p`
page | number | No | Page number (default: 1)
limit | number | No | Items per page (default: 20)
title | string | No | Search rooms by title (partial match)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/v2?roomType=g&page=1&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/v2?roomType=g&page=1&limit=20",
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
  '/channel/v2',
  parameters: {'roomType': 'g', 'page': '1', 'limit': '20'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "docs": [
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
        "uId": "64a1b2c3d4e5f6a7b8c9d0e1",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "rT": "g",
        "t": "Engineering Team",
        "tEn": "Engineering Team",
        "nTitle": null,
        "img": "https://s3.amazonaws.com/weellu/group-img.jpg",
        "isD": false,
        "isA": false,
        "isM": false,
        "isPin": false,
        "tTo": null,
        "chatWallpaper": null,
        "lastMsgId": "64f1a2b3c4d5e6f7a8b9c0d3",
        "uC": 5,
        "mentionsCount": 1,
        "lastMessage": {
          "_id": "64f1a2b3c4d5e6f7a8b9c0d3",
          "msg": "Hello everyone!",
          "mT": "text",
          "sId": "64a1b2c3d4e5f6a7b8c9d0e2",
          "createdAt": "2026-03-18T10:30:00.000Z"
        },
        "createdAt": "2026-03-01T08:00:00.000Z"
      }
    ],
    "totalDocs": 45,
    "limit": 20,
    "page": 1,
    "totalPages": 3,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "msg": null
}
```

## Get Unread Room Counter

`GET https://api.weellu.com/api/v1/channel/unread-room-counter`

Returns the total unread message count across all rooms for the authenticated user.

### Parameters

No parameters required.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/unread-room-counter" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/unread-room-counter",
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
final response = await chopper.get('/channel/unread-room-counter');
```

> Example Response

```json
{
  "code": 2000,
  "data": [
    {
      "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
      "uC": 5,
      "mentionsCount": 1
    },
    {
      "rId": "64f1a2b3c4d5e6f7a8b9c0d9",
      "uC": 12,
      "mentionsCount": 0
    }
  ],
  "msg": null
}
```

## Get or Create Peer Room

`GET https://api.weellu.com/api/v1/channel/get-peer-room/:peerId`

Gets an existing single (1-to-1) chat room with the specified peer, or creates one if it does not exist.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
peerId | string | Yes | MongoDB ObjectId of the peer user

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/get-peer-room/64a1b2c3d4e5f6a7b8c9d0e2" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/get-peer-room/64a1b2c3d4e5f6a7b8c9d0e2",
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
  '/channel/get-peer-room/64a1b2c3d4e5f6a7b8c9d0e2',
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
    "rT": "s",
    "t": "John Doe",
    "tEn": "John Doe",
    "nTitle": null,
    "img": "https://s3.amazonaws.com/weellu/user-avatar.jpg",
    "isD": false,
    "isA": false,
    "isM": false,
    "isPin": false,
    "pId": "64a1b2c3d4e5f6a7b8c9d0e2",
    "bId": null,
    "tTo": null,
    "chatWallpaper": null,
    "lastMsgId": null,
    "uC": 0,
    "mentionsCount": 0,
    "createdAt": "2026-03-18T10:30:00.000Z"
  },
  "msg": null
}
```

## Get Room by ID

`GET https://api.weellu.com/api/v1/channel/:roomId`

Returns full details of a specific room by its ID.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2",
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
final response = await chopper.get('/channel/64f1a2b3c4d5e6f7a8b9c0d2');
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
    "img": "https://s3.amazonaws.com/weellu/group-img.jpg",
    "isD": false,
    "isA": false,
    "isM": false,
    "isPin": false,
    "tTo": null,
    "chatWallpaper": null,
    "lastMsgId": "64f1a2b3c4d5e6f7a8b9c0d3",
    "uC": 3,
    "mentionsCount": 0,
    "lastMessage": {
      "_id": "64f1a2b3c4d5e6f7a8b9c0d3",
      "msg": "Let's sync up tomorrow",
      "mT": "text",
      "sId": "64a1b2c3d4e5f6a7b8c9d0e2",
      "createdAt": "2026-03-18T09:15:00.000Z"
    },
    "createdAt": "2026-03-01T08:00:00.000Z"
  },
  "msg": null
}
```

## Get Room Unread Count

`GET https://api.weellu.com/api/v1/channel/:roomId/un-read-count`

Returns the unread message count for a specific room.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/un-read-count" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/un-read-count",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/un-read-count',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "count": 7
  },
  "msg": null
}
```

## Archive Room

`PATCH https://api.weellu.com/api/v1/channel/:roomId/archive`

Archives a room for the authenticated user. Archived rooms are hidden from the main room list.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/archive" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/archive",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/archive',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "isA": true
  },
  "msg": null
}
```

## Unarchive Room

`PATCH https://api.weellu.com/api/v1/channel/:roomId/un-archive`

Unarchives a previously archived room, restoring it to the main room list.

<aside class="notice">
Note: The route path uses "un-archive" (with a hyphen), not "unarchive".
</aside>

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/un-archive" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/un-archive",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/un-archive',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "isA": false
  },
  "msg": null
}
```

## Pin Room

`PATCH https://api.weellu.com/api/v1/channel/:roomId/pin`

Pins a room to the top of the room list.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/pin" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/pin",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/pin',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "isPin": true
  },
  "msg": null
}
```

## Unpin Room

`PATCH https://api.weellu.com/api/v1/channel/:roomId/unpin`

Unpins a room from the top of the room list.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/unpin" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/unpin",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/unpin',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "isPin": false
  },
  "msg": null
}
```

## Toggle Notifications

`PATCH https://api.weellu.com/api/v1/channel/:roomId/notification`

Toggles push notifications on or off for a specific room. If currently enabled, it disables them and vice versa.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "isM": true
  },
  "msg": null
}
```

## Mute Room Notifications

`PATCH https://api.weellu.com/api/v1/channel/:roomId/notification/mute`

Mutes notifications for a specific room. The user will not receive push notifications for new messages in this room.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification/mute" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification/mute",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification/mute',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "isM": true
  },
  "msg": null
}
```

## Unmute Room Notifications

`PATCH https://api.weellu.com/api/v1/channel/:roomId/notification/un-mute`

Unmutes notifications for a specific room, re-enabling push notifications.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification/un-mute" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification/un-mute",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/notification/un-mute',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "isM": false
  },
  "msg": null
}
```

## Set Nickname

`PATCH https://api.weellu.com/api/v1/channel/:roomId/nick-name`

Sets a custom nickname/display name for a room. This overrides the default room title for the authenticated user only.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
name | string | Yes | The custom nickname to set for the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/nick-name" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "My Dev Team"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/nick-name",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ name: "My Dev Team" })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/nick-name',
  body: {'name': 'My Dev Team'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "nTitle": "My Dev Team"
  },
  "msg": null
}
```

## Set Wallpaper URL

`PATCH https://api.weellu.com/api/v1/channel/:roomId/wallpaper`

Sets a chat wallpaper for the room using a URL string.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
chatWallpaper | string | No | URL of the wallpaper image. Pass `null` to remove.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/wallpaper" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"chatWallpaper": "https://s3.amazonaws.com/weellu/wallpapers/blue-gradient.jpg"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/wallpaper",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      chatWallpaper: "https://s3.amazonaws.com/weellu/wallpapers/blue-gradient.jpg"
    })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/wallpaper',
  body: {'chatWallpaper': 'https://s3.amazonaws.com/weellu/wallpapers/blue-gradient.jpg'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "chatWallpaper": "https://s3.amazonaws.com/weellu/wallpapers/blue-gradient.jpg"
  },
  "msg": null
}
```

## Upload Wallpaper

`POST https://api.weellu.com/api/v1/channel/:roomId/wallpaper`

Uploads an image file to be used as the chat wallpaper. The server stores it on S3 and sets the wallpaper URL automatically.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
file | file | Yes | Image file to upload (JPEG, PNG, etc.)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/wallpaper" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@/path/to/wallpaper.jpg"
```

```javascript
const formData = new FormData();
formData.append("file", fileInput.files[0]);

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/wallpaper",
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
  Uri.parse('https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/wallpaper'),
);
request.headers['Authorization'] = 'Bearer YOUR_TOKEN';
request.files.add(await http.MultipartFile.fromPath('file', filePath));
final response = await request.send();
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "chatWallpaper": "https://s3.amazonaws.com/weellu/wallpapers/uploaded-64f1a2b3.jpg"
  },
  "msg": null
}
```

## Enable Translation

`PATCH https://api.weellu.com/api/v1/channel/:roomId/translate`

Enables automatic message translation for the room. All incoming messages will be translated to the specified language.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
transTo | string | Yes | Target language code (e.g., `en`, `es`, `pt`, `ar`, `fr`, `de`)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/translate" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"transTo": "en"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/translate",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer YOUR_TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ transTo: "en" })
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/translate',
  body: {'transTo': 'en'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "tTo": "en"
  },
  "msg": null
}
```

## Stop Translation

`PATCH https://api.weellu.com/api/v1/channel/:roomId/translate/stop`

Disables automatic message translation for the room.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/translate/stop" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/translate/stop",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/translate/stop',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "tTo": null
  },
  "msg": null
}
```

## Mark as Delivered

`PATCH https://api.weellu.com/api/v1/channel/:roomId/deliver`

Marks all messages in the room as delivered for the authenticated user. This updates the delivery status for all undelivered messages.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/deliver" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/deliver",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/deliver',
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

## URL Preview

`GET https://api.weellu.com/api/v1/channel/:roomId/url-preview`

Fetches Open Graph metadata for a URL to generate a link preview (title, description, image).

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
url | string | Yes | The URL to fetch preview data for

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/url-preview?url=https://example.com/article" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/url-preview?url=https://example.com/article",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/url-preview',
  parameters: {'url': 'https://example.com/article'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "title": "Example Article Title",
    "description": "A brief description of the article content.",
    "image": "https://example.com/og-image.jpg",
    "url": "https://example.com/article",
    "siteName": "Example.com"
  },
  "msg": null
}
```

## URL Preview (Room-Free)

`GET https://api.weellu.com/api/v1/channel/url-preview`

Same as URL Preview but without a room scope. Used by web story link previews, where the viewer may be looking at their own story and no peer room exists. TikTok links (`tiktok.com`, `vt.tiktok.com`) resolve through TikTok's oEmbed API, returning the actual post's title, author, and thumbnail instead of a generic card.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
url | string | Yes | The URL to fetch preview data for

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/url-preview?url=https://example.com/article" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/url-preview?url=https://example.com/article",
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
  '/channel/url-preview',
  parameters: {'url': 'https://example.com/article'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "title": "Example Article Title",
    "description": "A brief description of the article content.",
    "images": ["https://example.com/og-image.jpg"],
    "siteName": "Example.com"
  },
  "msg": null
}
```

## Get Assist Task

`GET https://api.weellu.com/api/v1/channel/:roomId/assist`

Returns the assist task associated with a room, if any. Used for AI assistant integration within chat rooms.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/assist" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/assist",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/assist',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d5",
    "roomId": "64f1a2b3c4d5e6f7a8b9c0d2",
    "assistId": "64a1b2c3d4e5f6a7b8c9d0e5",
    "status": "open",
    "createdAt": "2026-03-18T10:00:00.000Z"
  },
  "msg": null
}
```

## My Single Chat Info

`GET https://api.weellu.com/api/v1/channel/:roomId/single/my-info`

Returns the authenticated user's room member record for a single (1-to-1) chat room.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the single chat room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/single/my-info" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/single/my-info",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/single/my-info',
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
    "rT": "s",
    "t": "John Doe",
    "tEn": "John Doe",
    "nTitle": null,
    "img": "https://s3.amazonaws.com/weellu/user-avatar.jpg",
    "isD": false,
    "isA": false,
    "isM": false,
    "isPin": false,
    "pId": "64a1b2c3d4e5f6a7b8c9d0e2",
    "bId": null,
    "tTo": null,
    "chatWallpaper": null,
    "uC": 0,
    "mentionsCount": 0,
    "createdAt": "2026-03-01T08:00:00.000Z"
  },
  "msg": null
}
```

## Peer Single Chat Info

`GET https://api.weellu.com/api/v1/channel/:roomId/single/info`

Returns the peer user's information for a single (1-to-1) chat room, including their profile data.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the single chat room

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/single/info" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/single/info",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/single/info',
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "_id": "64a1b2c3d4e5f6a7b8c9d0e2",
    "fullName": "John Doe",
    "img": "https://s3.amazonaws.com/weellu/user-avatar.jpg",
    "bio": "Software Engineer",
    "lastSeenAt": "2026-03-18T10:25:00.000Z",
    "isOnline": true
  },
  "msg": null
}
```

## Delete Room

`DELETE https://api.weellu.com/api/v1/channel/:roomId`

Soft-deletes a room for the authenticated user. The room is marked as deleted and hidden from the room list.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2',
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

## Clear All Messages

`DELETE https://api.weellu.com/api/v1/channel/:roomId/messages/all`

Clears all messages in the room for the authenticated user. Other members' message history is not affected.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/messages/all" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/64f1a2b3c4d5e6f7a8b9c0d2/messages/all",
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
  '/channel/64f1a2b3c4d5e6f7a8b9c0d2/messages/all',
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

## Get All Starred Messages

`GET https://api.weellu.com/api/v1/channel/messages/stars`

Returns all messages that the authenticated user has starred across all rooms. Supports pagination.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number (default: 1)
limit | number | No | Items per page (default: 20)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/messages/stars?page=1&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/messages/stars?page=1&limit=20",
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
  '/channel/messages/stars',
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
        "_id": "64f1a2b3c4d5e6f7a8b9c0d3",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d2",
        "msg": "Important message to remember",
        "mT": "text",
        "sId": "64a1b2c3d4e5f6a7b8c9d0e2",
        "senderName": "John Doe",
        "senderImg": "https://s3.amazonaws.com/weellu/user-avatar.jpg",
        "createdAt": "2026-03-15T14:20:00.000Z"
      },
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0d7",
        "rId": "64f1a2b3c4d5e6f7a8b9c0d9",
        "msg": "Meeting link: https://meet.example.com/abc",
        "mT": "text",
        "sId": "64a1b2c3d4e5f6a7b8c9d0e3",
        "senderName": "Jane Smith",
        "senderImg": "https://s3.amazonaws.com/weellu/user-avatar2.jpg",
        "createdAt": "2026-03-10T09:00:00.000Z"
      }
    ],
    "totalDocs": 12,
    "limit": 20,
    "page": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false
  },
  "msg": null
}
```
