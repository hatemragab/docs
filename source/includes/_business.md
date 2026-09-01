# Business Profile

Business profile management endpoints. These allow users to create, update, and manage their business profiles on the Weellu platform. All endpoints require a valid `Authorization: Bearer TOKEN` header.

## Enums Reference

### BusinessStatus

Value | Description
----- | -----------
`active` | Business profile is active and visible
`notActive` | Business profile is deactivated/hidden
`none` | User has no business profile

### DaysEnum

Value | Description
----- | -----------
`sunday` | Sunday
`monday` | Monday
`tuesday` | Tuesday
`wednesday` | Wednesday
`thursday` | Thursday
`friday` | Friday
`saturday` | Saturday

## Data Models

### OpeningHour Object

Field | Type | Required | Description
----- | ---- | -------- | -----------
day | string | Yes | Day of the week. Must be one of `DaysEnum` values. Each day must be unique within the array.
open | string | No | Opening time (e.g. `"09:00"`). Ignored if `is24` is `true`.
close | string | No | Closing time (e.g. `"18:00"`). Ignored if `is24` is `true`.
is24 | boolean | Yes | Whether the business is open 24 hours on this day.

### BusinessProfile Object

Field | Type | Description
----- | ---- | -----------
_id | string | MongoDB ObjectId
userId | string | Owner user's MongoDB ObjectId
businessName | string | Business display name
description | string | Business description text
logo | string | S3 key/URL of the business logo image
cover | string | S3 key/URL of the cover image (nullable)
images | object[] | Array of banner images, each with `id` (ObjectId) and `value` (S3 key)
location | object | GeoJSON Point with `coordinates` ([longitude, latitude]) and `address` (string)
openingHours | object[] | Array of `OpeningHour` objects
acceptCard | boolean | Whether the business accepts card payments
acceptPix | boolean | Whether the business accepts Pix payments
acceptCash | boolean | Whether the business accepts cash payments
status | string | One of `BusinessStatus` enum values
createdAt | string | ISO 8601 creation timestamp
updatedAt | string | ISO 8601 last update timestamp

## Get My Business Profile

`GET https://api.weellu.com/api/v1/profile/business/get`

Returns the authenticated user's business profile, or `null` if no business profile exists.

### Parameters

