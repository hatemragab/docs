# Tasks

Task endpoints manage tasks within project channels. Tasks are a special message type (`mT: "task"`) with structured metadata in `msgAtt`. All endpoints require authentication via `Authorization: Bearer TOKEN`.

## Create Subtask

`POST https://api.weellu.com/api/v1/channel/:roomId/message`

Creates a full task beneath an existing top-level task. Use the normal multipart task-message endpoint and include `parentTaskId` in the JSON `attachment`. The parent must be an active task in the same project and accessible to the caller. Only one subtask level is supported.

Subtasks have their own task number, assignees, status, dates, checklist, attachments, activity, and chat. They are excluded from the project main task list, counts, category usage, and dashboard. Parent completion is independent. A parent with active subtasks cannot be deleted or transferred; hierarchy tasks cannot be transferred.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
localId | UUID | Yes | Client-generated idempotency identifier (multipart field)
messageType | string | Yes | Must be `task` (multipart field)
content | string | Yes | Task title (multipart field)
attachment.parentTaskId | string | Yes | MongoDB ObjectId of the top-level parent task
attachment | object | Yes | Normal `CreateTaskDto` JSON, including title, status, flag, dates, category, and optional assignees

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message" \
  -H "Authorization: Bearer TOKEN" \
  -F 'localId=550e8400-e29b-41d4-a716-446655440000' \
  -F 'messageType=task' \
  -F 'content=Prepare release notes' \
  -F 'attachment={"title":"Prepare release notes","desc":"","status":"open","flag":"normal","startAt":"2026-08-11T09:00:00.000Z","endAt":"2026-08-12T18:00:00.000Z","category":{"title":"General","color":"#10b981","icon":"984375"},"designation":[],"parentTaskId":"665d4c5e6f7a8b9c0d1e2f3a"}'
```

```javascript
const body = new FormData();
body.append("localId", crypto.randomUUID());
body.append("messageType", "task");
body.append("content", "Prepare release notes");
body.append("attachment", JSON.stringify({
  title: "Prepare release notes",
  desc: "",
  status: "open",
  flag: "normal",
  startAt: "2026-08-11T09:00:00.000Z",
  endAt: "2026-08-12T18:00:00.000Z",
  category: { title: "General", color: "#10b981", icon: "984375" },
  designation: [],
  parentTaskId: "665d4c5e6f7a8b9c0d1e2f3a"
}));
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message",
  { method: "POST", headers: { "Authorization": "Bearer TOKEN" }, body }
);
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/message'),
)
  ..headers['Authorization'] = 'Bearer TOKEN'
  ..fields['localId'] = const Uuid().v4()
  ..fields['messageType'] = 'task'
  ..fields['content'] = 'Prepare release notes'
  ..fields['attachment'] = jsonEncode({
    'title': 'Prepare release notes',
    'desc': '',
    'status': 'open',
    'flag': 'normal',
    'startAt': '2026-08-11T09:00:00.000Z',
    'endAt': '2026-08-12T18:00:00.000Z',
    'category': {'title': 'General', 'color': '#10b981', 'icon': '984375'},
    'designation': [],
    'parentTaskId': '665d4c5e6f7a8b9c0d1e2f3a',
  });
final response = await request.send();
```

## List Subtasks

`GET https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/subtasks`

Returns active direct children ordered by `msgAtt.subtaskOrder`, then creation time.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/subtasks" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/subtasks",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/subtasks'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "665f8a9b0c1d2e3f4a5b6c7d",
      "rId": "665b2a3c4d5e6f7a8b9c0d1e",
      "mT": "task",
      "msgAtt": {
        "title": "Prepare release notes",
        "status": "open",
        "parentTaskId": "665d4c5e6f7a8b9c0d1e2f3a",
        "subtaskOrder": 0
      }
    }
  ]
}
```

## Reorder Subtasks

`PATCH https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/subtasks/reorder`

