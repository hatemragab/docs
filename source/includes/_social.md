# Social

The Social API covers the follow system and user stories. All endpoints require authentication.

## Follow System

The follow system allows users to follow and unfollow each other. The relationship is stored as a `followerId` / `followingId` pair with a unique compound index, so duplicate follows are not possible.

### Follow / Unfollow Toggle

`POST https://api.weellu.com/api/v1/follow/`

Toggles the follow state between the authenticated user and the target user. If not following, this creates a follow. If already following, this unfollows. The target user receives a push notification on new follows.

#### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the target user to follow or unfollow. Must be a valid 24-character hex string.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/follow/" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "665a1a2b3c4d5e006f7a8b02"
  }'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/follow/", {
  method: "POST",
  headers: {
    "Authorization": "Bearer TOKEN",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    id: "665a1a2b3c4d5e006f7a8b02",
  }),
});
const data = await response.json();
```

```dart
final response = await apiService.post(
  '/follow/',
  body: {
    'id': '665a1a2b3c4d5e006f7a8b02',
  },
);
```

> Example Response (followed)

```json
{
  "data": {
    "_id": "665a3c4d5e6f7a008b9c0d01",
    "followerId": "665a1a2b3c4d5e006f7a8b01",
    "followingId": "665a1a2b3c4d5e006f7a8b02",
    "isEnable": true
  }
}
```

> Example Response (unfollowed)

```json
{
  "data": {
    "_id": "665a3c4d5e6f7a008b9c0d01",
    "followerId": "665a1a2b3c4d5e006f7a8b01",
    "followingId": "665a1a2b3c4d5e006f7a8b02",
    "isEnable": false
  }
}
```

### Get My Followers

`GET https://api.weellu.com/api/v1/follow/my-followers`

Returns a paginated list of users who follow the authenticated user.

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number, starting from 1. Defaults to `1`.
limit | number | No | Number of results per page. Defaults to `20`.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/follow/my-followers?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/follow/my-followers?page=1&limit=20",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/follow/my-followers',
  parameters: {'page': '1', 'limit': '20'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "665a3c4d5e6f7a008b9c0d01",
        "followerId": {
          "_id": "665a1a2b3c4d5e006f7a8b03",
          "fullName": "Alice Johnson",
          "userImages": {
            "chatImage": "https://cdn.weellu.com/pic100/avatar3.jpg",
            "smallImage": "https://cdn.weellu.com/si400/avatar3.jpg"
          },
          "bio": "Flutter developer"
        },
        "followingId": "665a1a2b3c4d5e006f7a8b01",
        "isEnable": true
      },
      {
        "_id": "665a3c4d5e6f7a008b9c0d02",
        "followerId": {
          "_id": "665a1a2b3c4d5e006f7a8b04",
          "fullName": "Bob Williams",
          "userImages": {
            "chatImage": "https://cdn.weellu.com/pic100/avatar4.jpg",
            "smallImage": "https://cdn.weellu.com/si400/avatar4.jpg"
          },
          "bio": "Backend engineer"
        },
        "followingId": "665a1a2b3c4d5e006f7a8b01",
        "isEnable": true
      }
    ],
    "totalDocs": 58,
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

### Get My Following

`GET https://api.weellu.com/api/v1/follow/my-following`

Returns a paginated list of users that the authenticated user follows.

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number, starting from 1. Defaults to `1`.
limit | number | No | Number of results per page. Defaults to `20`.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/follow/my-following?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/follow/my-following?page=1&limit=20",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/follow/my-following',
  parameters: {'page': '1', 'limit': '20'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "665a3c4d5e6f7a008b9c0d03",
        "followerId": "665a1a2b3c4d5e006f7a8b01",
        "followingId": {
          "_id": "665a1a2b3c4d5e006f7a8b05",
          "fullName": "Carol Davis",
          "userImages": {
            "chatImage": "https://cdn.weellu.com/pic100/avatar5.jpg",
            "smallImage": "https://cdn.weellu.com/si400/avatar5.jpg"
          },
          "bio": "Product designer"
        },
        "isEnable": true
      }
    ],
    "totalDocs": 12,
    "limit": 20,
    "page": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false,
    "nextPage": null,
    "prevPage": null
  }
}
```

### Remove Follower

`DELETE https://api.weellu.com/api/v1/follow/remove-follower/:followerId`

