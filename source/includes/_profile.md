# Profile

Profile management endpoints for the authenticated user. All endpoints require a valid `Authorization: Bearer TOKEN` header.

## Enums Reference

### MediaAutoDownloadOption

Value | Description
----- | -----------
`images` | Auto-download images
`videos` | Auto-download videos
`files` | Auto-download files
`voices` | Auto-download voice messages

### UserRole

Value | Description
----- | -----------
`assistant` | Virtual assistant role
`admin` | Admin role
`superAdmin` | Super admin role
`business` | Business role
`prime` | Prime user role

### BusinessStatus

Value | Description
----- | -----------
`active` | Business is active
`notActive` | Business is deactivated
`none` | No business profile

## Get My Profile

`GET https://api.weellu.com/api/v1/profile/`

Returns the authenticated user's profile with follower/following counts, active story count, and current device info.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/", {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
import 'package:chopper/chopper.dart';

final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "followersCount": 142,
    "followingCount": 87,
    "postsCount": 3,
    "newUpdateNote": null,
    "me": {
      "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
      "email": "john@example.com",
      "fullName": "John Doe",
      "fullNameEn": "John Doe",
      "userName": "johndoe",
      "userType": "normal",
      "businessStatus": "none",
      "roles": [],
      "userImages": {
        "originalImage": "https://cdn.weellu.com/users/64f1a2b3/original.jpg",
        "chatImage": "https://cdn.weellu.com/users/64f1a2b3/chat.jpg",
        "smallImage": "https://cdn.weellu.com/users/64f1a2b3/small.jpg"
      },
      "mediaAutoDownload": {
        "wifi": ["images", "videos", "files", "voices"],
        "mobile": ["images", "videos", "files", "voices"]
      }
    },
    "currentDevice": {
      "_id": "64f1a2b3c4d5e6f7a8b9c0d2",
      "platform": "android",
      "lang": "en",
      "clintVersion": "2.1.0+45"
    }
  }
}
```

## Get Peer Profile

`GET https://api.weellu.com/api/v1/profile/:id`

Returns another user's public profile with follower/following counts and whether you follow them.

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the target user

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/64f1a2b3c4d5e6f7a8b9c0d1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const userId = "64f1a2b3c4d5e6f7a8b9c0d1";
const response = await fetch(`https://api.weellu.com/api/v1/profile/${userId}`, {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final userId = '64f1a2b3c4d5e6f7a8b9c0d1';
final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/$userId'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "user": {
      "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
      "fullName": "Jane Smith",
      "email": "jane@example.com",
      "userType": "normal",
      "userImages": {
        "originalImage": "https://cdn.weellu.com/users/64f1a2b3/original.jpg",
        "chatImage": "https://cdn.weellu.com/users/64f1a2b3/chat.jpg",
        "smallImage": "https://cdn.weellu.com/users/64f1a2b3/small.jpg"
      }
    },
    "isFollowing": true,
    "followersCount": 312,
    "followingCount": 156,
    "postsCount": 7
  }
}
```

## Get App Configuration

`GET https://api.weellu.com/api/v1/profile/app-config`

Returns the application configuration settings.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/app-config" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/app-config", {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/app-config'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "maxGroupMembers": 512,
    "maxBroadcastMembers": 512,
    "maxProjectMembers": 5000,
    "maxFileSize": 104857600,
    "supportedMediaTypes": ["image/jpeg", "image/png", "video/mp4", "audio/mpeg"]
  }
}
```

## Search Users

`GET https://api.weellu.com/api/v1/profile/users/v2`

Search for users by name. Excludes blocked users. When `roomId` is provided, results are scoped to that room's members (useful for @mention autocomplete).

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
search | string | No | Search term to match against fullName or fullNameEn
limit | number | No | Number of results per page (default: 20, must be > 0)
page | number | No | Page number (default: 1, must be > 0)
roomId | string | No | MongoDB ObjectId of a room to scope results to room members only

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/users/v2?search=john&limit=10&page=1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const params = new URLSearchParams({
  search: "john",
  limit: "10",
  page: "1"
});
const response = await fetch(`https://api.weellu.com/api/v1/profile/users/v2?${params}`, {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/users/v2?search=john&limit=10&page=1'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0d1",
        "fullName": "John Doe",
        "fullNameEn": "John Doe",
        "userImages": {
          "originalImage": "https://cdn.weellu.com/users/64f1a2b3/original.jpg",
          "chatImage": "https://cdn.weellu.com/users/64f1a2b3/chat.jpg",
          "smallImage": "https://cdn.weellu.com/users/64f1a2b3/small.jpg"
        }
      }
    ],
    "totalDocs": 1,
    "totalPages": 1,
    "page": 1,
    "hasNextPage": false
  }
}
```

## Update Name

`PATCH https://api.weellu.com/api/v1/profile/name`

