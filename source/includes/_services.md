# Supporting Services

This section covers auxiliary services that support the core Weellu platform: user banning, room filters, reports, file uploads, bots, and the marketplace.

All endpoints require Bearer token authentication unless otherwise noted.

# User Ban

Manage user-to-user blocking. When a user bans another user, all direct communication between them is blocked.

## Check Ban Status

`GET https://api.weellu.com/api/v1/user-ban/:peerId/ban`

Checks the ban relationship between the authenticated user and a peer user. Returns whether either party has banned the other and the associated room ID if one exists.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
peerId | string | Yes | The peer user's ID (MongoDB ObjectId)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/ban" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/ban",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/ban',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "isMeBanner": false,
    "isPeerBanner": false,
    "roomId": "660d4e5f6a7b8c9d0e1f2a3b"
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
isMeBanner | boolean | `true` if the authenticated user has banned the peer
isPeerBanner | boolean | `true` if the peer user has banned the authenticated user
roomId | string | The direct chat room ID between the two users. Nullable if no room exists

## Ban User

`POST https://api.weellu.com/api/v1/user-ban/:peerId/ban`

Bans a peer user. The banned user will no longer be able to send messages to the authenticated user.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
peerId | string | Yes | The peer user's ID (MongoDB ObjectId)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/ban" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/ban",
  {
    method: "POST",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/ban',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "isMeBanner": true,
    "isPeerBanner": false,
    "roomId": "660d4e5f6a7b8c9d0e1f2a3b"
  }
}
```

## Unban User

`POST https://api.weellu.com/api/v1/user-ban/:peerId/un-ban`

Removes a ban on a peer user. The peer user will be able to communicate with the authenticated user again.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
peerId | string | Yes | The peer user's ID (MongoDB ObjectId)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/un-ban" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/un-ban",
  {
    method: "POST",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/user-ban/659f2b3c4d5e6f7a8b9c0d1e/un-ban',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "isMeBanner": false,
    "isPeerBanner": false,
    "roomId": "660d4e5f6a7b8c9d0e1f2a3b"
  }
}
```

# Room Filters

Room filters allow users to organize their chat rooms into custom named groups. Each filter has a name and a list of room IDs.

## Create Room Filter

`POST https://api.weellu.com/api/v1/room-filters`

Creates a new room filter for the authenticated user.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
name | string | Yes | Display name for the filter
rIds | string[] | No | Array of room IDs (MongoDB ObjectIds) to include in the filter

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/room-filters" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Work Chats",
    "rIds": [
      "660d4e5f6a7b8c9d0e1f2a3b",
      "660d4e5f6a7b8c9d0e1f2a3c"
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/room-filters",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      name: "Work Chats",
      rIds: [
        "660d4e5f6a7b8c9d0e1f2a3b",
        "660d4e5f6a7b8c9d0e1f2a3c",
      ],
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/room-filters',
  body: {
    'name': 'Work Chats',
    'rIds': [
      '660d4e5f6a7b8c9d0e1f2a3b',
      '660d4e5f6a7b8c9d0e1f2a3c',
    ],
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "660e5f6a7b8c9d0e1f2a3b4c",
    "userId": "659f1a2b3c4d5e6f7a8b9c0d",
    "name": "Work Chats",
    "rIds": [
      "660d4e5f6a7b8c9d0e1f2a3b",
      "660d4e5f6a7b8c9d0e1f2a3c"
    ],
    "createdAt": "2026-03-18T10:30:00.000Z",
    "updatedAt": "2026-03-18T10:30:00.000Z"
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
_id | string | Unique filter ID
userId | string | Owner user ID
name | string | Filter display name
rIds | string[] | Array of room IDs in this filter
createdAt | string | ISO 8601 creation timestamp
updatedAt | string | ISO 8601 last update timestamp

## Get All Room Filters

`GET https://api.weellu.com/api/v1/room-filters`

Returns all room filters belonging to the authenticated user.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/room-filters" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/room-filters",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/room-filters',
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "660e5f6a7b8c9d0e1f2a3b4c",
      "userId": "659f1a2b3c4d5e6f7a8b9c0d",
      "name": "Work Chats",
      "rIds": [
        "660d4e5f6a7b8c9d0e1f2a3b",
        "660d4e5f6a7b8c9d0e1f2a3c"
      ],
      "createdAt": "2026-03-18T10:30:00.000Z",
      "updatedAt": "2026-03-18T10:30:00.000Z"
    },
    {
      "_id": "660e5f6a7b8c9d0e1f2a3b4d",
      "userId": "659f1a2b3c4d5e6f7a8b9c0d",
      "name": "Family",
      "rIds": [
        "660d4e5f6a7b8c9d0e1f2a3d"
      ],
      "createdAt": "2026-03-17T08:00:00.000Z",
      "updatedAt": "2026-03-17T08:00:00.000Z"
    }
  ]
}
```

## Update Room Filter

`PATCH https://api.weellu.com/api/v1/room-filters/:id`