Removes a specific user from the authenticated user's followers list. This does not block the user -- they can follow again.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
followerId | string | Yes | MongoDB ObjectId of the follower to remove. Must be a valid 24-character hex string.

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/follow/remove-follower/665a1a2b3c4d5e006f7a8b03" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/follow/remove-follower/665a1a2b3c4d5e006f7a8b03",
  {
    method: "DELETE",
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.delete(
  '/follow/remove-follower/665a1a2b3c4d5e006f7a8b03',
);
```

> Example Response

```json
{
  "data": true
}
```

## User Stories

Stories are ephemeral content that expires after 24 hours. They support text, image, video, and voice types with privacy controls.

### Enums

#### StoryType

Value | Description
----- | -----------
`text` | Text-only story with customizable background, font, and alignment
`image` | Image story with optional caption
`video` | Video story with optional caption
`voice` | Voice/audio story
`file` | File attachment story

#### StoryPrivacy

Value | Description
----- | -----------
`public` | Visible to all users
`followers` | Visible only to followers
`following` | Visible only to users you follow
`somePeople` | Visible only to specific users listed in `somePeople`

#### StoryScope

Value | Description
----- | -----------
`all` | Broadcast to all eligible viewers
`followers` | Scoped to followers
`group` | Scoped to a group

#### StoryFontType

Value | Description
----- | -----------
`normal` | Default font style
`italic` | Italic font style
`bold` | Bold font style

#### StoryTextAlign

Value | Description
----- | -----------
`center` | Center-aligned text
`left` | Left-aligned text
`right` | Right-aligned text

### Create Story

`POST https://api.weellu.com/api/v1/user-story/`

Creates a new story. Uses multipart form data to support file uploads (up to 4 files). Stories expire automatically after 24 hours.

#### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
storyType | string | Yes | Type of story: `text`, `image`, `video`, `voice`, or `file`
content | string | Yes | Text content of the story. For non-text types, this is typically a description or transcript.
storyPrivacy | string | No | Privacy level: `public`, `followers`, `following`, or `somePeople`. Defaults to `public`.
scope | string | No | Distribution scope: `all`, `followers`, or `group`. Defaults to `all`.
attachment | string (JSON) | Conditional | JSON string with media attachment metadata. Required for non-text story types. Must be valid JSON.
backgroundColor | string | No | Hex color code for the story background (e.g., `#FF5733`). Used primarily for text stories.
textColor | string | No | Hex color code for the text (e.g., `#FFFFFF`). Used primarily for text stories.
textAlign | string | No | Text alignment: `center`, `left`, or `right`.
caption | string | No | Optional caption overlaid on media stories.
fontType | string | Conditional | Font style: `normal`, `italic`, or `bold`. Required when `storyType` is `text`.
somePeople | string (JSON array) | Conditional | JSON array of MongoDB ObjectId strings. Required when `storyPrivacy` is `somePeople`. Each ID must be a valid 24-character hex string.
exceptPeople | string (JSON array) | No | JSON array of MongoDB ObjectId strings for users to exclude from viewing. Each ID must be a valid 24-character hex string.
file | File | Conditional | Media file(s). Up to 4 files. Required for non-text story types.

> Example Request (Text Story)

```shell
curl -X POST "https://api.weellu.com/api/v1/user-story/" \
  -H "Authorization: Bearer TOKEN" \
  -F "storyType=text" \
  -F "content=Hello world! This is my first story." \
  -F "storyPrivacy=public" \
  -F "backgroundColor=#1A1A2E" \
  -F "textColor=#FFFFFF" \
  -F "textAlign=center" \
  -F "fontType=bold"
```

```javascript
const formData = new FormData();
formData.append("storyType", "text");
formData.append("content", "Hello world! This is my first story.");
formData.append("storyPrivacy", "public");
formData.append("backgroundColor", "#1A1A2E");
formData.append("textColor", "#FFFFFF");
formData.append("textAlign", "center");
formData.append("fontType", "bold");

const response = await fetch("https://api.weellu.com/api/v1/user-story/", {
  method: "POST",
  headers: { "Authorization": "Bearer TOKEN" },
  body: formData,
});
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/user-story/'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.fields['storyType'] = 'text';
request.fields['content'] = 'Hello world! This is my first story.';
request.fields['storyPrivacy'] = 'public';
request.fields['backgroundColor'] = '#1A1A2E';
request.fields['textColor'] = '#FFFFFF';
request.fields['textAlign'] = 'center';
request.fields['fontType'] = 'bold';

final response = await request.send();
```

> Example Request (Image Story)

```shell
curl -X POST "https://api.weellu.com/api/v1/user-story/" \
  -H "Authorization: Bearer TOKEN" \
  -F "storyType=image" \
  -F "content=Check out this view!" \
  -F "storyPrivacy=followers" \
  -F "caption=Sunset at the beach" \
  -F "attachment={\"width\":1080,\"height\":1920}" \
  -F "file=@/path/to/photo.jpg"
```

```javascript
const formData = new FormData();
formData.append("storyType", "image");
formData.append("content", "Check out this view!");
formData.append("storyPrivacy", "followers");
formData.append("caption", "Sunset at the beach");
formData.append("attachment", JSON.stringify({ width: 1080, height: 1920 }));
formData.append("file", imageFile);

const response = await fetch("https://api.weellu.com/api/v1/user-story/", {
  method: "POST",
  headers: { "Authorization": "Bearer TOKEN" },
  body: formData,
});
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/user-story/'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.fields['storyType'] = 'image';
request.fields['content'] = 'Check out this view!';
request.fields['storyPrivacy'] = 'followers';
request.fields['caption'] = 'Sunset at the beach';
request.fields['attachment'] = '{"width":1080,"height":1920}';
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/photo.jpg'));

final response = await request.send();
```

> Example Response

```json
{
  "data": {
    "_id": "665a4e5f6a7b8c009d0e1f23",
    "userId": "665a1a2b3c4d5e006f7a8b01",
    "storyType": "image",
    "storyPrivacy": "followers",
    "fontType": "normal",
    "content": "Check out this view!",
    "caption": "Sunset at the beach",
    "backgroundColor": null,
    "textColor": null,
    "textAlign": null,
    "somePeople": [],
    "att": {
      "width": 1080,
      "height": 1920,
      "url": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f23.jpg",
      "thumbnail": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f23_thumb.jpg"
    },
    "views": [],
    "likes": [],
    "posterId": null,
    "createdAt": "2026-03-18T12:00:00.000Z",
    "expireAt": "2026-03-19T12:00:00.000Z"
  }
}
```

### Get All Stories Feed

`GET https://api.weellu.com/api/v1/user-story/`

Returns the stories feed for the authenticated user. This includes stories from followed users and public stories, respecting privacy settings. Results are paginated.

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number. Defaults to `1`.
limit | number | No | Number of results per page. Defaults to `20`.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/user-story/?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/?page=1&limit=20",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/user-story/',
  parameters: {'page': '1', 'limit': '20'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "665a4e5f6a7b8c009d0e1f23",
        "userId": {
          "_id": "665a1a2b3c4d5e006f7a8b03",
          "fullName": "Alice Johnson",
          "userImages": {
            "chatImage": "https://cdn.weellu.com/pic100/avatar3.jpg"
          }
        },
        "storyType": "image",
        "storyPrivacy": "public",
        "fontType": "normal",
        "content": "Beautiful day!",
        "caption": "Morning vibes",
        "att": {
          "url": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f23.jpg",
          "thumbnail": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f23_thumb.jpg"
        },
        "views": ["665a1a2b3c4d5e006f7a8b01"],
        "likes": [],
        "createdAt": "2026-03-18T08:00:00.000Z",
        "expireAt": "2026-03-19T08:00:00.000Z"
      }
    ],
    "totalDocs": 15,
    "limit": 20,
    "page": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false,
    "nextPage": null,
    "prevPage": null
  }
}
```

### Get My Stories

`GET https://api.weellu.com/api/v1/user-story/me`