Updates the authenticated user's display name. Also propagates the change to all room memberships.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
fullName | string | Yes | New display name. Must not be empty. Maximum 50 characters.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/name" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"fullName": "John Smith"}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/name", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ fullName: "John Smith" })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/name'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({'fullName': 'John Smith'}),
);
```

> Example Response

```json
{
  "data": "Name has been changed successfully"
}
```

## Update Username

`PATCH https://api.weellu.com/api/v1/profile/user-name`

Updates the authenticated user's unique username. The username is stored in lowercase.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
userName | string | Yes | New username. Must be 3-20 characters, alphanumeric and underscores only (`/^[a-zA-Z0-9_]+$/`). Must be unique across all users.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/user-name" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"userName": "john_doe_99"}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/user-name", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ userName: "john_doe_99" })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/user-name'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({'userName': 'john_doe_99'}),
);
```

> Example Response

```json
{
  "data": "Done"
}
```

## Update Email

`PATCH https://api.weellu.com/api/v1/profile/email`

Updates the authenticated user's email address. Requires current password for verification.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
newEmail | string | Yes | New email address. Must be a valid email format. Maximum 200 characters. Must not already be in use.
currentPassword | string | Yes | Current account password for verification. Must not be empty.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/email" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"newEmail": "newemail@example.com", "currentPassword": "MyP@ssw0rd"}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/email", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    newEmail: "newemail@example.com",
    currentPassword: "MyP@ssw0rd"
  })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/email'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'newEmail': 'newemail@example.com',
    'currentPassword': 'MyP@ssw0rd',
  }),
);
```

> Example Response

```json
{
  "data": "Email updated successfully."
}
```

## Update Password

`PATCH https://api.weellu.com/api/v1/profile/password`

Changes the authenticated user's password. Optionally logs out all other devices.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
oldPassword | string | Yes | Current password for verification. Must not be empty.
newPassword | string | Yes | New password. Minimum 6 characters, maximum 255 characters.
logoutFromAll | boolean | Yes | If `true`, all other device sessions are terminated.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/password" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"oldPassword": "OldP@ss123", "newPassword": "NewStr0ng!Pass", "logoutFromAll": false}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/password", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    oldPassword: "OldP@ss123",
    newPassword: "NewStr0ng!Pass",
    logoutFromAll: false
  })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/password'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'oldPassword': 'OldP@ss123',
    'newPassword': 'NewStr0ng!Pass',
    'logoutFromAll': false,
  }),
);
```

> Example Response

```json
{
  "data": "Password changed successfully"
}
```

## Upload Profile Image

`PATCH https://api.weellu.com/api/v1/profile/image`

Uploads a new profile image. The image is processed and stored in multiple sizes. Also propagates to all room memberships.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
file | file | Yes | Image file (multipart/form-data). Supported formats: JPEG, PNG, WebP.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/image" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@/path/to/photo.jpg"
```

```javascript
const formData = new FormData();
formData.append("file", fileInput.files[0]);

const response = await fetch("https://api.weellu.com/api/v1/profile/image", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  },
  body: formData
});
const data = await response.json();
```

```dart
import 'package:http/http.dart' as http;

final request = http.MultipartRequest(
  'PATCH',
  Uri.parse('https://api.weellu.com/api/v1/profile/image'),
);
request.headers['Authorization'] = 'Bearer $token';
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/photo.jpg'));
final response = await request.send();
```

> Example Response

```json
{
  "data": "Image has been changed successfully"
}
```

## Update Language

`PATCH https://api.weellu.com/api/v1/profile/lang`

Updates the preferred language for the current device session.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
lang | string | Yes | Language code (e.g. `"en"`, `"pt"`, `"ar"`). Must not be empty.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/lang" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"lang": "pt"}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/lang", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ lang: "pt" })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/lang'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({'lang': 'pt'}),
);
```

> Example Response

```json
{
  "data": "Language has been updated"
}
```

## Update Media Auto-Download Settings

`PATCH https://api.weellu.com/api/v1/profile/media-download`

