# Flutter Message Long-Press Menu

Reference for the chat message long-press context menu in `weellu_chat_mobile`.

**Source files:**

- Menu UI: `packages/v_chat_message_page/lib/src/widgets/message_items/v_message_context_menu.dart`
- Option logic: `packages/v_chat_sdk_core/lib/src/controllers/v_message_item_controller.dart` → `getSheetItems()`
- Long-press trigger: `packages/v_chat_message_page/lib/src/widgets/message_items/v_message_item.dart`

---

## When Long-Press Works at All

| Condition | Result |
|---|---|
| Message is **info/center** (`VMessageType.info`) | No long-press menu — rendered as center info widget |
| Message is **deleted** (`isDeleted`) | Message not rendered — no menu |
| **Call invite** text (`call_invite`, `call_invite_chat`, or Portuguese call text) on **mobile** | Message hidden — no menu |
| Call invite on **web** + **you are receiver** | Message hidden — no menu |
| `messageActions` **or** `reactionBarConfig` is null | Falls back to old `onLongTap` + inline reaction bar (no full context menu) |
| **2+ messages** selected while menu is open | Menu hides until selection returns to 1 |

On long-press, **Select** is auto-triggered (enters selection mode), but **Select** is not shown in the menu UI.

---

## Menu Structure

| Section | Always shown? | Notes |
|---|---|---|
| **Reactions** (👍 ❤️ 😂 😮 😢 🙏 + more emoji) | Yes, when menu opens | Recent emojis from room + defaults; `+` opens full emoji picker |
| **Action list** | Only `isEnabled == true` actions | **Select** and **Cancel** are excluded from display |

**Mobile layout:** Reply, Copy, Edit, Forward, Delete show first. Other enabled actions go under **"Ver mais"**.

**Web layout:** All enabled actions in one list.

**Order:** Delete and Reply in private are always moved to the **bottom** (Delete last).

---

## Action Options and Conditions

| Option | Label (EN) | Shown in list when | Enabled when | Extra disable rules |
|---|---|---|---|---|
| **Reply** | Reply | Always in list | Default **on** | **Off** if any selected message `emitStatus != serverConfirm` (still sending / error) |
| **Reply in private** | Responder no privado | Always in list | Default **on** | **Off** if room is **1:1** OR **you are sender**. **Off** if not `serverConfirm` (via reply rule) |
| **Copy** | Copy | Always in list | Default **off** | **On** if message is **text**, **task details**, or **voice with transcription** (`isAlreadyTranslated`) |
| **Copy image** | Copiar imagem | Only on **web** | Default **off** | **On** only on **web**, **single** message, type **image** |
| **Delete** | Delete | Always in list | Default **on** | If `isAllDeleted`: **only Delete stays enabled**. Sub-sheet: "Delete for everyone" only if **all yours** + **not all deleted** + **all serverConfirm**; "Delete for me" always |
| **Info** | Info | Always in list | Default **on** | **Off** if **not your message**. **Off** if not `serverConfirm` |
| **Pin** / **Unpin** | Fixar / Desafixar | Always (one of two) | Always **on** | Pin if not pinned; Unpin if pinned |
| **Download** | Download | Always in list | Default **off** | **On** if any selected message is **image**, **video**, or **voice** (not generic file) |
| **Translate** | Translate | Always in list | Default **off** | **On** if type is translatable: **text, task, taskDetails, voice, image, sticker, video**. **Off** for poll, location, file, call, link, etc. |
| **Edit** | Editar | Only if: **not** selection mode, **1** message, type **text**, **you are sender** | **on** | Not disabled by sending status in code (can show while sending) |
| **Star** | Star | Only if **all** selected are **not** starred | **on** | — |
| **Unstar** | Un Star | Only if **all** selected **are** starred | **on** | — |
| **Forward** | Forward | Always in list | Default **on** | **Off** if not `serverConfirm`. If `isAllDeleted`: disabled except Delete |
| **Share** | Share | Always in list | Default **on** | **Off on web**. At runtime needs `serverConfirm` or shows error snackbar |
| **Save sticker** | Salvar Sticker | Only if message is **sticker** | **on** | — |
| **Select** | Selecionar | Always in list | **on** | Hidden from menu; auto-fired on long-press |

