# Task Mentions

Task-mention endpoints power the `#` trigger in chat message inputs. They let a user search tasks across the projects they belong to, and verify access before opening a task chip that was tapped inside a message.

Two endpoints live under `/api/v1/channel/task-mention/`. Both require a valid JWT (`Authorization: Bearer TOKEN`) and derive access from the caller's active `ProjectMember` records — no new role or permission model is introduced.

Task references are persisted **inside** the message `content` field as inline tokens of the form `[#<title-snapshot>:<taskId>]`, mirroring the existing user-mention token shape `[@<name>:<userId>]`. There is no parallel `taskMentions` field on the message document — render-time parsing of the content string is the source of truth.

## Search Task Mention Suggestions

`GET https://api.weellu.com/api/v1/channel/task-mention/suggestions`

Returns up to 10 tasks the caller is allowed to mention. Used to populate the `#` suggestion panel above the chat input. Soft-deleted tasks (`deletedAt != null`) are excluded. Results are sorted by status tier (non-terminal statuses — `pending`, `inProgress`, `reOpen` — rank above terminal ones — `done`, `closed`, `archived`) then by `updatedAt` descending.

### Behavior Matrix

Request | Server response
------- | ---------------
`query` non-empty | Up to 10 matches, **global** across every project the caller is an active member of. `currentProjectId` is **ignored**.
`query` empty AND `currentProjectId` provided AND caller is an active member | Up to 10 most-recently-active tasks from **that project only** (the zero-keystroke shortlist).
`query` empty AND `currentProjectId` absent | Empty `results: []`. Client should render a "type to search" empty state.
`query` empty AND `currentProjectId` provided BUT caller is not a member | Empty `results: []`. Silent downgrade — the server never returns 403 here, so a speculative client `currentProjectId` cannot be used to probe project existence.

### Query Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
query | string | No | Substring to match against task title (case-insensitive). Max 60 chars. Omit or leave empty to request the zero-keystroke shortlist.
currentProjectId | string (24-hex ObjectId) | No | The project-room id the user is currently typing inside. **Used only when `query` is empty.** Non-empty queries ignore this parameter and search globally. Omit when the user is typing in a single, group, or broadcast room.
limit | integer | No | Maximum number of results. Default 10. Values above 10 are clamped to 10.