Configures which media types are automatically downloaded on WiFi and/or mobile networks. At least one of `wifi` or `mobile` must be provided.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
wifi | string[] | No | Array of media types to auto-download on WiFi. Valid values: `"images"`, `"videos"`, `"files"`, `"voices"`. At least one of `wifi` or `mobile` is required.
mobile | string[] | No | Array of media types to auto-download on mobile data. Valid values: `"images"`, `"videos"`, `"files"`, `"voices"`. At least one of `wifi` or `mobile` is required.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/media-download" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"wifi": ["images", "videos", "files", "voices"], "mobile": ["images"]}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/media-download", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    wifi: ["images", "videos", "files", "voices"],
    mobile: ["images"]
  })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/media-download'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'wifi': ['images', 'videos', 'files', 'voices'],
    'mobile': ['images'],
  }),
);
```

> Example Response

```json
{
  "data": {
    "wifi": ["images", "videos", "files", "voices"],
    "mobile": ["images"]
  }
}
```

## Set Chat Wallpaper URL

`PATCH https://api.weellu.com/api/v1/profile/chat-wallpaper`

Sets the chat wallpaper to a URL string. Emits a `v1OnChatWallpaperChanged` socket event to all connected devices.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
chatWallpaper | string | No | URL or S3 key of the wallpaper image. Pass `null` or omit to remove wallpaper.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/chat-wallpaper" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"chatWallpaper": "https://cdn.weellu.com/wallpapers/sunset.jpg"}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/chat-wallpaper", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    chatWallpaper: "https://cdn.weellu.com/wallpapers/sunset.jpg"
  })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/chat-wallpaper'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'chatWallpaper': 'https://cdn.weellu.com/wallpapers/sunset.jpg',
  }),
);
```

> Example Response

```json
{
  "data": {
    "chatWallpaper": "https://cdn.weellu.com/wallpapers/sunset.jpg"
  }
}
```

## Upload Chat Wallpaper Image

`POST https://api.weellu.com/api/v1/profile/chat-wallpaper`

Uploads an image file as the chat wallpaper. The file is stored in S3 and the URL is saved to the user profile. Emits a `v1OnChatWallpaperChanged` socket event to all connected devices.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
file | file | Yes | Image file (multipart/form-data). Supported formats: JPEG, PNG, WebP.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/chat-wallpaper" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@/path/to/wallpaper.jpg"
```

```javascript
const formData = new FormData();
formData.append("file", fileInput.files[0]);

const response = await fetch("https://api.weellu.com/api/v1/profile/chat-wallpaper", {
  method: "POST",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  },
  body: formData
});
const data = await response.json();
```

```dart
import 'package:http/http.dart' as http;

final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/profile/chat-wallpaper'),
);
request.headers['Authorization'] = 'Bearer $token';
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/wallpaper.jpg'));
final response = await request.send();
```

> Example Response

```json
{
  "data": {
    "chatWallpaper": "media/64f1a2b3c4d5e6f7a8b9c0d1/wallpaper_1710432000.jpg"
  }
}
```

## Check App Version

`POST https://api.weellu.com/api/v1/profile/version/check`

Checks if the client app version is up to date. Compares the client's semantic version and build number against the latest server version for the current platform.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
semVer | string | Yes | Semantic version string (e.g. `"2.1.0"`). Must be valid semver format.
buildNumber | number | Yes | Build number (e.g. `45`).

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/version/check" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"semVer": "2.1.0", "buildNumber": 45}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/version/check", {
  method: "POST",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    semVer: "2.1.0",
    buildNumber: 45
  })
});
const data = await response.json();
```

```dart
final response = await chopperClient.post(
  Uri.parse('/api/v1/profile/version/check'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'semVer': '2.1.0',
    'buildNumber': 45,
  }),
);
```

> Example Response

```json
{
  "data": {
    "isNeedUpdates": true,
    "isCritical": false,
    "clientVersion": "2.1.0",
    "clientBuild": 45,
    "notes": "Bug fixes and performance improvements",
    "serverVersion": "2.2.0",
    "serverBuild": 50,
    "platform": "android"
  }
}
```

## Delete Account

`DELETE https://api.weellu.com/api/v1/profile/`

Soft-deletes the authenticated user's account by setting `deletedAt` to the current timestamp.

