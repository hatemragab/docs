# End-to-End Encryption Devices

These endpoints register the public X25519 identity keys used by Weellu's end-to-end encrypted text-message flow.

Requires `Authorization: Bearer TOKEN` with a user JWT. Approved API tokens are intentionally rejected because an E2E identity must be bound to a real authenticated device session.

<aside class="notice">
The API accepts and stores public keys only. X25519 private keys must remain in platform-secure client storage such as Android Keystore-backed storage, iOS Keychain, Windows Credential Manager-backed storage, or the Flutter web secure key store, and must never be sent to this API.
</aside>

<aside class="notice">
Every E2E key registered by a valid authenticated user-device session becomes <code>active</code> immediately on Android, iOS, web, Windows, and other supported platforms. The user's successful account authentication is the device-approval boundary. At most ten non-revoked E2E devices may exist for one account.
</aside>

## Register E2E Device

`POST https://api.weellu.com/api/v1/e2e/devices`

Registers and immediately activates the current authenticated device's public X25519 key. Repeating the same request from the same physical device is idempotent, refreshes its session binding, and promotes a legacy <code>pending</code> record to <code>active</code>. Activating a new key marks the account's existing encrypted rooms for key rotation so future epochs include the new device. A key ID cannot be reused with different key material, transferred to another physical device, or reactivated after revocation.

### Parameters

| Parameter  | Type           | Required | Description                                                                        |
| ---------- | -------------- | -------- | ---------------------------------------------------------------------------------- |
| keyId      | UUID v4 string | Yes      | Client-generated stable identifier for this key pair                               |
| publicKey  | string         | Yes      | Canonical unpadded base64url encoding of exactly 32 X25519 public-key bytes        |
| deviceName | string         | No       | User-facing device label, maximum 80 characters; surrounding whitespace is removed |

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/e2e/devices" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "keyId": "550e8400-e29b-41d4-a716-446655440000",
    "publicKey": "AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8",
    "deviceName": "Hatem Android"
  }'
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/e2e/devices", {
  method: "POST",
  headers: {
    Authorization: "Bearer TOKEN",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    keyId: "550e8400-e29b-41d4-a716-446655440000",
    publicKey: "AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8",
    deviceName: "Hatem Android",
  }),
});
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/e2e/devices'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'keyId': '550e8400-e29b-41d4-a716-446655440000',
    'publicKey': 'AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8',
    'deviceName': 'Hatem Android',
  }),
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "keyId": "550e8400-e29b-41d4-a716-446655440000",
    "publicKey": "AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8",
    "version": 1,
    "algorithm": "X25519",
    "status": "active",
    "deviceName": "Hatem Android",
    "platform": "Android",
    "isCurrent": true,
    "approvedAt": "2026-08-18T14:00:00.000Z",
    "lastSeenAt": "2026-08-18T14:00:00.000Z",
    "createdAt": "2026-08-18T14:00:00.000Z",
    "updatedAt": "2026-08-18T14:00:00.000Z"
  },
  "msg": null
}
```

Possible errors include HTTP `400` for invalid key encoding, HTTP `403` for API-token authentication, and HTTP `409` for key substitution, revoked-key reuse, physical-device transfer, or the ten-device limit.

## List E2E Devices

`GET https://api.weellu.com/api/v1/e2e/devices`

Returns this user's E2E device records, including active devices, legacy pending records, and revoked devices. New authenticated registrations are active immediately. Internal user IDs, session IDs, physical-device identifiers, and device slots are never returned.

### Parameters

None.

> Example Request