Replaces the order of all current children. `orderedTaskIds` must contain every active direct child exactly once; duplicates, missing ids, foreign tasks, and extra ids are rejected.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/subtasks/reorder" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"orderedTaskIds":["665f8a9b0c1d2e3f4a5b6c7d","665f8a9b0c1d2e3f4a5b6c7e"]}'
```

```javascript
await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/subtasks/reorder",
  {
    method: "PATCH",
    headers: { "Authorization": "Bearer TOKEN", "Content-Type": "application/json" },
    body: JSON.stringify({ orderedTaskIds: ["665f8a9b0c1d2e3f4a5b6c7d", "665f8a9b0c1d2e3f4a5b6c7e"] })
  }
);
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/subtasks/reorder'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'orderedTaskIds': ['665f8a9b0c1d2e3f4a5b6c7d', '665f8a9b0c1d2e3f4a5b6c7e']}),
);
```

Base URL: `https://api.weellu.com/api/v1/channel`

## Update Task

`PATCH https://api.weellu.com/api/v1/channel/:roomId/task/:taskId`

Updates one or more fields of a task. All body fields are optional -- send only the fields you want to change.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
status | string | No | New task status (body)
flag | string | No | New task priority flag (body)
title | string | No | New task title (body)
desc | string | No | New task description (body)
added | object[] | No | Array of user objects to add as designations (body). Each object must contain an `_id` field with a valid MongoDB ObjectId
deleted | object[] | No | Array of user objects to remove from designations (body). Each object must contain an `_id` field with a valid MongoDB ObjectId
isAllDesignation | boolean | No | If true, designate all project members (body)
categoryId | string | No | MongoDB ObjectId of the task category (body)
location | string | No | Location string for the task (body)

### TaskStatus Enum (status)

Value | Description
----- | -----------
`open` | Newly created task
`reOpen` | Reopened after completion
`onProgress` | Work in progress
`done` | Completed
`revision` | Under review
`canceled` | Cancelled
`concluded` | Finalized/concluded
`reopenRejected` | Reopen request was rejected
`reScheduled` | Rescheduled for later

### TaskFlag Enum (flag)

Value | Description
----- | -----------
`urgent` | Urgent priority
`veryHigh` | Very high priority
`high` | High priority
`normal` | Normal priority
`low` | Low priority

> Example Request (update status and flag)

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "status": "onProgress",
    "flag": "high"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      status: "onProgress",
      flag: "high"
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'status': 'onProgress', 'flag': 'high'}),
);
```

> Example Request (update designations)

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "added": [{"_id": "665a1f2e3b4c5d6e7f8a9b0e"}],
    "deleted": [{"_id": "665a1f2e3b4c5d6e7f8a9b0f"}],
    "categoryId": "665c3b4d5e6f7a8b9c0d1e2f"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      added: [{ _id: "665a1f2e3b4c5d6e7f8a9b0e" }],
      deleted: [{ _id: "665a1f2e3b4c5d6e7f8a9b0f" }],
      categoryId: "665c3b4d5e6f7a8b9c0d1e2f"
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'added': [{'_id': '665a1f2e3b4c5d6e7f8a9b0e'}],
    'deleted': [{'_id': '665a1f2e3b4c5d6e7f8a9b0f'}],
    'categoryId': '665c3b4d5e6f7a8b9c0d1e2f',
  }),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Transfer Task

`PATCH https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/transfer`

Transfers a task to a different project, optionally updating the designation list.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the current project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
toProject | string | Yes | MongoDB ObjectId of the destination project room (body)
designations | object[] | Yes | Array of user objects for designation in the new project. Can be empty array (body). Each object must contain an `_id` field with a valid MongoDB ObjectId

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/transfer" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "toProject": "665b2a3c4d5e6f7a8b9c0d2f",
    "designations": [
      {"_id": "665a1f2e3b4c5d6e7f8a9b0c"},
      {"_id": "665a1f2e3b4c5d6e7f8a9b0d"}
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/transfer",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      toProject: "665b2a3c4d5e6f7a8b9c0d2f",
      designations: [
        { _id: "665a1f2e3b4c5d6e7f8a9b0c" },
        { _id: "665a1f2e3b4c5d6e7f8a9b0d" }
      ]
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/transfer'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'toProject': '665b2a3c4d5e6f7a8b9c0d2f',
    'designations': [
      {'_id': '665a1f2e3b4c5d6e7f8a9b0c'},
      {'_id': '665a1f2e3b4c5d6e7f8a9b0d'},
    ],
  }),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Open Task

`PATCH https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/open`

Marks a task as opened/viewed by the current user. Used for tracking who has acknowledged the task.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/open" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/open",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/open'),
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

## Check Open Task

`GET https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/open-check`

Checks whether the current user has opened/viewed a specific task.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/open-check" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/open-check",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/open-check'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "opened": true,
    "openedAt": "2026-03-18T14:30:00.000Z"
  }
}
```

