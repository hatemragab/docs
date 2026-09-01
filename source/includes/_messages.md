# Messages

Message endpoints handle sending, retrieving, and managing messages within chat rooms (single, group, broadcast, and project). All endpoints require authentication via `Authorization: Bearer TOKEN`.

Base URL: `https://api.weellu.com/api/v1/channel/:roomId/message`

## Send Message

`POST https://api.weellu.com/api/v1/channel/:roomId/message/`

Sends a message to a room. Supports multipart form data with up to 4 file attachments. For media message types (`image`, `voice`, `video`, `file`), at least one file is required unless forwarding.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the target room (path parameter)
messageType | string | Yes | Type of message being sent (body)
content | string | Conditional | Text content of the message. Required for `text` type; optional for media types (body)
isEncrypted | boolean | No | Explicit content format flag. Defaults to `false`; legacy requests may omit it (body)
encryption | object or JSON string | Conditional | Required exactly when `isEncrypted=true`; AES-GCM metadata described below (body)
localId | string (UUID v4) | Yes | Client-generated UUID for idempotency and local message tracking (body)
parentId | string | No | MongoDB ObjectId of the parent task message (for task sub-messages) (body)
attachment | string (JSON) | Conditional | JSON-encoded attachment metadata. Required for `image`, `video`, `location`, `task`, and `voice` types (body)
mentions | string[] | No | Array of user MongoDB ObjectIds to mention. Can also be a comma-separated string (body)
forwardLocalId | string (UUID v4) | No | Local ID of the original message being forwarded (body)
replyToLocalId | string (UUID v4) | No | Local ID of the message being replied to (body)
replyToProduct | string | No | MongoDB ObjectId of a product being replied to (body)
storyId | string | No | MongoDB ObjectId of the story (required when messageType is `story`) (body)
linkAtt | string (JSON) | No | JSON-encoded link preview attachment (body)
shareUserIds | string[] | No | Array of user IDs to share (when messageType is `userShare`) (body)
shareUserId | string | No | Single user ID to share (when messageType is `userShare` and shareUserIds is not set) (body)
broadcastCategory | string | No | Category filter for broadcast messages (body)
file | file[] | Conditional | Up to 4 files via multipart upload. Required for `image`, `voice`, `video`, `file` types (body)

### Encryption Metadata (`encryption`)

Field | Type | Required | Description
----- | ---- | -------- | -----------
v | integer | Yes | Protocol version. E2E v1 requires `1`
alg | string | Yes | Authenticated-encryption algorithm. E2E v1 requires `A256GCM`
kid | string (UUID) | Yes | Opaque room-key identifier
epoch | integer | Yes | Positive room-key epoch
nonce | string | Yes | Unpadded base64url encoding of the 12-byte AES-GCM nonce
tag | string | Yes | Unpadded base64url encoding of the 16-byte authentication tag

<aside class="notice">
Plaintext and encrypted messages coexist. A legacy message with no `isEncrypted` or `enc` field is plaintext. An encrypted message must have `isEncrypted=true` and complete metadata; mismatched fields are rejected rather than guessed. E2E v1 is limited to `messageType=text`.
</aside>

### MessageType Enum

Value | Description
----- | -----------
`text` | Plain text message
`voice` | Voice recording
`image` | Image attachment
`video` | Video attachment
`file` | Generic file attachment
`location` | Geographic location
`task` | Task message (project rooms only)
`call` | Call event message
`story` | Story reference
`sticker` | Sticker message
`userShare` | User profile share
`info` | System info message (does not trigger notifications)

### BroadcastSendCategoryType Enum (broadcastCategory)

Value | Description
----- | -----------
`all` | Send to all broadcast members
`people` | Send to individual contacts only
`groups` | Send to groups only
`projects` | Send to projects only

> Example Request (text message)

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/" \
  -H "Authorization: Bearer TOKEN" \
  -F 'messageType=text' \
  -F 'content=Hello team! Please review the latest PR.' \
  -F 'localId=550e8400-e29b-41d4-a716-446655440000' \
  -F 'mentions=665a1f2e3b4c5d6e7f8a9b0c,665a1f2e3b4c5d6e7f8a9b0d'
