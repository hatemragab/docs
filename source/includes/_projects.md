# Projects

Project channels are advanced rooms designed for team collaboration, task management, and integrations. All endpoints require authentication via `Authorization: Bearer TOKEN`.

Base URL: `https://api.weellu.com/api/v1/channel`

## Create Project

`POST https://api.weellu.com/api/v1/channel/project`

Creates a new project channel. Supports multipart form data for an optional project image.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
projectName | string | Yes | Name of the project
peerIds | string[] | Yes | Array of user IDs to add as initial members (JSON-encoded array)
file | file | No | Project image (multipart upload)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/project" \
  -H "Authorization: Bearer TOKEN" \
  -F 'projectName=Sprint Q2 2026' \
  -F 'peerIds=["665a1f2e3b4c5d6e7f8a9b0c","665a1f2e3b4c5d6e7f8a9b0d"]' \
  -F 'file=@/path/to/image.png'
```

```javascript
const formData = new FormData();
formData.append("projectName", "Sprint Q2 2026");
formData.append("peerIds", JSON.stringify([
  "665a1f2e3b4c5d6e7f8a9b0c",
  "665a1f2e3b4c5d6e7f8a9b0d"
]));
formData.append("file", imageFile);

const response = await fetch("https://api.weellu.com/api/v1/channel/project", {
  method: "POST",
  headers: { "Authorization": "Bearer TOKEN" },
  body: formData
});
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/channel/project'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.fields['projectName'] = 'Sprint Q2 2026';
request.fields['peerIds'] = jsonEncode([
  '665a1f2e3b4c5d6e7f8a9b0c',
  '665a1f2e3b4c5d6e7f8a9b0d',
]);
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/image.png'));
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665b2a3c4d5e6f7a8b9c0d1e",
    "title": "Sprint Q2 2026",
    "roomType": "p",
    "image": "https://cdn.weellu.com/projects/665b2a3c4d5e6f7a8b9c0d1e.png",
    "createdAt": "2026-03-18T10:30:00.000Z"
  }
}
```

## Get Project Info

`GET https://api.weellu.com/api/v1/channel/:roomId/project/info`

Retrieves full project information including settings, members count, and configuration.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/info" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/info",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/info'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665b2a3c4d5e6f7a8b9c0d1e",
    "title": "Sprint Q2 2026",
    "roomType": "p",
    "image": "https://cdn.weellu.com/projects/665b2a3c4d5e6f7a8b9c0d1e.png",
    "membersCount": 12,
    "createdAt": "2026-03-18T10:30:00.000Z",
    "updatedAt": "2026-03-18T14:00:00.000Z"
  }
}
```

## Get My Project Info

`GET https://api.weellu.com/api/v1/channel/:roomId/project/my-info`

Retrieves the current user's membership information within a project, including their role and settings.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/my-info" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/my-info",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/my-info'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "role": "admin",
    "isMuted": false,
    "joinedAt": "2026-03-18T10:30:00.000Z"
  }
}
```

## Destroy Project

`DELETE https://api.weellu.com/api/v1/channel/:roomId/project/destroy`

Permanently deletes a project channel. Only the super admin can perform this action.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/destroy" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/destroy",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/destroy'),
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

## Update Project Title

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/title`

Updates the project channel title. Requires admin or super admin role.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
title | string | Yes | New project title (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/title" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Sprint Q3 2026"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/title",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ title: "Sprint Q3 2026" })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/title'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'title': 'Sprint Q3 2026'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Update Project Image

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/image`

Updates the project channel image. Multipart form data with an image file is required.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
file | file | Yes | New project image (multipart upload)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/image" \
  -H "Authorization: Bearer TOKEN" \
  -F 'file=@/path/to/new-image.png'
```

```javascript
const formData = new FormData();
formData.append("file", imageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/image",
  {
    method: "PATCH",
    headers: { "Authorization": "Bearer TOKEN" },
    body: formData
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'PATCH',
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/image'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/new-image.png'));
final response = await request.send();
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Get User Super Projects

`GET https://api.weellu.com/api/v1/channel/project/user-super-projects`

