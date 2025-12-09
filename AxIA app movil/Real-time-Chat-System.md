# Real-time Chat System

> **Relevant source files**
> * [ARCHITECTURE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md)
> * [CHANGELOG_MEJORAS_CHAT.md](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md)

## Purpose and Scope

This document provides an overview of the real-time chat system in AxIA, which enables bidirectional communication between the Flutter mobile application and the AI backend. The chat system uses WebSocket connections for low-latency message delivery and supports both text and audio messages.

For detailed information about specific subsystems, see:

* WebSocket connection lifecycle and protocols: [WebSocket Communication](/axchisan/AxIA/5.1-websocket-communication)
* Message deletion, chat clearing, and local storage: [Message Management](/axchisan/AxIA/5.2-message-management)
* UI improvements and performance optimizations: [Chat UI Enhancements](/axchisan/AxIA/5.3-chat-ui-enhancements)
* Authentication mechanisms: [Authentication & Security](/axchisan/AxIA/4-authentication-and-security)
* Audio recording and playback features: [Audio System](/axchisan/AxIA/6-audio-system)

## System Overview

The real-time chat system connects users to the AxIA AI assistant through a persistent WebSocket connection. Messages flow from the Flutter app through a FastAPI backend, which forwards them to an n8n workflow for AI processing. Responses return through the same WebSocket channel, enabling real-time conversational interactions.

The system supports:

* Text messages with Markdown rendering
* Audio messages with Base64 encoding
* Message history with local caching
* Message deletion and chat clearing
* Offline message queue
* Automatic reconnection on failure

## Core Architecture

```mermaid
flowchart TD

CS["ChatScreen<br>(lib/screens/chat/chat_screen.dart)"]
CP["ChatProvider<br>(lib/providers/chat_provider.dart)"]
CM["ChatMessage Model<br>(lib/models/chat_message.dart)"]
HIVE["Hive Database<br>(Local Storage)"]
AS["AudioService<br>(lib/services/audio_service.dart)"]
WSC["WebSocketChannel<br>(web_socket_channel package)"]
API["ApiService<br>(lib/services/api_service.dart)"]
WS["WebSocket Endpoint<br>/ws/{username}"]
WEBHOOK["n8n Webhook Forwarder<br>POST to n8n"]
APP_MSG["App Message Receiver<br>POST /app-message"]
N8N["AxIA Agent Workflow<br>webhook/axia"]
AI["AI Processing<br>OpenAI/Anthropic"]

CP --> WSC
CP --> API
WSC --> WS
WEBHOOK --> N8N
N8N --> APP_MSG
WS --> WSC
WSC --> CP

subgraph subGraph3 ["n8n Workflow"]
    N8N
    AI
    N8N --> AI
    AI --> N8N
end

subgraph subGraph2 ["FastAPI Backend"]
    WS
    WEBHOOK
    APP_MSG
    WS --> WEBHOOK
    APP_MSG --> WS
end

subgraph subGraph1 ["Network Layer"]
    WSC
    API
end

subgraph subGraph0 ["Flutter App"]
    CS
    CP
    CM
    HIVE
    AS
    CS --> CP
    CP --> CM
    CP --> HIVE
    CP --> AS
    CP --> CS
end
```

**Sources:** [ARCHITECTURE.md L1-L241](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L1-L241)

 [CHANGELOG_MEJORAS_CHAT.md L1-L200](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L1-L200)

## Message Flow Architecture

### Text Message Flow

```mermaid
sequenceDiagram
  participant User
  participant ChatScreen
  participant (chat_screen.dart)
  participant ChatProvider
  participant (chat_provider.dart)
  participant WebSocketChannel
  participant FastAPI
  participant /ws/{username}
  participant n8n Workflow
  participant /webhook/axia
  participant AI Model
  participant Hive Database

  User->>ChatScreen: "Type text in TextField"
  User->>ChatScreen: "Press send button"
  ChatScreen->>ChatProvider: "sendMessage(text)"
  ChatProvider->>Hive Database: "Save message locally"
  ChatProvider->>WebSocketChannel: "sink.add(jsonEncode({...}))"
  note over WebSocketChannel,/ws/{username}: "WebSocket payload:
  WebSocketChannel->>FastAPI: "WebSocket message"
  FastAPI->>n8n Workflow: "POST webhook/axia
  n8n Workflow->>AI Model: {channel: 'app', ...}"
  AI Model->>n8n Workflow: "Process text input"
  n8n Workflow->>FastAPI: "Generate text response"
  FastAPI->>WebSocketChannel: "POST /app-message
  WebSocketChannel->>ChatProvider: {output: '...', type: 'text'}"
  ChatProvider->>Hive Database: "WebSocket response"
  ChatProvider->>ChatScreen: "stream.listen callback"
  ChatScreen->>User: "Save response"
```