```shell
curl "https://api.weellu.com/api/v1/e2e/devices" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch("https://api.weellu.com/api/v1/e2e/devices", {
  headers: { Authorization: "Bearer TOKEN" },
});
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/e2e/devices'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": [
    {
      "keyId": "550e8400-e29b-41d4-a716-446655440000",
      "publicKey": "AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8",
      "version": 1,
      "algorithm": "X25519",
      "status": "active",
      "deviceName": "Hatem Android",
      "platform": "Android",
      "isCurrent": true,
      "approvedAt": "2026-08-18T14:00:00.000Z",
      "lastSeenAt": "2026-08-18T14:00:00.000Z",
      "createdAt": "2026-08-18T14:00:00.000Z",
      "updatedAt": "2026-08-18T14:00:00.000Z"
    },
    {
      "keyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
      "publicKey": "AQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQE",
      "version": 1,
      "algorithm": "X25519",
      "status": "active",
      "deviceName": "Flutter Web",
      "platform": "Web",
      "isCurrent": false,
      "approvedAt": "2026-08-18T14:02:00.000Z",
      "lastSeenAt": "2026-08-18T14:02:00.000Z",
      "createdAt": "2026-08-18T14:02:00.000Z",
      "updatedAt": "2026-08-18T14:02:00.000Z"
    }
  ],
  "msg": null
}
```

## Approve E2E Device

`POST https://api.weellu.com/api/v1/e2e/devices/:keyId/approve`

Compatibility endpoint for approving a legacy pending target key. Normal clients do not need this endpoint because registration now activates every key from a valid authenticated user-device session. The JWT session making this request must be bound to the active key named by `approverKeyId`; knowing an active key ID from another device is insufficient.

When this legacy flow is used, the trusted and pending clients should compare their matching security code before the trusted client calls this endpoint. The human-verified code is not transmitted to the server.

Both clients build that code by sorting the two strings `{keyId}:{publicKey}`, hashing UTF-8 `weellu-e2e-device-verification|v=1|u={userId}|a={first}|b={second}` with SHA-256, interpreting the first 10 digest bytes as an unsigned big-endian integer, reducing it modulo `1000000000000`, padding to 12 decimal digits, and displaying it as `0000 0000 0000`.

Approval marks every already-encrypted private/group room shared by the account as `rotationRequired` with reason `device_added`. New encrypted sends remain blocked until an active client creates the next epoch covering the newly active device. A trusted client should also upload historical envelopes with the room-envelope endpoint so the new device can read older encrypted messages.

### Parameters

| Parameter     | Type           | Required | Description                                                  |
| ------------- | -------------- | -------- | ------------------------------------------------------------ |
| keyId         | UUID v4 string | Yes      | Pending target key ID in the URL path                        |
| approverKeyId | UUID v4 string | Yes      | Active key bound to the current authenticated device session |

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/e2e/devices/b73d4876-788c-44d8-8252-a442ce0a9848/approve" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"approverKeyId":"550e8400-e29b-41d4-a716-446655440000"}'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/e2e/devices/b73d4876-788c-44d8-8252-a442ce0a9848/approve",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      approverKeyId: "550e8400-e29b-41d4-a716-446655440000",
    }),
  },
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/e2e/devices/b73d4876-788c-44d8-8252-a442ce0a9848/approve'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode({
    'approverKeyId': '550e8400-e29b-41d4-a716-446655440000',
  }),
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "keyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
    "publicKey": "AQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQE",
    "version": 1,
    "algorithm": "X25519",
    "status": "active",
    "deviceName": "Flutter Web",
    "platform": "Web",
    "isCurrent": false,
    "approvedAt": "2026-08-18T14:05:00.000Z",
    "approvedByKeyId": "550e8400-e29b-41d4-a716-446655440000",
    "lastSeenAt": "2026-08-18T14:02:00.000Z",
    "createdAt": "2026-08-18T14:02:00.000Z",
    "updatedAt": "2026-08-18T14:05:00.000Z"
  },
  "msg": null
}
```

Possible errors include HTTP `400` when a key attempts to approve itself, HTTP `403` when the current session is not the named active device, HTTP `404` when the target is absent, and HTTP `409` when the target was revoked.

## Revoke E2E Device

`DELETE https://api.weellu.com/api/v1/e2e/devices/:keyId`

Revokes one of the authenticated user's E2E device keys and immediately releases its live-device slot. The caller must itself be an active trusted E2E device; a pending session cannot revoke keys. The last active device cannot revoke itself. Revocation is idempotent. The same key ID and key material cannot be registered again.

Revocation prevents future room keys from being wrapped for the device. It cannot erase room keys that were already delivered to that device, so the API marks every encrypted private/group room shared by the user as rotation-required before revoking the key.

### Parameters

