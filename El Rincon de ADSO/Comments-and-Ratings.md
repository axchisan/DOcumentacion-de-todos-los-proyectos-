# Comments and Ratings

> **Relevant source files**
> * [src/frontend/inicio/index.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php)
> * [src/frontend/panel/panel-usuario.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php)
> * [src/frontend/repositorio/repositorio.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/repositorio.php)
> * [src/frontend/repositorio/ver_documento.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_documento.php)
> * [src/frontend/repositorio/ver_libro.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_libro.php)
> * [src/frontend/repositorio/ver_video.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php)
> * [src/uploads/6813fb2b5aff5.pdf](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/uploads/6813fb2b5aff5.pdf)

## Purpose and Scope

This document describes the comments and ratings system in El Rincón de ADSO, which allows authenticated users to share opinions and engage with content through text comments, star ratings, likes, and nested replies. The system operates in two contexts: general community discussions on the landing page and resource-specific comments on individual educational materials.

For information about the social networking features (friends, messaging), see [6](/axchisan/El-rincon-de-ADSO/6-social-features). For information about forum and event discussions, see [7.2](/axchisan/El-rincon-de-ADSO/7.2-forums-and-events).

## System Overview

The comments and ratings system provides community engagement through two distinct comment types:

| Comment Type | Location | Features | Rating System |
| --- | --- | --- | --- |
| Community Comments | Landing page (`index.php`) | Star ratings, likes, replies, edit/delete | 1-5 star ratings required |
| Resource Comments | Resource viewer pages | Likes, replies, edit/delete | No ratings |

The system is accessible only to authenticated users and stores all interactions in the `comentarios` table with associated metadata including author information, timestamps, and optional resource associations.

