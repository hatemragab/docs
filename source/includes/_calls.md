# Calls

The Calls API provides voice and video calling capabilities powered by Agora and WebRTC. All endpoints require authentication.

## Enums

### CallStatus

Value | Description
----- | -----------
`ring` | Call is currently ringing
`canceled` | Call was canceled by the caller
`timeout` | Call timed out (no answer)
`offline` | Callee was offline
`rejected` | Call was rejected by the callee
`finished` | Call ended normally
`serverRestart` | Call ended due to server restart
`inCall` | Call is currently active

### MeetPlatform

Value | Description
----- | -----------
`webRtc` | Peer-to-peer WebRTC calling
`agora` | Agora cloud-based calling (recommended for group calls)

## Create Call

`POST https://api.weellu.com/api/v1/call/create/:roomId`

Initiates a new voice or video call in the specified room. The server will ring all participants in the room and send push notifications to offline users.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the chat room

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
withVideo | boolean | Yes | `true` for video call, `false` for voice-only call
meetPlatform | string | Yes | Platform to use: `webRtc` or `agora`
payload | any | No | Arbitrary JSON payload passed to callees (e.g., SDP offer for WebRTC)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/call/create/665a1f3b2e4c8a001d3b7f01" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "withVideo": true,
    "meetPlatform": "agora",
    "payload": {"sdp": "offer-data"}
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/create/665a1f3b2e4c8a001d3b7f01",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      withVideo: true,
      meetPlatform: "agora",
      payload: { sdp: "offer-data" },
    }),
  }
);
const data = await response.json();
```

```dart
import 'package:chopper/chopper.dart';

final response = await apiService.post(
  '/call/create/665a1f3b2e4c8a001d3b7f01',
  body: {
    'withVideo': true,
    'meetPlatform': 'agora',
    'payload': {'sdp': 'offer-data'},
  },
);
```

> Example Response

```json
{
  "data": {
    "_id": "665a2b8c4f1e9a002a4c8d12",
    "caller": "665a1a2b3c4d5e006f7a8b01",
    "participants": [
      "665a1a2b3c4d5e006f7a8b01"
    ],
    "callee": "665a1a2b3c4d5e006f7a8b02",
    "callStatus": "ring",
    "roomId": "665a1f3b2e4c8a001d3b7f01",
    "roomType": "s",
    "withVideo": true,
    "meetPlatform": "agora",
    "endAt": null,
    "createdAt": "2026-03-18T10:30:00.000Z"
  }
}
```

## Get Active Ringing Call

`GET https://api.weellu.com/api/v1/call/active`

Returns the currently active ringing call for the authenticated user, if any. Useful on app startup to check if a call is in progress.

### Parameters

No parameters required.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/call/active" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/call/active", {
  headers: { "Authorization": "Bearer TOKEN" },
});
const data = await response.json();
```

```dart
final response = await apiService.get('/call/active');
```

> Example Response (active call exists)

```json
{
  "data": {
    "_id": "665a2b8c4f1e9a002a4c8d12",
    "caller": "665a1a2b3c4d5e006f7a8b01",
    "participants": [
      "665a1a2b3c4d5e006f7a8b01"
    ],
    "callee": "665a1a2b3c4d5e006f7a8b02",
    "callStatus": "ring",
    "roomId": "665a1f3b2e4c8a001d3b7f01",
    "roomType": "s",
    "withVideo": false,
    "meetPlatform": "webRtc",
    "endAt": null,
    "createdAt": "2026-03-18T10:30:00.000Z"
  }
}
```

> Example Response (no active call)

```json
{
  "data": null
}
```

## Get Agora Access Token

`GET https://api.weellu.com/api/v1/call/agora-access/:roomId`

Generates a temporary Agora RTC token for the authenticated user to join the call channel. The token is scoped to the specific room and expires after a set duration.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the chat room with an active Agora call

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/call/agora-access/665a1f3b2e4c8a001d3b7f01" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/agora-access/665a1f3b2e4c8a001d3b7f01",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/call/agora-access/665a1f3b2e4c8a001d3b7f01',
);
```

> Example Response

```json
{
  "data": {
    "token": "006a1b2c3d4e5f...agoraRtcToken",
    "agoraUid": 123456789,
    "channelName": "665a1f3b2e4c8a001d3b7f01"
  }
}
```

## Accept Call

`POST https://api.weellu.com/api/v1/call/accept/:callId`