**Sources:** [ARCHITECTURE.md L37-L45](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L37-L45)

 [ARCHITECTURE.md L99-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L99-L117)

### Audio Message Flow

```mermaid
sequenceDiagram
  participant User
  participant ChatScreen
  participant (chat_screen.dart)
  participant AudioService
  participant (audio_service.dart)
  participant ChatProvider
  participant (chat_provider.dart)
  participant WebSocketChannel
  participant FastAPI
  participant /ws/{username}
  participant n8n Workflow
  participant Whisper STT
  participant AI Model
  participant ElevenLabs TTS

  User->>ChatScreen: "Press & hold mic button"
  ChatScreen->>AudioService: "startRecording()"
  note over AudioService,(audio_service.dart): "AAC encoding
  User->>ChatScreen: "Release button"
  ChatScreen->>AudioService: "stopRecording()"
  AudioService->>AudioService: "Convert to Base64"
  AudioService-->>ChatScreen: "Return base64 string"
  ChatScreen->>ChatProvider: "sendAudioMessage(base64)"
  ChatProvider->>WebSocketChannel: "sink.add({type: 'audio', audio_base64: '...'})"
  WebSocketChannel->>FastAPI: "WebSocket message"
  FastAPI->>n8n Workflow: "POST webhook/axia"
  n8n Workflow->>Whisper STT: "Transcribe audio"
  Whisper STT->>n8n Workflow: "Text transcript"
  n8n Workflow->>AI Model: "Process transcript"
  AI Model->>n8n Workflow: "Generate response"
  n8n Workflow->>ElevenLabs TTS: "Convert to speech"
  ElevenLabs TTS->>n8n Workflow: "Audio binary"
  n8n Workflow->>n8n Workflow: "Convert to Base64"
  n8n Workflow->>FastAPI: "POST /app-message
  FastAPI->>WebSocketChannel: {audio_base64: '...', debe_ser_audio: true}"
  WebSocketChannel->>ChatProvider: "WebSocket response"
  ChatProvider->>AudioService: "stream.listen callback"
  AudioService->>User: "playAudio(base64)"
```

**Sources:** [ARCHITECTURE.md L46-L52](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L46-L52)

 [CHANGELOG_MEJORAS_CHAT.md L7-L17](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L7-L17)

 [CHANGELOG_MEJORAS_CHAT.md L145-L156](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L145-L156)

## State Management

The chat system uses the Provider pattern for state management, with `ChatProvider` as the central state manager.

### ChatProvider Responsibilities

| Responsibility | Methods | Description |
| --- | --- | --- |
| **WebSocket Connection** | `initializeWebSocket()`, `reconnect()`, `dispose()` | Manages WebSocket lifecycle and connection state |
| **Message Transmission** | `sendMessage(text)`, `sendAudioMessage(base64)` | Sends text and audio messages through WebSocket |
| **Message Storage** | `_saveToHive()`, `loadMessagesFromHive()` | Persists messages locally using Hive database |
| **Message Management** | `deleteMessage(id)`, `clearChat()` | Handles message deletion and chat clearing |
| **State Notifications** | `notifyListeners()` | Updates UI when state changes |
| **Error Handling** | `onError`, `onDone` callbacks | Handles connection failures and reconnection |

### State Flow Diagram

```mermaid
flowchart TD

INIT["Initialization State"]
CONNECTED["Connected State"]
MESSAGES["Messages List<br>List<ChatMessage>"]
LOADING["isLoading bool"]
ERROR["Connection Error State"]
SCREEN["ChatScreen Widget"]
BUILDER["Consumer<ChatProvider>"]
LISTVIEW["ListView.builder<br>(Message List)"]
HIVE_BOX["Hive Box<br>'chat_messages'"]
CACHE["Cached Messages"]

MESSAGES --> BUILDER
MESSAGES --> HIVE_BOX
CACHE --> MESSAGES
LOADING --> BUILDER

subgraph subGraph2 ["Storage Layer"]
    HIVE_BOX
    CACHE
    HIVE_BOX --> CACHE
end

subgraph subGraph1 ["UI Layer"]
    SCREEN
    BUILDER
    LISTVIEW
    BUILDER --> LISTVIEW
    LISTVIEW --> SCREEN
end

subgraph subGraph0 ["ChatProvider State"]
    INIT
    CONNECTED
    MESSAGES
    LOADING
    ERROR
    INIT --> CONNECTED
    CONNECTED --> MESSAGES
    CONNECTED --> ERROR
    ERROR --> CONNECTED
end
```