Updates an existing room filter. You can change the name, the room list, or both.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The filter ID (MongoDB ObjectId)

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
name | string | No | New display name for the filter
rIds | string[] | No | New array of room IDs. Replaces the entire list

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/room-filters/660e5f6a7b8c9d0e1f2a3b4c" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Work & Projects",
    "rIds": [
      "660d4e5f6a7b8c9d0e1f2a3b",
      "660d4e5f6a7b8c9d0e1f2a3c",
      "660d4e5f6a7b8c9d0e1f2a3e"
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/room-filters/660e5f6a7b8c9d0e1f2a3b4c",
  {
    method: "PATCH",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      name: "Work & Projects",
      rIds: [
        "660d4e5f6a7b8c9d0e1f2a3b",
        "660d4e5f6a7b8c9d0e1f2a3c",
        "660d4e5f6a7b8c9d0e1f2a3e",
      ],
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.patch(
  '/api/v1/room-filters/660e5f6a7b8c9d0e1f2a3b4c',
  body: {
    'name': 'Work & Projects',
    'rIds': [
      '660d4e5f6a7b8c9d0e1f2a3b',
      '660d4e5f6a7b8c9d0e1f2a3c',
      '660d4e5f6a7b8c9d0e1f2a3e',
    ],
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "660e5f6a7b8c9d0e1f2a3b4c",
    "userId": "659f1a2b3c4d5e6f7a8b9c0d",
    "name": "Work & Projects",
    "rIds": [
      "660d4e5f6a7b8c9d0e1f2a3b",
      "660d4e5f6a7b8c9d0e1f2a3c",
      "660d4e5f6a7b8c9d0e1f2a3e"
    ],
    "createdAt": "2026-03-18T10:30:00.000Z",
    "updatedAt": "2026-03-18T11:00:00.000Z"
  }
}
```

## Delete Room Filter

`DELETE https://api.weellu.com/api/v1/room-filters/:id`

Deletes a room filter. This does not affect the rooms themselves, only removes the filter grouping.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The filter ID (MongoDB ObjectId)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/room-filters/660e5f6a7b8c9d0e1f2a3b4c" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/room-filters/660e5f6a7b8c9d0e1f2a3b4c",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/room-filters/660e5f6a7b8c9d0e1f2a3b4c',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "660e5f6a7b8c9d0e1f2a3b4c",
    "userId": "659f1a2b3c4d5e6f7a8b9c0d",
    "name": "Work Chats",
    "rIds": [],
    "createdAt": "2026-03-18T10:30:00.000Z",
    "updatedAt": "2026-03-18T11:15:00.000Z"
  }
}
```

# Report System

Submit bug reports, crash logs, and user feedback. Reports are stored with platform information for debugging.

## Create Report

`POST https://api.weellu.com/api/v1/report-system`

Submits a new report to the system. Reports are automatically tagged with the user's platform information.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
content | string | Yes | Human-readable description of the issue
stack | string | No | Stack trace, technical error details, or extra context for moderators (e.g., room/story id)
type | string | Yes | Report category (e.g., `crash`, `bug`, `feedback`, `abuse`, `user_report`, `story_report`)
targetId | string (MongoId) | No | Id of the reported user, for user-generated-content reports. Populated as a user reference in the admin panel

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/report-system" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "App crashes when opening group chat with 100+ members",
    "stack": "Error: RangeError (index): Invalid value\n  at GroupChatWidget.build (group_chat.dart:142)\n  at StatelessElement.build (framework.dart:4748)",
    "type": "crash"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/report-system",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      content: "App crashes when opening group chat with 100+ members",
      stack: "Error: RangeError (index): Invalid value\n  at GroupChatWidget.build (group_chat.dart:142)",
      type: "crash",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/report-system',
  body: {
    'content': 'App crashes when opening group chat with 100+ members',
    'stack': 'Error: RangeError (index): Invalid value...',
    'type': 'crash',
  },
);
```

> Example Response

```json
{
  "_id": "660f6a7b8c9d0e1f2a3b4c5d",
  "content": "App crashes when opening group chat with 100+ members",
  "stack": "Error: RangeError (index): Invalid value\n  at GroupChatWidget.build (group_chat.dart:142)\n  at StatelessElement.build (framework.dart:4748)",
  "type": "crash",
  "platform": "android",
  "uId": "659f1a2b3c4d5e6f7a8b9c0d",
  "createdAt": "2026-03-18T10:30:00.000Z",
  "updatedAt": "2026-03-18T10:30:00.000Z"
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
_id | string | Unique report ID
content | string | Report description
stack | string | Stack trace, technical details, or moderation context
type | string | Report category
targetId | string | Reported user's id (UGC reports only, may be absent)
platform | string | Platform from which the report was submitted (e.g., `android`, `ios`, `web`)
uId | string | ID of the user who submitted the report
createdAt | string | ISO 8601 creation timestamp
updatedAt | string | ISO 8601 last update timestamp

## List Reports

`GET https://api.weellu.com/api/v1/report-system`

Returns all submitted reports, ordered by newest first.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/report-system" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/report-system",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/report-system',
);
```

> Example Response

```json
[
  {
    "_id": "660f6a7b8c9d0e1f2a3b4c5d",
    "content": "App crashes when opening group chat with 100+ members",
    "stack": "Error: RangeError (index): Invalid value...",
    "type": "crash",
    "platform": "android",
    "uId": "659f1a2b3c4d5e6f7a8b9c0d",
    "createdAt": "2026-03-18T10:30:00.000Z",
    "updatedAt": "2026-03-18T10:30:00.000Z"
  },
  {
    "_id": "660f6a7b8c9d0e1f2a3b4c5e",
    "content": "Voice messages not playing on iOS 17",
    "stack": "AVAudioPlayer error: -11800",
    "type": "bug",
    "platform": "ios",
    "uId": "659f2b3c4d5e6f7a8b9c0d1e",
    "createdAt": "2026-03-17T15:00:00.000Z",
    "updatedAt": "2026-03-17T15:00:00.000Z"
  }
]
```

# S3 Upload

Generate presigned URLs for direct file uploads to S3 storage. This is the standard single-file upload mechanism for chat attachments and story media.

## Create Presigned Upload URL

`POST https://api.weellu.com/api/v1/s3`