**Sources:** [src/frontend/inicio/index.php L266-L313](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L266-L313)

 [src/frontend/repositorio/ver_video.php L337-L391](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L337-L391)

 [src/frontend/repositorio/ver_libro.php L314-L368](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_libro.php#L314-L368)

 [src/frontend/repositorio/ver_documento.php L333-L387](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_documento.php#L333-L387)

## Comment Types

### Community Comments

Community comments appear on the landing page and allow users to share general opinions about topics, books, or concepts they're learning. These comments include mandatory star ratings and are displayed in a paginated grid format.

```mermaid
flowchart TD

User["Authenticated User"]
Form["Comment Form<br>index.php:268-292"]
StarRating["Star Rating Input<br>index.php:280-289<br>Required 1-5 stars"]
Subject["Subject Field<br>name='libro'"]
Content["Comment Textarea<br>name='comentario'"]
AddAPI["add_comment.php<br>Backend API"]
Database["comentarios table<br>Community comments<br>documento_id = NULL"]
Grid["Community Grid<br>index.php:296-298<br>Paginated display"]
LikeBtn["Like Button<br>toggle_like.php"]
ReplyBtn["Reply Button<br>add_reply.php"]
EditBtn["Edit Button<br>Author only"]
DeleteBtn["Delete Button<br>Author only"]

User --> Form
Form --> StarRating
Form --> Subject
Form --> Content
Form --> AddAPI
AddAPI --> Database
Database --> Grid
Grid --> LikeBtn
Grid --> ReplyBtn
Grid --> EditBtn
Grid --> DeleteBtn
```

**Key characteristics:**

* **Subject field**: Users specify what they're commenting about (e.g., "Python para principiantes")
* **Star ratings**: Mandatory selection of 1-5 stars visualized with Font Awesome icons
* **Database identifier**: `documento_id` is NULL for community comments
* **Display location**: `#comunidad` section on `index.php`

**Sources:** [src/frontend/inicio/index.php L268-L292](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L268-L292)

 [src/frontend/inicio/index.php L280-L289](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L280-L289)

 [src/frontend/inicio/index.php L426-L465](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L426-L465)

### Resource Comments

Resource comments are attached to specific educational resources (books, videos, documents) and appear on the resource viewer pages. These comments do not include star ratings and focus on feedback about the specific resource being viewed.

```mermaid
flowchart TD

Resource["Resource Viewer Page<br>ver_libro.php<br>ver_video.php<br>ver_documento.php"]
User["Authenticated User"]
CommentForm["Comment Form<br>Line 320-332 (libro)<br>Line 342-355 (video)<br>Line 339-351 (documento)"]
Textarea["Textarea<br>name='contenido'<br>placeholder='Escribe un comentario...'"]
SubmitBtn["Submit Button<br>id='btn-enviar-comentario'"]
FormHandler["Form Submit Handler<br>data-documento-id attribute"]
Query["Database Query<br>Lines 80-90 (video)<br>SELECT from comentarios<br>WHERE documento_id = :documento_id"]
CommentsList["Comments List<br>id='comments-container'<br>Lines 362-389"]
DeleteAction["Delete Action<br>btn-delete-comment<br>Author only<br>Lines 376-383"]

User --> CommentForm
Resource --> CommentForm
CommentForm --> Textarea
CommentForm --> SubmitBtn
SubmitBtn --> FormHandler
FormHandler --> Query
Query --> CommentsList
Resource --> Query
CommentsList --> DeleteAction
```

**Key characteristics:**

* **Resource association**: Each comment is linked to a specific `documento_id`
* **No ratings**: Star ratings are not used for resource comments
* **Direct display**: Comments load immediately with the resource page
* **Simple form**: Single textarea input with submit button

**Sources:** [src/frontend/repositorio/ver_video.php L80-L90](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L80-L90)

 [src/frontend/repositorio/ver_video.php L337-L391](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L337-L391)

 [src/frontend/repositorio/ver_libro.php L80-L90](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_libro.php#L80-L90)

 [src/frontend/repositorio/ver_documento.php L85-L95](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_documento.php#L85-L95)

## Features

### Star Ratings

Star ratings are exclusive to community comments and provide a 1-5 scale visual feedback mechanism using Font Awesome icons.

**Implementation details:**

| Component | Location | Description |
| --- | --- | --- |
| HTML Structure | [index.php L280-L289](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L280-L289) | Five star spans with `data-value` attributes |
| Hidden Input | [index.php L282](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L282-L282) | `<input type="hidden" name="valoracion" id="valoracion">` |
| Interactive Behavior | [index.php L377-L423](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L377-L423) | Click, mouseover, and mouseout event listeners |
| Validation | [index.php L432-L436](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L432-L436) | Ensures rating is between 1-5 before submission |

**Star rating interaction flow:**

```mermaid
sequenceDiagram
  participant User
  participant Star Element
  participant .star
  participant valoracion input
  participant add-comment-form
  participant add_comment.php

  User->>Star Element: Click star (data-value="3")
  Star Element->>valoracion input: Set value = 3
  Star Element->>Star Element: Update visual state
  User->>add-comment-form: (fill stars 1-3)
  add-comment-form->>add-comment-form: Submit form
  loop [Valid rating]
    add-comment-form->>add_comment.php: Validate valoracion 1-5
    add_comment.php->>add-comment-form: POST with valoracion=3
    add-comment-form->>User: Success response
    add-comment-form->>User: Display success, reset form
  end
```

The star rating system uses CSS classes `far` (empty star) and `fas` (filled star) from Font Awesome to toggle visual states. Selected stars also receive a `.selected` class for state tracking.

**Sources:** [src/frontend/inicio/index.php L280-L289](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L280-L289)

 [src/frontend/inicio/index.php L377-L423](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L377-L423)

 [src/frontend/inicio/index.php L432-L436](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L432-L436)

### Likes

The like system allows users to show appreciation for comments. Each comment displays a like count and button that toggles between liked and unliked states.

**Frontend implementation:**

```javascript
// Like button structure in comment cards
// Located in: index.php, lines 468-506
fetch('../../backend/comunidad/toggle_like.php', {
    method: 'POST',
    body: `comentario_id=${comentarioId}`
})
.then(response => response.json())
.then(data => {
    if (data.success) {
        // Update icon class: 'far' <-> 'fas'
        // Update button class: add/remove 'comment-card__like--active'
        // Update like count display
    }
})
```

**Like toggle flow:**

```mermaid
flowchart TD

CommentCard["Comment Card<br>class='comment-card'"]
LikeBtn["Like Button<br>class='comment-card__like'<br>data-comment-id"]
Icon["Heart Icon<br>fas/far fa-heart"]
Count["Like Count<br>Display text"]
ClickHandler["Click Handler<br>index.php:468-506"]
API["toggle_like.php"]
Response["JSON Response<br>{success, action, likes}"]

CommentCard --> LikeBtn
LikeBtn --> Icon
LikeBtn --> Count
LikeBtn --> ClickHandler
ClickHandler --> API
API --> Response
Response --> Icon
Response --> Icon
Response --> Count
Response --> LikeBtn
```

**Visual states:**

* **Unliked**: `<i class="far fa-heart"></i>` (outline heart)
* **Liked**: `<i class="fas fa-heart liked"></i>` (filled heart)
* **Active button**: `.comment-card__like--active` class added

**Sources:** [src/frontend/inicio/index.php L468-L506](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L468-L506)

### Nested Replies

Comments support a one-level reply system where users can respond directly to existing comments. Replies are displayed beneath their parent comment.

**Reply structure:**

| Component | Purpose | Implementation |
| --- | --- | --- |
| Reply Button | Toggles reply form visibility | [index.php L509-L519](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L509-L519) |
| Reply Form | Hidden form for each comment | `id="reply-form-{comment_id}"` |
| Submit Handler | Processes reply submission | [index.php L522-L554](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L522-L554) |
| Backend API | `add_reply.php` | Receives `comentario_id` and `respuesta` |

**Reply interaction diagram:**

```mermaid
sequenceDiagram
  participant User
  participant Reply Button
  participant comment-card__reply
  participant Reply Form
  participant id=reply-form-{id}
  participant Submit Handler
  participant add-reply-form
  participant add_reply.php
  participant Page Reload

  User->>Reply Button: Click "Responder"
  Reply Button->>Reply Form: Toggle display
  User->>Reply Form: (none <-> block)
  User->>Submit Handler: Enter reply text
  Submit Handler->>add_reply.php: Submit form
  add_reply.php->>Submit Handler: POST comentario_id & respuesta
  Submit Handler->>User: {success: true, message}
  Submit Handler->>Submit Handler: Alert success message
  Submit Handler->>Reply Form: Reset form
  Submit Handler->>Page Reload: Hide form
```

**Sources:** [src/frontend/inicio/index.php L509-L554](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L509-L554)

### Edit and Delete

Users can edit or delete their own comments. These actions are restricted by author verification on both frontend display and backend processing.

**Access control:**

```python
// Displayed only if current user is comment author
// Example from ver_video.php:378-384
<?php if ($usuario_id == $comentario['autor_id']): ?>
<div class="comment__actions">
    <button class="btn-delete-comment" data-id="<?php echo $comentario['id']; ?>">
        <i class="fas fa-trash-alt"></i> Eliminar
    </button>
</div>
<?php endif; ?>
```

**Delete operation flow:**

```mermaid
flowchart TD

CommentDisplay["Comment Display<br>Author verification<br>usuario_id == autor_id"]
DeleteBtn["Delete Button<br>btn-delete-comment<br>data-id attribute"]
ConfirmDialog["Confirm Dialog<br>'¿Estás seguro?'"]
DeleteHandler["Delete Handler<br>index.php:559-586"]
API["delete_comment.php"]
Response["Response Handler"]
Reload["Reload Comments<br>currentPage = 1"]

CommentDisplay --> DeleteBtn
DeleteBtn --> ConfirmDialog
ConfirmDialog --> DeleteHandler
DeleteHandler --> API
API --> Response
Response --> Reload
```

**Edit operation components:**

For community comments (index.php), the edit functionality creates an inline form:

```mermaid
flowchart TD

EditBtn["Edit Button<br>comment-card__edit<br>data-comment-id"]
FormGen["Form Generator<br>index.php:589-723"]
InlineForm["Inline Edit Form<br>edit-comment-form<br>Pre-filled with:<br>- libro<br>- comentario<br>- valoracion"]
StarRating["Star Rating Widget<br>id=edit-star-rating-{id}"]
SubmitHandler["Submit Handler<br>Dynamic form submission"]
API["edit_comment.php"]

EditBtn --> FormGen
FormGen --> InlineForm
InlineForm --> StarRating
InlineForm --> SubmitHandler
SubmitHandler --> API
```

The edit form dynamically generates with pre-populated fields and includes the star rating widget for community comments.

**Sources:** [src/frontend/inicio/index.php L559-L586](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L559-L586)

 (delete), [src/frontend/inicio/index.php L589-L723](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L589-L723)

 (edit), [src/frontend/repositorio/ver_video.php L378-L384](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L378-L384)

## Frontend Implementation

### Community Comments Section

The community comments section on `index.php` provides a comprehensive interface for viewing and interacting with general community discussions.

**Section structure:**

```mermaid
flowchart TD

Section["#comunidad Section<br>index.php:254-314"]
Header["Section Header<br>Title & Description<br>Lines 257-263"]
AddForm["Add Comment Form<br>Lines 268-292<br>Only if usuario_id set"]
Grid["Community Grid<br>id='community-grid'<br>Lines 296-298"]
Pagination["Pagination Controls<br>id='community-pagination'<br>Lines 301-303"]
CTA["Create Account CTA<br>Lines 306-312<br>Only if NOT logged in"]
SubjectInput["Subject Input<br>id='libro'<br>name='libro'"]
CommentTextarea["Comment Textarea<br>id='comentario'<br>name='comentario'"]
StarWidget["Star Rating Widget<br>id='star-rating'<br>Lines 281-288"]
SubmitBtn["Submit Button<br>btn btn--primary"]

Section --> Header
Section --> AddForm
Section --> Grid
Section --> Pagination
Section --> CTA
AddForm --> SubjectInput
AddForm --> CommentTextarea
AddForm --> StarWidget
AddForm --> SubmitBtn
```

**JavaScript initialization:**

The page includes extensive JavaScript for comment functionality:

| Function | Lines | Purpose |
| --- | --- | --- |
| `attachLikeButtonListeners()` | 468-506 | Binds click handlers to like buttons |
| `attachReplyButtonListeners()` | 509-519 | Toggles reply form visibility |
| `attachReplyFormListeners()` | 522-554 | Handles reply form submission |
| `attachEditDeleteListeners()` | 557-723 | Manages edit and delete operations |
| `loadCommunityComments()` | Referenced | Fetches and renders comment data |

**Sources:** [src/frontend/inicio/index.php L254-L314](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L254-L314)

 [src/frontend/inicio/index.php L468-L723](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L468-L723)

### Resource Comments Section

Resource viewer pages include a simpler comments section focused on feedback about the specific resource being viewed.

**Component hierarchy:**

```mermaid
flowchart TD

Container["resource-comments<br>Wrapper div"]
Section["resource-section<br>Section wrapper"]
Title["Section Title<br>h2: 'Comentarios'"]
FormCheck["Is user<br>authenticated?"]
CommentForm["comment-form<br>form#form-comentario<br>Lines 320-332 (libro)"]
LoginPrompt["comment-login-prompt<br>'Debes iniciar sesión'<br>Lines 334-336"]
CommentsList["comments-list<br>id='comments-container'<br>Lines 339-367"]
Textarea["textarea[name='contenido']"]
SubmitButton["btn btn--primary<br>id='btn-enviar-comentario'"]
StatusDiv["comentario-status<br>Loading indicator"]
CommentLoop["PHP foreach loop<br>$comentarios as $comentario"]
CommentDiv["comment div<br>data-id attribute"]
CommentHeader["Author & date"]
CommentText["Comment content"]
CommentActions["Delete button<br>if author matches"]

Container --> Section
Section --> Title
Section --> FormCheck
FormCheck --> CommentForm
FormCheck --> LoginPrompt
Section --> CommentsList
CommentForm --> Textarea
CommentForm --> SubmitButton
CommentForm --> StatusDiv
CommentsList --> CommentLoop
CommentLoop --> CommentDiv
CommentDiv --> CommentHeader
CommentDiv --> CommentText
CommentDiv --> CommentActions
```

**Key differences from community comments:**

* No star rating system
* Simpler form with single textarea
* Comments loaded directly from database query (no AJAX initially)
* Only delete action available (no edit, like, or reply shown in these files)
* `data-documento-id` attribute links form to resource

**Database query for resource comments:**

```python
-- Example from ver_video.php:80-90
SELECT c.*, u.nombre_usuario
FROM comentarios c
JOIN usuarios u ON c.autor_id = u.id
WHERE c.documento_id = :documento_id
ORDER BY c.fecha_creacion DESC
```

**Sources:** [src/frontend/repositorio/ver_video.php L337-L391](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L337-L391)

 [src/frontend/repositorio/ver_libro.php L314-L368](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_libro.php#L314-L368)

 [src/frontend/repositorio/ver_documento.php L333-L387](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_documento.php#L333-L387)

## Backend APIs

The comments system relies on several backend API endpoints located in the `src/backend/comunidad/` directory.

**API Endpoints:**

| Endpoint | Method | Parameters | Purpose | References |
| --- | --- | --- | --- | --- |
| `add_comment.php` | POST | `libro`, `comentario`, `valoracion` | Create new community comment | [index.php L438-L464](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L438-L464) |
| `toggle_like.php` | POST | `comentario_id` | Toggle like on comment | [index.php L475-L503](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L475-L503) |
| `add_reply.php` | POST | `comentario_id`, `respuesta` | Add reply to comment | [index.php L529-L551](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L529-L551) |
| `delete_comment.php` | POST | `comentario_id` | Delete comment (author only) | [index.php L563-L583](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L563-L583) |
| `edit_comment.php` | POST | `comentario_id`, `libro`, `comentario`, `valoracion` | Edit comment (author only) | [index.php L658-L705](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L658-L705) |

**API Response format:**

All APIs return JSON responses with consistent structure:

```
{
  "success": true/false,
  "message": "Descriptive message",
  "data": {
    // Additional data (e.g., likes count, action type)
  }
}
```

**API interaction pattern:**

```mermaid
sequenceDiagram
  participant Frontend
  participant Backend API
  participant comunidad/*.php
  participant Session Check
  participant usuario_id
  participant Database
  participant comentarios table
  participant Response

  Frontend->>Backend API: POST request with data
  Backend API->>Session Check: Verify usuario_id in session
  loop [Not authenticated]
    Session Check->>Response: {success: false, message}
    Response->>Frontend: Redirect or error
    Session Check->>Database: Execute query
    Database->>Backend API: Return result
    Backend API->>Response: {success: true, message, data}
    Response->>Frontend: Update UI
  end
```

**Error handling:**

Frontend code consistently uses try-catch blocks and checks for `data.success`:

```javascript
// Pattern from index.php:438-464
fetch(endpoint, {
    method: 'POST',
    body: formData
})
.then(response => response.json())
.then(data => {
    if (data.success) {
        alert(data.message);
        // Update UI, reset form, reload comments
    } else {
        alert(data.message);
    }
})
.catch(error => {
    console.error('Error:', error);
    alert('Error message...');
});
```

**Sources:** [src/frontend/inicio/index.php L438-L464](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L438-L464)

 [src/frontend/inicio/index.php L475-L503](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L475-L503)

 [src/frontend/inicio/index.php L529-L551](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L529-L551)

 [src/frontend/inicio/index.php L563-L583](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L563-L583)

## User Permissions and Access Control

The comments system implements multiple layers of access control to ensure users can only perform authorized actions.

**Permission matrix:**

| Action | Anonymous User | Authenticated User | Comment Author |
| --- | --- | --- | --- |
| View comments | ✓ | ✓ | ✓ |
| Add comment | ✗ | ✓ | ✓ |
| Like comment | ✗ | ✓ | ✓ |
| Reply to comment | ✗ | ✓ | ✓ |
| Edit comment | ✗ | ✗ | ✓ (own only) |
| Delete comment | ✗ | ✗ | ✓ (own only) |

**Authentication checks:**

```mermaid
flowchart TD

PageLoad["Page Load"]
SessionCheck["Session<br>usuario_id<br>exists?"]
AnonView["Anonymous View<br>- Read comments only<br>- Show login prompt"]
AuthView["Authenticated View<br>- Show comment form<br>- Show like buttons<br>- Show reply buttons"]
AuthorCheck["Current user<br>== Comment<br>author?"]
BasicActions["Basic Actions<br>- Add comment<br>- Like<br>- Reply"]
OwnerActions["Owner Actions<br>- Edit<br>- Delete"]

PageLoad --> SessionCheck
SessionCheck --> AnonView
SessionCheck --> AuthView
AuthView --> AuthorCheck
AuthorCheck --> BasicActions
AuthorCheck --> OwnerActions
```

**Frontend conditional rendering:**

```php
// Authentication check - index.php:267
<?php if ($usuario_id): ?>
    <!-- Comment form displayed -->
<?php endif; ?>

// Author verification - ver_video.php:378
<?php if ($usuario_id == $comentario['autor_id']): ?>
    <!-- Edit/delete buttons displayed -->
<?php endif; ?>
```

**Backend verification (typical pattern):**

```javascript
// Session validation
session_start();
if (!isset($_SESSION['usuario_id'])) {
    echo json_encode(['success' => false, 'message' => 'Debes iniciar sesión']);
    exit();
}

// Author verification for edit/delete
$query = "SELECT autor_id FROM comentarios WHERE id = :id";
$stmt = $db->prepare($query);
$stmt->execute([':id' => $comentario_id]);
$comment = $stmt->fetch(PDO::FETCH_ASSOC);

if ($comment['autor_id'] != $_SESSION['usuario_id']) {
    echo json_encode(['success' => false, 'message' => 'No autorizado']);
    exit();
}
```

**Sources:** [src/frontend/inicio/index.php L267](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L267-L267)

 [src/frontend/repositorio/ver_video.php L378](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L378-L378)

 [src/frontend/repositorio/ver_video.php L342](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L342-L342)

## Data Flow Diagrams

### Complete Comment Lifecycle

This diagram shows the full lifecycle of a comment from creation to deletion:

```mermaid
flowchart TD

User["User<br>Authenticated"]
Form["Comment Form<br>Subject + Text + Rating"]
Validate["Frontend Validation<br>- Check rating 1-5<br>- Check required fields"]
AddAPI["add_comment.php<br>POST endpoint"]
InsertDB["INSERT INTO comentarios<br>autor_id, contenido,<br>libro, valoracion,<br>fecha_creacion"]
LoadQuery["SELECT comentarios<br>JOIN usuarios<br>ORDER BY fecha_creacion"]
Render["Render Comment Cards<br>- Author name<br>- Date<br>- Content<br>- Star rating<br>- Like count"]
AttachHandlers["Attach Event Handlers<br>- Like buttons<br>- Reply buttons<br>- Edit buttons<br>- Delete buttons"]
LikeAction["Toggle Like<br>toggle_like.php"]
ReplyAction["Add Reply<br>add_reply.php"]
EditAction["Edit Comment<br>edit_comment.php"]
DeleteAction["Delete Comment<br>delete_comment.php"]
LikeUpdate["UPDATE likes count"]
ReplyInsert["INSERT reply record"]
EditUpdate["UPDATE comentarios<br>SET contenido, libro, valoracion"]
DeleteRemove["DELETE FROM comentarios<br>WHERE id AND autor_id"]

User --> Form
InsertDB --> LoadQuery
AttachHandlers --> LikeAction
AttachHandlers --> ReplyAction
AttachHandlers --> EditAction
AttachHandlers --> DeleteAction
LikeUpdate --> LoadQuery
ReplyInsert --> LoadQuery
EditUpdate --> LoadQuery
DeleteRemove --> LoadQuery

subgraph subGraph2 ["Interaction Flow"]
    LikeAction
    ReplyAction
    EditAction
    DeleteAction
    LikeUpdate
    ReplyInsert
    EditUpdate
    DeleteRemove
    LikeAction --> LikeUpdate
    ReplyAction --> ReplyInsert
    EditAction --> EditUpdate
    DeleteAction --> DeleteRemove
end

subgraph subGraph1 ["Display Flow"]
    LoadQuery
    Render
    AttachHandlers
    LoadQuery --> Render
    Render --> AttachHandlers
end

subgraph subGraph0 ["Creation Flow"]
    Form
    Validate
    AddAPI
    InsertDB
    Form --> Validate
    Validate --> AddAPI
    AddAPI --> InsertDB
end
```

**Sources:** [src/frontend/inicio/index.php L268-L723](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php#L268-L723)

 [src/frontend/repositorio/ver_video.php L80-L391](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L80-L391)

### Resource Comment Integration

This diagram shows how comments integrate with the resource viewing system:

```mermaid
flowchart TD

ResourceURL["Resource URL<br>ver_libro.php?id=123<br>ver_video.php?id=456<br>ver_documento.php?id=789"]
SessionCheck["Session Check<br>$usuario_id = $_SESSION['usuario_id']"]
ResourceQuery["Resource Query<br>SELECT documentos d<br>JOIN usuarios u<br>WHERE d.id = :documento_id"]
CommentsQuery["Comments Query<br>SELECT comentarios c<br>JOIN usuarios u<br>WHERE c.documento_id = :documento_id<br>ORDER BY fecha_creacion DESC"]
RenderPage["Render Resource Page"]
CommentsTitle["Section Title: 'Comentarios'"]
AuthCheck["User<br>authenticated?"]
ShowForm["Display Comment Form<br>form#form-comentario<br>data-documento-id"]
ShowPrompt["Display Login Prompt<br>'Debes iniciar sesión'"]
DisplayComments["Display Comments List<br>foreach comentarios<br>- Author<br>- Date<br>- Content<br>- Delete button if author"]
JSHandler["JavaScript Handler<br>form-comentario submit"]
CommentAPI["Comment API<br>(Inferred endpoint)"]
DBInsert["INSERT comentarios<br>documento_id = 123<br>autor_id = current_user<br>contenido = text"]
Reload["Page Reload or<br>Dynamic Comment Add"]

ResourceURL --> SessionCheck
SessionCheck --> ResourceQuery
SessionCheck --> CommentsQuery
ResourceQuery --> RenderPage
CommentsQuery --> RenderPage
RenderPage --> CommentsTitle
CommentsQuery --> DisplayComments
ShowForm --> JSHandler
JSHandler --> CommentAPI
CommentAPI --> DBInsert
DBInsert --> Reload
Reload --> CommentsQuery

subgraph subGraph0 ["Comments Section"]
    CommentsTitle
    AuthCheck
    ShowForm
    ShowPrompt
    DisplayComments
    CommentsTitle --> AuthCheck
    AuthCheck --> ShowForm
    AuthCheck --> ShowPrompt
end
```

**Sources:** [src/frontend/repositorio/ver_libro.php L1-L368](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_libro.php#L1-L368)

 [src/frontend/repositorio/ver_video.php L1-L391](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_video.php#L1-L391)

 [src/frontend/repositorio/ver_documento.php L1-L387](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/ver_documento.php#L1-L387)