| Parameter | Type           | Required | Description                                    |
| --------- | -------------- | -------- | ---------------------------------------------- |
| keyId     | UUID v4 string | Yes      | E2E key ID to revoke, supplied in the URL path |

> Example Request

```shell
curl -X DELETE "https://api.weellu.com/api/v1/e2e/devices/b73d4876-788c-44d8-8252-a442ce0a9848" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/e2e/devices/b73d4876-788c-44d8-8252-a442ce0a9848",
  {
    method: "DELETE",
    headers: { Authorization: "Bearer TOKEN" },
  },
);
const data = await response.json();
```

```dart
final response = await http.delete(
  Uri.parse('https://api.weellu.com/api/v1/e2e/devices/b73d4876-788c-44d8-8252-a442ce0a9848'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "keyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
    "publicKey": "AQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQE",
    "version": 1,
    "algorithm": "X25519",
    "status": "revoked",
    "deviceName": "Flutter Web",
    "platform": "Web",
    "isCurrent": false,
    "approvedAt": "2026-08-18T14:05:00.000Z",
    "approvedByKeyId": "550e8400-e29b-41d4-a716-446655440000",
    "revokedAt": "2026-08-18T15:00:00.000Z",
    "lastSeenAt": "2026-08-18T14:02:00.000Z",
    "createdAt": "2026-08-18T14:02:00.000Z",
    "updatedAt": "2026-08-18T15:00:00.000Z"
  },
  "msg": null
}
```

Returns HTTP `403` when the caller is not an active trusted device, HTTP `404` if the key does not belong to the authenticated user, and HTTP `409` when the last active device attempts to revoke itself.

## Get Room Encryption Bundle

`GET https://api.weellu.com/api/v1/e2e/rooms/:roomId`

Returns the authenticated member's room-encryption state, the public keys of every active member device, and only the historical room-key envelopes addressed to the current device. Private keys and envelopes addressed to other devices are never returned.

Private rooms (`RoomType.Single`), including direct rooms with assistant metadata, and group chats (`RoomType.GroupChat`) are eligible. Projects, inboxes, broadcasts, channels, tasks, blocked rooms, and WhatsApp rooms remain plaintext.

When a direct assistant room has an active E2E epoch, encrypted messages bypass server-side assistant automation because the server cannot decrypt their content. Human participants continue to send and receive those messages normally.

### Parameters

| Parameter | Type                    | Required | Description                                                              |
| --------- | ----------------------- | -------- | ------------------------------------------------------------------------ |
| roomId    | MongoDB ObjectId string | Yes      | Room identifier in the URL path; the authenticated user must be a member |

> Example Request

```shell
curl "https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b",
  { headers: { Authorization: "Bearer TOKEN" } },
);
const data = await response.json();
```

```dart
final response = await http.get(
  Uri.parse('https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b'),
  headers: {'Authorization': 'Bearer $token'},
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "eligibility": {
      "eligible": true,
      "roomType": "g"
    },
    "enabled": true,
    "generation": 2,
    "rotationRequired": false,
    "currentEpoch": {
      "epoch": 3,
      "keyId": "550e8400-e29b-41d4-a716-446655440000"
    },
    "wrap": {
      "version": 1,
      "algorithm": "X25519-HKDF-SHA256-A256GCM",
      "hkdfSalt": "weellu-e2e-room-key-wrap-v1"
    },
    "devices": [
      {
        "userId": "65faf9abe5c7cd1a0e10436c",
        "keyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
        "publicKey": "AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8",
        "platform": "Android",
        "deviceName": "Hatem Android"
      },
      {
        "userId": "65faf9abe5c7cd1a0e10436d",
        "keyId": "c83d4876-788c-44d8-8252-a442ce0a9849",
        "publicKey": "AQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQE",
        "platform": "Ios",
        "deviceName": "Peer iPhone"
      }
    ],
    "membersWithoutActiveDevice": [],
    "myDevice": {
      "keyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
      "status": "active"
    },
    "myEnvelopes": [
      {
        "epoch": 3,
        "generation": 2,
        "keyId": "550e8400-e29b-41d4-a716-446655440000",
        "senderKeyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
        "senderPublicKey": "AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8",
        "wrapVersion": 1,
        "wrapAlgorithm": "X25519-HKDF-SHA256-A256GCM",
        "aad": "weellu-e2e-room-key|v=1|r=65faf9abe5c7cd1a0e10436b|e=3|k=550e8400-e29b-41d4-a716-446655440000|s=b73d4876-788c-44d8-8252-a442ce0a9848|u=65faf9abe5c7cd1a0e10436c|d=b73d4876-788c-44d8-8252-a442ce0a9848",
        "nonce": "AQEBAQEBAQEBAQEB",
        "ciphertext": "AgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgI",
        "tag": "AwMDAwMDAwMDAwMDAwMDAw",
        "createdAt": "2026-08-18T16:00:00.000Z"
      }
    ]
  },
  "msg": null
}
```