```

```javascript
const formData = new FormData();
formData.append("messageType", "text");
formData.append("content", "Hello team! Please review the latest PR.");
formData.append("localId", "550e8400-e29b-41d4-a716-446655440000");
formData.append("mentions", "665a1f2e3b4c5d6e7f8a9b0c,665a1f2e3b4c5d6e7f8a9b0d");

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/",
  {
    method: "POST",
    headers: { "Authorization": "Bearer TOKEN" },
    body: formData
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.fields['messageType'] = 'text';
request.fields['content'] = 'Hello team! Please review the latest PR.';
request.fields['localId'] = '550e8400-e29b-41d4-a716-446655440000';
request.fields['mentions'] = '665a1f2e3b4c5d6e7f8a9b0c,665a1f2e3b4c5d6e7f8a9b0d';
final response = await request.send();
```

> Example Request (image message)

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/" \
  -H "Authorization: Bearer TOKEN" \
  -F 'messageType=image' \
  -F 'content=Check this screenshot' \
  -F 'localId=550e8400-e29b-41d4-a716-446655440001' \
  -F 'attachment={"width":1920,"height":1080}' \
  -F 'file=@/path/to/screenshot.png'
```

```javascript
const formData = new FormData();
formData.append("messageType", "image");
formData.append("content", "Check this screenshot");
formData.append("localId", "550e8400-e29b-41d4-a716-446655440001");
formData.append("attachment", JSON.stringify({ width: 1920, height: 1080 }));
formData.append("file", imageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/",
  {
    method: "POST",
    headers: { "Authorization": "Bearer TOKEN" },
    body: formData
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.fields['messageType'] = 'image';
request.fields['content'] = 'Check this screenshot';
request.fields['localId'] = '550e8400-e29b-41d4-a716-446655440001';
request.fields['attachment'] = jsonEncode({'width': 1920, 'height': 1080});
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/screenshot.png'));
final response = await request.send();
```

> Example Request (task message)

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/" \
  -H "Authorization: Bearer TOKEN" \
  -F 'messageType=task' \
  -F 'content=Fix the payment gateway timeout issue' \
  -F 'localId=550e8400-e29b-41d4-a716-446655440002' \
  -F 'attachment={"title":"Fix payment gateway timeout","status":"open","flag":"urgent"}' \
  -F 'mentions=665a1f2e3b4c5d6e7f8a9b0c'
```

```javascript
const formData = new FormData();
formData.append("messageType", "task");
formData.append("content", "Fix the payment gateway timeout issue");
formData.append("localId", "550e8400-e29b-41d4-a716-446655440002");
formData.append("attachment", JSON.stringify({
  title: "Fix payment gateway timeout",
  status: "open",
  flag: "urgent"
}));
formData.append("mentions", "665a1f2e3b4c5d6e7f8a9b0c");

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/",
  {
    method: "POST",
    headers: { "Authorization": "Bearer TOKEN" },
    body: formData
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.fields['messageType'] = 'task';
request.fields['content'] = 'Fix the payment gateway timeout issue';
request.fields['localId'] = '550e8400-e29b-41d4-a716-446655440002';
request.fields['attachment'] = jsonEncode({
  'title': 'Fix payment gateway timeout',
  'status': 'open',
  'flag': 'urgent',
});
request.fields['mentions'] = '665a1f2e3b4c5d6e7f8a9b0c';
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665d4c5e6f7a8b9c0d1e2f3a",
    "sId": "665a1f2e3b4c5d6e7f8a9b0c",
    "sName": "John Doe",
    "sImg": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0c_thumb.png",
    "plm": "Android",
    "rId": "665b2a3c4d5e6f7a8b9c0d1e",
    "c": "Hello team! Please review the latest PR.",
    "isEncrypted": false,
    "enc": null,
    "mT": "text",
    "msgAtt": null,
    "rTo": null,
    "lId": "550e8400-e29b-41d4-a716-446655440000",
    "mentions": ["665a1f2e3b4c5d6e7f8a9b0c", "665a1f2e3b4c5d6e7f8a9b0d"],
    "mentionAll": false,
    "stars": [],
    "reactions": [],
    "isEdited": false,
    "parentId": null,
    "createdAt": "2026-03-18T14:30:00.000Z",
    "updatedAt": "2026-03-18T14:30:00.000Z"
  }
}
```

## Send Multipart Message

`POST https://api.weellu.com/api/v1/channel/:roomId/message/multipart`