Creates a presigned S3 URL that allows the client to upload a file directly to cloud storage without routing the binary through the API server.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
uploadType | string | Yes | The upload context. One of: `chat`, `story`
identifier | string | Yes | A UUID v4 identifier for the upload, used as part of the S3 key
fileName | string | Yes | Original file name including extension (e.g., `photo.jpg`)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/s3" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "uploadType": "chat",
    "identifier": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
    "fileName": "vacation_photo.jpg"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/s3",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      uploadType: "chat",
      identifier: "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      fileName: "vacation_photo.jpg",
    }),
  }
);
const data = await response.json();
```

```dart
import 'package:uuid/uuid.dart';

final response = await chopper.post(
  '/api/v1/s3',
  body: {
    'uploadType': 'chat',
    'identifier': const Uuid().v4(),
    'fileName': 'vacation_photo.jpg',
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "presignedUrl": "https://weellu-bucket.s3.amazonaws.com/chat/659f1a2b/f47ac10b-58cc-4372-a567-0e02b2c3d479/vacation_photo.jpg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=...",
    "fileUrl": "https://weellu-bucket.s3.amazonaws.com/chat/659f1a2b/f47ac10b-58cc-4372-a567-0e02b2c3d479/vacation_photo.jpg"
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
presignedUrl | string | Temporary S3 URL for uploading the file via HTTP PUT. Expires after a set time
fileUrl | string | The permanent URL where the file will be accessible after upload

<aside class="notice">
After receiving the presigned URL, upload your file using an HTTP PUT request directly to that URL with the file binary as the request body.
</aside>

# Multipart Upload

For large files, use multipart upload to split files into chunks and upload them in parallel. This provides resumability and better reliability for large media transfers.

## Initiate Multipart Upload

`POST https://api.weellu.com/api/v1/multipart-upload/initiate`

Starts a new multipart upload session. The server creates an S3 multipart upload and returns presigned URLs for each part. The response includes chunk metadata so the client knows exactly which bytes to upload for each part.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
localId | string | Yes | Client-side unique identifier for the upload (used for deduplication and resume)
fileName | string | Yes | Original file name including extension
fileSize | number | Yes | Total file size in bytes. Must be at least 1
mimeType | string | Yes | MIME type of the file (e.g., `video/mp4`, `image/png`)
fileHash | string | Yes | Hash of the complete file for integrity verification
chunks | array | Yes | Array of chunk descriptor objects defining how the file is split
chunks[].partNumber | number | Yes | Sequential part number for this chunk
chunks[].size | number | Yes | Size of this chunk in bytes. Must be at least 1
chunks[].startByte | number | Yes | Starting byte offset in the original file (0-indexed)
chunks[].endByte | number | Yes | Ending byte offset in the original file (0-indexed)
chunks[].md5 | string | Yes | MD5 hash of this chunk for per-part integrity verification
metadata | object | No | Optional key-value metadata to attach to the upload

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/multipart-upload/initiate" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "localId": "upload-abc-123",
    "fileName": "large_video.mp4",
    "fileSize": 52428800,
    "mimeType": "video/mp4",
    "fileHash": "d41d8cd98f00b204e9800998ecf8427e",
    "chunks": [
      {
        "partNumber": 1,
        "size": 10485760,
        "startByte": 0,
        "endByte": 10485759,
        "md5": "a1b2c3d4e5f6a7b8c9d0e1f2"
      },
      {
        "partNumber": 2,
        "size": 10485760,
        "startByte": 10485760,
        "endByte": 20971519,
        "md5": "b2c3d4e5f6a7b8c9d0e1f2a3"
      },
      {
        "partNumber": 3,
        "size": 10485760,
        "startByte": 20971520,
        "endByte": 31457279,
        "md5": "c3d4e5f6a7b8c9d0e1f2a3b4"
      },
      {
        "partNumber": 4,
        "size": 10485760,
        "startByte": 31457280,
        "endByte": 41943039,
        "md5": "d4e5f6a7b8c9d0e1f2a3b4c5"
      },
      {
        "partNumber": 5,
        "size": 10485760,
        "startByte": 41943040,
        "endByte": 52428799,
        "md5": "e5f6a7b8c9d0e1f2a3b4c5d6"
      }
    ],
    "metadata": {
      "roomId": "660d4e5f6a7b8c9d0e1f2a3b",
      "messageType": "video"
    }
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/multipart-upload/initiate",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      localId: "upload-abc-123",
      fileName: "large_video.mp4",
      fileSize: 52428800,
      mimeType: "video/mp4",
      fileHash: "d41d8cd98f00b204e9800998ecf8427e",
      chunks: [
        { partNumber: 1, size: 10485760, startByte: 0, endByte: 10485759, md5: "a1b2c3d4e5f6a7b8c9d0e1f2" },
        { partNumber: 2, size: 10485760, startByte: 10485760, endByte: 20971519, md5: "b2c3d4e5f6a7b8c9d0e1f2a3" },
      ],
      metadata: { roomId: "660d4e5f6a7b8c9d0e1f2a3b" },
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/multipart-upload/initiate',
  body: {
    'localId': 'upload-abc-123',
    'fileName': 'large_video.mp4',
    'fileSize': 52428800,
    'mimeType': 'video/mp4',
    'fileHash': 'd41d8cd98f00b204e9800998ecf8427e',
    'chunks': [
      {'partNumber': 1, 'size': 10485760, 'startByte': 0, 'endByte': 10485759, 'md5': 'a1b2c3d4e5f6a7b8c9d0e1f2'},
      {'partNumber': 2, 'size': 10485760, 'startByte': 10485760, 'endByte': 20971519, 'md5': 'b2c3d4e5f6a7b8c9d0e1f2a3'},
    ],
  },
);
```

> Example Response

```json
{
  "code": 201,
  "data": {
    "sessionId": "660f7b8c9d0e1f2a3b4c5d6e",
    "uploadId": "VXBsb2FkSWQ_example_abc123",
    "totalParts": 5,
    "presignedUrls": [
      {
        "partNumber": 1,
        "url": "https://weellu-bucket.s3.amazonaws.com/uploads/660f7b8c/part1?X-Amz-Algorithm=...",
        "partSize": 10485760,
        "expiresIn": 3600
      },
      {
        "partNumber": 2,
        "url": "https://weellu-bucket.s3.amazonaws.com/uploads/660f7b8c/part2?X-Amz-Algorithm=...",
        "partSize": 10485760,
        "expiresIn": 3600
      }
    ],
    "resumeInfo": null
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
sessionId | string | Server-side session ID for this upload. Used in all subsequent calls
uploadId | string | S3 multipart upload ID
totalParts | number | Total number of parts expected
presignedUrls | array | Array of presigned URL objects for each part
presignedUrls[].partNumber | number | Part number this URL is for
presignedUrls[].url | string | Presigned S3 URL for uploading this part via HTTP PUT
presignedUrls[].partSize | number | Expected size in bytes for this part
presignedUrls[].expiresIn | number | Time in seconds until the presigned URL expires
resumeInfo | object | Present when resuming a previously started upload. Nullable
resumeInfo.uploadedParts | number[] | Part numbers that have already been uploaded
resumeInfo.remainingParts | number[] | Part numbers still pending upload

## Get Presigned URL for Part

`POST https://api.weellu.com/api/v1/multipart-upload/part/presigned-url`

Generates a fresh presigned URL for a specific part. Use this when a previously issued presigned URL has expired or when you need to retry a failed part upload.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
sessionId | string | Yes | The upload session ID (MongoDB ObjectId)
partNumber | number | Yes | The part number to generate a URL for. Range: 1-10000

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/multipart-upload/part/presigned-url" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "sessionId": "660f7b8c9d0e1f2a3b4c5d6e",
    "partNumber": 3
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/multipart-upload/part/presigned-url",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      sessionId: "660f7b8c9d0e1f2a3b4c5d6e",
      partNumber: 3,
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/multipart-upload/part/presigned-url',
  body: {
    'sessionId': '660f7b8c9d0e1f2a3b4c5d6e',
    'partNumber': 3,
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "presignedUrl": "https://weellu-bucket.s3.amazonaws.com/uploads/660f7b8c/part3?X-Amz-Algorithm=...",
    "partNumber": 3,
    "partSize": 10485760,
    "expiresIn": 3600,
    "isUploaded": false,
    "etag": null,
    "message": null
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
presignedUrl | string | Presigned S3 URL for uploading this part. Nullable if part already uploaded
partNumber | number | The requested part number
partSize | number | Expected size in bytes for this part. Nullable
expiresIn | number | Time in seconds until the URL expires. Nullable
isUploaded | boolean | Whether this part has already been successfully uploaded. Nullable
etag | string | The ETag of the part if already uploaded. Nullable
message | string | Informational message (e.g., "Part already uploaded"). Nullable

## Complete Part Upload

`POST https://api.weellu.com/api/v1/multipart-upload/part/complete`

Marks a specific part as successfully uploaded. The client must call this after uploading each part to S3.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
sessionId | string | Yes | The upload session ID (MongoDB ObjectId)
partNumber | number | Yes | The part number that was uploaded. Range: 1-10000
etag | string | Yes | The ETag returned by S3 in the PUT response header
md5 | string | Yes | MD5 hash of the uploaded part for integrity verification

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/multipart-upload/part/complete" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "sessionId": "660f7b8c9d0e1f2a3b4c5d6e",
    "partNumber": 1,
    "etag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
    "md5": "a1b2c3d4e5f6a7b8c9d0e1f2"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/multipart-upload/part/complete",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      sessionId: "660f7b8c9d0e1f2a3b4c5d6e",
      partNumber: 1,
      etag: '"d41d8cd98f00b204e9800998ecf8427e"',
      md5: "a1b2c3d4e5f6a7b8c9d0e1f2",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/multipart-upload/part/complete',
  body: {
    'sessionId': '660f7b8c9d0e1f2a3b4c5d6e',
    'partNumber': 1,
    'etag': '"d41d8cd98f00b204e9800998ecf8427e"',
    'md5': 'a1b2c3d4e5f6a7b8c9d0e1f2',
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "success": true,
    "remainingParts": [2, 3, 4, 5]
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
success | boolean | Whether the part was marked as complete
remainingParts | number[] | Array of part numbers still pending upload

## Complete Multipart Upload

`POST https://api.weellu.com/api/v1/multipart-upload/complete`

Finalizes the multipart upload after all parts have been uploaded and confirmed. This triggers S3 to assemble the parts into the final file.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
sessionId | string | Yes | The upload session ID (MongoDB ObjectId)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/multipart-upload/complete" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "sessionId": "660f7b8c9d0e1f2a3b4c5d6e"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/multipart-upload/complete",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      sessionId: "660f7b8c9d0e1f2a3b4c5d6e",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.post(
  '/api/v1/multipart-upload/complete',
  body: {
    'sessionId': '660f7b8c9d0e1f2a3b4c5d6e',
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "success": true,
    "fileUrl": "https://weellu-bucket.s3.amazonaws.com/uploads/660f7b8c/large_video.mp4",
    "key": "uploads/660f7b8c/large_video.mp4"
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
success | boolean | Whether the upload was finalized successfully
fileUrl | string | The permanent URL where the assembled file is accessible
key | string | The S3 object key for the file

## Abort Multipart Upload

`DELETE https://api.weellu.com/api/v1/multipart-upload/abort/:sessionId`

Aborts an in-progress multipart upload. This cleans up any uploaded parts from S3 and marks the session as aborted. Cannot be used on already-completed uploads.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
sessionId | string | Yes | The upload session ID

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/multipart-upload/abort/660f7b8c9d0e1f2a3b4c5d6e" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/multipart-upload/abort/660f7b8c9d0e1f2a3b4c5d6e",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/multipart-upload/abort/660f7b8c9d0e1f2a3b4c5d6e',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "success": true,
    "message": "Upload aborted successfully"
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
success | boolean | Whether the upload was aborted
message | string | Human-readable confirmation message

# Bot

Manage project bots and bot forms. Bots provide automated question-and-answer trees within project rooms.

## Bot Form

Bot forms associate a bot configuration with a specific chat room and user pairing.

### Create Bot Form

`POST https://api.weellu.com/api/v1/bot-form`

Creates a new bot form linking two users and a room. Accepts an optional image file via multipart form data.

#### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
firstId | string | Yes | First user ID (MongoDB ObjectId)
lastId | string | Yes | Second user ID (MongoDB ObjectId)
roomId | string | Yes | Chat room ID (MongoDB ObjectId)
file | file | No | Optional image file for the bot form

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/bot-form" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -F "firstId=659f1a2b3c4d5e6f7a8b9c0d" \
  -F "lastId=659f2b3c4d5e6f7a8b9c0d1e" \
  -F "roomId=660d4e5f6a7b8c9d0e1f2a3b" \
  -F "file=@bot_avatar.png"
```

```javascript
const formData = new FormData();
formData.append("firstId", "659f1a2b3c4d5e6f7a8b9c0d");
formData.append("lastId", "659f2b3c4d5e6f7a8b9c0d1e");
formData.append("roomId", "660d4e5f6a7b8c9d0e1f2a3b");
formData.append("file", imageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/bot-form",
  {
    method: "POST",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/bot-form'),
);
request.headers['Authorization'] = 'Bearer YOUR_JWT_TOKEN';
request.fields['firstId'] = '659f1a2b3c4d5e6f7a8b9c0d';
request.fields['lastId'] = '659f2b3c4d5e6f7a8b9c0d1e';
request.fields['roomId'] = '660d4e5f6a7b8c9d0e1f2a3b';
request.files.add(await http.MultipartFile.fromPath('file', 'bot_avatar.png'));
final response = await request.send();
```

> Example Response

```json
{
  "_id": "661a2b3c4d5e6f7a8b9c0d1e",
  "firstId": "659f1a2b3c4d5e6f7a8b9c0d",
  "lastId": "659f2b3c4d5e6f7a8b9c0d1e",
  "roomId": "660d4e5f6a7b8c9d0e1f2a3b",
  "image": "https://weellu-bucket.s3.amazonaws.com/bots/661a2b3c/avatar.png",
  "createdAt": "2026-03-18T10:30:00.000Z"
}
```

### Get Bot Form

`GET https://api.weellu.com/api/v1/bot-form`

Retrieves a bot form by peer ID and room ID.

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
peerId | string | Yes | The peer user's ID (MongoDB ObjectId)
roomId | string | Yes | The chat room ID (MongoDB ObjectId)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/bot-form?peerId=659f2b3c4d5e6f7a8b9c0d1e&roomId=660d4e5f6a7b8c9d0e1f2a3b" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/bot-form?peerId=659f2b3c4d5e6f7a8b9c0d1e&roomId=660d4e5f6a7b8c9d0e1f2a3b",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/bot-form',
  parameters: {
    'peerId': '659f2b3c4d5e6f7a8b9c0d1e',
    'roomId': '660d4e5f6a7b8c9d0e1f2a3b',
  },
);
```

> Example Response

```json
{
  "_id": "661a2b3c4d5e6f7a8b9c0d1e",
  "firstId": "659f1a2b3c4d5e6f7a8b9c0d",
  "lastId": "659f2b3c4d5e6f7a8b9c0d1e",
  "roomId": "660d4e5f6a7b8c9d0e1f2a3b",
  "image": "https://weellu-bucket.s3.amazonaws.com/bots/661a2b3c/avatar.png",
  "createdAt": "2026-03-18T10:30:00.000Z"
}
```

### Get Connected Project

`GET https://api.weellu.com/api/v1/bot-form/get-connected-project/:id`

Retrieves the project connected to a bot form.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The bot form ID (MongoDB ObjectId)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/bot-form/get-connected-project/661a2b3c4d5e6f7a8b9c0d1e" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/bot-form/get-connected-project/661a2b3c4d5e6f7a8b9c0d1e",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/bot-form/get-connected-project/661a2b3c4d5e6f7a8b9c0d1e',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "660d4e5f6a7b8c9d0e1f2a3b",
    "name": "Project Alpha",
    "image": "https://weellu-bucket.s3.amazonaws.com/projects/alpha.png"
  }
}
```

### Get My Bot Form

`GET https://api.weellu.com/api/v1/bot-form/get-my-form`

Retrieves the authenticated user's own bot form configuration.

#### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/bot-form/get-my-form" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/bot-form/get-my-form",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/bot-form/get-my-form',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "661a2b3c4d5e6f7a8b9c0d1e",
    "firstId": "659f1a2b3c4d5e6f7a8b9c0d",
    "lastId": "659f2b3c4d5e6f7a8b9c0d1e",
    "roomId": "660d4e5f6a7b8c9d0e1f2a3b",
    "image": "https://weellu-bucket.s3.amazonaws.com/bots/661a2b3c/avatar.png",
    "createdAt": "2026-03-18T10:30:00.000Z"
  }
}
```

### Update Bot Form Image

`PATCH https://api.weellu.com/api/v1/bot-form`

Updates the image of the authenticated user's bot form.

#### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
file | file | Yes | New image file for the bot form

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/bot-form" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -F "file=@new_avatar.png"
```

```javascript
const formData = new FormData();
formData.append("file", newImageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/bot-form",
  {
    method: "PATCH",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'PATCH',
  Uri.parse('https://api.weellu.com/api/v1/bot-form'),
);
request.headers['Authorization'] = 'Bearer YOUR_JWT_TOKEN';
request.files.add(await http.MultipartFile.fromPath('file', 'new_avatar.png'));
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "661a2b3c4d5e6f7a8b9c0d1e",
    "image": "https://weellu-bucket.s3.amazonaws.com/bots/661a2b3c/new_avatar.png"
  }
}
```

## Project Bot

Manage the question-and-answer tree for project bots. Bot questions form a hierarchical menu that users can navigate.

### Get Bot Questions

`GET https://api.weellu.com/api/v1/profile/project/bot/question`

Returns a list of bot questions. Use `isParentIdNull=true` to get top-level menu items.

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
isParentIdNull | string | No | If `"true"`, returns only top-level questions (menu items). Boolean string
userId | string | No | Filter questions by a specific user ID (MongoDB ObjectId)
sort | string | No | Sort field and direction (e.g., `"-createdAt"` for newest first)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/project/bot/question?isParentIdNull=true" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/profile/project/bot/question?isParentIdNull=true",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/profile/project/bot/question',
  parameters: {'isParentIdNull': 'true'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "661b3c4d5e6f7a8b9c0d1e2f",
      "content": "How can I help you?",
      "description": "Main menu for customer support",
      "icon": "help_outline",
      "image": null,
      "parentId": null,
      "createdAt": "2026-03-18T10:30:00.000Z"
    },
    {
      "_id": "661b3c4d5e6f7a8b9c0d1e30",
      "content": "Pricing Information",
      "description": "View our pricing plans",
      "icon": "attach_money",
      "image": "https://weellu-bucket.s3.amazonaws.com/bots/pricing.png",
      "parentId": null,
      "createdAt": "2026-03-18T09:00:00.000Z"
    }
  ]
}
```

### Get Single Bot Question

`GET https://api.weellu.com/api/v1/profile/project/bot/one`