When encryption is not active, `enabled` is `false` and `currentEpoch` is `null`. `membersWithoutActiveDevice` must be empty before the client can create an epoch. If the current key is pending, `myEnvelopes` is empty until approval and a later envelope upload or rotation. If a previously encrypted room later becomes ineligible, new epochs are blocked but already-addressed `myEnvelopes` are still returned so historical messages remain decryptable.

## Get Room Encryption Status

`GET https://api.weellu.com/api/v1/e2e/rooms/:roomId/status`

Read-only encryption status for the chat-info screen. Returns presentation data only — enabled flag, current epoch and key id, activation/rotation timestamps, rotation-pending flag, and member/device coverage counts. It never exposes key material, envelopes, escrow data, or per-device records, and works for members without a registered E2E device.

> Example Response

```json
{
  "code": 2000,
  "data": {
    "eligible": true,
    "enabled": true,
    "epoch": 2,
    "keyId": "550e8400-e29b-41d4-a716-446655440000",
    "activatedAt": "2026-08-30T12:00:00.000Z",
    "rotatedAt": "2026-09-01T08:15:00.000Z",
    "generation": 3,
    "rotationRequired": false,
    "memberCount": 2,
    "membersWithE2eDevices": 2,
    "e2eDeviceCount": 3
  },
  "msg": null
}
```

### Activation announcement

Creating the room's FIRST epoch also inserts a one-shot system notice into the chat timeline: an `Info` message with `msgAtt.action: "e2eEnabled"` and content "Messages in this chat are now end-to-end encrypted.", broadcast to all members over `v1OnNewMessage` (no push notification, no unread bump). Clients that do not know the action render the stored content text directly. Key rotations (epoch > 1) stay silent by design.

## Create Room Key Epoch

`POST https://api.weellu.com/api/v1/e2e/rooms/:roomId/epochs`

Activates E2E text for a room or rotates it to the next key epoch. The current authenticated device must be an active E2E device and must submit exactly one encrypted copy of the same random 32-byte room key for every active device belonging to every current room member.

The server validates membership, eligibility, sequential epoch number, generation, sender-device binding, recipient coverage, duplicate recipients, key formats, and immutable retry hashes. The server stores the encrypted envelopes plus the required `escrowKey` — a server-side recoverable copy used to automatically re-wrap history to new devices (delete+reinstall) and to users newly added to a group. Private, group, Order (`o`), and assistant direct rooms are eligible; Project/Task/Broadcast/Inbox/System, blocked, WhatsApp, and broadcast-linked groups remain ineligible.

The message-send policy also verifies that the current epoch has an envelope for every active member device. This closes the short transition window after approval or revocation even if rotation-state updates race with a send.

### Parameters