> Example Request — empty query + currentProjectId (zero-keystroke shortlist)

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/task-mention/suggestions?currentProjectId=507f1f77bcf86cd799439aaa" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const params = new URLSearchParams({
  currentProjectId: "507f1f77bcf86cd799439aaa",
});
const response = await fetch(
  `https://api.weellu.com/api/v1/channel/task-mention/suggestions?${params}`,
  { headers: { Authorization: "Bearer TOKEN" } }
);
const { data } = await response.json();
```

```dart
final response = await http.get(
  Uri.parse(
    'https://api.weellu.com/api/v1/channel/task-mention/suggestions'
    '?currentProjectId=507f1f77bcf86cd799439aaa',
  ),
  headers: {'Authorization': 'Bearer TOKEN'},
);
final data = jsonDecode(response.body)['data'];
```

> Example Request — non-empty query (global search)

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/task-mention/suggestions?query=dark&limit=10" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const params = new URLSearchParams({ query: "dark", limit: "10" });
const response = await fetch(
  `https://api.weellu.com/api/v1/channel/task-mention/suggestions?${params}`,
  { headers: { Authorization: "Bearer TOKEN" } }
);
const { data } = await response.json();
```

```dart
final response = await http.get(
  Uri.parse(
    'https://api.weellu.com/api/v1/channel/task-mention/suggestions'
    '?query=dark&limit=10',
  ),
  headers: {'Authorization': 'Bearer TOKEN'},
);
final data = jsonDecode(response.body)['data'];
```

> Example Response

```json
{
  "code": 200,
  "data": {
    "results": [
      {
        "taskId": "507f1f77bcf86cd799439011",
        "title": "Fix login bug",
        "status": "pending",
        "projectId": "507f1f77bcf86cd799439aaa",
        "projectName": "Project Alpha",
        "updatedAt": "2026-04-10T12:34:56.000Z"
      },
      {
        "taskId": "507f1f77bcf86cd799439012",
        "title": "Dark mode toggle",
        "status": "inProgress",
        "projectId": "507f1f77bcf86cd799439aaa",
        "projectName": "Project Alpha",
        "updatedAt": "2026-04-09T10:00:00.000Z"
      }
    ]
  },
  "msg": "OK"
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
results[].taskId | string | MongoDB ObjectId of the `TaskTemplate` message. Embed in the `[#title:taskId]` token as-is.
results[].title | string | Sanitized task title. Guaranteed to contain no `:` or `]` characters — safe to embed directly in a token.
results[].status | string | One of `pending`, `inProgress`, `done`, `closed`, `reOpen`, `archived`. Matches `TaskStatus` at `weellu_api/src/core/enums/task.status.enum.ts`.
results[].projectId | string | Room id of the project that owns the task.
results[].projectName | string | Display name of the owning project. Shown as the secondary line in the suggestion row.
results[].updatedAt | string (ISO-8601) | Last activity timestamp of the task; used for recency ranking.

### Error Responses

Status | Condition
------ | ---------
400 | `query` exceeds 60 chars, `currentProjectId` is not a 24-hex string, or `limit` is non-numeric.
401 | Missing or invalid JWT.
500 | Unexpected server error.

---

## Open Task Mention (Tap-Time Verify)

`GET https://api.weellu.com/api/v1/channel/task-mention/:taskId/open`

Called when a user taps a rendered `#` task chip inside a chat message. Performs the live access check and returns fresh task data in a single round-trip so the mobile client can show one loading dialog and branch into one of four outcomes (success / access-denied / deleted / network-error).

Access is verified against the task's **current** owning project, not whatever project the task lived in when the referencing message was sent.

### Path Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
taskId | string (24-hex ObjectId) | Yes | MongoDB ObjectId of the `TaskTemplate` message. The value embedded after the colon in a `[#title:taskId]` token.

> Example Request

```shell
curl -X GET "https://api.weellu.com/api/v1/channel/task-mention/507f1f77bcf86cd799439011/open" \
  -H "Authorization: Bearer TOKEN"
```

```javascript
const response = await fetch(
  "https://api.weellu.com/api/v1/channel/task-mention/507f1f77bcf86cd799439011/open",
  { headers: { Authorization: "Bearer TOKEN" } }
);
if (response.status === 200) {
  const { data } = await response.json();
  openTaskDetail(data.task);
} else if (response.status === 403) {
  showSheet("You no longer have access to this task");
} else if (response.status === 404) {
  showSheet("This task was deleted");
}
```

```dart
final response = await http.get(
  Uri.parse(
    'https://api.weellu.com/api/v1/channel/task-mention/507f1f77bcf86cd799439011/open',
  ),
  headers: {'Authorization': 'Bearer TOKEN'},
);
switch (response.statusCode) {
  case 200:
    final data = jsonDecode(response.body)['data'];
    openTaskDetail(data['task']);
    break;
  case 403:
    showSheet('You no longer have access to this task');
    break;
  case 404:
    showSheet('This task was deleted');
    break;
}
```

> Example Response — 200 OK

```json
{
  "code": 200,
  "data": {
    "task": {
      "_id": "507f1f77bcf86cd799439011",
      "rId": "507f1f77bcf86cd799439aaa",
      "mT": "TaskTemplate",
      "content": "Fix login bug",
      "msgAtt": {
        "title": "Fix login bug",
        "status": "pending",
        "designation": ["665a1f2e3b4c5d6e7f8a9b0c"]
      },
      "updatedAt": "2026-04-10T12:34:56.000Z",
      "createdAt": "2026-04-01T09:00:00.000Z"
    },
    "project": {
      "id": "507f1f77bcf86cd799439aaa",
      "name": "Project Alpha",
      "coverUrl": null,
      "role": "Member"
    },
    "permissions": {
      "canEdit": false,
      "canDelete": false,
      "canChangeStatus": true
    }
  },
  "msg": "OK"
}
```

### Response Fields

Field | Type | Description
----- | ---- | -----------
task | object | Full task payload — identical shape to what `TaskChannelService.getById()` returns. Clients can feed this straight into the existing task detail page.
task.msgAtt | object | Live task state. Known keys include `title`, `status`, `designation`, `reOpenTimes`. Treat as opaque beyond `title` and `status`.
project | object | Summary of the owning project for the chip's header.
project.role | string | The caller's `ProjectRoleType` inside this project (e.g., `SuperAdmin`, `Admin`, `Member`).
permissions | object | Per-caller booleans for task-edit affordances. Derived from the caller's project role.

### Error Responses

Status | Code | Condition
------ | ---- | ---------
400 | Bad Request | `taskId` is not a 24-hex ObjectId.
401 | Unauthorized | Missing or invalid JWT.
403 | `TASK_ACCESS_DENIED` | Caller is not an active member of the task's current owning project.
404 | `TASK_NOT_FOUND` | Task message does not exist, is soft-deleted (`deletedAt != null`), or has been hard-deleted. Soft and hard deletes are indistinguishable to the client.
500 | Internal Error | Unexpected server error.

> Example Response — 403 Forbidden

```json
{
  "statusCode": 403,
  "error": "Forbidden",
  "message": "TASK_ACCESS_DENIED"
}
```

> Example Response — 404 Not Found

```json
{
  "statusCode": 404,
  "error": "Not Found",
  "message": "TASK_NOT_FOUND"
}
```

<aside class="notice">
Task mentions do <strong>not</strong> trigger any dedicated notification channel. A message containing one or more <code>[#title:taskId]</code> tokens behaves exactly like any other chat message — recipients in the room receive the room's normal message push, nothing more. Only <code>@</code> user mentions trigger per-recipient mention notifications.
</aside>

<aside class="warning">
<code>#</code> task tokens are <strong>stripped</strong> from a message when it is edited, forwarded, or quote-replied. The token is rewritten to its plain-text <code>#title</code> form before persisting the edit / forward / quote. To re-establish a live task link after editing, re-select the task from the suggestion panel.
</aside>