Returns a single bot question by ID, or the root question if no ID is provided.

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | No | The question ID (MongoDB ObjectId). If omitted, returns the root question

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/project/bot/one?id=661b3c4d5e6f7a8b9c0d1e2f" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/profile/project/bot/one?id=661b3c4d5e6f7a8b9c0d1e2f",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/profile/project/bot/one',
  parameters: {'id': '661b3c4d5e6f7a8b9c0d1e2f'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "661b3c4d5e6f7a8b9c0d1e2f",
    "content": "How can I help you?",
    "description": "Main menu for customer support",
    "icon": "help_outline",
    "image": null,
    "parentId": null,
    "createdAt": "2026-03-18T10:30:00.000Z"
  }
}
```

### Create Bot Question

`POST https://api.weellu.com/api/v1/profile/project/bot/question`

Creates a new top-level bot question (menu item). Accepts an optional image file via multipart form data.

#### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
content | string | Yes | The question or menu item text
parentId | string | No | Parent question ID to create a nested question (MongoDB ObjectId)
description | string | No | Additional description or help text
icon | string | No | Icon identifier for the question
file | file | No | Optional image file

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/project/bot/question" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -F "content=What are your business hours?" \
  -F "description=Operating hours and availability" \
  -F "icon=schedule"
```

```javascript
const formData = new FormData();
formData.append("content", "What are your business hours?");
formData.append("description", "Operating hours and availability");
formData.append("icon", "schedule");

