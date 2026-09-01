---
title: Weellu API Reference

language_tabs:
  - shell: cURL
  - javascript: JavaScript
  - dart: Dart

toc_footers:
  - <a href='https://weellu.com'>Weellu Platform</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - authentication
  - api_tokens
  - profile
  - business
  - channels
  - groups
  - broadcasts
  - projects
  - messages
  - e2e
  - chat_task_mentions
  - tasks
  - calls
  - social
  - notifications
  - services
  - admin
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Weellu API - Real-time communication platform
---

# Introduction

Welcome to the **Weellu API**! You can use this API to access Weellu platform endpoints for messaging, channels, voice/video calls, social features, and more.

All API endpoints are served from:

`https://api.weellu.com/api/v1/`

We provide code examples in **cURL**, **JavaScript**, and **Dart**. You can view them in the dark area to the right, and switch between languages using the tabs above.

## Authentication Overview

> All authenticated requests require this header:

```shell
curl "https://api.weellu.com/api/v1/endpoint" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
fetch('https://api.weellu.com/api/v1/endpoint', {
  headers: {
    'Authorization': 'Bearer YOUR_TOKEN'
  }
});
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/endpoint'),
  headers: {'Authorization': 'Bearer $token'},
);
```

Weellu uses **Bearer tokens** for authentication. There are two types of tokens:

Type | How to Obtain | Use Case
---- | ------------- | --------
**JWT Token** | `POST /auth/login` | Full access to all user endpoints
**API Token** | Request via Settings > API | Integration access (requires admin approval)

Include your token in all API requests in a header:

`Authorization: Bearer YOUR_TOKEN`

<aside class="notice">
Replace <code>YOUR_TOKEN</code> with your JWT access token or approved API token.
</aside>

## Standard Response Format

> All successful responses follow this structure:

```json
{
  "code": 2000,
  "data": { },
  "msg": null
}
```

All API responses are wrapped in a standard envelope:

Field | Type | Description
----- | ---- | -----------
code | integer | Status code: `2000` (success), `2100` (verify email sent), `4000` (failed), `5500` (client needs update)
data | any | The response payload (object, array, string, or null)
msg | string/null | Optional message

## Pagination

> Paginated responses follow this structure:

```json
{
  "code": 2000,
  "data": {
    "docs": [ ],
    "totalDocs": 150,
    "totalPages": 8,
    "page": 1,
    "limit": 20,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "msg": null
}
```

Paginated endpoints accept these query parameters:

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
page | integer | 1 | Page number (1-indexed)
limit | integer | 20 | Results per page (max 100)
lastId | string | - | MongoDB ID for cursor-based pagination
pType | string | "page" | Pagination type: `page` or `id`
createSort | string | "desc" | Sort order: `asc` or `desc`

## Common Field Abbreviations

The API uses short field names in responses for bandwidth efficiency:

Abbreviation | Full Name | Context
------------ | --------- | -------
rId | Room ID | Messages, room members
sId | Sender ID | Messages
uId | User ID | Room members
pId | Peer ID | Single chats
cId | Creator ID | Room settings
mT | Message Type | Messages
c | Content | Message text
t | Title | Room title
img | Image | Thumbnails/avatars
rT | Room Type | Room classification
uC | Unread Count | Room members
sAt | Seen At | Message status
dAt | Delivered At | Message status
lId | Local ID | Client-generated message ID
isD | Is Deleted | Soft delete flag
isA | Is Archived | Archive flag
isM | Is Muted | Mute flag
isPin | Is Pinned | Pin flag

## Room Types

Type | Code | Description
---- | ---- | -----------
Single | `s` | One-on-one direct chat
Group | `g` | Group chat with multiple members
Broadcast | `b` | One-to-many broadcast channel
Project | `p` | Project management channel with tasks
Channel | `c` | Public channel
System | `sy` | System notifications

## Platform Values

Value | Description
----- | -----------
Android | Android mobile app
Ios | iOS mobile app
Web | Web browser client
Linux | Linux desktop client
Mac | macOS desktop client
Windows | Windows desktop client
Other | Other clients