**Sources:** [ARCHITECTURE.md L148-L189](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L148-L189)

 [CHANGELOG_MEJORAS_CHAT.md L67-L78](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L67-L78)

## WebSocket Protocol

The WebSocket connection is established at app startup when entering `ChatScreen` and uses JWT authentication.

### Connection Details

| Parameter | Value |
| --- | --- |
| **Endpoint** | `wss://apiaxia.axchisan.com/ws/{username}` |
| **Authentication** | JWT token in query parameter: `?token={JWT}` |
| **Protocol** | WebSocket Secure (WSS) |
| **Package** | `web_socket_channel` (Flutter) |
| **Connection Type** | Persistent, bidirectional |

### Message Payload Structure

#### Outgoing Message (Flutter → Backend)

```json
{
  "session_id": "uuid-string",
  "type": "text|audio",
  "text": "message content (for text messages)",
  "audio_base64": "base64-encoded-audio (for audio messages)",
  "timestamp": "2024-11-26T12:00:00Z",
  "user": "username"
}
```

#### Incoming Message (Backend → Flutter)

```
{
  "session_id": "uuid-string",
  "output": "response text or transcript",
  "type": "text|audio",
  "debe_ser_audio": true|false,
  "audio_base64": "base64-encoded-audio (if debe_ser_audio=true)",
  "audio_url": null,
  "timestamp": "2024-11-26T12:00:01Z"
}
```

**Sources:** [ARCHITECTURE.md L19-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L19-L33)

 [ARCHITECTURE.md L61-L65](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L61-L65)

 [ARCHITECTURE.md L99-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L99-L117)

## Local Storage with Hive

The chat system uses Hive, a lightweight NoSQL database, for local message persistence.

### Storage Architecture

```mermaid
flowchart TD

SEND["sendMessage()<br>sendAudioMessage()"]
RECEIVE["WebSocket.stream.listen()"]
LOAD["loadMessagesFromHive()"]
DELETE["deleteMessage()<br>clearChat()"]
BOX["Hive.box('chat_messages')"]
PUT["box.put(key, value)"]
GET["box.values.toList()"]
DEL["box.delete(key)<br>box.clear()"]
FILE["chat_messages.hive<br>(Device Storage)"]

SEND --> PUT
RECEIVE --> PUT
LOAD --> GET
DELETE --> DEL
BOX --> FILE

subgraph subGraph2 ["Storage File"]
    FILE
end

subgraph subGraph1 ["Hive Operations"]
    BOX
    PUT
    GET
    DEL
    PUT --> BOX
    GET --> BOX
    DEL --> BOX
end

subgraph ChatProvider ["ChatProvider"]
    SEND
    RECEIVE
    LOAD
    DELETE
end
```

### Hive Box Operations

| Operation | Method | Purpose |
| --- | --- | --- |
| **Initialize** | `Hive.initFlutter()` | Initialize Hive with Flutter storage path |
| **Open Box** | `Hive.openBox('chat_messages')` | Open or create messages box |
| **Save Message** | `box.put(message.id, message.toJson())` | Persist message to disk |
| **Load Messages** | `box.values.map((e) => ChatMessage.fromJson(e))` | Restore messages on app start |
| **Delete Message** | `box.delete(messageId)` | Remove single message |
| **Clear All** | `box.clear()` | Remove all messages |

**Sources:** [ARCHITECTURE.md L238-L240](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L238-L240)

 [CHANGELOG_MEJORAS_CHAT.md L33-L42](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L33-L42)

## Key Code Entities

### Primary Files

| File Path | Primary Classes/Functions | Purpose |
| --- | --- | --- |
| `lib/screens/chat/chat_screen.dart` | `ChatScreen` widget | Main chat UI with message list, input field, and audio controls |
| `lib/providers/chat_provider.dart` | `ChatProvider` class | State management for chat messages and WebSocket connection |
| `lib/models/chat_message.dart` | `ChatMessage` model | Data structure for individual messages |
| `lib/services/audio_service.dart` | `AudioService` class | Audio recording and playback functionality |
| `lib/services/api_service.dart` | `ApiService` class | HTTP client with JWT authentication |
| `backend/main.py` | `/ws/{username}` endpoint | FastAPI WebSocket server |
| `backend/main.py` | `/app-message` endpoint | Receives responses from n8n |

