# Data Layer

> **Relevant source files**
> * [db.json](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json)
> * [src/app/models/group.model.ts](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/models/group.model.ts)
> * [src/app/services/music.service.ts](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts)

## Purpose and Scope

The Data Layer encompasses all data structures, data access services, and data sources used by the MusicApp application. This includes TypeScript interfaces that define data contracts, services that handle HTTP communication, and the mock API that simulates a backend during development.

This document covers the architectural patterns, data flow, and implementation details of how the application manages and retrieves music group information. For information about how data is consumed by UI components, see [Features and Pages](/axchisan/MusicApp-Ionic/3-features-and-pages). For build-time configuration of the mock API server, see [Environment Configuration](/axchisan/MusicApp-Ionic/5.3-environment-configuration).

---

## Data Layer Architecture

The data layer follows a three-tier architecture with clear separation of concerns:

### Architecture Overview Diagram

```

```

**Sources:** [src/app/services/music.service.ts L1-L26](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L1-L26)

 [src/app/models/group.model.ts L1-L6](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/models/group.model.ts#L1-L6)

 [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

---

## Data Models

The application uses TypeScript interfaces to define strict data contracts. This ensures type safety throughout the application and provides IntelliSense support in the IDE.

### Group Interface

The `Group` interface is the primary data model in the application, representing a music group entity.

**Interface Definition:**

| Property | Type | Description |
| --- | --- | --- |
| `id` | `number` | Unique identifier for the group |
| `name` | `string` | Name of the music group |
| `fragment` | `string` | Brief description or tagline |
| `image` | `string` | URL to the group's album cover or avatar image |

**Implementation Location:** [src/app/models/group.model.ts L1-L6](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/models/group.model.ts#L1-L6)

The interface is exported and can be imported anywhere in the application:

```

```

This interface is used for:

* Type annotations in service return values
* Type annotations in component properties
* Type checking during compilation
* IntelliSense and autocomplete in editors

**Sources:** [src/app/models/group.model.ts L1-L6](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/models/group.model.ts#L1-L6)

---

## Data Services

### MusicService

The `MusicService` is an Angular injectable service that provides a centralized abstraction for data access operations. It encapsulates all HTTP communication with the backend API and transforms raw responses into typed domain models.

#### Service Configuration

```

```

The service is registered with `providedIn: 'root'` at [src/app/services/music.service.ts L8](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L8-L8)

 making it available as a singleton throughout the application without needing to add it to any module's `providers` array.

**Sources:** [src/app/services/music.service.ts L7-L9](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L7-L9)

#### Service Properties

| Property | Type | Access | Description |
| --- | --- | --- | --- |
| `apiUrl` | `string` | `private` | Base URL for the groups API endpoint |
| `http` | `HttpClient` | `private` | Angular HTTP client for making requests |

The `apiUrl` is hardcoded to `'http://localhost:3000/groups'` at [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)

 This points to the mock JSON server running during development.

**Sources:** [src/app/services/music.service.ts L12-L14](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L14)

#### Service Methods

##### getGroups()

```

```

**Method Signature:** `getGroups(): Observable<Group[]>`

**Implementation Details:**

1. **HTTP Request** [src/app/services/music.service.ts L17](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L17-L17) : Makes a GET request to `apiUrl` using Angular's `HttpClient`
2. **Type Transformation** [src/app/services/music.service.ts L18-L23](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L18-L23) : Uses RxJS `map` operator to transform the raw JSON array into typed `Group[]` objects
3. **Return Type**: Returns an `Observable<Group[]>` that components can subscribe to

The transformation ensures that each object in the response matches the `Group` interface structure:

```

```

This explicit mapping provides an additional layer of validation and makes the data contract explicit, even though the API response already matches the interface structure.

**Sources:** [src/app/services/music.service.ts L16-L25](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L16-L25)

---

## Mock API and Data Source

### JSON Server Configuration

The application uses a mock REST API during development, powered by `json-server`. The data is stored in `db.json` at the project root.

#### Mock API Endpoint Structure

| Endpoint | Method | Description | Response Type |
| --- | --- | --- | --- |
| `/groups` | GET | Retrieves all music groups | `Group[]` |
| `/groups/:id` | GET | Retrieves a specific group (not currently used) | `Group` |

The server runs on `http://localhost:3000` and automatically generates RESTful endpoints based on the JSON structure.

**Sources:** [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

 [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)

### Data Structure

The `db.json` file contains a single top-level `groups` array with 10 music group entries:

```

```

#### Sample Data Entry

Each group object in the array contains four properties that match the `Group` interface:

**Example - Queen (id: 1):**

| Property | Value |
| --- | --- |
| `id` | `1` |
| `name` | `"Queen"` |
| `fragment` | `"Banda británica de rock formada en 1970 en Londres. Conocida por 'Bohemian Rhapsody'."` |
| `image` | `"https://upload.wikimedia.org/wikipedia/en/4/4b/Queen_Greatest_Hits.png"` |

**Sources:** [db.json L2-L8](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L2-L8)

#### Complete Dataset Overview

| ID | Group Name | Genre/Description | Image Source |
| --- | --- | --- | --- |
| 1 | Queen | British rock, 1970, "Bohemian Rhapsody" | Wikipedia |
| 2 | The Beatles | Liverpool icons, "Hey Jude", "Let It Be" | Wikipedia |
| 3 | Oasis | British britpop, "Wonderwall" | Wikipedia |
| 4 | Nirvana | Grunge pioneers, "Smells Like Teen Spirit" | Wikipedia |
| 5 | Radiohead | Alternative rock, "Creep", "Paranoid Android" | Wikipedia |
| 6 | Coldplay | British pop rock, "Yellow", "Clocks" | Wikipedia |
| 7 | Metallica | Thrash metal, "Master of Puppets" | Wikipedia |
| 8 | Pink Floyd | Progressive rock, "The Dark Side of the Moon" | Wikipedia |
| 9 | Arctic Monkeys | Indie rock, "Do I Wanna Know?" | Wikipedia |
| 10 | Daft Punk | French electronic, "Get Lucky" | Wikipedia |

**Sources:** [db.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/db.json#L1-L64)

---

## Data Flow Patterns

### Request-Response Cycle

The following sequence diagram illustrates the complete data flow from component initialization through API request to data rendering:

```

```

**Sources:** [src/app/services/music.service.ts L16-L25](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L16-L25)

### Observable Stream Transformation

The data transformation pipeline uses RxJS operators to ensure type safety:

```

```

The transformation happens at [src/app/services/music.service.ts L18-L23](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L18-L23)

 and ensures that:

1. The raw JSON response is mapped to strongly-typed `Group` objects
2. Each property is explicitly extracted from the source object
3. The result is cast to the `Group` interface type
4. Consumers receive type-safe data with IntelliSense support

**Sources:** [src/app/services/music.service.ts L17-L24](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L17-L24)

---

## Type Safety and Contracts

The data layer enforces type safety through a contract chain:

```

```

This ensures that:

* The service cannot return data that doesn't match `Group[]`
* Components cannot assign incompatible data to group properties
* Templates can access only properties defined in the `Group` interface
* Type errors are caught at compile time rather than runtime

**Sources:** [src/app/models/group.model.ts L1-L6](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/models/group.model.ts#L1-L6)

 [src/app/services/music.service.ts L16](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L16-L16)

---

## Dependency Injection and Service Registration

The `MusicService` uses Angular's dependency injection system for lifecycle management:

```

```

**Key Points:**

* **Registration**: Service is registered at [src/app/services/music.service.ts L8](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L8-L8)  with `providedIn: 'root'`
* **Scope**: Single instance shared across entire application
* **Lifecycle**: Created lazily on first injection, destroyed when application terminates
* **Injection**: Components inject via constructor parameter of type `MusicService`

**Sources:** [src/app/services/music.service.ts L7-L9](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L7-L9)

 [src/app/services/music.service.ts L14](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L14-L14)

---

## Extension Points

The current data layer is designed for extensibility. Potential enhancements include:

### Additional Service Methods

Future methods could be added to `MusicService`:

| Method | Signature | Purpose |
| --- | --- | --- |
| `getGroupById(id: number)` | `Observable<Group>` | Retrieve single group |
| `searchGroups(query: string)` | `Observable<Group[]>` | Filter groups by name |
| `createGroup(group: Group)` | `Observable<Group>` | Add new group (POST) |
| `updateGroup(id: number, group: Partial<Group>)` | `Observable<Group>` | Update existing group (PUT/PATCH) |
| `deleteGroup(id: number)` | `Observable<void>` | Remove group (DELETE) |

### Additional Data Models

Future interfaces could extend the data layer:

* `Album` interface for individual albums within groups
* `Song` interface for tracks within albums
* `Artist` interface for individual band members
* `Genre` interface for categorizing groups

### Environment-Specific API URLs

The hardcoded `apiUrl` at [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)

 could be replaced with environment-specific configuration to support different backends for development, staging, and production environments.

**Sources:** [src/app/services/music.service.ts L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/src/app/services/music.service.ts#L12-L12)