const response = await fetch(
  "https://api.weellu.com/api/v1/profile/project/bot/question",
  {
    method: "POST",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/profile/project/bot/question'),
);
request.headers['Authorization'] = 'Bearer YOUR_JWT_TOKEN';
request.fields['content'] = 'What are your business hours?';
request.fields['description'] = 'Operating hours and availability';
request.fields['icon'] = 'schedule';
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "661c4d5e6f7a8b9c0d1e2f3a",
    "content": "What are your business hours?",
    "description": "Operating hours and availability",
    "icon": "schedule",
    "image": null,
    "parentId": null,
    "createdAt": "2026-03-18T11:00:00.000Z"
  }
}
```

### Create Bot Answer

`POST https://api.weellu.com/api/v1/profile/project/bot/answer/:id`

Creates an answer (child node) under an existing bot question.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The parent question ID (MongoDB ObjectId)

#### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
content | string | Yes | The answer text
description | string | No | Additional description
icon | string | No | Icon identifier
file | file | No | Optional image file

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/project/bot/answer/661b3c4d5e6f7a8b9c0d1e2f" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -F "content=We are open Monday-Friday, 9AM-6PM EST"
```

```javascript
const formData = new FormData();
formData.append("content", "We are open Monday-Friday, 9AM-6PM EST");