### ChatMessage Model Structure

The `ChatMessage` model represents individual messages in the chat:

```python
class ChatMessage {
  String id;              // Unique identifier (UUID)
  String text;            // Message content or transcript
  bool isUser;            // true = user message, false = AI response
  DateTime timestamp;     // Message creation time
  String? audioBase64;    // Base64-encoded audio (optional)
  String? type;           // "text" or "audio"
  String? sessionId;      // Links messages to conversation session
}
```

**Sources:** [ARCHITECTURE.md L148-L189](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L148-L189)

 [CHANGELOG_MEJORAS_CHAT.md L64-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L64-L89)

## Connection Lifecycle

```mermaid
stateDiagram-v2
    [*] --> Disconnected : "App starts"
    Disconnected --> Connecting
    Connecting --> Connected : "WebSocket handshake success"
    Connecting --> ConnectionError : "Handshake fails / Timeout"
    Connected --> MessageExchange : "Normal operation"
    MessageExchange --> Connected : "Message sent/received"
    Connected --> ConnectionError : "Network failureonError callback"
    ConnectionError --> Reconnecting : "User taps 'Reconnect' button"
    Reconnecting --> Connecting : "Normal operation"
    Connected --> Disconnected : "User navigates awayChatProvider.dispose()"
    MessageExchange --> Disconnected : "User navigates away"
    ConnectionError --> [*] : "Message sent/received"
    Disconnected --> [*] : "ChatProvider.reconnect()"
```

### Connection States

| State | Indicator | User Action Available |
| --- | --- | --- |
| **Disconnected** | No connection banner | Navigate to ChatScreen to connect |
| **Connecting** | Loading indicator | Wait for connection |
| **Connected** | No indicator (normal operation) | Send messages, record audio |
| **MessageExchange** | Message bubbles appear | Continue conversation |
| **ConnectionError** | Red banner: "Connection lost" | Tap "Reconnect" button |
| **Reconnecting** | Loading indicator on banner | Wait for reconnection |

**Sources:** [ARCHITECTURE.md L131-L146](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L131-L146)

## Error Handling and Recovery

### Error Types

| Error Type | Detection | Recovery Mechanism |
| --- | --- | --- |
| **WebSocket Disconnection** | `onError` callback fires | Display banner with "Reconnect" button |
| **Token Expiration** | 401 response from backend | Redirect to LoginScreen, clear token |
| **Network Timeout** | No response within timeout | Automatic retry with exponential backoff |
| **Message Send Failure** | WebSocket closed before send | Queue message, retry on reconnection |
| **Audio Encoding Error** | Exception in AudioService | Show error dialog, cancel recording |

### Reconnection Flow

```mermaid
sequenceDiagram
  participant ChatProvider
  participant WebSocketChannel
  participant ChatScreen UI
  participant User

  note over WebSocketChannel: "Connection lost"
  WebSocketChannel->>ChatProvider: "onError callback"
  ChatProvider->>ChatScreen UI: "notifyListeners()
  ChatScreen UI->>User: showErrorBanner = true"
  User->>ChatScreen UI: "Display red banner: 'Connection lost. Reconnect?'"
  ChatScreen UI->>ChatProvider: "Tap Reconnect button"
  ChatProvider->>WebSocketChannel: "reconnect()"
  WebSocketChannel-->>ChatProvider: "Close existing connection"
  ChatProvider->>ChatProvider: "Closed"
  ChatProvider->>WebSocketChannel: "await Future.delayed(1 second)"
  WebSocketChannel->>ChatProvider: "initializeWebSocket()"
  ChatProvider->>ChatScreen UI: "Connection established"
  ChatScreen UI->>User: "notifyListeners()
```

**Sources:** [ARCHITECTURE.md L131-L146](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L131-L146)

 [CHANGELOG_MEJORAS_CHAT.md L67-L78](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L67-L78)

## Integration with n8n

The chat system communicates with n8n for AI processing through a webhook-based architecture.

### n8n Integration Points

