# Activity Notifications

Activity notifications keep users informed about events relevant to them, such as story interactions, group additions, mentions, and follows.

All notification endpoints require Bearer token authentication.

## Get Notification Feed

`GET https://api.weellu.com/api/v1/activity-notifications/feed`

Returns a paginated feed of the authenticated user's activity notifications, ordered by most recent first.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number for pagination. Default: `1`
limit | number | No | Number of notifications per page. Default: `20`

> Example Request

```shell
curl "https://api.weellu.com/api/v1/activity-notifications/feed?page=1&limit=20" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/feed?page=1&limit=20",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/activity-notifications/feed',
  parameters: {'page': 1, 'limit': 20},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "data": [
      {
        "_id": "660a1b2c3d4e5f6a7b8c9d0e",
        "userId": "659f1a2b3c4d5e6f7a8b9c0d",
        "type": "story_like",
        "actorUserId": "659f2b3c4d5e6f7a8b9c0d1e",
        "actorUserName": "John Doe",
        "targetId": "660b2c3d4e5f6a7b8c9d0e1f",
        "targetName": "My Story",
        "targetType": "story",
        "message": null,
        "isRead": false,
        "createdAt": "2026-03-18T10:30:00.000Z"
      },
      {
        "_id": "660a1b2c3d4e5f6a7b8c9d0f",
        "userId": "659f1a2b3c4d5e6f7a8b9c0d",
        "type": "group_mention",
        "actorUserId": "659f3c4d5e6f7a8b9c0d1e2f",
        "actorUserName": "Jane Smith",
        "targetId": "660c3d4e5f6a7b8c9d0e1f2a",
        "targetName": "Project Alpha",
        "targetType": "group",
        "message": "mentioned you in a message",
        "isRead": true,
        "createdAt": "2026-03-18T09:15:00.000Z"
      }
    ],
    "pagination": {
      "totalDocs": 42,
      "limit": 20,
      "page": 1,
      "totalPages": 3,
      "hasNextPage": true,
      "hasPrevPage": false
    }
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
data | array | Array of notification objects
data[]._id | string | Unique notification ID
data[].userId | string | ID of the user who owns the notification
data[].type | string | Notification type. One of: `story_like`, `story_reply`, `group_addition`, `project_addition`, `group_mention`, `project_mention`, `single_mention`, `follow`
data[].actorUserId | string | ID of the user who triggered the notification
data[].actorUserName | string | Display name of the actor user
data[].targetId | string | ID of the target entity (story, group, project, or message). Nullable
data[].targetName | string | Name of the target entity. Nullable
data[].targetType | string | Type of the target. One of: `story`, `group`, `project`, `message`. Nullable
data[].message | string | Optional human-readable message. Nullable
data[].isRead | boolean | Whether the notification has been read
data[].createdAt | string | ISO 8601 timestamp when the notification was created
pagination | object | Pagination metadata
pagination.totalDocs | number | Total number of notifications
pagination.limit | number | Items per page
pagination.page | number | Current page number
pagination.totalPages | number | Total number of pages
pagination.hasNextPage | boolean | Whether there is a next page
pagination.hasPrevPage | boolean | Whether there is a previous page

## Get Aggregated Feed

`GET https://api.weellu.com/api/v1/activity-notifications/feed/aggregated`

Returns an aggregated view of notifications, grouping similar notifications together (e.g., "John and 3 others liked your story").

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number for pagination. Default: `1`
limit | number | No | Number of aggregated items per page. Default: `20`

> Example Request