Sends a message using JSON body instead of multipart form data. Useful for text-only or pre-uploaded content. File uploads are not supported on this endpoint.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the target room (path parameter)
messageType | string | Yes | Type of message (body, JSON)
content | string | Conditional | Message text content (body, JSON)
isEncrypted | boolean | No | Explicit content format flag. Defaults to `false`; legacy requests may omit it
encryption | object or JSON string | Conditional | Required exactly when `isEncrypted=true`; uses the versioned AES-GCM metadata documented above
localId | string (UUID v4) | Yes | Client-generated UUID (body, JSON)
parentId | string | No | Parent task message ID (body, JSON)
attachment | string (JSON) | No | JSON-encoded attachment metadata (body, JSON)
mentions | string[] | No | Array of mentioned user IDs (body, JSON)
forwardLocalId | string (UUID v4) | No | Local ID of forwarded message (body, JSON)
replyToLocalId | string (UUID v4) | No | Local ID of replied message (body, JSON)
linkAtt | string (JSON) | No | JSON-encoded link preview (body, JSON)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/multipart" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "messageType": "text",
    "content": "Meeting starts in 10 minutes!",
    "localId": "550e8400-e29b-41d4-a716-446655440003"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/multipart",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      messageType: "text",
      content: "Meeting starts in 10 minutes!",
      localId: "550e8400-e29b-41d4-a716-446655440003"
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/multipart'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'messageType': 'text',
    'content': 'Meeting starts in 10 minutes!',
    'localId': '550e8400-e29b-41d4-a716-446655440003',
  }),
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665d4c5e6f7a8b9c0d1e2f3b",
    "sId": "665a1f2e3b4c5d6e7f8a9b0c",
    "sName": "John Doe",
    "sImg": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0c_thumb.png",
    "plm": "Web",
    "rId": "665b2a3c4d5e6f7a8b9c0d1e",
    "c": "Meeting starts in 10 minutes!",
    "isEncrypted": false,
    "enc": null,
    "mT": "text",
    "lId": "550e8400-e29b-41d4-a716-446655440003",
    "mentions": [],
    "mentionAll": false,
    "createdAt": "2026-03-18T14:35:00.000Z"
  }
}
```

## Get Messages

`GET https://api.weellu.com/api/v1/channel/:roomId/message/`

Retrieves messages from a room with filtering and pagination support. Results are a newest-first array. Supports directional cursor-based pagination using `lastId`, `before`, and `after` parameters.

Requires `Authorization: Bearer TOKEN` header.

<aside class="notice">
This endpoint allows 1,200 requests per 60 seconds per throttler tracker. The global API rate limit is also 1,200 requests per 60 seconds per route handler.
</aside>

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
limit | string (number) | No | Number of messages to return (default: 50, production maximum: 100)
filter | string | No | Filter messages by content type
before | string | No | MongoDB ObjectId; return messages older than this message (cursor-based pagination backward)
after | string | No | MongoDB ObjectId; return messages newer than this message (cursor-based pagination forward)
text | string | No | Full-text search on message content
fromDate | string (ISO 8601) | No | Start date for date range filter (requires `toDate`)
toDate | string (ISO 8601) | No | End date for date range filter (requires `fromDate`)
parentId | string | No | MongoDB ObjectId; filter messages by parent task ID (for task sub-messages)
page | string (number) | No | Accepted from the shared pagination DTO but ignored by this endpoint
lastId | string | No | MongoDB ObjectId for cursor-based pagination used by Flutter and web clients

