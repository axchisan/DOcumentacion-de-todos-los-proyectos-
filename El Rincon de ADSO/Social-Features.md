# Social Features

> **Relevant source files**
> * [README.md](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/README.md)
> * [src/frontend/friends/amigos.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php)
> * [src/frontend/inicio/img/icono.png](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/img/icono.png)
> * [src/frontend/inicio/img/inicio.png](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/img/inicio.png)
> * [src/frontend/login/img/slide1.jpg](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/login/img/slide1.jpg)
> * [src/frontend/login/img/slide2.jpg](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/login/img/slide2.jpg)
> * [src/frontend/login/img/slide3.jpg](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/login/img/slide3.jpg)
> * [src/frontend/mensajes/mensajes.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php)
> * [src/frontend/notificaciones/notificaciones.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/notificaciones/notificaciones.php)
> * [src/frontend/perfil/perfil.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/perfil/perfil.php)

## Purpose and Scope

This document provides an overview of the social networking capabilities within El Rincón de ADSO, including the architecture, data models, and interaction patterns that enable users to connect, communicate, and share resources. The social features form a tightly integrated subsystem that controls access to messaging, profiles, and community engagement based on friendship relationships.

**Scope of this document:**

* Overview of the social features architecture and database design
* Core workflows for friend management, messaging, and notifications
* Frontend-backend interaction patterns and real-time update mechanisms
* Security and access control enforced through friendship relationships

**For detailed information on specific subsystems, see:**

* [Friends Management](/axchisan/El-rincon-de-ADSO/6.1-friends-management) - Friend requests, acceptance/rejection, friend lists, and user search
* [Messaging System](/axchisan/El-rincon-de-ADSO/6.2-messaging-system) - Real-time chat, message CRUD operations, and friend-only messaging
* [Notifications](/axchisan/El-rincon-de-ADSO/6.3-notifications) - Notification types, polling mechanism, and badge display
* [User Profiles](/axchisan/El-rincon-de-ADSO/6.4-user-profiles) - Profile viewing, online status, and friendship requirements
* [Comments and Ratings](/axchisan/El-rincon-de-ADSO/7.1-comments-and-ratings) - Community engagement on resources (separate from social features)

---

## System Architecture Overview

The social features system consists of four primary subsystems that work together to create a cohesive social networking experience. All features require user authentication and enforce friend-based access control.

```

```