```shell
curl "https://api.weellu.com/api/v1/activity-notifications/feed/aggregated?page=1&limit=20" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/feed/aggregated?page=1&limit=20",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/activity-notifications/feed/aggregated',
  parameters: {'page': 1, 'limit': 20},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "data": [
      {
        "type": "story_like",
        "targetId": "660b2c3d4e5f6a7b8c9d0e1f",
        "targetName": "My Story",
        "targetType": "story",
        "count": 4,
        "actors": [
          {
            "actorUserId": "659f2b3c4d5e6f7a8b9c0d1e",
            "actorUserName": "John Doe"
          },
          {
            "actorUserId": "659f3c4d5e6f7a8b9c0d1e2f",
            "actorUserName": "Jane Smith"
          }
        ],
        "latestAt": "2026-03-18T10:30:00.000Z",
        "isRead": false
      }
    ],
    "pagination": {
      "totalDocs": 15,
      "limit": 20,
      "page": 1,
      "totalPages": 1,
      "hasNextPage": false,
      "hasPrevPage": false
    }
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
data | array | Array of aggregated notification groups
data[].type | string | Notification type shared by the group
data[].targetId | string | ID of the shared target entity
data[].targetName | string | Name of the shared target entity
data[].targetType | string | Type of the shared target
data[].count | number | Total number of notifications in this group
data[].actors | array | List of actor users in this group
data[].actors[].actorUserId | string | ID of an actor user
data[].actors[].actorUserName | string | Display name of an actor user
data[].latestAt | string | ISO 8601 timestamp of the most recent notification in the group
data[].isRead | boolean | Whether all notifications in the group have been read
pagination | object | Pagination metadata (same structure as feed endpoint)

## Get Unread Notifications

`GET https://api.weellu.com/api/v1/activity-notifications/unread`

Returns all unread notifications for the authenticated user.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/activity-notifications/unread" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/unread",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/activity-notifications/unread',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "data": [
      {
        "_id": "660a1b2c3d4e5f6a7b8c9d0e",
        "userId": "659f1a2b3c4d5e6f7a8b9c0d",
        "type": "follow",
        "actorUserId": "659f2b3c4d5e6f7a8b9c0d1e",
        "actorUserName": "John Doe",
        "targetId": null,
        "targetName": null,
        "targetType": null,
        "message": null,
        "isRead": false,
        "createdAt": "2026-03-18T10:30:00.000Z"
      }
    ],
    "count": 1
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
data | array | Array of unread notification objects (same structure as feed)
count | number | Total number of unread notifications returned

## Get Unread Count

`GET https://api.weellu.com/api/v1/activity-notifications/unread/count`

Returns the count of unread notifications for the authenticated user. Useful for displaying badge counts.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/activity-notifications/unread/count" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/unread/count",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/activity-notifications/unread/count',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "unreadCount": 7
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
unreadCount | number | Total number of unread notifications

## Get Notifications by Type

`GET https://api.weellu.com/api/v1/activity-notifications/by-type/:type`

Returns a paginated list of notifications filtered by a specific notification type.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
type | string | Yes | The notification type to filter by. One of: `story_like`, `story_reply`, `group_addition`, `project_addition`, `group_mention`, `project_mention`, `single_mention`, `follow`

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number for pagination. Default: `1`
limit | number | No | Number of notifications per page. Default: `20`

> Example Request

```shell
curl "https://api.weellu.com/api/v1/activity-notifications/by-type/story_like?page=1&limit=10" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/by-type/story_like?page=1&limit=10",
  {
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.get(
  '/api/v1/activity-notifications/by-type/story_like',
  parameters: {'page': 1, 'limit': 10},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "data": [
      {
        "_id": "660a1b2c3d4e5f6a7b8c9d0e",
        "userId": "659f1a2b3c4d5e6f7a8b9c0d",
        "type": "story_like",
        "actorUserId": "659f2b3c4d5e6f7a8b9c0d1e",
        "actorUserName": "John Doe",
        "targetId": "660b2c3d4e5f6a7b8c9d0e1f",
        "targetName": "My Story",
        "targetType": "story",
        "message": null,
        "isRead": false,
        "createdAt": "2026-03-18T10:30:00.000Z"
      }
    ],
    "pagination": {
      "totalDocs": 5,
      "limit": 10,
      "page": 1,
      "totalPages": 1,
      "hasNextPage": false,
      "hasPrevPage": false
    }
  }
}
```

### Response Fields

