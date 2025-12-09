# Messaging System

> **Relevant source files**
> * [src/frontend/friends/amigos.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php)
> * [src/frontend/mensajes/mensajes.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php)
> * [src/frontend/notificaciones/notificaciones.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/notificaciones/notificaciones.php)
> * [src/frontend/perfil/perfil.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/perfil/perfil.php)

## Purpose and Scope

The Messaging System enables authenticated users to exchange real-time messages with their friends. This system enforces strict access control, allowing communication only between users who have accepted friend requests. The system supports full CRUD operations on messages, with users able to edit and delete only their own messages.

For information about establishing friendships, see [Friends Management](/axchisan/El-rincon-de-ADSO/6.1-friends-management). For notification delivery when messages are received, see [Notifications](/axchisan/El-rincon-de-ADSO/6.3-notifications). For overall social feature context, see [Social Features](/axchisan/El-rincon-de-ADSO/6-social-features).

**Sources:** [src/frontend/mensajes/mensajes.php L1-L87](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L1-L87)

---

## System Architecture

The messaging system follows a client-server architecture with real-time polling for updates. The frontend interface communicates with a RESTful API backend to perform message operations.

### Component Overview

```

```

**Sources:** [src/frontend/mensajes/mensajes.php L200-L235](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L200-L235)

 [src/frontend/mensajes/mensajes.php L237-L482](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L237-L482)

---

## Access Control and Friendship Validation

The messaging system enforces strict access control. Users can only exchange messages with accepted friends. This validation occurs at both the page load level and the API level.

### Friendship Verification Process