const response = await fetch(
  "https://api.weellu.com/api/v1/profile/project/bot/answer/661b3c4d5e6f7a8b9c0d1e2f",
  {
    method: "POST",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/profile/project/bot/answer/661b3c4d5e6f7a8b9c0d1e2f'),
);
request.headers['Authorization'] = 'Bearer YOUR_JWT_TOKEN';
request.fields['content'] = 'We are open Monday-Friday, 9AM-6PM EST';
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "661d5e6f7a8b9c0d1e2f3a4b",
    "content": "We are open Monday-Friday, 9AM-6PM EST",
    "description": null,
    "icon": null,
    "image": null,
    "parentId": "661b3c4d5e6f7a8b9c0d1e2f",
    "createdAt": "2026-03-18T11:30:00.000Z"
  }
}
```

### Update Bot Question

`PATCH https://api.weellu.com/api/v1/profile/project/bot/:id`

Updates an existing bot question or answer. Supports updating content, description, status, and image.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The question/answer ID (MongoDB ObjectId)

#### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
content | string | No | Updated question/answer text
description | string | No | Updated description
status | string | No | Bot text status (enum value)
file | file | No | New image file

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/project/bot/661b3c4d5e6f7a8b9c0d1e2f" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -F "content=Updated: How can we assist you today?" \
  -F "description=Updated main menu"