Returns all active (non-expired) stories created by the authenticated user.

#### Parameters

No parameters required.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/user-story/me" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/user-story/me", {
  headers: { "Authorization": "Bearer TOKEN" },
});
const data = await response.json();
```

```dart
final response = await apiService.get('/user-story/me');
```

> Example Response

```json
{
  "data": [
    {
      "_id": "665a4e5f6a7b8c009d0e1f23",
      "userId": "665a1a2b3c4d5e006f7a8b01",
      "storyType": "text",
      "storyPrivacy": "public",
      "fontType": "bold",
      "content": "Excited about the new release!",
      "backgroundColor": "#6C63FF",
      "textColor": "#FFFFFF",
      "textAlign": "center",
      "caption": null,
      "att": null,
      "somePeople": [],
      "views": [
        "665a1a2b3c4d5e006f7a8b02",
        "665a1a2b3c4d5e006f7a8b03"
      ],
      "likes": [
        "665a1a2b3c4d5e006f7a8b02"
      ],
      "posterId": null,
      "createdAt": "2026-03-18T10:00:00.000Z",
      "expireAt": "2026-03-19T10:00:00.000Z"
    }
  ]
}
```

### Get User's Stories

`GET https://api.weellu.com/api/v1/user-story/:id/v2`

Returns all active stories for a specific user, respecting privacy settings. If the target user has privacy-restricted stories, only stories visible to the authenticated user are returned.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the user whose stories to retrieve

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number. Defaults to `1`.
limit | number | No | Number of results per page. Defaults to `20`.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/user-story/665a1a2b3c4d5e006f7a8b03/v2?page=1&limit=20" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/665a1a2b3c4d5e006f7a8b03/v2?page=1&limit=20",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/user-story/665a1a2b3c4d5e006f7a8b03/v2',
  parameters: {'page': '1', 'limit': '20'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "665a4e5f6a7b8c009d0e1f24",
        "userId": "665a1a2b3c4d5e006f7a8b03",
        "storyType": "video",
        "storyPrivacy": "public",
        "fontType": "normal",
        "content": "New tutorial video",
        "caption": "Learn Flutter in 5 minutes",
        "att": {
          "url": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f24.mp4",
          "thumbnail": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f24_thumb.jpg",
          "duration": 300
        },
        "views": [],
        "likes": [],
        "createdAt": "2026-03-18T11:00:00.000Z",
        "expireAt": "2026-03-19T11:00:00.000Z"
      }
    ],
    "totalDocs": 3,
    "limit": 20,
    "page": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false,
    "nextPage": null,
    "prevPage": null
  }
}
```

### Forward Story

`POST https://api.weellu.com/api/v1/user-story/forward/:id`