Retrieves all projects where the current user is a super admin.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | string | No | Page number (default: 1)
limit | string | No | Items per page (default: 20)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/project/user-super-projects?page=1&limit=10" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/project/user-super-projects?page=1&limit=10",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/project/user-super-projects?page=1&limit=10'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "665b2a3c4d5e6f7a8b9c0d1e",
      "title": "Sprint Q2 2026",
      "roomType": "p",
      "image": "https://cdn.weellu.com/projects/665b2a3c4d5e6f7a8b9c0d1e.png"
    },
    {
      "_id": "665b2a3c4d5e6f7a8b9c0d2f",
      "title": "Design System",
      "roomType": "p",
      "image": "https://cdn.weellu.com/projects/665b2a3c4d5e6f7a8b9c0d2f.png"
    }
  ]
}
```

## List Members (v2)

`GET https://api.weellu.com/api/v1/channel/:roomId/project/members/v2`

Retrieves the project members list with pagination (v2 endpoint).

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
page | string | No | Page number (default: 1)
limit | string | No | Items per page (default: 20)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/v2?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/v2?page=1&limit=20",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/v2?page=1&limit=20'),
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
        "role": "superAdmin"
      },
      {
        "_id": "665a1f2e3b4c5d6e7f8a9b0d",
        "fullName": "Jane Smith",
        "userImage": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0d.png",
        "role": "member"
      }
    ],
    "totalDocs": 12,
    "limit": 20,
    "page": 1,
    "totalPages": 1
  }
}
```

## Add Members

`POST https://api.weellu.com/api/v1/channel/:roomId/project/members`

Adds one or more members to the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
ids | string[] | Yes | Array of user MongoDB ObjectIds to add (body)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ids": ["665a1f2e3b4c5d6e7f8a9b0e", "665a1f2e3b4c5d6e7f8a9b0f"]}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      ids: ["665a1f2e3b4c5d6e7f8a9b0e", "665a1f2e3b4c5d6e7f8a9b0f"]
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'ids': ['665a1f2e3b4c5d6e7f8a9b0e', '665a1f2e3b4c5d6e7f8a9b0f'],
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

## Kick Member

`DELETE https://api.weellu.com/api/v1/channel/:roomId/project/members/:peerId`

Removes a member from the project. Requires admin or super admin role.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
peerId | string | Yes | MongoDB ObjectId of the user to remove (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/665a1f2e3b4c5d6e7f8a9b0d" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/665a1f2e3b4c5d6e7f8a9b0d",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/665a1f2e3b4c5d6e7f8a9b0d'),
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

## Update Member Role

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/members/:peerId/:role`

Changes a member's role within the project. Only admins and super admins can update roles.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
peerId | string | Yes | MongoDB ObjectId of the target user (path parameter)
role | string | Yes | New role for the user (path parameter)

### ProjectRoleType Enum

Value | Description
----- | -----------
`member` | Regular project member
`superAdmin` | Project owner with full control
`assist` | Assistant role with limited admin capabilities
`admin` | Administrator with management permissions

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/665a1f2e3b4c5d6e7f8a9b0d/admin" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/665a1f2e3b4c5d6e7f8a9b0d/admin",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/members/665a1f2e3b4c5d6e7f8a9b0d/admin'),
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

## Transfer Super Admin

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/transfer-super-admin/:peerId`

Transfers super admin ownership of the project to another member. Only the current super admin can perform this action.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
peerId | string | Yes | MongoDB ObjectId of the new super admin (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/transfer-super-admin/665a1f2e3b4c5d6e7f8a9b0d" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/transfer-super-admin/665a1f2e3b4c5d6e7f8a9b0d",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/transfer-super-admin/665a1f2e3b4c5d6e7f8a9b0d'),
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

## Leave Project

`DELETE https://api.weellu.com/api/v1/channel/:roomId/project/leave`

Leaves the project channel. Super admins must transfer ownership before leaving.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/leave" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/leave",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/leave'),
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

## List Categories

`GET https://api.weellu.com/api/v1/channel/:roomId/project/categories`

Retrieves all task categories defined for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "665c3b4d5e6f7a8b9c0d1e2f",
      "title": "Bug Fix",
      "color": "#FF5733",
      "icon": "bug_report"
    },
    {
      "_id": "665c3b4d5e6f7a8b9c0d1e30",
      "title": "Feature",
      "color": "#33FF57",
      "icon": "star"
    }
  ]
}
```

## Create Category

`POST https://api.weellu.com/api/v1/channel/:roomId/project/categories`