| Validation Step | Implementation | Location |
| --- | --- | --- |
| Session Check | Verify `$_SESSION['usuario_id']` exists | [mensajes.php L8-L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/mensajes.php#L8-L12) |
| Friend ID Validation | Check `$_GET['friend_id']` is numeric | [mensajes.php L40-L44](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/mensajes.php#L40-L44) |
| Friendship Status | Query `amistades` table for `estado = 'accepted'` | [mensajes.php L49-L59](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/mensajes.php#L49-L59) |
| Friend Exists | Verify friend user record exists | [mensajes.php L68-L77](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/mensajes.php#L68-L77) |

### Friendship Query

The system verifies bidirectional friendship using this query pattern:

```

```

If the count returns 0, the user is redirected to `amigos.php`.

**Sources:** [src/frontend/mensajes/mensajes.php L39-L65](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L39-L65)

---

## Message Lifecycle

The following diagram illustrates the complete lifecycle of a message from creation to display or deletion:

```

```

**Sources:** [src/frontend/mensajes/mensajes.php L430-L461](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L430-L461)

 [src/frontend/mensajes/mensajes.php L356-L428](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L356-L428)

---

## Frontend Interface

The frontend interface (`mensajes.php`) provides a chat-style UI with message history and input controls.

### Page Structure

```

```

### Chat Header Initialization

The chat header displays the friend's information retrieved from the database:

* **Friend Name**: `$friend_name` from usuarios table [79](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/79)
* **Friend Email**: `$friend_email` from usuarios table [80](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/80)
* **Friend Image**: Loads from `uploads/` or uses placeholder [81](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/81)

**Sources:** [src/frontend/mensajes/mensajes.php L200-L216](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L200-L216)

 [src/frontend/mensajes/mensajes.php L68-L82](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L68-L82)

---

## Backend API Operations

The messaging API (`backend/api/mensajes.php`) handles all CRUD operations for messages. The frontend communicates with this endpoint using different HTTP methods.

### API Endpoint Methods

| HTTP Method | Operation | Request Body | Response |
| --- | --- | --- | --- |
| GET | Retrieve messages | Query param: `friend_id` | JSON array of message objects |
| POST | Create message | `{ friend_id, content }` | Success/error status |
| PUT | Update message | `{ message_id, content }` | Success/error status |
| DELETE | Delete message | `{ message_id }` | Success/error status |

### GET: Load Messages

The `loadMessages()` function fetches messages for the current conversation:

```

```

The API returns messages in JSON format:

```

```

### POST: Send Message

The `sendMessage()` function creates a new message:

```

```

### PUT: Edit Message

Users can edit their own messages through an inline form:

```

```

### DELETE: Remove Message

Users can delete their own messages after confirmation:

```

```

**Sources:** [src/frontend/mensajes/mensajes.php L261-L328](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L261-L328)

 [src/frontend/mensajes/mensajes.php L430-L461](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L430-L461)

 [src/frontend/mensajes/mensajes.php L356-L428](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L356-L428)

---

## Real-time Updates and Polling

The messaging system implements real-time message delivery through client-side polling at 5-second intervals.

### Polling Mechanism

```

```

### Incremental Loading Strategy

The system uses `lastMessageId` tracking to avoid re-rendering all messages on each poll:

* **Initial Load**: `lastMessageId = 0`, renders all messages [177, 285-287](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/177, 285-287)
* **Polling Updates**: Filters messages where `msg.id > lastMessageId` [279-282](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/279-282)
* **Update Tracking**: Sets `lastMessageId` to highest message ID seen [314-316](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/314-316)
* **Scroll Behavior**: Auto-scrolls to bottom on new messages [323](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/323)

The polling interval is configured at:

```

```

**Sources:** [src/frontend/mensajes/mensajes.php L261-L328](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L261-L328)

 [src/frontend/mensajes/mensajes.php L463-L465](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L463-L465)

---

## Message CRUD Operations

### Create: Sending Messages

Users send messages by typing in the input field and clicking send or pressing Enter.

**Validation:**

* Empty messages are not sent [433-435](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/433-435)
* Content is trimmed before validation [432](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/432)

**Process:**

1. User enters text in `#message-input`
2. Click `#send-message-btn` or press Enter [468-475](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/468-475)
3. `sendMessage()` validates content
4. POST request to API with `friend_id` and `content`
5. Input field cleared on success [452](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/452)
6. `loadMessages()` called to display new message [453](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/453)

### Read: Displaying Messages

Messages are displayed with different styling based on sender:

* **Sent Messages**: `.message--sent` class, aligned right [292](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/292)
* **Received Messages**: `.message--received` class, aligned left [292](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/292)

Each message includes:

* Message content text [296](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/296)
* Timestamp formatted as `dd/mm/yyyy hh:mm` [297](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/297)
* Edit/Delete buttons (for own messages only) [299-308](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/299-308)

### Update: Editing Messages

Users can edit their own messages through an inline form that appears on hover.

**Edit Flow:**

1. Hover over own message to reveal actions [105-108](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/105-108)
2. Click "Editar" button [333-341](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/333-341)
3. Edit form displays with current content [305-308](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/305-308)
4. Modify text in `.message__edit-input` [306](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/306)
5. Click "Guardar" or "Cancelar" [307-308](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/307-308)
6. PUT request updates message if saved [357-392](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/357-392)
7. Messages reload to show updated content [383](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/383)

### Delete: Removing Messages

Users can delete their own messages after confirmation.

**Delete Flow:**

1. Hover over own message to reveal actions
2. Click "Borrar" button [395-427](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/395-427)
3. Confirmation dialog appears [397-399](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/397-399)
4. If confirmed, DELETE request sent with `message_id` [405-413](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/405-413)
5. Messages reload with deleted message removed [418](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/418)

**Sources:** [src/frontend/mensajes/mensajes.php L100-L152](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L100-L152)

 [src/frontend/mensajes/mensajes.php L290-L311](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L290-L311)

 [src/frontend/mensajes/mensajes.php L330-L428](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L330-L428)

 [src/frontend/mensajes/mensajes.php L430-L475](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L430-L475)

---

## Database Schema

The messaging system relies on two primary tables: `mensajes` and `amistades`.

### Messages Table Structure

The `mensajes` table stores all message records:

| Column | Type | Description |
| --- | --- | --- |
| `id` | INTEGER/SERIAL | Primary key, auto-increment |
| `remitente_id` | INTEGER | Foreign key to usuarios.id (sender) |
| `destinatario_id` | INTEGER | Foreign key to usuarios.id (recipient) |
| `contenido` | TEXT | Message content |
| `fecha_envio` | TIMESTAMP | Message creation timestamp |
| `editado` | BOOLEAN | Flag indicating if message was edited |
| `fecha_edicion` | TIMESTAMP | Last edit timestamp (nullable) |

### Friendship Verification

The `amistades` table controls messaging access:

| Column | Type | Description |
| --- | --- | --- |
| `id` | INTEGER/SERIAL | Primary key |
| `usuario_id` | INTEGER | Foreign key to usuarios.id |
| `amigo_id` | INTEGER | Foreign key to usuarios.id |
| `estado` | VARCHAR | Enum: 'pending', 'accepted', 'rejected' |
| `fecha_creacion` | TIMESTAMP | Friendship creation/update date |

**Key Constraint**: Messages can only be sent when `estado = 'accepted'` for the bidirectional friendship relationship.

**Sources:** [src/frontend/mensajes/mensajes.php L49-L65](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L49-L65)

---

## Security Considerations

The messaging system implements multiple security layers:

### Session Validation

All pages require active session with `usuario_id`:

* Session lifetime: 3600 seconds (1 hour) [2-3](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/2-3)
* Session check at page load [8-12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/8-12)
* Redirect to login if session missing [9-11](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/9-11)

### Friendship-Based Access Control

Multi-step validation ensures only friends can message:

1. **Friend ID Validation**: Ensures numeric, valid friend ID [40-44](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/40-44)
2. **Database Verification**: Queries `amistades` table [49-59](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/49-59)
3. **Bidirectional Check**: Verifies friendship in both directions [51-53](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/51-53)
4. **Status Check**: Only allows `estado = 'accepted'` [53](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/53)
5. **Redirect on Failure**: Returns to `amigos.php` if unauthorized [62-64](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/62-64)

### Message Ownership Validation

Edit and delete operations are restricted to message owners:

* Frontend shows buttons only for `remitente_id == userId` [299-308](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/299-308)
* Backend API must validate ownership before modification
* Message ID passed explicitly in requests [360, 402](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/360, 402)

### Input Sanitization

User input is sanitized at multiple points:

* **Display**: `htmlspecialchars()` on friend data [79-80](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/79-80)
* **Database**: PDO prepared statements prevent SQL injection [19-22, 49-59](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/19-22, 49-59)
* **Client-side**: Empty message validation [363-366](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/363-366)
* **Trimming**: Content trimmed before sending [432](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/432)

### XSS Prevention

Message content is rendered as text within paragraph tags, preventing script execution. The system uses:

* Escaped HTML in friend name display [79-80](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/79-80)
* JavaScript `.textContent` or proper escaping in message rendering
* Avatar URLs use cache-busting with time parameter [81](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/81)

**Sources:** [src/frontend/mensajes/mensajes.php L1-L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L1-L12)

 [src/frontend/mensajes/mensajes.php L39-L87](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L39-L87)

 [src/frontend/mensajes/mensajes.php L290-L311](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L290-L311)

 [src/frontend/mensajes/mensajes.php L356-L428](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L356-L428)

---

## Integration Points

The messaging system integrates with other platform components:

### Friends Management Integration

* **Entry Point**: "Chatear" button on friend cards [src/frontend/friends/amigos.php L378-L380](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L378-L380)
* **URL Format**: `mensajes.php?friend_id={id}`
* **Dependency**: Requires accepted friendship status

### Notifications Integration

* Messages trigger notifications for recipients
* Notification badge displays unread count [167-174](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/167-174)
* Links to notifications page in navbar [168](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/168)
* For details, see [Notifications](/axchisan/El-rincon-de-ADSO/6.3-notifications)

### User Profile Integration

* Friend avatar displayed in chat header [204-206](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/204-206)
* Links to profile viewing [src/frontend/friends/amigos.php L381-L383](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L381-L383)
* Online status determination using 5-minute threshold [src/frontend/friends/amigos.php L200-L216](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L200-L216)
* For profile details, see [User Profiles](/axchisan/El-rincon-de-ADSO/6.4-user-profiles)

### Database Connection

* Uses singleton pattern via `conexionDB::getConexion()` [15](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/15)
* PDO connection with prepared statements [19-22](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/19-22)
* For database configuration, see [Database Configuration](/axchisan/El-rincon-de-ADSO/2.1-database-configuration)

**Sources:** [src/frontend/mensajes/mensajes.php L6-L22](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L6-L22)

 [src/frontend/mensajes/mensajes.php L167-L174](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L167-L174)

 [src/frontend/friends/amigos.php L378-L383](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L378-L383)