### Parameters

No parameters required.

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/profile/" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/", {
  method: "DELETE",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.delete(
  Uri.parse('/api/v1/profile/'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": "account deleted !"
}
```

## Update Last Visit

`PATCH https://api.weellu.com/api/v1/profile/visit`

Increments the visit counter for the current device session.

### Parameters

No parameters required.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/visit" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/visit", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/visit'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": "Done"
}
```

## Toggle Virtual Assistant

`PATCH https://api.weellu.com/api/v1/profile/assistant`

Enables or disables the virtual assistant role for the authenticated user. When enabled, adds the `assistant` role. When disabled, removes the role and clears the `assistId`.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
isEnable | boolean | Yes | `true` to enable the virtual assistant role, `false` to disable it.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/assistant" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"isEnable": true}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/assistant", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ isEnable: true })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/assistant'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({'isEnable': true}),
);
```

> Example Response

```json
{
  "data": "Role added"
}
```

## Get Peer Last Seen

`GET https://api.weellu.com/api/v1/profile/users/:peerId/last-seen`

Returns the last seen timestamp for a specific user.

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
peerId | string | Yes | MongoDB ObjectId of the target user

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/users/64f1a2b3c4d5e6f7a8b9c0d1/last-seen" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const peerId = "64f1a2b3c4d5e6f7a8b9c0d1";
const response = await fetch(`https://api.weellu.com/api/v1/profile/users/${peerId}/last-seen`, {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final peerId = '64f1a2b3c4d5e6f7a8b9c0d1';
final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/users/$peerId/last-seen'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": "2026-03-18T14:30:00.000Z"
}
```

## Register Push Key

`POST https://api.weellu.com/api/v1/profile/push`

Registers a push notification key and/or VoIP key for the current device. At least one of `pushKey` or `voipKey` must be provided.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
pushKey | string | No | FCM push notification token. At least one of `pushKey` or `voipKey` is required.
voipKey | string | No | VoIP push notification token (for iOS CallKit). At least one of `pushKey` or `voipKey` is required.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/push" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"pushKey": "dGVzdC1mY20tdG9rZW4tZXhhbXBsZQ==", "voipKey": "dm9pcC10b2tlbi1leGFtcGxl"}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/push", {
  method: "POST",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    pushKey: "dGVzdC1mY20tdG9rZW4tZXhhbXBsZQ==",
    voipKey: "dm9pcC10b2tlbi1leGFtcGxl"
  })
});
const data = await response.json();
```

```dart
final response = await chopperClient.post(
  Uri.parse('/api/v1/profile/push'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'pushKey': 'dGVzdC1mY20tdG9rZW4tZXhhbXBsZQ==',
    'voipKey': 'dm9pcC10b2tlbi1leGFtcGxl',
  }),
);
```

> Example Response

```json
{
  "data": "PushKey added"
}
```

## Add FCM Token (Deprecated)

`POST https://api.weellu.com/api/v1/profile/fcm`

<aside class="warning">This endpoint is deprecated. Use <code>POST /profile/push</code> instead.</aside>

Registers an FCM push notification token for the current device.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
pushKey | string | Yes | FCM push notification token.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/fcm" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"pushKey": "dGVzdC1mY20tdG9rZW4tZXhhbXBsZQ=="}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/fcm", {
  method: "POST",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ pushKey: "dGVzdC1mY20tdG9rZW4tZXhhbXBsZQ==" })
});
const data = await response.json();
```

```dart
final response = await chopperClient.post(
  Uri.parse('/api/v1/profile/fcm'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({'pushKey': 'dGVzdC1mY20tdG9rZW4tZXhhbXBsZQ=='}),
);
```

> Example Response

```json
{
  "data": "PushKey added"
}
```

## Update FCM Token

`PATCH https://api.weellu.com/api/v1/profile/fcm`

Updates the FCM push notification token for the current device.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
pushKey | string | Yes | Updated FCM push notification token. Must not be empty.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/fcm" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"pushKey": "bmV3LWZjbS10b2tlbi1leGFtcGxl"}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/fcm", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ pushKey: "bmV3LWZjbS10b2tlbi1leGFtcGxl" })
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/fcm'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({'pushKey': 'bmV3LWZjbS10b2tlbi1leGFtcGxl'}),
);
```

> Example Response

```json
{
  "data": "updated!"
}
```

## Delete FCM Token

`DELETE https://api.weellu.com/api/v1/profile/fcm`