Creates a new task category for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
title | string | Yes | Category name
color | string | Yes | Hex color code (e.g. `#FF5733`)
icon | string | Yes | Icon identifier

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Improvement", "color": "#3357FF", "icon": "trending_up"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      title: "Improvement",
      color: "#3357FF",
      icon: "trending_up"
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'title': 'Improvement', 'color': '#3357FF', 'icon': 'trending_up'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665c3b4d5e6f7a8b9c0d1e31",
    "title": "Improvement",
    "color": "#3357FF",
    "icon": "trending_up"
  }
}
```

## Update Category

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/categories/:categoryId`

Updates an existing task category. All fields are optional.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
categoryId | string | Yes | MongoDB ObjectId of the category (path parameter)
title | string | No | New category name
color | string | No | New hex color code
icon | string | No | New icon identifier

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories/665c3b4d5e6f7a8b9c0d1e2f" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Critical Bug", "color": "#FF0000"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories/665c3b4d5e6f7a8b9c0d1e2f",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ title: "Critical Bug", color: "#FF0000" })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories/665c3b4d5e6f7a8b9c0d1e2f'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'title': 'Critical Bug', 'color': '#FF0000'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Delete Category

`DELETE https://api.weellu.com/api/v1/channel/:roomId/project/categories/:categoryId`

Deletes a task category from the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
categoryId | string | Yes | MongoDB ObjectId of the category (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories/665c3b4d5e6f7a8b9c0d1e2f" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories/665c3b4d5e6f7a8b9c0d1e2f",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/categories/665c3b4d5e6f7a8b9c0d1e2f'),
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

## Filter Tasks (v3)

`POST https://api.weellu.com/api/v1/channel/:roomId/project/tasks/v3`

Filters and paginates project tasks with advanced criteria. Uses POST to support complex filter body.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
filter | string | Yes | Tab filter for task ownership context (body)
from | string (ISO 8601) | No | Start date for date range filter (body)
to | string (ISO 8601) | No | End date for date range filter (body, required if `from` is set)
title | string | No | Text search on task title (body)
status | string[] | No | Filter by task status values (body)
flag | string[] | No | Filter by task flag/priority values (body)
categoryIds | string[] | No | Filter by category MongoDB ObjectIds (body)
memberIds | string[] | No | Filter by member/sender MongoDB ObjectIds (body)
withMyMentions | boolean | No | Only show tasks where the current user is mentioned (body)
limit | number | No | Number of results per page (default: 20, must be > 0) (body)
lastUpdateAt | string (ISO 8601) | No | Cursor-based pagination: return tasks updated before this date (body)

### TasksTabFilter Enum (filter)

Value | Description
----- | -----------
`my` | Tasks created by the current user
`assets` | Tasks assigned to the current user
`waiting` | Tasks awaiting action from the current user

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

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tasks/v3" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "filter": "my",
    "from": "2026-03-01T00:00:00.000Z",
    "to": "2026-03-31T23:59:59.999Z",
    "status": ["open", "onProgress"],
    "flag": ["urgent", "high"],
    "categoryIds": ["665c3b4d5e6f7a8b9c0d1e2f"],
    "memberIds": ["665a1f2e3b4c5d6e7f8a9b0c"],
    "limit": 20
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tasks/v3",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      filter: "my",
      from: "2026-03-01T00:00:00.000Z",
      to: "2026-03-31T23:59:59.999Z",
      status: ["open", "onProgress"],
      flag: ["urgent", "high"],
      categoryIds: ["665c3b4d5e6f7a8b9c0d1e2f"],
      memberIds: ["665a1f2e3b4c5d6e7f8a9b0c"],
      limit: 20
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tasks/v3'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'filter': 'my',
    'from': '2026-03-01T00:00:00.000Z',
    'to': '2026-03-31T23:59:59.999Z',
    'status': ['open', 'onProgress'],
    'flag': ['urgent', 'high'],
    'categoryIds': ['665c3b4d5e6f7a8b9c0d1e2f'],
    'memberIds': ['665a1f2e3b4c5d6e7f8a9b0c'],
    'limit': 20,
  }),
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
      "c": "Fix login page crash on iOS",
      "mT": "task",
      "msgAtt": {
        "title": "Fix login page crash on iOS",
        "status": "open",
        "flag": "urgent",
        "category": {
          "id": "665c3b4d5e6f7a8b9c0d1e2f",
          "title": "Bug Fix",
          "color": "#FF5733"
        }
      },
      "mentions": ["665a1f2e3b4c5d6e7f8a9b0d"],
      "createdAt": "2026-03-15T09:30:00.000Z",
      "updatedAt": "2026-03-18T14:00:00.000Z"
    }
  ]
}
```

## Get Project Activity Logs

`GET https://api.weellu.com/api/v1/channel/:roomId/project/activity-logs`