Forwards an existing story as a new story on the authenticated user's profile. The original story's media and content are copied, and the `posterId` field on the new story references the original creator.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the story to forward

#### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
caption | string | No | Optional new caption for the forwarded story
scope | string | No | Distribution scope: `all`, `followers`, or `group`
storyPrivacy | string | No | Privacy level: `public`, `followers`, `following`, or `somePeople`
somePeople | string (JSON array) | Conditional | JSON array of MongoDB ObjectId strings. Required when `storyPrivacy` is `somePeople`. Each ID must be a valid 24-character hex string.
exceptPeople | string (JSON array) | No | JSON array of MongoDB ObjectId strings for users to exclude

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/user-story/forward/665a4e5f6a7b8c009d0e1f23" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "caption": "Look at this amazing story!",
    "storyPrivacy": "public",
    "scope": "all"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/forward/665a4e5f6a7b8c009d0e1f23",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      caption: "Look at this amazing story!",
      storyPrivacy: "public",
      scope: "all",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await apiService.post(
  '/user-story/forward/665a4e5f6a7b8c009d0e1f23',
  body: {
    'caption': 'Look at this amazing story!',
    'storyPrivacy': 'public',
    'scope': 'all',
  },
);
```

> Example Response

```json
{
  "data": {
    "_id": "665a5f6a7b8c9d00e0f1a234",
    "userId": "665a1a2b3c4d5e006f7a8b01",
    "posterId": "665a1a2b3c4d5e006f7a8b03",
    "storyType": "image",
    "storyPrivacy": "public",
    "fontType": "normal",
    "content": "Check out this view!",
    "caption": "Look at this amazing story!",
    "att": {
      "url": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f23.jpg",
      "thumbnail": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f23_thumb.jpg"
    },
    "views": [],
    "likes": [],
    "somePeople": [],
    "createdAt": "2026-03-18T13:00:00.000Z",
    "expireAt": "2026-03-19T13:00:00.000Z"
  }
}
```

### Reply to Story

`POST https://api.weellu.com/api/v1/user-story/:id/reply`

Sends a reply message to a story. The reply is delivered as a chat message to the story creator. Supports text and media replies via multipart form data (up to 2 files).

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the story to reply to

#### Body Parameters (multipart/form-data)

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
content | string | Yes | Reply message text content
localId | string | Yes | Client-generated UUID v4 for deduplication
messageType | string | Yes | Type of reply message: `text`, `image`, `video`, `voice`, or `file`
attachment | string (JSON) | Conditional | JSON string with attachment metadata. Required for non-text message types. Must be valid JSON.
linkAtt | string (JSON) | No | JSON string with link preview metadata. Must be valid JSON.
storyId | string | No | MongoDB ObjectId of the story being replied to (auto-filled from path parameter)
file | File | Conditional | Media file(s). Up to 2 files. Required when `messageType` is `image`, `video`, `voice`, or `file`.