Same response structure as the [Get Notification Feed](#get-notification-feed) endpoint, but filtered to the specified type.

## Mark Notifications as Read

`PUT https://api.weellu.com/api/v1/activity-notifications/mark-as-read`

Marks one or more notifications as read. You can either pass specific notification IDs or mark all as read.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
notificationIds | string[] | No | Array of notification IDs (MongoDB ObjectIds) to mark as read
markAllAsRead | boolean | No | If `true`, marks all unread notifications as read regardless of `notificationIds`

> Example Request

```shell
curl -X PUT "https://api.weellu.com/api/v1/activity-notifications/mark-as-read" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "notificationIds": [
      "660a1b2c3d4e5f6a7b8c9d0e",
      "660a1b2c3d4e5f6a7b8c9d0f"
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/mark-as-read",
  {
    method: "PUT",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      notificationIds: [
        "660a1b2c3d4e5f6a7b8c9d0e",
        "660a1b2c3d4e5f6a7b8c9d0f",
      ],
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.put(
  '/api/v1/activity-notifications/mark-as-read',
  body: {
    'notificationIds': [
      '660a1b2c3d4e5f6a7b8c9d0e',
      '660a1b2c3d4e5f6a7b8c9d0f',
    ],
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Notifications marked as read"
  }
}
```

## Mark Single Notification as Read

`PUT https://api.weellu.com/api/v1/activity-notifications/:id/read`

Marks a single notification as read and returns the updated notification.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | The notification ID (MongoDB ObjectId)

> Example Request

```shell
curl -X PUT "https://api.weellu.com/api/v1/activity-notifications/660a1b2c3d4e5f6a7b8c9d0e/read" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/660a1b2c3d4e5f6a7b8c9d0e/read",
  {
    method: "PUT",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.put(
  '/api/v1/activity-notifications/660a1b2c3d4e5f6a7b8c9d0e/read',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "data": {
      "_id": "660a1b2c3d4e5f6a7b8c9d0e",
      "userId": "659f1a2b3c4d5e6f7a8b9c0d",
      "type": "story_like",
      "actorUserId": "659f2b3c4d5e6f7a8b9c0d1e",
      "actorUserName": "John Doe",
      "targetId": "660b2c3d4e5f6a7b8c9d0e1f",
      "targetName": "My Story",
      "targetType": "story",
      "message": null,
      "isRead": true,
      "createdAt": "2026-03-18T10:30:00.000Z"
    }
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
data | object | The updated notification object with `isRead` set to `true`

## Delete Notifications

`DELETE https://api.weellu.com/api/v1/activity-notifications`

Deletes specific notifications by their IDs.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
notificationIds | string[] | Yes | Array of notification IDs (MongoDB ObjectIds) to delete

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/activity-notifications" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "notificationIds": [
      "660a1b2c3d4e5f6a7b8c9d0e",
      "660a1b2c3d4e5f6a7b8c9d0f"
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications",
  {
    method: "DELETE",
    headers: {
      Authorization: "Bearer YOUR_JWT_TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      notificationIds: [
        "660a1b2c3d4e5f6a7b8c9d0e",
        "660a1b2c3d4e5f6a7b8c9d0f",
      ],
    }),
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/activity-notifications',
  body: {
    'notificationIds': [
      '660a1b2c3d4e5f6a7b8c9d0e',
      '660a1b2c3d4e5f6a7b8c9d0f',
    ],
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Notifications deleted successfully"
  }
}
```

## Delete All Notifications

`DELETE https://api.weellu.com/api/v1/activity-notifications/all`

Deletes all notifications for the authenticated user. This action is irreversible.

### Parameters

No parameters required.

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/activity-notifications/all" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/all",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/activity-notifications/all',
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "All notifications have been deleted"
  }
}
```

## Delete Notifications by Type and Target

`DELETE https://api.weellu.com/api/v1/activity-notifications/by-type-target`

Deletes all notifications matching a specific type and target combination. Useful for cleaning up notifications related to a deleted entity.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
type | string | Yes | The notification type. One of: `story_like`, `story_reply`, `group_addition`, `project_addition`, `group_mention`, `project_mention`, `single_mention`, `follow`
targetId | string | Yes | The target entity ID (MongoDB ObjectId)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/activity-notifications/by-type-target?type=story_like&targetId=660b2c3d4e5f6a7b8c9d0e1f" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/activity-notifications/by-type-target?type=story_like&targetId=660b2c3d4e5f6a7b8c9d0e1f",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer YOUR_JWT_TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await chopper.delete(
  '/api/v1/activity-notifications/by-type-target',
  parameters: {
    'type': 'story_like',
    'targetId': '660b2c3d4e5f6a7b8c9d0e1f',
  },
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "message": "Deleted notifications: 3",
    "deletedCount": 3
  }
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
message | string | Human-readable confirmation with count
deletedCount | number | Number of notifications that were deleted