Retrieves the activity log for the entire project with pagination.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
page | string | No | Page number (default: 1)
limit | string | No | Items per page (default: 20)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/activity-logs?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/activity-logs?page=1&limit=20",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/activity-logs?page=1&limit=20'),
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
          "taskTitle": "Fix login page crash",
          "oldStatus": "open",
          "newStatus": "onProgress"
        },
        "createdAt": "2026-03-18T14:05:00.000Z"
      }
    ],
    "totalDocs": 45,
    "limit": 20,
    "page": 1,
    "totalPages": 3
  }
}
```

## List System Messages

`GET https://api.weellu.com/api/v1/channel/:roomId/project/system-messages`

Retrieves all system messages configured for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": [
    {
      "_id": "665f6e7a8b9c0d1e2f3a4b5c",
      "title": "Welcome Message",
      "message": "Welcome to the project! Please read the guidelines."
    }
  ]
}
```

## Create System Message

`POST https://api.weellu.com/api/v1/channel/:roomId/project/system-messages`

Creates a new system message for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
title | string | Yes | System message title (body)
message | string | Yes | System message content (body)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Daily Standup", "message": "Please post your daily update by 10am."}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      title: "Daily Standup",
      message: "Please post your daily update by 10am."
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'title': 'Daily Standup', 'message': 'Please post your daily update by 10am.'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "_id": "665f6e7a8b9c0d1e2f3a4b5d",
    "title": "Daily Standup",
    "message": "Please post your daily update by 10am."
  }
}
```

## Update System Message

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/system-messages/:messageId`

Updates an existing system message.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the system message (path parameter)
title | string | Yes | Updated title (body)
message | string | Yes | Updated message content (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages/665f6e7a8b9c0d1e2f3a4b5d" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Daily Standup Updated", "message": "Please post your daily update by 9:30am."}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages/665f6e7a8b9c0d1e2f3a4b5d",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      title: "Daily Standup Updated",
      message: "Please post your daily update by 9:30am."
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages/665f6e7a8b9c0d1e2f3a4b5d'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'title': 'Daily Standup Updated', 'message': 'Please post your daily update by 9:30am.'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Delete System Message

`DELETE https://api.weellu.com/api/v1/channel/:roomId/project/system-messages/:messageId`