Removes the FCM push notification token from the current device. The device will no longer receive push notifications.

### Parameters

No parameters required.

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/profile/fcm" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/fcm", {
  method: "DELETE",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.delete(
  Uri.parse('/api/v1/profile/fcm'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": "Fcm deleted"
}
```

## Create Quick Message

`POST https://api.weellu.com/api/v1/profile/quick-message`

Creates a text shortcut (quick message) for fast message insertion.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
shortcut | string | Yes | Shortcut trigger text (e.g. `/hello`). Must not be empty.
desc | string | Yes | The full message text that replaces the shortcut. Must not be empty.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/quick-message" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"shortcut": "/hello", "desc": "Hello! How can I help you today?"}'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/quick-message", {
  method: "POST",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    shortcut: "/hello",
    desc: "Hello! How can I help you today?"
  })
});
const data = await response.json();
```

```dart
final response = await chopperClient.post(
  Uri.parse('/api/v1/profile/quick-message'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'shortcut': '/hello',
    'desc': 'Hello! How can I help you today?',
  }),
);
```

> Example Response

```json
{
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d3",
    "userId": "64f1a2b3c4d5e6f7a8b9c0d1",
    "shortcut": "/hello",
    "desc": "Hello! How can I help you today?",
    "createdAt": "2026-03-18T10:00:00.000Z",
    "updatedAt": "2026-03-18T10:00:00.000Z"
  }
}
```

## List Quick Messages

`GET https://api.weellu.com/api/v1/profile/quick-message`

Returns a paginated list of the authenticated user's quick messages. Optionally filter by shortcut text.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
limit | number | No | Number of results per page (default: 50)
page | number | No | Page number (default: 1)
shortcut | string | No | Filter by shortcut text (case-insensitive partial match)

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/quick-message?limit=20&page=1&shortcut=hello" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const params = new URLSearchParams({
  limit: "20",
  page: "1",
  shortcut: "hello"
});
const response = await fetch(`https://api.weellu.com/api/v1/profile/quick-message?${params}`, {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/quick-message?limit=20&page=1&shortcut=hello'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0d3",
        "userId": "64f1a2b3c4d5e6f7a8b9c0d1",
        "shortcut": "/hello",
        "desc": "Hello! How can I help you today?",
        "createdAt": "2026-03-18T10:00:00.000Z",
        "updatedAt": "2026-03-18T10:00:00.000Z"
      }
    ],
    "totalDocs": 1,
    "totalPages": 1,
    "page": 1,
    "hasNextPage": false
  }
}
```

## Delete Quick Message

`DELETE https://api.weellu.com/api/v1/profile/quick-message/:id`

Deletes a specific quick message by its ID.

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the quick message to delete

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/profile/quick-message/64f1a2b3c4d5e6f7a8b9c0d3" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const quickMsgId = "64f1a2b3c4d5e6f7a8b9c0d3";
const response = await fetch(`https://api.weellu.com/api/v1/profile/quick-message/${quickMsgId}`, {
  method: "DELETE",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final quickMsgId = '64f1a2b3c4d5e6f7a8b9c0d3';
final response = await chopperClient.delete(
  Uri.parse('/api/v1/profile/quick-message/$quickMsgId'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": "Done"
}
```

## Create Chat Export

`POST https://api.weellu.com/api/v1/profile/exports`

Creates a new chat export job. The export is processed asynchronously in a background queue. Rate limited to one export every 24 hours, and only one active export at a time.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
roomId | string | Yes | MongoDB ObjectId of the room to export. You must be a member of this room.
dateFrom | string | No | ISO 8601 date string for the start of the export range (e.g. `"2026-01-01T00:00:00.000Z"`).
dateTo | string | No | ISO 8601 date string for the end of the export range (e.g. `"2026-03-18T23:59:59.000Z"`).
includeMedia | boolean | No | Whether to include media attachments in the export (default: `false`).

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/exports" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "roomId": "64f1a2b3c4d5e6f7a8b9c0d4",
    "dateFrom": "2026-01-01T00:00:00.000Z",
    "dateTo": "2026-03-18T23:59:59.000Z",
    "includeMedia": false
  }'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/exports", {
  method: "POST",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    roomId: "64f1a2b3c4d5e6f7a8b9c0d4",
    dateFrom: "2026-01-01T00:00:00.000Z",
    dateTo: "2026-03-18T23:59:59.000Z",
    includeMedia: false
  })
});
const data = await response.json();
```

```dart
final response = await chopperClient.post(
  Uri.parse('/api/v1/profile/exports'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'roomId': '64f1a2b3c4d5e6f7a8b9c0d4',
    'dateFrom': '2026-01-01T00:00:00.000Z',
    'dateTo': '2026-03-18T23:59:59.000Z',
    'includeMedia': false,
  }),
);
```

> Example Response

```json
{
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d5",
    "userId": "64f1a2b3c4d5e6f7a8b9c0d1",
    "roomId": "64f1a2b3c4d5e6f7a8b9c0d4",
    "roomTitle": "Project Alpha",
    "dateFrom": "2026-01-01T00:00:00.000Z",
    "dateTo": "2026-03-18T23:59:59.000Z",
    "includeMedia": false,
    "status": "pending",
    "createdAt": "2026-03-18T15:00:00.000Z",
    "updatedAt": "2026-03-18T15:00:00.000Z"
  }
}
```

## List Chat Exports

`GET https://api.weellu.com/api/v1/profile/exports`