---

## Status / Sender Quick Reference

| Scenario | What changes in menu |
|---|---|
| **You are sender** + text | Edit may appear; Info stays on (if `serverConfirm`) |
| **You are receiver** | Info **hidden** |
| **Sending** (`emitStatus = sending`) | Reply, Forward, Info **disabled** |
| **Error** (`emitStatus = error`) | Same as sending |
| **Server confirmed** (`serverConfirm`) | Reply, Forward, Info work normally |
| **Deleted for everyone** (`isAllDeleted`) | **Only Delete** enabled |
| **1:1 chat** | Reply in private **hidden** |
| **Group / project** + other's message | Reply in private **available** (if `serverConfirm`) |
| **Web** | Share **disabled**; Copy image **available** for images |
| **Mobile** | Extra actions under **Ver mais**; Share **enabled** |

---

## Message Types vs Common Actions

| Message type | Copy | Download | Translate | Edit | Save sticker |
|---|---|---|---|---|---|
| Text | ✅ | ❌ | ✅ | ✅ (if you sent it) | ❌ |
| Image | ❌ | ✅ | ✅ | ❌ | ❌ |
| Video | ❌ | ✅ | ✅ | ❌ | ❌ |
| Voice | ✅ (if transcribed) | ✅ | ✅ | ❌ | ❌ |
| Sticker | ❌ | ❌ | ✅ | ❌ | ✅ |
| Task details | ✅ | ❌ | ✅ | ❌ | ❌ |
| File (non-voice) | ❌ | ❌ | ❌ | ❌ | ❌ |
| Location | ❌ | ❌ | ❌ | ❌ | ❌ |
| Poll / Link / Call / User share | ❌ | ❌ | ❌ | ❌ | ❌ |

---

## Translatable Message Types

From `VMessageType.isTranslatable`:

- `text`
- `task`
- `taskDetails`
- `voice`
- `image`
- `sticker`
- `video`

---

## Emit Status Values

From `VMessageEmitStatus`:

| Status | Effect on menu |
|---|---|
| `serverConfirm` | Reply, Forward, Info enabled (other rules apply) |
| `sending` | Reply, Forward, Info **disabled** |
| `error` | Reply, Forward, Info **disabled** |
| `unknown` | Reply, Forward, Info **disabled** |

---

## Other Long-Press Flows (Not the Chat Bubble Menu)

### Inbox task row (`onTaskMessageItemLongPress`)

Uses a horizontal **bottom sheet**, not `VMessageContextMenu`.

| Option | Default state |
|---|---|
| Reply | Off |
| Pin / Unpin | On |
| Copy | On |
| Delete | On |
| Info | Off |
| Translate | On |
| Share | On (off on web) |
| Forward | Off |

### Image / video viewer (`getMediaSheetItems`)

Shown in the media viewer action bar.

| Option | Notes |
|---|---|
| Reply | On if `onSwipe` provided |
| Delete | On |
| Info | On — disabled if not `serverConfirm`, or not your message in multi-select |
| Star / Unstar | Same rules as chat menu |
| Forward | On — disabled if not `serverConfirm` |
| Download | On |
| Find in chat | On if `onFindInChat` provided |

Same `isAllDeleted` rule: only Delete stays enabled.

---

## Multi-Select Mode Bar

When multiple messages are selected, the bottom action bar (`input_widget_state.dart`) shows:

| Platform | Actions |
|---|---|
| Mobile | Forward, Share, Delete |
| Web | Forward, Delete (Share excluded) |

From `getSheetItems(isSelectionMode: true)`: only Delete, Forward, Share, Cancel are returned (Cancel used internally).