Deletes a single system message.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
messageId | string | Yes | MongoDB ObjectId of the system message (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages/665f6e7a8b9c0d1e2f3a4b5d" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages/665f6e7a8b9c0d1e2f3a4b5d",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages/665f6e7a8b9c0d1e2f3a4b5d'),
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

## Delete All System Messages

`DELETE https://api.weellu.com/api/v1/channel/:roomId/project/system-messages`

Deletes all system messages for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages'),
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

## Toggle System Messages Setting

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/system-messages`

Batch updates the system messages configuration for the project (replaces all messages).

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
messages | object[] | Yes | Array of system message objects (body)
messages[].title | string | Yes | System message title
messages[].message | string | Yes | System message content

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      {"title": "Welcome", "message": "Welcome to the project!"},
      {"title": "Rules", "message": "Follow the coding standards."}
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      messages: [
        { title: "Welcome", message: "Welcome to the project!" },
        { title: "Rules", message: "Follow the coding standards." }
      ]
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/system-messages'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'messages': [
      {'title': 'Welcome', 'message': 'Welcome to the project!'},
      {'title': 'Rules', 'message': 'Follow the coding standards.'},
    ]
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

## Add Menu

`POST https://api.weellu.com/api/v1/channel/:roomId/project/menu/:id`

Adds a menu item to the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
id | string | Yes | MongoDB ObjectId of the menu item to add (path parameter)

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/menu/665f7a8b9c0d1e2f3a4b5c6d" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/menu/665f7a8b9c0d1e2f3a4b5c6d",
  { method: "POST", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/menu/665f7a8b9c0d1e2f3a4b5c6d'),
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

## Delete Menu

`DELETE https://api.weellu.com/api/v1/channel/:roomId/project/menu/delete`

Removes the menu from the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/menu/delete" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/menu/delete",
  { method: "DELETE", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/menu/delete'),
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

## IXC Integration: Set Config

`PATCH https://api.weellu.com/api/v1/channel/:roomId/ixc-integration/config`

Configures IXC integration credentials for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
token | string | Yes | IXC API authentication token (body)
host | string | Yes | IXC API host URL (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/config" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"token": "ixc_api_token_abc123", "host": "https://ixc.example.com"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/config",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      token: "ixc_api_token_abc123",
      host: "https://ixc.example.com"
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/config'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'token': 'ixc_api_token_abc123', 'host': 'https://ixc.example.com'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## IXC Integration: Enable

`PATCH https://api.weellu.com/api/v1/channel/:roomId/ixc-integration/enabled`

Enables IXC integration for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/enabled" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/enabled",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/enabled'),
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

## IXC Integration: Disable

`PATCH https://api.weellu.com/api/v1/channel/:roomId/ixc-integration/disable`

Disables IXC integration for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/disable" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/disable",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/disable'),
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

## IXC Integration: Get Config

`GET https://api.weellu.com/api/v1/channel/:roomId/ixc-integration/getConfig`

Retrieves the current IXC integration configuration.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/getConfig" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/getConfig",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/getConfig'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "token": "ixc_api_token_abc123",
    "host": "https://ixc.example.com",
    "enabled": true
  }
}
```

## IXC Integration: Status

`GET https://api.weellu.com/api/v1/channel/:roomId/ixc-integration/status`

Retrieves the current IXC integration status (enabled/disabled).

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/status" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/status",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/ixc-integration/status'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "enabled": true
  }
}
```

## Fibranossa Integration: Get Config

`GET https://api.weellu.com/api/v1/channel/:roomId/fibranossa-integration/config`

Retrieves the Fibranossa integration configuration for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/config" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/config",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/config'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "token": "fibranossa_token_xyz789",
    "enabled": true
  }
}
```

## Fibranossa Integration: Set Token

`PATCH https://api.weellu.com/api/v1/channel/:roomId/fibranossa-integration/token`

Sets the Fibranossa integration authentication token.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
token | string | Yes | Fibranossa API token (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/token" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"token": "fibranossa_token_xyz789"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/token",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ token: "fibranossa_token_xyz789" })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/token'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'token': 'fibranossa_token_xyz789'}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Fibranossa Integration: Toggle Status

`PATCH https://api.weellu.com/api/v1/channel/:roomId/fibranossa-integration/status`

Toggles the Fibranossa integration on or off.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/status" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/status",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/fibranossa-integration/status'),
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

## Location Sharing: Enable

`PATCH https://api.weellu.com/api/v1/channel/:roomId/location-sharing/enable`

Enables location sharing for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/enable" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/enable",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/enable'),
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

## Location Sharing: Disable

`PATCH https://api.weellu.com/api/v1/channel/:roomId/location-sharing/disable`

Disables location sharing for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/disable" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/disable",
  { method: "PATCH", headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/disable'),
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

## Location Sharing: Status

`GET https://api.weellu.com/api/v1/channel/:roomId/location-sharing/status`

Retrieves the current location sharing status for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/status" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/status",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/location-sharing/status'),
  headers: {'Authorization': 'Bearer TOKEN'},
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "enabled": true
  }
}
```

## Tracking: Start

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/tracking/start`