> Example Request (Text Reply)

```shell
curl -X POST "https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23/reply" \
  -H "Authorization: Bearer TOKEN" \
  -F "content=This is amazing! Where was this taken?" \
  -F "localId=a1b2c3d4-e5f6-7890-abcd-ef1234567890" \
  -F "messageType=text"
```

```javascript
const formData = new FormData();
formData.append("content", "This is amazing! Where was this taken?");
formData.append("localId", "a1b2c3d4-e5f6-7890-abcd-ef1234567890");
formData.append("messageType", "text");

const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23/reply",
  {
    method: "POST",
    headers: { "Authorization": "Bearer TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23/reply'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.fields['content'] = 'This is amazing! Where was this taken?';
request.fields['localId'] = 'a1b2c3d4-e5f6-7890-abcd-ef1234567890';
request.fields['messageType'] = 'text';

final response = await request.send();
```

> Example Request (Image Reply)

```shell
curl -X POST "https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23/reply" \
  -H "Authorization: Bearer TOKEN" \
  -F "content=Here is my view!" \
  -F "localId=b2c3d4e5-f6a7-8901-bcde-f12345678901" \
  -F "messageType=image" \
  -F "attachment={\"width\":1080,\"height\":720}" \
  -F "file=@/path/to/reply-photo.jpg"
```

```javascript
const formData = new FormData();
formData.append("content", "Here is my view!");
formData.append("localId", "b2c3d4e5-f6a7-8901-bcde-f12345678901");
formData.append("messageType", "image");
formData.append("attachment", JSON.stringify({ width: 1080, height: 720 }));
formData.append("file", replyImageFile);

const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23/reply",
  {
    method: "POST",
    headers: { "Authorization": "Bearer TOKEN" },
    body: formData,
  }
);
const data = await response.json();
```

```dart
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23/reply'),
);
request.headers['Authorization'] = 'Bearer TOKEN';
request.fields['content'] = 'Here is my view!';
request.fields['localId'] = 'b2c3d4e5-f6a7-8901-bcde-f12345678901';
request.fields['messageType'] = 'image';
request.fields['attachment'] = '{"width":1080,"height":720}';
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/reply-photo.jpg'));

final response = await request.send();
```

> Example Response

```json
{
  "data": {
    "_id": "665a6a7b8c9d0e00f1a2b345",
    "sId": "665a1a2b3c4d5e006f7a8b01",
    "sName": "John Doe",
    "sImg": "https://cdn.weellu.com/pic100/avatar1.jpg",
    "rId": "665a1f3b2e4c8a001d3b7f03",
    "c": "This is amazing! Where was this taken?",
    "mT": "text",
    "lId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "msgAtt": null,
    "createdAt": "2026-03-18T14:00:00.000Z"
  }
}
```

### Repost Story

`POST https://api.weellu.com/api/v1/user-story/:id/repost`

Reposts an existing story to the authenticated user's story feed. Unlike forwarding, a repost links directly to the original story: the response includes `posterId` (the original author) and `posterStoryId` (the original story's `_id`), so clients can open the original post from the repost attribution.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the story to repost

#### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
scope | string | No | Distribution scope: `all`, `followers`, or `group`
storyPrivacy | string | No | Privacy level: `public`, `followers`, `following`, or `somePeople`
somePeople | string (JSON array) | Conditional | JSON array of MongoDB ObjectId strings. Required when `storyPrivacy` is `somePeople`. Each ID must be a valid 24-character hex string.
exceptPeople | string (JSON array) | No | JSON array of MongoDB ObjectId strings for users to exclude

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23/repost" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "storyPrivacy": "followers",
    "scope": "all"
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23/repost",
  {
    method: "POST",
    headers: {
      "Authorization": "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      storyPrivacy: "followers",
      scope: "all",
    }),
  }
);
const data = await response.json();
```

```dart
final response = await apiService.post(
  '/user-story/665a4e5f6a7b8c009d0e1f23/repost',
  body: {
    'storyPrivacy': 'followers',
    'scope': 'all',
  },
);
```

> Example Response

```json
{
  "data": {
    "_id": "665a7b8c9d0e1f00a2b3c456",
    "userId": "665a1a2b3c4d5e006f7a8b01",
    "posterId": "665a1a2b3c4d5e006f7a8b03",
    "posterStoryId": "665a4e5f6a7b8c009d0e1f23",
    "storyType": "image",
    "storyPrivacy": "followers",
    "fontType": "normal",
    "content": "Check out this view!",
    "att": {
      "url": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f23.jpg",
      "thumbnail": "https://cdn.weellu.com/story/665a4e5f6a7b8c009d0e1f23_thumb.jpg"
    },
    "views": [],
    "likes": [],
    "somePeople": [],
    "createdAt": "2026-03-18T15:00:00.000Z",
    "expireAt": "2026-03-19T15:00:00.000Z"
  }
}
```

### Delete Story

`DELETE https://api.weellu.com/api/v1/user-story/:id`