## Get Task Designation (v2)

`GET https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/designation/v2`

Retrieves the list of users designated (assigned) to a task with their details.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/designation/v2" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/designation/v2",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/designation/v2'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "665a1f2e3b4c5d6e7f8a9b0c",
      "fullName": "John Doe",
      "userImage": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0c.png",
      "username": "johndoe"
    },
    {
      "_id": "665a1f2e3b4c5d6e7f8a9b0d",
      "fullName": "Jane Smith",
      "userImage": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0d.png",
      "username": "janesmith"
    }
  ]
}
```

## Get Task Activity Logs

`GET https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/activity-logs`

Retrieves the activity log for a specific task with pagination. Includes status changes, designation updates, flag changes, etc.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
page | string (number) | No | Page number (default: 1)
limit | string (number) | No | Items per page (default: 20)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/activity-logs?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/activity-logs?page=1&limit=20",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/activity-logs?page=1&limit=20'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "docs": [
      {
        "_id": "665e5d6f7a8b9c0d1e2f3a4b",
        "action": "updateTaskStatus",
        "userId": "665a1f2e3b4c5d6e7f8a9b0c",
        "userName": "John Doe",
        "details": {
          "oldStatus": "open",
          "newStatus": "onProgress"
        },
        "createdAt": "2026-03-18T14:00:00.000Z"
      },
      {
        "_id": "665e5d6f7a8b9c0d1e2f3a4c",
        "action": "addTaskDesignation",
        "userId": "665a1f2e3b4c5d6e7f8a9b0c",
        "userName": "John Doe",
        "details": {
          "addedUser": "Jane Smith"
        },
        "createdAt": "2026-03-18T13:50:00.000Z"
      }
    ],
    "totalDocs": 8,
    "limit": 20,
    "page": 1,
    "totalPages": 1
  }
}
```

## Get Task Message Status

`GET https://api.weellu.com/api/v1/channel/:roomId/task/message/:messageId/status/:type`

Retrieves delivery or read status for a task message, showing which users have seen or received it.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the task message (path parameter)
type | string | Yes | Status type to query (path parameter)
page | string (number) | No | Page number (default: 1)
limit | string (number) | No | Items per page (default: 20)

### MessageStatusType Enum (type)