Starts real-time location tracking for the current user in the project. Joins the user to the project tracking Socket.IO room.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
latitude | number | No | Initial latitude coordinate (body)
longitude | number | No | Initial longitude coordinate (body)
userName | string | No | Display name for tracking (body)
avatar | string | No | Avatar URL for tracking display (body)
start | boolean | No | Explicit start flag (body)
taskId | string | No | Associated task ID (body)
route | object[] | No | Array of initial route points (body)
route[].latitude | number | Yes | Route point latitude
route[].longitude | number | Yes | Route point longitude
route[].timestamp | number | No | Route point Unix timestamp (milliseconds)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/start" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "latitude": -23.5505,
    "longitude": -46.6333,
    "userName": "John Doe",
    "start": true,
    "taskId": "665d4c5e6f7a8b9c0d1e2f3a"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/start",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      latitude: -23.5505,
      longitude: -46.6333,
      userName: "John Doe",
      start: true,
      taskId: "665d4c5e6f7a8b9c0d1e2f3a"
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/start'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'latitude': -23.5505,
    'longitude': -46.6333,
    'userName': 'John Doe',
    'start': true,
    'taskId': '665d4c5e6f7a8b9c0d1e2f3a',
  }),
);
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "tracking": true,
    "startedAt": "2026-03-18T14:30:00.000Z"
  }
}
```

## Tracking: Update

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/tracking/update`

Updates the current user's tracking location.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
latitude | number | Yes | Current latitude coordinate (body)
longitude | number | Yes | Current longitude coordinate (body)
isMoving | boolean | No | Whether the user is currently moving (body)
taskId | string | No | Associated task ID (body)
status | string | No | Current tracking status (body)
route | object[] | No | Array of route points since last update (body)
route[].latitude | number | Yes | Route point latitude
route[].longitude | number | Yes | Route point longitude
route[].timestamp | number | No | Route point Unix timestamp (milliseconds)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/update" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "latitude": -23.5510,
    "longitude": -46.6340,
    "isMoving": true,
    "route": [
      {"latitude": -23.5507, "longitude": -46.6336, "timestamp": 1710772200000},
      {"latitude": -23.5510, "longitude": -46.6340, "timestamp": 1710772260000}
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/update",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      latitude: -23.5510,
      longitude: -46.6340,
      isMoving: true,
      route: [
        { latitude: -23.5507, longitude: -46.6336, timestamp: 1710772200000 },
        { latitude: -23.5510, longitude: -46.6340, timestamp: 1710772260000 }
      ]
    })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/update'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({
    'latitude': -23.5510,
    'longitude': -46.6340,
    'isMoving': true,
    'route': [
      {'latitude': -23.5507, 'longitude': -46.6336, 'timestamp': 1710772200000},
      {'latitude': -23.5510, 'longitude': -46.6340, 'timestamp': 1710772260000},
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

## Tracking: Stop

`PATCH https://api.weellu.com/api/v1/channel/:roomId/project/tracking/stop`

Stops real-time location tracking for the current user. Removes the user from the project tracking Socket.IO room.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)
stop | boolean | No | Explicit stop flag (body)

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/stop" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"stop": true}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/stop",
  {
    method: "PATCH",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ stop: true })
  }
);
const data = await response.json();
```

```dart
final response = await http.patch(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking/stop'),
  headers: {'Authorization': 'Bearer TOKEN', 'Content-Type': 'application/json'},
  body: jsonEncode({'stop': true}),
);
```

> Example Response

```json
{
  "code": 200,
  "data": "Done"
}
```

## Tracking: Get

`GET https://api.weellu.com/api/v1/channel/:roomId/project/tracking`

Retrieves all active tracking sessions for the project.

### Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the project room (path parameter)

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking",
  { headers: { "Authorization": "Bearer TOKEN" } }
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/channel/665b2a3c4d5e6f7a8b9c0d1e/project/tracking'),
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
      "userName": "John Doe",
      "avatar": "https://cdn.weellu.com/users/665a1f2e3b4c5d6e7f8a9b0c.png",
      "latitude": -23.5510,
      "longitude": -46.6340,
      "isMoving": true,
      "taskId": "665d4c5e6f7a8b9c0d1e2f3a",
      "startedAt": "2026-03-18T14:30:00.000Z",
      "updatedAt": "2026-03-18T14:35:00.000Z"
    }
  ]
}
```