**Sources:** [src/frontend/friends/amigos.php L1-L404](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L1-L404)

 [src/frontend/mensajes/mensajes.php L1-L484](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L1-L484)

 [src/frontend/notificaciones/notificaciones.php L1-L294](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/notificaciones/notificaciones.php#L1-L294)

 [src/frontend/perfil/perfil.php L1-L235](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/perfil/perfil.php#L1-L235)

---

## Database Schema

The social features rely on three core database tables that establish relationships between users and track their interactions.

### Core Tables

| Table | Primary Key | Foreign Keys | Purpose |
| --- | --- | --- | --- |
| `usuarios` | `id` | - | Stores user account data, profile information, and online status |
| `amistades` | `id` | `usuario_id` → `usuarios.id``amigo_id` → `usuarios.id` | Manages friendship relationships and request states |
| `mensajes` | `id` | `remitente_id` → `usuarios.id``destinatario_id` → `usuarios.id` | Stores chat messages between friends |
| `notificaciones` | `id` | `usuario_id` → `usuarios.id``relacionado_id` → `usuarios.id` | Tracks notifications for friend requests, acceptances, and messages |

### Table: amistades

```

```

**Estado values:**

* `'pending'` - Friend request sent but not yet accepted
* `'accepted'` - Friendship established (both users are friends)
* `'rejected'` - Friend request was rejected

**Key characteristics:**

* Unidirectional relationship: `usuario_id` is the sender, `amigo_id` is the receiver
* When a request is accepted, the relationship remains unidirectional but grants bidirectional access
* Queries must check both directions: `(usuario_id = X AND amigo_id = Y) OR (usuario_id = Y AND amigo_id = X)`

**Sources:** [src/frontend/friends/amigos.php L42-L72](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L42-L72)

 [src/frontend/friends/amigos.php L181-L197](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L181-L197)

### Table: notificaciones

```

```

**Tipo values:**

* `'friend_request'` - New friend request received
* `'friend_request_accepted'` - Friend request was accepted
* `'message'` - New message received (when implemented)

**Sources:** [src/frontend/friends/amigos.php L95-L103](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L95-L103)

 [src/frontend/friends/amigos.php L131-L138](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L131-L138)

---

## Friend Management Workflow

The friend management system implements a request-based model where users must send friend requests that are accepted or rejected by the recipient.

```

```

**Sources:** [src/frontend/friends/amigos.php L39-L108](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L39-L108)

 [src/frontend/friends/amigos.php L110-L160](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L110-L160)

### User Search with Exclusions

The friend search functionality excludes users who already have pending requests or established friendships:

```

```

**Sources:** [src/frontend/friends/amigos.php L42-L72](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L42-L72)

### Friend List Retrieval

The friends list query handles the bidirectional nature of the `amistades` table:

```

```

**Sources:** [src/frontend/friends/amigos.php L189-L197](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L189-L197)

---

## Messaging System Architecture

The messaging system enables real-time chat between friends using a polling-based approach. Messages can only be sent between users who have an accepted friendship.

```

```

**Sources:** [src/frontend/mensajes/mensajes.php L1-L484](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L1-L484)

 [src/backend/api/mensajes.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/api/mensajes.php)

 (referenced but not provided)

### Friendship Verification

Before any messaging operations, the system verifies that both users are friends:

[src/frontend/mensajes/mensajes.php L49-L65](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L49-L65)

```

```

### Real-time Message Loading

The frontend implements 5-second polling to fetch new messages:

[src/frontend/mensajes/mensajes.php L262-L328](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L262-L328)

Key JavaScript functions:

* `loadMessages()` - Fetches messages via `GET /api/mensajes.php?friend_id={id}`
* `lastMessageId` - Tracks the last loaded message to avoid redundant renders
* `setInterval(loadMessages, 5000)` - Polls every 5 seconds

### Message CRUD Operations

The messaging system supports full CRUD operations, but only for the message sender:

**Send Message:**
[src/frontend/mensajes/mensajes.php L431-L461](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L431-L461)

* `POST /api/mensajes.php` with `{friend_id, content}`
* Creates notification for recipient

**Edit Message:**
[src/frontend/mensajes/mensajes.php L357-L392](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L357-L392)

* `PUT /api/mensajes.php` with `{message_id, content}`
* Only sender can edit their own messages
* UI shows edit form on hover over sent messages

**Delete Message:**
[src/frontend/mensajes/mensajes.php L395-L427](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L395-L427)

* `DELETE /api/mensajes.php` with `{message_id}`
* Confirmation prompt before deletion
* Only sender can delete their own messages

---

## Notification System

The notification system tracks three types of events and uses client-side polling to provide near-real-time updates.

### Notification Types and Generation

```

```

**Sources:** [src/frontend/friends/amigos.php L95-L103](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L95-L103)

 [src/frontend/friends/amigos.php L131-L138](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L131-L138)

### Real-time Notification Polling

The notifications page implements aggressive 5-second polling to provide near-real-time updates:

[src/frontend/notificaciones/notificaciones.php L139-L216](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/notificaciones/notificaciones.php#L139-L216)

**JavaScript polling mechanism:**

```

```

### Notification Badge Display

All authenticated pages display an unread notification count in the navigation:

[src/frontend/friends/amigos.php L33-L36](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L33-L36)

```

```

The badge is rendered in the navbar:
[src/frontend/friends/amigos.php L249-L254](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L249-L254)

```

```

### Mark as Read Functionality

Users can mark individual notifications or all notifications as read:

[src/frontend/notificaciones/notificaciones.php L218-L251](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/notificaciones/notificaciones.php#L218-L251)

**Individual notification:**

```

```

**All notifications:**
[src/frontend/notificaciones/notificaciones.php L254-L278](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/notificaciones/notificaciones.php#L254-L278)

```

```

---

## User Profile System

The profile viewing system enforces friendship-based access control and displays user information with online status indicators.

### Profile Access Control

Profiles can only be viewed by friends:

[src/frontend/perfil/perfil.php L49-L66](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/perfil/perfil.php#L49-L66)

```

```

### Online Status Calculation

The system determines if a user is "online" based on the `ultima_conexion` timestamp:

[src/frontend/perfil/perfil.php L91-L107](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/perfil/perfil.php#L91-L107)

```

```

**Online threshold:** 5 minutes since last connection

**Sources:** [src/frontend/perfil/perfil.php L91-L107](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/perfil/perfil.php#L91-L107)

 [src/frontend/friends/amigos.php L200-L216](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L200-L216)

### Profile Data Display

Profile information includes:

[src/frontend/perfil/perfil.php L69-L88](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/perfil/perfil.php#L69-L88)

| Field | Database Column | Fallback |
| --- | --- | --- |
| Name | `nombre_usuario` | (required) |
| Email | `correo` | (required) |
| Phone | `telefono` | "No especificado" |
| Profession | `profesion` | "No especificado" |
| Bio | `bio` | "Este usuario aún no ha añadido una biografía." |
| Avatar | `imagen` | Pravatar placeholder |
| Online Status | Calculated from `ultima_conexion` | "Desconocida" |

---

## Security and Access Control

The social features implement multiple layers of security to ensure data privacy and prevent unauthorized access.

### Session-based Authentication

All social feature pages require active user sessions:

[src/frontend/friends/amigos.php L7-L11](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L7-L11)

```

```

### Friendship-based Access Control

Access to messaging and profiles is restricted to accepted friendships:

```

```

**Friendship verification pattern used in:**

* [src/frontend/mensajes/mensajes.php L49-L65](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L49-L65)  - Messaging access
* [src/frontend/perfil/perfil.php L49-L66](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/perfil/perfil.php#L49-L66)  - Profile viewing

### SQL Injection Prevention

All database queries use PDO prepared statements:

[src/frontend/friends/amigos.php L80-L86](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L80-L86)

```

```

### Output Escaping

All user-generated content is escaped before display:

[src/frontend/friends/amigos.php L29-L30](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L29-L30)

```

```

---

## Frontend-Backend Integration Patterns

The social features use a hybrid approach combining server-side rendering for initial page loads and AJAX for real-time updates.

### Page Load Pattern

```

```

### AJAX Update Pattern

```

```

**Sources:** [src/frontend/notificaciones/notificaciones.php L280-L282](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/notificaciones/notificaciones.php#L280-L282)

 [src/frontend/mensajes/mensajes.php L464-L465](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L464-L465)

### Form Submission Pattern

For actions like sending friend requests, the system uses traditional form POST:

[src/frontend/friends/amigos.php L76-L108](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L76-L108)

```

```

---

## Data Flow Examples

### Complete Friend Request Flow

```

```

**Sources:** [src/frontend/friends/amigos.php L76-L142](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/friends/amigos.php#L76-L142)

### Message Exchange Flow

```

```

**Sources:** [src/frontend/mensajes/mensajes.php L431-L465](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/mensajes/mensajes.php#L431-L465)

---

## Summary

The social features subsystem provides a complete friend-based social networking layer with the following characteristics:

**Architecture:**

* Unidirectional friend relationships stored bidirectionally accessible
* Polling-based real-time updates (5-second intervals)
* Friend-only access control for messaging and profiles
* Notification system tracking three event types

**Key Components:**

* `amigos.php` - Friend management interface with search, requests, and friend list
* `mensajes.php` - Real-time chat with CRUD operations on messages
* `notificaciones.php` - Centralized notification center with polling updates
* `perfil.php` - Friend profile viewer with online status
* `api/notificaciones.php` - Notification CRUD API
* `api/mensajes.php` - Messaging CRUD API

**Database Tables:**

* `amistades` - Friendship relationships with state tracking
* `mensajes` - Chat message storage
* `notificaciones` - Event notification queue
* `usuarios` - User profiles and online status

**Security:**

* Session-based authentication on all pages
* Friendship verification before granting access
* PDO prepared statements for all queries
* HTML escaping for all output

For implementation details of individual subsystems, refer to the detailed subsection pages: [Friends Management](/axchisan/El-rincon-de-ADSO/6.1-friends-management), [Messaging System](/axchisan/El-rincon-de-ADSO/6.2-messaging-system), [Notifications](/axchisan/El-rincon-de-ADSO/6.3-notifications), and [User Profiles](/axchisan/El-rincon-de-ADSO/6.4-user-profiles).