Value | Description
----- | -----------
`seen` | Users who have read the message
`deliver` | Users who have received the message

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/message/665d4c5e6f7a8b9c0d1e2f3a/status/seen?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/message/665d4c5e6f7a8b9c0d1e2f3a/status/seen?page=1&limit=20",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/message/665d4c5e6f7a8b9c0d1e2f3a/status/seen?page=1&limit=20'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "docs": [
      {
        "_id": "665a1f2e3b4c5d6e7f8a9b0c",
        "fullName": "John Doe",
        "userImage": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0c.png",
        "seenAt": "2026-03-18T14:35:00.000Z"
      },
      {
        "_id": "665a1f2e3b4c5d6e7f8a9b0d",
        "fullName": "Jane Smith",
        "userImage": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0d.png",
        "seenAt": "2026-03-18T14:40:00.000Z"
      }
    ],
    "totalDocs": 2,
    "limit": 20,
    "page": 1,
    "totalPages": 1
  }
}
```

## Update Task IXC Service Order

`PATCH https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/ixc-service-order`

Links an IXC service order to a task. Requires IXC integration to be enabled on the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
ixcServiceOrder | string | Yes | IXC service order identifier (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/ixc-service-order" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ixcServiceOrder": "OS-2026-00451"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/ixc-service-order",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ ixcServiceOrder: "OS-2026-00451" })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/ixc-service-order'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'ixcServiceOrder': 'OS-2026-00451'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Update Task Location

`PATCH https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/location`

Updates the location associated with a task.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
location | string | Yes | Location string (e.g. address or coordinates) (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/location" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"location": "-23.5505,-46.6333"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/location",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ location: "-23.5505,-46.6333" })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/location'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'location': '-23.5505,-46.6333'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

---

# Reminders

Reminder endpoints manage scheduled notifications for tasks within project channels. Reminders are created against a specific task and notify targeted users at calculated intervals. All endpoints require authentication via `Authorization: Bearer TOKEN`.

Base URL: `https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/reminder`

## Create Reminder

`POST https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/reminder`

Creates a new reminder for a task. The reminder will notify the specified users after the given number of minutes.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
targetUserIds | string[] | Yes | Array of user MongoDB ObjectIds to notify. Minimum 1, maximum 50 (body)
minutes | number | Yes | Minutes from now to trigger the reminder. Minimum 1, maximum 525600 (1 year) (body)
customMessage | string | No | Custom notification message. Maximum 500 characters (body)

### Constraints

- `targetUserIds`: 1 to 50 user IDs
- `minutes`: 1 to 525600 (1 minute to 365 days)
- `customMessage`: up to 500 characters

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "targetUserIds": [
      "665a1f2e3b4c5d6e7f8a9b0c",
      "665a1f2e3b4c5d6e7f8a9b0d"
    ],
    "minutes": 60,
    "customMessage": "Please update the task status before end of day."
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      targetUserIds: [
        "665a1f2e3b4c5d6e7f8a9b0c",
        "665a1f2e3b4c5d6e7f8a9b0d"
      ],
      minutes: 60,
      customMessage: "Please update the task status before end of day."
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'targetUserIds': [
      '665a1f2e3b4c5d6e7f8a9b0c',
      '665a1f2e3b4c5d6e7f8a9b0d',
    ],
    'minutes': 60,
    'customMessage': 'Please update the task status before end of day.',
  }),
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665f8a9b0c1d2e3f4a5b6c7d",
    "taskId": "665d4c5e6f7a8b9c0d1e2f3a",
    "roomId": "665b2a3c4d5e6f7a8b9c0d1e",
    "creatorId": "665a1f2e3b4c5d6e7f8a9b0c",
    "targetUserIds": [
      "665a1f2e3b4c5d6e7f8a9b0c",
      "665a1f2e3b4c5d6e7f8a9b0d"
    ],
    "alerts": [
      {
        "_id": "665f8a9b0c1d2e3f4a5b6c7e",
        "scheduledAt": "2026-03-18T15:30:00.000Z",
        "status": "pending"
      }
    ],
    "status": "active",
    "customMessage": "Please update the task status before end of day.",
    "createdAt": "2026-03-18T14:30:00.000Z",
    "updatedAt": "2026-03-18T14:30:00.000Z"
  }
}
```

## List Reminders

`GET https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/reminder`