### MessageFilter Enum (filter)

Value | Description
----- | -----------
`all` | All message types (default)
`media` | Images and videos only
`links` | Messages containing links
`file` | File attachments only
`voice` | Voice messages only

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/?limit=30&filter=all" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/?limit=30&filter=all",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/?limit=30&filter=all'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Request (cursor-based pagination)

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/?limit=20&before=665d4c5e6f7a8b9c0d1e2f3a" \
  -H "Authorization: Bearer TOKEN"
```

> Example Request (date range + text search)

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/?fromDate=2026-03-01T00:00:00.000Z&toDate=2026-03-18T23:59:59.999Z&text=payment" \
  -H "Authorization: Bearer TOKEN"
```

> Example Request (task sub-messages)

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/?parentId=665d4c5e6f7a8b9c0d1e2f3a&limit=50" \
  -H "Authorization: Bearer TOKEN"
```

> Example Response

```json
{
  "code": 2000,
  "data": [
    {
      "_id": "665d4c5e6f7a8b9c0d1e2f3b",
      "sId": "665a1f2e3b4c5d6e7f8a9b0c",
      "sName": "John Doe",
      "sImg": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0c_thumb.png",
      "plm": "Android",
      "rId": "665b2a3c4d5e6f7a8b9c0d1e",
      "c": "Hello team!",
      "mT": "text",
      "msgAtt": null,
      "rTo": null,
      "lId": "550e8400-e29b-41d4-a716-446655440000",
      "mentions": [],
      "mentionAll": false,
      "stars": [],
      "reactions": [],
      "isEdited": false,
      "pinnedAt": null,
      "parentId": null,
      "createdAt": "2026-03-18T14:30:00.000Z",
      "updatedAt": "2026-03-18T14:30:00.000Z"
    },
    {
      "_id": "665d4c5e6f7a8b9c0d1e2f3c",
      "sId": "665a1f2e3b4c5d6e7f8a9b0d",
      "sName": "Jane Smith",
      "sImg": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0d_thumb.png",
      "plm": "Ios",
      "rId": "665b2a3c4d5e6f7a8b9c0d1e",
      "c": "",
      "mT": "image",
      "msgAtt": {
        "fileSource": "https://cdn.weellu.com/chat/665d4c5e.jpg",
        "width": 1920,
        "height": 1080
      },
      "lId": "550e8400-e29b-41d4-a716-446655440001",
      "mentions": [],
      "stars": [],
      "reactions": [
        { "userId": "665a1f2e3b4c5d6e7f8a9b0c", "reaction": "thumbsup" }
      ],
      "isEdited": false,
      "createdAt": "2026-03-18T14:31:00.000Z",
      "updatedAt": "2026-03-18T14:32:00.000Z"
    }
  ],
  "msg": null
}
```

## Delete Message

`DELETE https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/delete/:type`

Deletes a message. Can delete for everyone or only for the current user.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message (path parameter)
type | string | Yes | Deletion scope (path parameter)

### DeleteMessageType Enum (type)

Value | Description
----- | -----------
`all` | Delete for all participants (sender only, within time window)
`me` | Delete only for the current user (hide from own view)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/delete/all" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/delete/all",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/delete/all'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Add Reaction

`PATCH https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/reaction`

Adds or updates a reaction on a message. Each user can have one reaction per message; sending a new reaction replaces the previous one.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message (path parameter)
reaction | string | Yes | Reaction emoji identifier (e.g. `thumbsup`, `heart`, `fire`) (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reaction" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"reaction": "thumbsup"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reaction",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ reaction: "thumbsup" })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reaction'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'reaction': 'thumbsup'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Remove Reaction

`DELETE https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/reaction`

Removes the current user's reaction from a message.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reaction" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reaction",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reaction'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Get Reactions

`GET https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/reactions`

Retrieves all reactions on a specific message with user details.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reactions" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reactions",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/reactions'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "userId": "665a1f2e3b4c5d6e7f8a9b0c",
      "reaction": "thumbsup"
    },
    {
      "userId": "665a1f2e3b4c5d6e7f8a9b0d",
      "reaction": "heart"
    }
  ]
}
```

