# Mock API and Development Data

> **Relevant source files**
> * [db.json](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json)
> * [src/app/services/music.service.ts](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts)

## Purpose and Scope

This document describes the mock API infrastructure used for development, including the `db.json` data file and how it simulates a RESTful backend. The mock API provides a local HTTP server that responds to HTTP requests with JSON data, enabling frontend development without requiring a production backend.

For information about the TypeScript model that structures this data, see [Data Models](/axchisan/MusicApp-Ionic/4.1-data-models). For details on how the application consumes this API, see [Music Service](/axchisan/MusicApp-Ionic/4.2-music-service).

---

## Mock API Architecture

The application uses a file-based mock API to simulate backend functionality during development. This approach allows frontend development to proceed independently of backend implementation.

### Mock API Request Flow

```

```

**Sources:** [src/app/services/music.service.ts L12-L25](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L25)

 [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

---

## Data File Structure

The mock API data is stored in `db.json` at the repository root. This file follows the json-server convention where top-level keys become RESTful endpoints.

### db.json Schema

```

```

**Sources:** [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

### Current Data Inventory

The `db.json` file contains a single collection called `groups` with 10 music group records:

| ID | Group Name | Description Keyword | Image Source |
| --- | --- | --- | --- |
| 1 | Queen | Banda británica de rock, 1970 | Wikipedia |
| 2 | The Beatles | Liverpool, íconos del rock | Wikipedia |
| 3 | Oasis | Banda británica de britpop | Wikipedia |
| 4 | Nirvana | Pioneros del grunge | Wikipedia |
| 5 | Radiohead | Rock alternativo | Wikipedia |
| 6 | Coldplay | Pop rock británico | Wikipedia |
| 7 | Metallica | Thrash metal | Wikipedia |
| 8 | Pink Floyd | Rock progresivo | Wikipedia |
| 9 | Arctic Monkeys | Indie rock | Wikipedia |
| 10 | Daft Punk | Electrónica francesa | Wikipedia |

**Sources:** [db.json L2-L63](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L2-L63)

---

## Group Object Schema

Each group object in the `groups` array follows a consistent structure:

### Properties

| Property | Type | Description | Example |
| --- | --- | --- | --- |
| `id` | `number` | Unique identifier for the group | `1` |
| `name` | `string` | Display name of the music group | `"Queen"` |
| `fragment` | `string` | Short description with key songs/facts | `"Banda británica de rock formada en 1970..."` |
| `image` | `string` | URL to album cover or group image | `"https://upload.wikimedia.org/..."` |

### Example Group Object

```

```

**Sources:** [db.json L3-L8](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L3-L8)

---

## JSON Server Integration

The mock API is served using `json-server`, a Node.js package that creates a RESTful API from a JSON file. While the package is not explicitly visible in the provided files, the service URL pattern indicates its use.

### API Endpoint Mapping

```

```

### Expected HTTP Interface

The `json-server` automatically exposes the following RESTful endpoints:

| Method | Endpoint | Description | Response |
| --- | --- | --- | --- |
| GET | `/groups` | Retrieve all groups | Array of group objects |
| GET | `/groups/:id` | Retrieve single group by ID | Single group object |
| POST | `/groups` | Create new group | Created group object |
| PUT | `/groups/:id` | Update existing group | Updated group object |
| PATCH | `/groups/:id` | Partial update of group | Updated group object |
| DELETE | `/groups/:id` | Delete group | 200 OK |

**Note:** Currently, only the `GET /groups` endpoint is consumed by the application at [src/app/services/music.service.ts L16-L25](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L16-L25)

**Sources:** [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)

---

## Service Integration

The `MusicService` is configured to communicate with the mock API server.

### Connection Configuration

```

```

The service defines the API URL as a private property:

* **Property:** `apiUrl` at [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)
* **Value:** `'http://localhost:3000/groups'`
* **Protocol:** HTTP (not HTTPS) for local development
* **Host:** `localhost` (127.0.0.1)
* **Port:** `3000` (default json-server port)
* **Endpoint:** `/groups` (maps to `groups` key in `db.json`)

**Sources:** [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)

---

## Data Transformation Pipeline

The raw JSON data from `db.json` undergoes transformation before reaching the application components.

### Transformation Flow

```

```

### Transformation Logic

The `MusicService.getGroups()` method applies the following transformation at [src/app/services/music.service.ts L16-L25](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L16-L25)

:

1. **HTTP Request:** `this.http.get<any[]>(this.apiUrl)` - Makes GET request, expecting array
2. **RxJS Pipe:** `.pipe()` - Chains operators on the Observable stream
3. **Map Operator:** `map(groups => ...)` - Transforms each array element
4. **Property Mapping:** Explicitly maps each property to match `Group` interface: * `id: g.id` * `name: g.name` * `fragment: g.fragment` * `image: g.image`
5. **Type Assertion:** `as Group` - Ensures TypeScript type safety

This explicit mapping ensures that any future changes to the API response structure are caught at compile time and can be handled in a single location.

**Sources:** [src/app/services/music.service.ts L16-L25](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L16-L25)

---

## Development Workflow

### Starting the Mock API Server

To run the application with the mock API, two separate processes must be running:

1. **JSON Server** (serves `db.json` on port 3000)
2. **Ionic Dev Server** (serves the Angular application)

The typical development workflow:

```

```

### Expected Commands

While not explicitly defined in the provided files, the standard setup would include:

**Start mock API server:**

```

```

**Start Ionic dev server:**

```

```

Or a combined script in `package.json`:

```

```

**Sources:** [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

 [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)

---

## Data Validation and Constraints

### Current Data Characteristics

The existing data in `db.json` follows these patterns:

| Aspect | Observation | Implication |
| --- | --- | --- |
| **ID Range** | Sequential 1-10 | New entries should continue sequence |
| **Name Length** | 4-15 characters | UI assumes reasonable name length |
| **Fragment Language** | Spanish text | Application is Spanish-language focused |
| **Fragment Length** | 30-80 characters | Fits in card layout without truncation |
| **Image Sources** | All from Wikipedia | External dependency on wikimedia.org |
| **Image Format** | PNG and JPG | Both formats supported |

### Data Integrity Considerations

**ID Uniqueness:** The `id` field serves as the primary key. Each group must have a unique numeric identifier. The current implementation doesn't handle duplicate IDs or missing IDs gracefully.

**Required Fields:** All four properties (`id`, `name`, `fragment`, `image`) are expected by the TypeScript `Group` model. Missing properties would cause runtime errors during the mapping operation at [src/app/services/music.service.ts L18-L23](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L18-L23)

**Image URLs:** The application assumes image URLs are valid and accessible. Broken links result in broken image placeholders in the UI. All current images use HTTPS Wikipedia URLs.

**Sources:** [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

 [src/app/services/music.service.ts L17-L24](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L17-L24)

---

## Extending the Mock Data

### Adding New Groups

To add a new music group to the mock API:

1. Open `db.json` in an editor
2. Add a new object to the `groups` array
3. Ensure the new object has all required properties: * `id` (unique number, continue sequence from 11) * `name` (string, group name) * `fragment` (string, Spanish description) * `image` (string, valid image URL)
4. Save the file
5. The json-server will automatically reload (if using `--watch` flag)
6. New data appears immediately in the running application

### Example Addition

```

```

**Sources:** [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

---

## Mock API vs Production API

The mock API structure defined in `db.json` should mirror the expected production API response format. This ensures smooth transition from development to production.

### Environment Switching

The application should use environment configuration for API URLs:

```

```

**Current Implementation Note:** The `apiUrl` is currently hardcoded at [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)

 For production readiness, this should be moved to environment configuration files. See [Environment Configuration](/axchisan/MusicApp-Ionic/5.3-environment-configuration) for details on managing environment-specific settings.

**Sources:** [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)

---

## Summary

The mock API infrastructure provides:

* **File-based Data Storage:** `db.json` contains all group data at the repository root
* **RESTful Interface:** json-server exposes standard REST endpoints on `localhost:3000`
* **Separation of Concerns:** Mock API allows frontend development without backend dependency
* **Easy Data Modification:** JSON format enables quick data updates for testing
* **Type Safety:** Service transformation ensures data conforms to TypeScript models
* **Development Workflow:** Two-process setup (API server + dev server) for local development

The current implementation serves 10 music groups with consistent Spanish-language descriptions and Wikipedia images, accessible through a single GET endpoint consumed by the `MusicService`.

**Sources:** [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

 [src/app/services/music.service.ts L1-L26](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L1-L26)