Retrieves all reminders for a specific task.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "665f8a9b0c1d2e3f4a5b6c7d",
      "taskId": "665d4c5e6f7a8b9c0d1e2f3a",
      "roomId": "665b2a3c4d5e6f7a8b9c0d1e",
      "creatorId": "665a1f2e3b4c5d6e7f8a9b0c",
      "targetUserIds": [
        "665a1f2e3b4c5d6e7f8a9b0c",
        "665a1f2e3b4c5d6e7f8a9b0d"
      ],
      "alerts": [
        {
          "_id": "665f8a9b0c1d2e3f4a5b6c7e",
          "scheduledAt": "2026-03-18T15:30:00.000Z",
          "status": "pending"
        }
      ],
      "status": "active",
      "customMessage": "Please update the task status before end of day.",
      "createdAt": "2026-03-18T14:30:00.000Z",
      "updatedAt": "2026-03-18T14:30:00.000Z"
    }
  ]
}
```

## Get Reminder

`GET https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/reminder/:reminderId`

Retrieves a single reminder by its ID.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
reminderId | string | Yes | MongoDB ObjectId of the reminder (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665f8a9b0c1d2e3f4a5b6c7d",
    "taskId": "665d4c5e6f7a8b9c0d1e2f3a",
    "roomId": "665b2a3c4d5e6f7a8b9c0d1e",
    "creatorId": "665a1f2e3b4c5d6e7f8a9b0c",
    "targetUserIds": [
      "665a1f2e3b4c5d6e7f8a9b0c",
      "665a1f2e3b4c5d6e7f8a9b0d"
    ],
    "alerts": [
      {
        "_id": "665f8a9b0c1d2e3f4a5b6c7e",
        "scheduledAt": "2026-03-18T15:30:00.000Z",
        "status": "sent"
      }
    ],
    "status": "completed",
    "customMessage": "Please update the task status before end of day.",
    "createdAt": "2026-03-18T14:30:00.000Z",
    "updatedAt": "2026-03-18T15:30:05.000Z"
  }
}
```

### ReminderStatus Enum

Value | Description
----- | -----------
`active` | Reminder is scheduled and has pending alerts
`cancelled` | Reminder was cancelled before completion
`completed` | All alerts have been sent

### AlertStatus Enum

Value | Description
----- | -----------
`pending` | Alert is scheduled but not yet sent
`sent` | Alert notification has been delivered
`cancelled` | Alert was cancelled

## Update Reminder

`PATCH https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/reminder/:reminderId`

Updates an existing reminder. You can change the target users and/or the timing.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
reminderId | string | Yes | MongoDB ObjectId of the reminder (path parameter)
targetUserIds | string[] | No | Updated array of user IDs. Minimum 1, maximum 50 (body)
minutes | number | No | Updated minutes from now. Minimum 1, maximum 525600 (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "targetUserIds": [
      "665a1f2e3b4c5d6e7f8a9b0c",
      "665a1f2e3b4c5d6e7f8a9b0d",
      "665a1f2e3b4c5d6e7f8a9b0e"
    ],
    "minutes": 120
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      targetUserIds: [
        "665a1f2e3b4c5d6e7f8a9b0c",
        "665a1f2e3b4c5d6e7f8a9b0d",
        "665a1f2e3b4c5d6e7f8a9b0e"
      ],
      minutes: 120
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'targetUserIds': [
      '665a1f2e3b4c5d6e7f8a9b0c',
      '665a1f2e3b4c5d6e7f8a9b0d',
      '665a1f2e3b4c5d6e7f8a9b0e',
    ],
    'minutes': 120,
  }),
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665f8a9b0c1d2e3f4a5b6c7d",
    "taskId": "665d4c5e6f7a8b9c0d1e2f3a",
    "roomId": "665b2a3c4d5e6f7a8b9c0d1e",
    "creatorId": "665a1f2e3b4c5d6e7f8a9b0c",
    "targetUserIds": [
      "665a1f2e3b4c5d6e7f8a9b0c",
      "665a1f2e3b4c5d6e7f8a9b0d",
      "665a1f2e3b4c5d6e7f8a9b0e"
    ],
    "alerts": [
      {
        "_id": "665f8a9b0c1d2e3f4a5b6c80",
        "scheduledAt": "2026-03-18T16:30:00.000Z",
        "status": "pending"
      }
    ],
    "status": "active",
    "customMessage": "Please update the task status before end of day.",
    "createdAt": "2026-03-18T14:30:00.000Z",
    "updatedAt": "2026-03-18T14:35:00.000Z"
  }
}
```

## Delete Reminder

`DELETE https://api.weellu.com/api/v1/channel/:roomId/task/:taskId/reminder/:reminderId`

Deletes a reminder and cancels any pending alerts.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
taskId | string | Yes | MongoDB ObjectId of the task message (path parameter)
reminderId | string | Yes | MongoDB ObjectId of the reminder (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/task/665d4c5e6f7a8b9c0d1e2f3a/reminder/665f8a9b0c1d2e3f4a5b6c7d'),
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