| Parameter                   | Type                    | Required | Description                                                             |
| --------------------------- | ----------------------- | -------- | ----------------------------------------------------------------------- |
| roomId                      | MongoDB ObjectId string | Yes      | Eligible private/group room in the URL path                             |
| epoch                       | integer, minimum 1      | Yes      | Must be `1` for activation or exactly the current epoch plus one        |
| generation                  | integer, minimum 0      | Yes      | Optimistic room-device generation returned by the bundle endpoint       |
| keyId                       | UUID v4 string          | Yes      | Fresh identifier for the random 32-byte room key                        |
| senderKeyId                 | UUID v4 string          | Yes      | Active E2E key bound to the current JWT device session                  |
| envelopes                   | array, minimum 1        | Yes      | Exact recipient set for all active room-member devices                  |
| envelopes[].recipientUserId | MongoDB ObjectId string | Yes      | User owning the recipient device                                        |
| envelopes[].recipientKeyId  | UUID v4 string          | Yes      | Recipient's active device key                                           |
| envelopes[].nonce           | string                  | Yes      | Canonical unpadded base64url encoding of a fresh 12-byte AES-GCM nonce  |
| envelopes[].ciphertext      | string                  | Yes      | Canonical unpadded base64url encoding of the encrypted 32-byte room key |
| envelopes[].tag             | string                  | Yes      | Canonical unpadded base64url encoding of the 16-byte AES-GCM tag        |
| escrowKey                   | string                  | Yes      | Canonical unpadded base64url of the plaintext 32-byte room key for server-side history escrow. The server re-wraps it to new devices and newly added group members so delete+reinstall and late joins see full history without a helper device online. Legacy stored epochs may predate the requirement. |

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b/epochs" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "epoch": 1,
    "generation": 0,
    "keyId": "550e8400-e29b-41d4-a716-446655440000",
    "senderKeyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
    "envelopes": [
      {
        "recipientUserId": "65faf9abe5c7cd1a0e10436c",
        "recipientKeyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
        "nonce": "AQEBAQEBAQEBAQEB",
        "ciphertext": "AgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgI",
        "tag": "AwMDAwMDAwMDAwMDAwMDAw"
      },
      {
        "recipientUserId": "65faf9abe5c7cd1a0e10436d",
        "recipientKeyId": "c83d4876-788c-44d8-8252-a442ce0a9849",
        "nonce": "BAQEBAQEBAQEBAQE",
        "ciphertext": "BQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQU",
        "tag": "BgYGBgYGBgYGBgYGBgYGBg"
      }
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b/epochs",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify(epochPayload),
  },
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b/epochs'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode(epochPayload),
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "roomId": "65faf9abe5c7cd1a0e10436b",
    "roomType": "g",
    "epoch": 1,
    "generation": 0,
    "keyId": "550e8400-e29b-41d4-a716-446655440000",
    "senderKeyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
    "wrapVersion": 1,
    "wrapAlgorithm": "X25519-HKDF-SHA256-A256GCM",
    "recipientCount": 2,
    "createdAt": "2026-08-18T16:00:00.000Z"
  },
  "msg": null
}
```

For each recipient, compute X25519 using the sender private key and recipient public key. Derive a 32-byte wrapping key using HKDF-SHA256 with UTF-8 salt `weellu-e2e-room-key-wrap-v1` and the exact UTF-8 `aad` string returned by the bundle format as HKDF info. Encrypt the raw 32-byte room key with AES-256-GCM using that same `aad` and a unique random 12-byte nonce. Submit ciphertext and the 16-byte tag separately.

Returns HTTP `409` when the epoch or generation is stale, membership changed during creation, a member lacks an active device, or another key won a concurrent creation. Missing, extra, or duplicate recipient envelopes return HTTP `400`.

<aside class="warning">
<strong>Rollout compatibility (pre-force-update).</strong> While server compat mode is enabled, plaintext text messages remain accepted in E2E-enabled rooms so pre-E2E installs keep working; encrypted sends must still carry <code>isEncrypted: true</code> with metadata matching the current room key epoch, and a pending key rotation never blocks plaintext sends. E2E-capable senders additionally submit <code>fallbackPlaintext</code> (stored as <code>fallbackC</code>) plus <code>mentionAll</code>/<code>e2eMentions</code> routing ids extracted from plaintext before encryption. Legacy readers — identified by the absence of the <code>x-e2e-client: 1</code> request header — receive <code>fallbackC</code> projected as <code>c</code> (or a placeholder) on history, room previews, live sockets, and push payloads; <code>fallbackC</code> is never exposed. After a forced app update the operator disables compat mode and encrypted-only enforcement applies.

Existing plaintext history remains readable. Non-text attachments remain outside E2E version 1.

Encrypted messages cannot use the legacy text-edit Socket.IO event because that event accepts plaintext and has no authenticated encryption envelope. Version 1 clients hide the edit action for encrypted messages, the Flutter SDK rejects direct edit attempts, and the API rejects attempts from older or modified clients. Plaintext messages keep the existing edit behavior.

Flutter local search keeps decrypted text in memory only. Per-room search scans the retained encrypted room cache; global search scans a bounded set of recent encrypted candidates. Messages whose room key is unavailable are omitted from search results instead of exposing ciphertext as searchable text.

</aside>

## Add Historical Device Envelopes

`POST https://api.weellu.com/api/v1/e2e/rooms/:roomId/envelopes`