```

```javascript
const formData = new FormData();
formData.append("content", "Updated: How can we assist you today?");
formData.append("description", "Updated main menu");

const response = await fetch(
  "https://api.weellu.com/api/v1/profile/project/bot/661b3c4d5e6f7a8b9c0d1e2f",
  {
    method: "PATCH",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'PATCH',
  Uri.parse('https://api.weellu.com/api/v1/profile/project/bot/661b3c4d5e6f7a8b9c0d1e2f'),
);
request.headers['Authorization'] = 'Bearer YOUR_JWT_TOKEN';
request.fields['content'] = 'Updated: How can we assist you today?';
request.fields['description'] = 'Updated main menu';
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "661b3c4d5e6f7a8b9c0d1e2f",
    "content": "Updated: How can we assist you today?",
    "description": "Updated main menu",
    "icon": "help_outline",
    "image": null,
    "parentId": null,
    "createdAt": "2026-03-18T10:30:00.000Z"
  }
}
```

### Delete Bot Question

`DELETE https://api.weellu.com/api/v1/profile/project/bot/:id`

Deletes a bot question or answer. If the question has child answers, they may also be removed.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The question/answer ID (MongoDB ObjectId)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/profile/project/bot/661b3c4d5e6f7a8b9c0d1e2f" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/profile/project/bot/661b3c4d5e6f7a8b9c0d1e2f",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/profile/project/bot/661b3c4d5e6f7a8b9c0d1e2f',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Question deleted successfully"
  }
}
```

# Marketplace

Browse the Weellu marketplace, discover products, and explore categories.

## Get Market Home

`GET https://api.weellu.com/api/v1/market/home`