Accepts an incoming call. This notifies the caller that the call has been accepted and transitions the call status to `inCall`.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
callId | string | Yes | MongoDB ObjectId of the call to accept

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
payload | any | No | Arbitrary JSON payload sent to the caller (e.g., SDP answer for WebRTC)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/call/accept/665a2b8c4f1e9a002a4c8d12" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "payload": {"sdp": "answer-data"}
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/accept/665a2b8c4f1e9a002a4c8d12",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      payload: { sdp: "answer-data" },
    }),
  }
);
const data = await response.json();
```

```dart
final response = await apiService.post(
  '/call/accept/665a2b8c4f1e9a002a4c8d12',
  body: {
    'payload': {'sdp': 'answer-data'},
  },
);
```

> Example Response

```json
{
  "data": {
    "_id": "665a2b8c4f1e9a002a4c8d12",
    "callStatus": "inCall",
    "roomId": "665a1f3b2e4c8a001d3b7f01",
    "withVideo": true,
    "meetPlatform": "agora"
  }
}
```

## Invite Users to Call

`POST https://api.weellu.com/api/v1/call/invite/:callId`

Invites additional users to an ongoing call. Only works for group or project rooms. Each invited user will receive a push notification and ring event.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
callId | string | Yes | MongoDB ObjectId of the active call

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
userIds | string[] | Yes | Array of MongoDB ObjectId strings of users to invite. Must be non-empty. Each element must be a valid string.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/call/invite/665a2b8c4f1e9a002a4c8d12" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "userIds": [
      "665a1a2b3c4d5e006f7a8b03",
      "665a1a2b3c4d5e006f7a8b04"
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/invite/665a2b8c4f1e9a002a4c8d12",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      userIds: [
        "665a1a2b3c4d5e006f7a8b03",
        "665a1a2b3c4d5e006f7a8b04",
      ],
    }),
  }
);
const data = await response.json();
```

```dart
final response = await apiService.post(
  '/call/invite/665a2b8c4f1e9a002a4c8d12',
  body: {
    'userIds': [
      '665a1a2b3c4d5e006f7a8b03',
      '665a1a2b3c4d5e006f7a8b04',
    ],
  },
);
```

> Example Response

```json
{
  "data": true
}
```

## End Call

`POST https://api.weellu.com/api/v1/call/end/v2/:callId`

Ends an active call. Can be called by any participant. If the call is still ringing, this cancels it. If in progress, this finishes it. The server broadcasts the end event to all participants via Socket.IO.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
callId | string | Yes | MongoDB ObjectId of the call to end

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
endAt | string | No | ISO 8601 datetime string marking when the call ended. If omitted, the server uses the current time. Must be a valid ISO 8601 date (e.g., `2026-03-18T10:45:00.000Z`).

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/call/end/v2/665a2b8c4f1e9a002a4c8d12" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "endAt": "2026-03-18T10:45:00.000Z"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/end/v2/665a2b8c4f1e9a002a4c8d12",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      endAt: "2026-03-18T10:45:00.000Z",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await apiService.post(
  '/call/end/v2/665a2b8c4f1e9a002a4c8d12',
  body: {
    'endAt': '2026-03-18T10:45:00.000Z',
  },
);
```

> Example Response

```json
{
  "data": {
    "_id": "665a2b8c4f1e9a002a4c8d12",
    "callStatus": "finished",
    "endAt": "2026-03-18T10:45:00.000Z"
  }
}
```

## Get Call Participants

`GET https://api.weellu.com/api/v1/call/participants/:callId`