No parameters required.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/business/get" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/business/get", {
  method: "GET",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.get(
  Uri.parse('/api/v1/profile/business/get'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0e1",
    "userId": "64f1a2b3c4d5e6f7a8b9c0d1",
    "businessName": "Cafe Primavera",
    "description": "Artisan coffee shop and bakery in downtown Sao Paulo",
    "logo": "https://cdn.weellu.com/business/64f1a2b3/logo.jpg",
    "cover": "https://cdn.weellu.com/business/64f1a2b3/cover.jpg",
    "images": [
      {
        "id": "64f1a2b3c4d5e6f7a8b9c100",
        "value": "https://cdn.weellu.com/business/64f1a2b3/banner1.jpg"
      },
      {
        "id": "64f1a2b3c4d5e6f7a8b9c101",
        "value": "https://cdn.weellu.com/business/64f1a2b3/banner2.jpg"
      }
    ],
    "location": {
      "type": "Point",
      "coordinates": [-46.6333, -23.5505],
      "address": "Rua Augusta, 1234, Sao Paulo, SP"
    },
    "openingHours": [
      {
        "day": "monday",
        "open": "08:00",
        "close": "22:00",
        "is24": false
      },
      {
        "day": "tuesday",
        "open": "08:00",
        "close": "22:00",
        "is24": false
      },
      {
        "day": "wednesday",
        "open": "08:00",
        "close": "22:00",
        "is24": false
      },
      {
        "day": "thursday",
        "open": "08:00",
        "close": "22:00",
        "is24": false
      },
      {
        "day": "friday",
        "open": "08:00",
        "close": "23:00",
        "is24": false
      },
      {
        "day": "saturday",
        "is24": true
      },
      {
        "day": "sunday",
        "open": "10:00",
        "close": "18:00",
        "is24": false
      }
    ],
    "acceptCard": true,
    "acceptPix": true,
    "acceptCash": true,
    "status": "active",
    "createdAt": "2026-01-15T10:30:00.000Z",
    "updatedAt": "2026-03-10T14:20:00.000Z"
  }
}
```

## Get Peer Business Profile

`GET https://api.weellu.com/api/v1/profile/:id/business`

Returns another user's business profile, or `null` if they have no business profile.

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
id | string | Yes | MongoDB ObjectId of the target user

> Example Request

```shell
curl "https://api.weellu.com/api/v1/profile/64f1a2b3c4d5e6f7a8b9c0d1/business" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const userId = "64f1a2b3c4d5e6f7a8b9c0d1";
const response = await fetch(`https://api.weellu.com/api/v1/profile/${userId}/business`, {
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
  Uri.parse('/api/v1/profile/$userId/business'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0e1",
    "userId": "64f1a2b3c4d5e6f7a8b9c0d1",
    "businessName": "Cafe Primavera",
    "description": "Artisan coffee shop and bakery in downtown Sao Paulo",
    "logo": "https://cdn.weellu.com/business/64f1a2b3/logo.jpg",
    "cover": "https://cdn.weellu.com/business/64f1a2b3/cover.jpg",
    "images": [
      {
        "id": "64f1a2b3c4d5e6f7a8b9c100",
        "value": "https://cdn.weellu.com/business/64f1a2b3/banner1.jpg"
      }
    ],
    "location": {
      "type": "Point",
      "coordinates": [-46.6333, -23.5505],
      "address": "Rua Augusta, 1234, Sao Paulo, SP"
    },
    "openingHours": [
      {
        "day": "monday",
        "open": "08:00",
        "close": "22:00",
        "is24": false
      }
    ],
    "acceptCard": true,
    "acceptPix": true,
    "acceptCash": true,
    "status": "active",
    "createdAt": "2026-01-15T10:30:00.000Z",
    "updatedAt": "2026-03-10T14:20:00.000Z"
  }
}
```

## Create Business Profile

`POST https://api.weellu.com/api/v1/profile/business`

Creates a new business profile for the authenticated user. Uses `multipart/form-data` encoding for file uploads. A user can only have one business profile. If a profile already exists (even if inactive), this endpoint returns an error -- use the activate endpoint instead.

File upload limits: each file max 40 MB, up to 20 banner images.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
logo | file | Yes | Business logo image file. Exactly one file required. Field name: `logo`.
cover | file | No | Business cover image file. At most one file. Field name: `cover`.
file | file[] | No | Banner images. Up to 20 files. Field name: `file`.
businessName | string | Yes | Business display name.
address | string | Yes | Business street address.
description | string | Yes | Business description text.
longitude | number | Yes | Longitude coordinate of the business location.
latitude | number | Yes | Latitude coordinate of the business location.
acceptCard | boolean | Yes | Whether the business accepts card payments.
acceptPix | boolean | Yes | Whether the business accepts Pix payments.
acceptCash | boolean | Yes | Whether the business accepts cash payments.
openingHours | string (JSON) | Yes | JSON-encoded array of `OpeningHour` objects. Each day must be unique. See the OpeningHour object model above.

### OpeningHour JSON Schema

Each element in the `openingHours` array:

Field | Type | Required | Description
----- | ---- | -------- | -----------
day | string | Yes | One of: `sunday`, `monday`, `tuesday`, `wednesday`, `thursday`, `friday`, `saturday`. Must be unique.
open | string | No | Opening time string (e.g. `"09:00"`).
close | string | No | Closing time string (e.g. `"18:00"`).
is24 | boolean | Yes | `true` if open 24 hours for this day.

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/profile/business" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "logo=@/path/to/logo.png" \
  -F "cover=@/path/to/cover.jpg" \
  -F "file=@/path/to/banner1.jpg" \
  -F "file=@/path/to/banner2.jpg" \
  -F "businessName=Cafe Primavera" \
  -F "address=Rua Augusta, 1234, Sao Paulo, SP" \
  -F "description=Artisan coffee shop and bakery" \
  -F "longitude=-46.6333" \
  -F "latitude=-23.5505" \
  -F "acceptCard=true" \
  -F "acceptPix=true" \
  -F "acceptCash=true" \
  -F 'openingHours=[{"day":"monday","open":"08:00","close":"22:00","is24":false},{"day":"saturday","is24":true}]'