Returns the marketplace home page data including featured products, highlights, and promotional content.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/market/home" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/market/home",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/market/home',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "banners": [
      {
        "_id": "661e6f7a8b9c0d1e2f3a4b5c",
        "imageUrl": "https://weellu-bucket.s3.amazonaws.com/banners/spring_sale.jpg",
        "redirectUrl": "https://weellu.com/market/sale",
        "order": 0
      }
    ],
    "highlights": [
      {
        "_id": "661f7a8b9c0d1e2f3a4b5c6d",
        "title": "Latest Electronics",
        "products": [
          {
            "_id": "66207b8c9d0e1f2a3b4c5d6e",
            "title": "Wireless Headphones",
            "price": 79.99,
            "priceUnit": "USD",
            "images": ["https://weellu-bucket.s3.amazonaws.com/products/headphones.jpg"]
          }
        ]
      }
    ],
    "categories": [
      {
        "_id": "66218c9d0e1f2a3b4c5d6e7f",
        "name": "Electronics",
        "icon": "devices",
        "iconColor": "#2196F3"
      }
    ]
  }
}
```

## Get More Highlights

`GET https://api.weellu.com/api/v1/market/more/highlight`

Returns additional highlight sections with pagination for the marketplace.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number for pagination
limit | number | No | Number of highlights per page

> Example Request

```shell
curl "https://api.weellu.com/api/v1/market/more/highlight?page=1&limit=10" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/market/more/highlight?page=1&limit=10",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/market/more/highlight',
  parameters: {'page': 1, 'limit': 10},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "661f7a8b9c0d1e2f3a4b5c6d",
      "title": "Trending Now",
      "products": [
        {
          "_id": "66207b8c9d0e1f2a3b4c5d6e",
          "title": "Smart Watch",
          "price": 199.99,
          "priceUnit": "USD",
          "images": ["https://weellu-bucket.s3.amazonaws.com/products/watch.jpg"]
        }
      ]
    }
  ]
}
```

## Get Categories

`GET https://api.weellu.com/api/v1/market/category`

Returns a list of marketplace product categories.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number for pagination
limit | number | No | Number of categories per page

> Example Request

```shell
curl "https://api.weellu.com/api/v1/market/category" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/market/category",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/market/category',
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "66218c9d0e1f2a3b4c5d6e7f",
      "name": "Electronics",
      "icon": "devices",
      "iconColor": "#2196F3",
      "parentId": null
    },
    {
      "_id": "66219d0e1f2a3b4c5d6e7f8a",
      "name": "Smartphones",
      "icon": "smartphone",
      "iconColor": "#4CAF50",
      "parentId": "66218c9d0e1f2a3b4c5d6e7f"
    },
    {
      "_id": "6621ae1f2a3b4c5d6e7f8a9b",
      "name": "Fashion",
      "icon": "checkroom",
      "iconColor": "#E91E63",
      "parentId": null
    }
  ]
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
_id | string | Category ID
name | string | Category display name
icon | string | Icon identifier
iconColor | string | Hex color code for the icon
parentId | string | Parent category ID for subcategories. Null for top-level categories

## Get Category Attributes

`GET https://api.weellu.com/api/v1/market/attribute/:id`

Returns the attribute definitions for a specific category. Attributes define the filterable properties of products in that category (e.g., screen size for electronics, fabric type for clothing).

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The category ID (MongoDB ObjectId)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/market/attribute/66218c9d0e1f2a3b4c5d6e7f" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/market/attribute/66218c9d0e1f2a3b4c5d6e7f",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/market/attribute/66218c9d0e1f2a3b4c5d6e7f',
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "6622bf2a3b4c5d6e7f8a9b0c",
      "name": "Screen Size",
      "type": "select",
      "options": ["5.5\"", "6.1\"", "6.7\""],
      "categoryId": "66218c9d0e1f2a3b4c5d6e7f",
      "required": true
    },
    {
      "_id": "6622c03b4c5d6e7f8a9b0c1d",
      "name": "Storage",
      "type": "select",
      "options": ["64GB", "128GB", "256GB", "512GB"],
      "categoryId": "66218c9d0e1f2a3b4c5d6e7f",
      "required": true
    },
    {
      "_id": "6622c14c5d6e7f8a9b0c1d2e",
      "name": "Color",
      "type": "text",
      "options": [],
      "categoryId": "66218c9d0e1f2a3b4c5d6e7f",
      "required": false
    }
  ]
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
_id | string | Attribute ID
name | string | Attribute display name
type | string | Input type (e.g., `select`, `text`, `number`)
options | array | Available options for select-type attributes
categoryId | string | The category this attribute belongs to
required | boolean | Whether this attribute is required when creating a product