```mermaid
flowchart TD

WS_HANDLER["WebSocket Handler<br>/ws/{username}"]
N8N_CLIENT["HTTP Client<br>requests library"]
APP_MSG_HANDLER["POST /app-message<br>Response Handler"]
WEBHOOK_NODE["Webhook Node<br>/webhook/axia"]
SWITCH_NODE["Switch Node<br>channel == 'app'"]
APP_BRANCH["App Logic Branch"]
HTTP_NODE["HTTP Response Node<br>POST /app-message"]

N8N_CLIENT --> WEBHOOK_NODE
HTTP_NODE --> APP_MSG_HANDLER

subgraph subGraph1 ["n8n Workflow"]
    WEBHOOK_NODE
    SWITCH_NODE
    APP_BRANCH
    HTTP_NODE
    WEBHOOK_NODE --> SWITCH_NODE
    SWITCH_NODE --> APP_BRANCH
    APP_BRANCH --> HTTP_NODE
end

subgraph subGraph0 ["FastAPI Backend"]
    WS_HANDLER
    N8N_CLIENT
    APP_MSG_HANDLER
    WS_HANDLER --> N8N_CLIENT
    APP_MSG_HANDLER --> WS_HANDLER
end
```

### Webhook Payload Structure (FastAPI → n8n)

```json
{
  "channel": "app",
  "session_id": "uuid-string",
  "user": "username",
  "timestamp": "ISO-8601-timestamp",
  "type": "text|audio",
  "text": "message content (if text)",
  "audio_base64": "base64-string (if audio)",
  "remoteJid": "app:username@domain"
}
```

### Response Payload Structure (n8n → FastAPI)

```
{
  "username": "AxchiSan",
  "session_id": "uuid-string",
  "output": "AI response or transcript",
  "type": "text|audio",
  "debe_ser_audio": true|false,
  "audio_url": null,
  "audio_base64": "base64-string (if audio response)"
}
```

**Sources:** [ARCHITECTURE.md L91-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L91-L117)

 [CHANGELOG_MEJORAS_CHAT.md L126-L156](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L126-L156)

## Performance Optimizations

The chat system includes several performance optimizations to ensure smooth operation:

### Optimization Summary

| Optimization | Implementation | Impact |
| --- | --- | --- |
| **Lazy Loading** | `ListView.builder` with itemBuilder | Only renders visible messages |
| **Simplified Animations** | Removed complex fade animations | Eliminates frame drops |
| **Jump vs Animate** | Changed `animateTo()` to `jumpTo()` | Instant scroll without lag |
| **Reduced Logging** | Removed debug print statements | Cleaner console, less overhead |
| **Singleton WebSocket** | Single persistent connection | Avoids reconnection overhead |
| **Local Caching** | Hive database for message history | Instant load on app restart |
| **Base64 Encoding** | Encode audio client-side | Reduces server processing time |

### Before and After Performance

| Metric | Before Optimization | After Optimization |
| --- | --- | --- |
| **Scroll Lag** | Noticeable lag with 50+ messages | Smooth scrolling |
| **Frame Drops** | 39+ frames during animations | <5 frames |
| **Console Saturation** | 100+ debug prints per message | Only critical logs |
| **Memory Usage** | All messages in memory | Only visible messages rendered |

**Sources:** [CHANGELOG_MEJORAS_CHAT.md L49-L63](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L49-L63)

 [CHANGELOG_MEJORAS_CHAT.md L158-L169](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L158-L169)

## Message Types and Rendering

The chat system supports two primary message types with different rendering strategies:

### Message Type Comparison

| Feature | Text Message | Audio Message |
| --- | --- | --- |
| **Input Method** | TextField typing | Microphone button (press & hold) |
| **Encoding** | Plain text (UTF-8) | AAC audio → Base64 |
| **Transmission Size** | 1-2 KB typical | 50-200 KB typical |
| **Server Processing** | Direct to AI model | Whisper transcription → AI |
| **Response Format** | Markdown text | Text + optional audio Base64 |
| **UI Rendering** | `flutter_markdown` widget | `just_audio` player widget |
| **Playback Controls** | N/A | Play/pause, seek, speed (0.5x-2x) |
| **Storage** | Text in Hive | Base64 string in Hive |

### Time Format Display

All messages display timestamps in 12-hour format with AM/PM:

```
// Example: 11:49 PM instead of 23:49
DateFormat('h:mm a').format(message.timestamp)
```

**Sources:** [CHANGELOG_MEJORAS_CHAT.md L19-L31](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L19-L31)

 [CHANGELOG_MEJORAS_CHAT.md L43-L48](https://github.com/axchisan/AxIA/blob/1fe26c44/CHANGELOG_MEJORAS_CHAT.md#L43-L48)