Permanently deletes a story. Only the story creator can delete their own stories.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the story to delete

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/665a4e5f6a7b8c009d0e1f23",
  {
    method: "DELETE",
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.delete(
  '/user-story/665a4e5f6a7b8c009d0e1f23',
);
```

> Example Response

```json
{
  "data": true
}
```

### Add View

`POST https://api.weellu.com/api/v1/user-story/views/:id`

Records that the authenticated user has viewed the specified story. Duplicate views from the same user are ignored.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the story being viewed

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/user-story/views/665a4e5f6a7b8c009d0e1f23" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/views/665a4e5f6a7b8c009d0e1f23",
  {
    method: "POST",
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.post(
  '/user-story/views/665a4e5f6a7b8c009d0e1f23',
);
```

> Example Response

```json
{
  "data": true
}
```

### Toggle Like

`POST https://api.weellu.com/api/v1/user-story/likes/:id`

Toggles the like state on a story for the authenticated user. If not liked, this adds a like. If already liked, this removes it. The story creator receives a push notification on new likes.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the story to like or unlike

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/user-story/likes/665a4e5f6a7b8c009d0e1f23" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/likes/665a4e5f6a7b8c009d0e1f23",
  {
    method: "POST",
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.post(
  '/user-story/likes/665a4e5f6a7b8c009d0e1f23',
);
```

> Example Response (liked)

```json
{
  "data": true
}
```

> Example Response (unliked)

```json
{
  "data": false
}
```

### Get Story Viewers

`GET https://api.weellu.com/api/v1/user-story/views/:id`

Returns a paginated list of users who have viewed the specified story. Only the story creator can access this endpoint.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the story

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number. Defaults to `1`.
limit | number | No | Number of results per page. Defaults to `30`.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/user-story/views/665a4e5f6a7b8c009d0e1f23?page=1&limit=30" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/views/665a4e5f6a7b8c009d0e1f23?page=1&limit=30",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/user-story/views/665a4e5f6a7b8c009d0e1f23',
  parameters: {'page': '1', 'limit': '30'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "665a1a2b3c4d5e006f7a8b02",
        "fullName": "Jane Smith",
        "userImages": {
          "chatImage": "https://cdn.weellu.com/pic100/avatar2.jpg",
          "smallImage": "https://cdn.weellu.com/si400/avatar2.jpg"
        }
      },
      {
        "_id": "665a1a2b3c4d5e006f7a8b03",
        "fullName": "Alice Johnson",
        "userImages": {
          "chatImage": "https://cdn.weellu.com/pic100/avatar3.jpg",
          "smallImage": "https://cdn.weellu.com/si400/avatar3.jpg"
        }
      }
    ],
    "totalDocs": 25,
    "limit": 30,
    "page": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false,
    "nextPage": null,
    "prevPage": null
  }
}
```

### Get Story Likers

`GET https://api.weellu.com/api/v1/user-story/likes/:id`

Returns a paginated list of users who have liked the specified story.

#### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the story

#### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
page | number | No | Page number. Defaults to `1`.
limit | number | No | Number of results per page. Defaults to `30`.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/user-story/likes/665a4e5f6a7b8c009d0e1f23?page=1&limit=30" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/user-story/likes/665a4e5f6a7b8c009d0e1f23?page=1&limit=30",
  {
    headers: { "Authorization": "Bearer TOKEN" },
  }
);
const data = await response.json();
```

```dart
final response = await apiService.get(
  '/user-story/likes/665a4e5f6a7b8c009d0e1f23',
  parameters: {'page': '1', 'limit': '30'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "665a1a2b3c4d5e006f7a8b04",
        "fullName": "Bob Williams",
        "userImages": {
          "chatImage": "https://cdn.weellu.com/pic100/avatar4.jpg",
          "smallImage": "https://cdn.weellu.com/si400/avatar4.jpg"
        }
      }
    ],
    "totalDocs": 1,
    "limit": 30,
    "page": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false,
    "nextPage": null,
    "prevPage": null
  }
}
```