Returns the list of participants currently in the specified call with their Agora UIDs and profile information.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
callId | string | Yes | MongoDB ObjectId of the call

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/call/participants/665a2b8c4f1e9a002a4c8d12" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/participants/665a2b8c4f1e9a002a4c8d12",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/call/participants/665a2b8c4f1e9a002a4c8d12',
);
```

> Example Response

```json
{
  "data": [
    {
      "agoraUid": 123456789,
      "odooId": "665a1a2b3c4d5e006f7a8b01",
      "name": "John Doe",
      "image": "https://cdn.weellu.com/pic100/avatar1.jpg",
      "withVideo": true,
      "roomId": "665a1f3b2e4c8a001d3b7f01",
      "callId": "665a2b8c4f1e9a002a4c8d12"
    },
    {
      "agoraUid": 987654321,
      "odooId": "665a1a2b3c4d5e006f7a8b02",
      "name": "Jane Smith",
      "image": "https://cdn.weellu.com/pic100/avatar2.jpg",
      "withVideo": false,
      "roomId": "665a1f3b2e4c8a001d3b7f01",
      "callId": "665a2b8c4f1e9a002a4c8d12"
    }
  ]
}
```

## Get Call History

`GET https://api.weellu.com/api/v1/call/history`

Returns the paginated call history for the authenticated user, sorted by most recent. Each entry includes call status, duration, participants, and whether it was a video call.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number, starting from 1. Defaults to `1`.
limit | number | No | Number of results per page. Defaults to `30`. Max `100`. Values outside 1-100 are clamped to `30`.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/call/history?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/history?page=1&limit=20",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/call/history',
  parameters: {'page': '1', 'limit': '20'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "665a2b8c4f1e9a002a4c8d12",
        "caller": {
          "_id": "665a1a2b3c4d5e006f7a8b01",
          "fullName": "John Doe",
          "userImages": {
            "chatImage": "https://cdn.weellu.com/pic100/avatar1.jpg"
          }
        },
        "participants": [
          "665a1a2b3c4d5e006f7a8b01",
          "665a1a2b3c4d5e006f7a8b02"
        ],
        "callee": "665a1a2b3c4d5e006f7a8b02",
        "callStatus": "finished",
        "roomId": "665a1f3b2e4c8a001d3b7f01",
        "roomType": "s",
        "withVideo": true,
        "meetPlatform": "agora",
        "endAt": "2026-03-18T10:45:00.000Z",
        "createdAt": "2026-03-18T10:30:00.000Z"
      },
      {
        "_id": "665a2b8c4f1e9a002a4c8d13",
        "caller": {
          "_id": "665a1a2b3c4d5e006f7a8b03",
          "fullName": "Alice Johnson",
          "userImages": {
            "chatImage": "https://cdn.weellu.com/pic100/avatar3.jpg"
          }
        },
        "participants": [
          "665a1a2b3c4d5e006f7a8b03"
        ],
        "callee": "665a1a2b3c4d5e006f7a8b01",
        "callStatus": "rejected",
        "roomId": "665a1f3b2e4c8a001d3b7f02",
        "roomType": "s",
        "withVideo": false,
        "meetPlatform": "webRtc",
        "endAt": null,
        "createdAt": "2026-03-18T09:15:00.000Z"
      }
    ],
    "totalDocs": 42,
    "limit": 20,
    "page": 1,
    "totalPages": 3,
    "hasNextPage": true,
    "hasPrevPage": false,
    "nextPage": 2,
    "prevPage": null
  }
}
```

## Clear All Call History

`DELETE https://api.weellu.com/api/v1/call/history/clear`

Clears the entire call history for the authenticated user. This is a soft delete -- the user's ID is added to `deleteFrom` on each history entry, so other participants can still see their history.

### Parameters

No parameters required.

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/call/history/clear" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/history/clear",
  {
    method: "DELETE",
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.delete('/call/history/clear');
```

> Example Response

```json
{
  "data": true
}
```

## Delete Single History Entry

`DELETE https://api.weellu.com/api/v1/call/history/clear/:id`

Deletes a single call history entry for the authenticated user. This is a soft delete scoped to the requesting user only.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the call history entry to delete

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/call/history/clear/665a2b8c4f1e9a002a4c8d12" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/call/history/clear/665a2b8c4f1e9a002a4c8d12",
  {
    method: "DELETE",
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.delete(
  '/call/history/clear/665a2b8c4f1e9a002a4c8d12',
);
```

> Example Response

```json
{
  "data": true
}
```