Returns a paginated list of the authenticated user's chat exports, sorted by most recent first. Completed exports include a pre-signed download URL valid for 7 days.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
limit | number | No | Number of results per page (default: 20, must be > 0)
page | number | No | Page number (default: 1, must be > 0)
lastId | string | No | MongoDB ObjectId for cursor-based pagination

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/exports?limit=10&page=1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/exports?limit=10&page=1", {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/exports?limit=10&page=1'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "docs": [
      {
        "_id": "64f1a2b3c4d5e6f7a8b9c0d5",
        "userId": "64f1a2b3c4d5e6f7a8b9c0d1",
        "roomId": "64f1a2b3c4d5e6f7a8b9c0d4",
        "roomTitle": "Project Alpha",
        "dateFrom": "2026-01-01T00:00:00.000Z",
        "dateTo": "2026-03-18T23:59:59.000Z",
        "includeMedia": false,
        "status": "completed",
        "filePath": "exports/64f1a2b3/chat-export-1710432000.json",
        "fileSize": 245678,
        "totalMessages": 1523,
        "completedAt": "2026-03-18T15:02:30.000Z",
        "expiresAt": "2026-03-25T15:02:30.000Z",
        "createdAt": "2026-03-18T15:00:00.000Z",
        "updatedAt": "2026-03-18T15:02:30.000Z",
        "downloadUrl": "https://s3.amazonaws.com/weellu/exports/64f1a2b3/chat-export-1710432000.json?X-Amz-Signature=..."
      }
    ],
    "totalDocs": 1,
    "totalPages": 1,
    "page": 1,
    "hasNextPage": false
  }
}
```

## Get Chat Export by ID

`GET https://api.weellu.com/api/v1/profile/exports/:exportId`

Returns a single chat export by its ID. If the export is completed, includes a pre-signed download URL valid for 7 days.

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
exportId | string | Yes | MongoDB ObjectId of the chat export

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/exports/64f1a2b3c4d5e6f7a8b9c0d5" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const exportId = "64f1a2b3c4d5e6f7a8b9c0d5";
const response = await fetch(`https://api.weellu.com/api/v1/profile/exports/${exportId}`, {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final exportId = '64f1a2b3c4d5e6f7a8b9c0d5';
final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/exports/$exportId'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0d5",
    "userId": "64f1a2b3c4d5e6f7a8b9c0d1",
    "roomId": "64f1a2b3c4d5e6f7a8b9c0d4",
    "roomTitle": "Project Alpha",
    "dateFrom": "2026-01-01T00:00:00.000Z",
    "dateTo": "2026-03-18T23:59:59.000Z",
    "includeMedia": false,
    "status": "completed",
    "filePath": "exports/64f1a2b3/chat-export-1710432000.json",
    "fileSize": 245678,
    "totalMessages": 1523,
    "completedAt": "2026-03-18T15:02:30.000Z",
    "expiresAt": "2026-03-25T15:02:30.000Z",
    "createdAt": "2026-03-18T15:00:00.000Z",
    "updatedAt": "2026-03-18T15:02:30.000Z",
    "downloadUrl": "https://s3.amazonaws.com/weellu/exports/64f1a2b3/chat-export-1710432000.json?X-Amz-Signature=..."
  }
}
```