```

```javascript
const formData = new FormData();
formData.append("logo", logoFile);
formData.append("cover", coverFile);
formData.append("file", bannerFile1);
formData.append("file", bannerFile2);
formData.append("businessName", "Cafe Primavera");
formData.append("address", "Rua Augusta, 1234, Sao Paulo, SP");
formData.append("description", "Artisan coffee shop and bakery");
formData.append("longitude", "-46.6333");
formData.append("latitude", "-23.5505");
formData.append("acceptCard", "true");
formData.append("acceptPix", "true");
formData.append("acceptCash", "true");
formData.append("openingHours", JSON.stringify([
  { day: "monday", open: "08:00", close: "22:00", is24: false },
  { day: "tuesday", open: "08:00", close: "22:00", is24: false },
  { day: "wednesday", open: "08:00", close: "22:00", is24: false },
  { day: "thursday", open: "08:00", close: "22:00", is24: false },
  { day: "friday", open: "08:00", close: "23:00", is24: false },
  { day: "saturday", is24: true },
  { day: "sunday", open: "10:00", close: "18:00", is24: false }
]));

const response = await fetch("https://api.weellu.com/api/v1/profile/business", {
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
import 'dart:convert';

final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.weellu.com/api/v1/profile/business'),
);
request.headers['Authorization'] = 'Bearer $token';

// Files
request.files.add(await http.MultipartFile.fromPath('logo', '/path/to/logo.png'));
request.files.add(await http.MultipartFile.fromPath('cover', '/path/to/cover.jpg'));
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/banner1.jpg'));
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/banner2.jpg'));

// Text fields
request.fields['businessName'] = 'Cafe Primavera';
request.fields['address'] = 'Rua Augusta, 1234, Sao Paulo, SP';
request.fields['description'] = 'Artisan coffee shop and bakery';
request.fields['longitude'] = '-46.6333';
request.fields['latitude'] = '-23.5505';
request.fields['acceptCard'] = 'true';
request.fields['acceptPix'] = 'true';
request.fields['acceptCash'] = 'true';
request.fields['openingHours'] = jsonEncode([
  {'day': 'monday', 'open': '08:00', 'close': '22:00', 'is24': false},
  {'day': 'saturday', 'is24': true},
]);

final response = await request.send();
```

> Example Response

```json
{
  "data": "User profile created successfully"
}
```

## Update Business Profile

`PATCH https://api.weellu.com/api/v1/profile/business`

Updates an existing business profile. Uses `multipart/form-data` encoding. All fields are optional -- only provided fields are updated. New banner images are appended to the existing list. To remove banners, use the `deletedBanners` field.

File upload limits: each file max 40 MB, up to 20 banner images per request.

### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
logo | file | No | New business logo image. Field name: `logo`.
cover | file | No | New business cover image. Field name: `cover`.
file | file[] | No | Additional banner images to append. Up to 20 files. Field name: `file`.
businessName | string | No | Updated business display name.
address | string | No | Updated business street address.
description | string | No | Updated business description text.
longitude | number | No | Updated longitude coordinate.
latitude | number | No | Updated latitude coordinate.
acceptCard | boolean | No | Whether the business accepts card payments.
acceptPix | boolean | No | Whether the business accepts Pix payments.
acceptCash | boolean | No | Whether the business accepts cash payments.
openingHours | string (JSON) | No | JSON-encoded array of `OpeningHour` objects. Replaces all existing opening hours. Each day must be unique.
deletedBanners | string | No | Comma-separated list of banner ObjectIds to remove, or a JSON array string (e.g. `"id1,id2"` or `"[id1,id2]"`).

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/business" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "businessName=Cafe Primavera - Pinheiros" \
  -F "description=Artisan coffee shop, bakery, and brunch spot" \
  -F "acceptCard=true" \
  -F "file=@/path/to/new-banner.jpg" \
  -F "deletedBanners=64f1a2b3c4d5e6f7a8b9c100"
```

```javascript
const formData = new FormData();
formData.append("businessName", "Cafe Primavera - Pinheiros");
formData.append("description", "Artisan coffee shop, bakery, and brunch spot");
formData.append("acceptCard", "true");
formData.append("file", newBannerFile);
formData.append("deletedBanners", "64f1a2b3c4d5e6f7a8b9c100");

const response = await fetch("https://api.weellu.com/api/v1/profile/business", {
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
  Uri.parse('https://api.weellu.com/api/v1/profile/business'),
);
request.headers['Authorization'] = 'Bearer $token';

request.fields['businessName'] = 'Cafe Primavera - Pinheiros';
request.fields['description'] = 'Artisan coffee shop, bakery, and brunch spot';
request.fields['acceptCard'] = 'true';
request.fields['deletedBanners'] = '64f1a2b3c4d5e6f7a8b9c100';
request.files.add(await http.MultipartFile.fromPath('file', '/path/to/new-banner.jpg'));

final response = await request.send();
```

> Example Response

```json
{
  "data": {
    "_id": "64f1a2b3c4d5e6f7a8b9c0e1",
    "userId": "64f1a2b3c4d5e6f7a8b9c0d1",
    "businessName": "Cafe Primavera - Pinheiros",
    "description": "Artisan coffee shop, bakery, and brunch spot",
    "logo": "https://cdn.weellu.com/business/64f1a2b3/logo.jpg",
    "cover": "https://cdn.weellu.com/business/64f1a2b3/cover.jpg",
    "images": [
      {
        "id": "64f1a2b3c4d5e6f7a8b9c101",
        "value": "https://cdn.weellu.com/business/64f1a2b3/banner2.jpg"
      },
      {
        "id": "64f1a2b3c4d5e6f7a8b9c102",
        "value": "https://cdn.weellu.com/business/64f1a2b3/new-banner.jpg"
      }
    ],
    "location": {
      "type": "Point",
      "coordinates": [-46.6333, -23.5505],
      "address": "Rua Augusta, 1234, Sao Paulo, SP"
    },
    "openingHours": [
      {
        "day": "monday",
        "open": "08:00",
        "close": "22:00",
        "is24": false
      },
      {
        "day": "saturday",
        "is24": true
      }
    ],
    "acceptCard": true,
    "acceptPix": true,
    "acceptCash": true,
    "status": "active",
    "createdAt": "2026-01-15T10:30:00.000Z",
    "updatedAt": "2026-03-18T16:00:00.000Z"
  }
}
```

## Deactivate Business Profile

`PATCH https://api.weellu.com/api/v1/profile/business/inactivate`

Deactivates the authenticated user's business profile. Sets the business status to `notActive`. The business profile data is preserved and can be reactivated later.

### Parameters

No parameters required.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/business/inactivate" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/business/inactivate", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/business/inactivate'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": "User profile inactive successfully"
}
```

## Activate Business Profile

`PATCH https://api.weellu.com/api/v1/profile/business/active`

Reactivates the authenticated user's business profile. Sets the business status to `active`. The business profile must already exist (created via `POST /profile/business`).

### Parameters

No parameters required.

> Example Request

```shell
curl -X PATCH "https://api.weellu.com/api/v1/profile/business/active" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/profile/business/active", {
  method: "PATCH",
  headers: {
    "Authorization": "Bearer YOUR_TOKEN"
  }
});
const data = await response.json();
```

```dart
final response = await chopperClient.patch(
  Uri.parse('/api/v1/profile/business/active'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "data": "User profile active successfully"
}
```