## Star Message

`POST https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/star`

Stars (bookmarks) a message for the current user.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message to star (path parameter)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/star" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/star",
  { method: "POST", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/star'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Un-star Message

`POST https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/un-star`

Removes a star (bookmark) from a message for the current user.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message to un-star (path parameter)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/un-star" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/un-star",
  { method: "POST", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/un-star'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Get Room Starred Messages

`GET https://api.weellu.com/api/v1/channel/:roomId/message/stars`

Retrieves all starred (bookmarked) messages for the current user within a specific room.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
page | string (number) | No | Page number (default: 1)
limit | string (number) | No | Items per page (default: 20)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/stars?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/stars?page=1&limit=20",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/stars?page=1&limit=20'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "665d4c5e6f7a8b9c0d1e2f3a",
      "sId": "665a1f2e3b4c5d6e7f8a9b0c",
      "sName": "John Doe",
      "sImg": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0c_thumb.png",
      "rId": "665b2a3c4d5e6f7a8b9c0d1e",
      "c": "Important message to remember",
      "mT": "text",
      "createdAt": "2026-03-15T09:30:00.000Z"
    }
  ]
}
```

## Pin Message

`PATCH https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/pin`

Pins a message in the room. Works for all room types including projects.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message to pin (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/pin" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/pin",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/pin'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Pin Message (v2 with Expiry)

`PATCH https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/pin/v2`

Pins a message with an optional expiry date. Works for all room types including projects.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message to pin (path parameter)
date | string (ISO 8601) | Yes | Expiry date for the pin (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/pin/v2" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"date": "2026-04-18T23:59:59.000Z"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/pin/v2",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ date: "2026-04-18T23:59:59.000Z" })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/pin/v2'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'date': '2026-04-18T23:59:59.000Z'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Un-pin Message

`PATCH https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/un-pin`

Removes a pin from a message. Works for all room types including projects.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the message to un-pin (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/un-pin" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/un-pin",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/un-pin'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Get Pinned Messages (v2)

`GET https://api.weellu.com/api/v1/channel/:roomId/message/pin/v2`

Retrieves all currently pinned messages in a room. Optionally filter by parent task ID.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
parentTaskId | string | No | MongoDB ObjectId of a parent task to scope pinned messages (query parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/pin/v2" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/pin/v2",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/pin/v2'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Request (filtered by parent task)

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/pin/v2?parentTaskId=665d4c5e6f7a8b9c0d1e2f3a" \
  -H "Authorization: Bearer TOKEN"
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "665d4c5e6f7a8b9c0d1e2f3a",
      "sId": "665a1f2e3b4c5d6e7f8a9b0c",
      "sName": "John Doe",
      "rId": "665b2a3c4d5e6f7a8b9c0d1e",
      "c": "Sprint goals for this week",
      "mT": "text",
      "pinnedAt": "2026-03-18T10:00:00.000Z",
      "pinnedTo": "2026-04-18T23:59:59.000Z",
      "createdAt": "2026-03-17T09:00:00.000Z"
    }
  ]
}
```

## Voice Transcription

`GET https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/transcribe`

Transcribes a voice message to text using AI.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the voice message (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/transcribe" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/transcribe",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/transcribe'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "text": "Hey team, just wanted to remind everyone about the standup meeting at 10am tomorrow. Please prepare your updates."
  }
}
```

## Convert to MP3

`POST https://api.weellu.com/api/v1/channel/:roomId/message/:messageId/convert-to-mp3`

Converts a voice message to MP3 format.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the voice message (path parameter)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/convert-to-mp3" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/convert-to-mp3",
  { method: "POST", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message/665d4c5e6f7a8b9c0d1e2f3a/convert-to-mp3'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "url": "https://cdn.weellu.com/chat/665d4c5e6f7a8b9c0d1e2f3a.mp3"
  }
}
```