Adds immutable room-key envelopes for an active device that was approved after one or more epochs already existed. This is how an approved web or mobile device receives encrypted history without exposing room keys to the server.

The current trusted sender must already have an envelope for every epoch it is distributing. Each target must be an active device of a current room member. Retries with identical bytes are idempotent; an attempt to replace an existing recipient envelope is rejected.

### Parameters

| Parameter                   | Type                    | Required | Description                                             |
| --------------------------- | ----------------------- | -------- | ------------------------------------------------------- |
| roomId                      | MongoDB ObjectId string | Yes      | Private/group room in the URL path                      |
| senderKeyId                 | UUID v4 string          | Yes      | Active key bound to the current authenticated device    |
| envelopes                   | array, minimum 1        | Yes      | Historical recipient envelopes to add                   |
| envelopes[].epoch           | integer, minimum 1      | Yes      | Existing room epoch                                     |
| envelopes[].keyId           | UUID v4 string          | Yes      | Existing room key ID for that epoch                     |
| envelopes[].recipientUserId | MongoDB ObjectId string | Yes      | Current room member who owns the target key             |
| envelopes[].recipientKeyId  | UUID v4 string          | Yes      | Active target device key                                |
| envelopes[].nonce           | string                  | Yes      | Canonical unpadded base64url 12-byte AES-GCM nonce      |
| envelopes[].ciphertext      | string                  | Yes      | Canonical unpadded base64url encrypted 32-byte room key |
| envelopes[].tag             | string                  | Yes      | Canonical unpadded base64url 16-byte AES-GCM tag        |

> Example Request

```shell
curl -X POST "https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b/envelopes" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "senderKeyId": "b73d4876-788c-44d8-8252-a442ce0a9848",
    "envelopes": [
      {
        "epoch": 1,
        "keyId": "550e8400-e29b-41d4-a716-446655440000",
        "recipientUserId": "65faf9abe5c7cd1a0e10436d",
        "recipientKeyId": "c83d4876-788c-44d8-8252-a442ce0a9849",
        "nonce": "BAQEBAQEBAQEBAQE",
        "ciphertext": "BQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQU",
        "tag": "BgYGBgYGBgYGBgYGBgYGBg"
      }
    ]
  }'
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b/envelopes",
  {
    method: "POST",
    headers: {
      Authorization: "Bearer TOKEN",
      "Content-Type": "application/json",
    },
    body: JSON.stringify(historicalEnvelopePayload),
  },
);
const data = await response.json();
```

```dart
final response = await http.post(
  Uri.parse('https://api.weellu.com/api/v1/e2e/rooms/65faf9abe5c7cd1a0e10436b/envelopes'),
  headers: {
    'Authorization': 'Bearer $token',
    'Content-Type': 'application/json',
  },
  body: jsonEncode(historicalEnvelopePayload),
);
```

> Example Response

```json
{
  "code": 2000,
  "data": {
    "added": [
      {
        "epoch": 1,
        "recipientKeyId": "c83d4876-788c-44d8-8252-a442ce0a9849"
      }
    ],
    "existing": []
  },
  "msg": null
}
```

Use the same X25519/HKDF-SHA256/AES-256-GCM construction as epoch creation, but build AAD with this request's `senderKeyId` and the historical epoch/key identifiers. The server records the trusted sender public key inside each added envelope so it remains decryptable even if that sender is revoked later.

Returns HTTP `403` if the current device lacks the source epoch or the room is blocked, HTTP `400` for an inactive/non-member target, and HTTP `409` for epoch/key mismatch or immutable-envelope replacement.
