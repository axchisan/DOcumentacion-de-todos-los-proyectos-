# Message Management

> **Relevant source files**
> * [ARCHITECTURE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md)
> * [CHANGELOG_MEJORAS_CHAT.md](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md)

## Purpose and Scope

This document describes the message management subsystem within the AxIA chat interface, focusing on local message storage, deletion operations, and state synchronization. Message management encompasses the persistence of chat messages using Hive database, individual message deletion, bulk chat clearing, and the mechanisms that propagate these changes to the UI.

For information about the underlying WebSocket communication protocol and message transmission, see [WebSocket Communication](/axchisan/AxIA/5.1-websocket-communication). For UI-specific enhancements like time formatting and visual optimizations, see [Chat UI Enhancements](/axchisan/AxIA/5.3-chat-ui-enhancements).

---

## Local Storage Architecture

AxIA uses Hive as its local NoSQL database for persisting chat messages on the device. This enables offline message access, reduces server load, and provides instant message history when the chat screen opens.

### Hive Integration Overview

```

```

**Sources:** [ARCHITECTURE.md L148-L189](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L148-L189)

 [ARCHITECTURE.md L238](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L238-L238)

### Message Persistence Flow

When messages are sent or received through the WebSocket connection, they are immediately persisted to the Hive database. The `ChatProvider` acts as the intermediary between the WebSocket stream and local storage.

| Operation | Storage Action | UI Update Trigger |
| --- | --- | --- |
| Send Message | Write to Hive → Send via WebSocket | `notifyListeners()` |
| Receive Message | WebSocket callback → Write to Hive | `notifyListeners()` |
| Load History | Read from Hive on init | `notifyListeners()` |
| Delete Message | Remove from Hive | `notifyListeners()` |
| Clear Chat | Clear Hive box | `notifyListeners()` |

**Sources:** [ARCHITECTURE.md L36-L44](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L36-L44)

 [CHANGELOG_MEJORAS_CHAT.md L196-L199](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L196-L199)

---

## Message Data Model

The `ChatMessage` model represents individual messages stored in Hive and displayed in the UI. This model supports both text and audio message types.

### ChatMessage Structure

```

```

The model includes:

* **id**: Unique identifier for the message
* **text**: Message content (or transcription for audio)
* **type**: Either `"text"` or `"audio"`
* **timestamp**: Message creation time
* **isUser**: `true` if sent by user, `false` if from AxIA
* **audioBase64**: Base64-encoded audio data for audio messages
* **sessionId**: WebSocket session identifier for correlation

**Sources:** [ARCHITECTURE.md L156](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L156-L156)

 [ARCHITECTURE.md L63-L64](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L63-L64)

---

## Individual Message Deletion

Users can delete individual messages by long-pressing on any message bubble. This triggers a context menu with a delete option.

### Deletion Flow Diagram

```

```

**Sources:** [CHANGELOG_MEJORAS_CHAT.md L32-L42](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L32-L42)

 [CHANGELOG_MEJORAS_CHAT.md L67-L77](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L67-L77)

### Implementation Details

The `deleteMessage()` method in `ChatProvider` performs the following operations:

1. **Remove from in-memory list**: Filters the message from the `messages` list
2. **Delete from Hive**: Removes the message from persistent storage by key
3. **Notify listeners**: Triggers UI rebuild via `notifyListeners()`

The operation is synchronous from the UI perspective, providing immediate feedback. No server-side deletion occurs as messages are stored locally only.

**Sources:** [CHANGELOG_MEJORAS_CHAT.md L73-L76](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L73-L76)

 [lib/providers/chat_provider.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/providers/chat_provider.dart)

---

## Bulk Chat Clearing

The clear chat functionality allows users to delete all messages at once. This is accessible through the three-dot menu in the chat screen's app bar.

### Clear Chat Flow

```

```

**Sources:** [CHANGELOG_MEJORAS_CHAT.md L35-L42](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L35-L42)

 [CHANGELOG_MEJORAS_CHAT.md L67-L71](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L67-L71)

### Safety Mechanisms

The clear chat operation includes user protection:

1. **Confirmation Dialog**: Shows before executing the operation
2. **Explicit Confirmation Required**: User must actively click "Confirm" button
3. **Immediate Feedback**: UI updates immediately after confirmation
4. **No Undo Available**: Operation is permanent (messages cannot be recovered)

| Trigger | Action | Storage Impact | UI Impact |
| --- | --- | --- | --- |
| Menu tap | Show dialog | None | Dialog displayed |
| Confirm | Execute clear | All messages deleted | Empty chat view |
| Cancel | Dismiss dialog | None | Returns to chat |

**Sources:** [CHANGELOG_MEJORAS_CHAT.md L37-L39](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L37-L39)

---

## State Management and UI Updates

The `ChatProvider` uses Flutter's `ChangeNotifier` pattern to manage message state and propagate updates to the UI. All mutation operations trigger rebuild cycles.

### State Update Propagation

```

```

**Sources:** [ARCHITECTURE.md L36-L44](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L36-L44)

 [lib/providers/chat_provider.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/providers/chat_provider.dart)

### Provider Pattern Implementation

The `ChatProvider` extends `ChangeNotifier` and is provided at the application root using the `provider` package. The chat screen consumes this provider to:

1. **Access message list**: `Provider.of<ChatProvider>(context).messages`
2. **Listen to changes**: `Consumer<ChatProvider>` widget automatically rebuilds
3. **Trigger operations**: Call provider methods directly

```

```

**Sources:** [ARCHITECTURE.md L160-L171](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L160-L171)

 [lib/main.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/main.dart)

---

## Message List Rendering

The chat UI uses `ListView.builder` for efficient rendering of message lists, with optimizations to prevent lag during scrolling.

### Rendering Optimizations

| Technique | Purpose | Implementation |
| --- | --- | --- |
| `ListView.builder` | Lazy loading | Only builds visible items |
| `jumpTo()` vs `animateTo()` | Reduce lag | Jump instead of animate scroll |
| Item keys | Widget reuse | Uses message ID as key |
| Reverse ordering | Chat UX | `reverse: true` for bottom-up |

**Sources:** [CHANGELOG_MEJORAS_CHAT.md L49-L55](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L49-L55)

 [CHANGELOG_MEJORAS_CHAT.md L160-L169](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L160-L169)

### Scroll Management

When new messages arrive, the chat automatically scrolls to the bottom to show the latest message:

```

```

The scroll controller uses `jumpTo(0)` instead of `animateTo()` to avoid frame drops during rapid message arrival. Because the ListView is reversed, position 0 represents the bottom of the chat.

**Sources:** [CHANGELOG_MEJORAS_CHAT.md L52-L53](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L52-L53)

 [lib/screens/chat/chat_screen.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/screens/chat/chat_screen.dart)

---

## Synchronization Considerations

Message management in AxIA is entirely client-side. The system does not synchronize deleted messages with the backend or across devices.

### Local-Only Storage Model

```

```

**Key characteristics:**

* **No cloud backup**: Messages exist only on the local device
* **No cross-device sync**: Each device maintains independent message history
* **No server storage**: Backend does not persist messages
* **Real-time only**: Messages flow through WebSocket but are not stored server-side

This design prioritizes:

1. **Privacy**: User messages remain on device
2. **Simplicity**: No complex sync logic required
3. **Performance**: No server-side database queries for message history
4. **Offline capability**: Full message history available without network

**Sources:** [ARCHITECTURE.md L236-L241](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L236-L241)

 [CHANGELOG_MEJORAS_CHAT.md L196-L199](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L196-L199)

---

## Code References Summary

The message management system is implemented across these key files:

| File | Primary Responsibility |
| --- | --- |
| `lib/models/chat_message.dart` | Message data structure and serialization |
| `lib/providers/chat_provider.dart` | State management, CRUD operations, Hive interface |
| `lib/screens/chat/chat_screen.dart` | UI rendering, gesture detection, user interactions |
| `lib/services/audio_service.dart` | Audio message handling (referenced for audio messages) |

**Sources:** [ARCHITECTURE.md L156-L190](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L156-L190)

 [CHANGELOG_MEJORAS_CHAT.md L64-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L64-L89)