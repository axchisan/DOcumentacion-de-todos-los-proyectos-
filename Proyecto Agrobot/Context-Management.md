# Context Management

> **Relevant source files**
> * [app/chatbot/question_processor.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py)
> * [app/routes/routes.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py)

## Purpose and Scope

This document describes how the Agrobot chatbot maintains conversational context and user state across interactions. Context management enables the system to remember user location, track mentioned crops, and provide personalized responses without requiring users to repeat information. This includes both in-memory context within the `QuestionProcessor` class and persistent session state managed by Flask.

For information about how context influences response generation, see [Response Strategy](/axchisan/ProyectoAgroBot/4.4-response-strategy). For details on location extraction and geocoding services, see [Location Services](/axchisan/ProyectoAgroBot/6.2-location-services).

---

## Context Data Structure

The chatbot maintains a context dictionary with five key elements that track user state across the conversation.

### Context Dictionary Schema

| Field | Type | Purpose | Example Value |
| --- | --- | --- | --- |
| `department` | `str` or `None` | User's Colombian department | `"Antioquia"` |
| `city` | `str` or `None` | User's city/municipality | `"Medellín"` |
| `last_crop` | `str` or `None` | Most recently mentioned crop | `"maíz"` |
| `lat` | `float` or `None` | GPS latitude coordinate | `6.2442` |
| `lon` | `float` or `None` | GPS longitude coordinate | `-75.5812` |

The context is initialized in the `QuestionProcessor.__init__` method with all values set to `None`:

```

```

**Sources:** [app/chatbot/question_processor.py L28-L34](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L28-L34)

---

## Context Initialization and Lifecycle

The following diagram illustrates how context is created and flows through the system from Flask initialization to query processing:

```

```

**Sources:** [app/chatbot/question_processor.py L13-L34](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L13-L34)

 [app/routes/routes.py L19-L22](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py#L19-L22)

### Dual-Layer State Management

Agrobot uses a two-tier approach to state management:

1. **Request-Level Context**: The `context` dictionary in `QuestionProcessor` is updated during each request and contains state for the current conversation turn
2. **Session-Level State**: Flask's session storage persists location data across requests, providing continuity between page loads

This architecture allows the chatbot to function both as a stateful conversational agent (within a single request) and maintain user preferences across multiple HTTP requests.

**Sources:** [app/chatbot/question_processor.py L28-L34](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L28-L34)

 [app/routes/routes.py L26-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py#L26-L29)

---

## Context Update Mechanisms

Context can be updated through three primary mechanisms: text extraction, GPS coordinates, and explicit session updates.

### Text-Based Location Extraction

The `update_context` method processes user input to extract location information:

```

```

The method checks if location information is present in the text and updates the context dictionary accordingly. If a department is found, it updates `context["department"]`; if a city is found, it updates `context["city"]`.

**Sources:** [app/chatbot/question_processor.py L88-L94](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L88-L94)

### GPS-Based Location Updates

When GPS coordinates are available, the system uses reverse geocoding to obtain location names:

```

```

The GPS workflow has two steps:

1. Frontend JavaScript captures coordinates and posts to `/update_location`
2. Coordinates are stored in session and passed to `process_question` on subsequent requests
3. The `update_context` method uses these coordinates to reverse-geocode location names

**Sources:** [app/chatbot/question_processor.py L78-L86](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L78-L86)

 [app/routes/routes.py L154-L168](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py#L154-L168)

### Crop Mention Tracking

The `extract_crop` method maintains the `last_crop` context field, which enables follow-up queries without repeating the crop name:

```

```

The method normalizes text, searches for known crop names, and stores the first match found. If no crop is detected in the current input, it returns the previously stored crop from `context["last_crop"]`.

**Sources:** [app/chatbot/question_processor.py L58-L69](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L58-L69)

 [app/chatbot/question_processor.py L250](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L250-L250)

---

## Context Usage in Response Generation

Context data is consumed throughout the query processing pipeline to provide location-aware and personalized responses.

### Context-Driven Response Selection

```

```

The system uses a fallback strategy where context values take precedence over default parameters:

```

```

This pattern ensures that once a user establishes their location (either through text or GPS), all subsequent queries use that location automatically.

**Sources:** [app/chatbot/question_processor.py L139-L140](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L139-L140)

### Intent-Specific Context Utilization

Different intent handlers consume context in specialized ways:

| Intent Type | Context Fields Used | Purpose |
| --- | --- | --- |
| `weather` | `lat`, `lon`, `city` | Fetch weather for user's location |
| `weather_sowing_advice` | `lat`, `lon`, `city` | Provide agricultural weather advice |
| `irrigation_advice` | `lat`, `lon`, `city` | Calculate irrigation needs from humidity |
| `recommendation` | `department` | Filter crop recommendations by region |
| `location_based_recommendation` | `department` | Same as recommendation |
| `crop_profitability` | `department`, `last_crop` | Compare crop profitability in user's region |
| `crop_production` | `department`, `last_crop` | Query production statistics |
| `crop_timing` | `last_crop` | Retrieve planting calendar |

### Weather Query Context Flow

Weather-related queries demonstrate the most complex context usage, supporting both coordinate-based and city-based lookups:

```

```

This hierarchical fallback ensures the best available location data is used: GPS coordinates are preferred (most accurate), followed by city name, with a clear error message if neither is available.

**Sources:** [app/chatbot/question_processor.py L157-L174](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L157-L174)

---

## Session Management Integration

Flask's server-side session storage provides persistence across HTTP requests, complementing the in-memory context.

### Session-Context Synchronization

```

```

### Session Variables

The Flask session maintains four primary location variables that mirror the context structure:

| Session Key | Type | Source | Usage |
| --- | --- | --- | --- |
| `session['lat']` | `float` | GPS capture via `/update_location` | Passed to `process_question` |
| `session['lon']` | `float` | GPS capture via `/update_location` | Passed to `process_question` |
| `session['city']` | `str` | Reverse geocoding or text extraction | Default city for queries |
| `session['department']` | `str` | Reverse geocoding or text extraction | Default department for queries |

**Sources:** [app/routes/routes.py L26-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py#L26-L29)

 [app/routes/routes.py L154-L168](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py#L154-L168)

### Location Update Endpoint

The `/update_location` endpoint enables JavaScript to submit GPS coordinates:

```

```

The endpoint stores both the raw coordinates and the reverse-geocoded location names, ensuring all location representations are available for subsequent requests.

**Sources:** [app/routes/routes.py L154-L168](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py#L154-L168)

---

## Context Lifecycle Example

The following diagram illustrates a complete context lifecycle across multiple user interactions:

```

```

This sequence demonstrates:

1. Initial context population from user text
2. Context persistence via session across requests
3. Crop tracking for anaphoric reference resolution
4. Implicit context usage in follow-up queries

**Sources:** [app/chatbot/question_processor.py L77-L95](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L77-L95)

 [app/chatbot/question_processor.py L132-L344](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L132-L344)

 [app/routes/routes.py L24-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py#L24-L44)

---

## Implementation Details

### Context Update Method

The `update_context` method implements the core context management logic:

```

```

Key behaviors:

* Coordinate updates take precedence over existing context values
* Text-based location extraction only updates if extraction succeeds
* Both methods can execute in the same call, with text overriding coordinates
* Partial updates are supported (only department or only city)

**Sources:** [app/chatbot/question_processor.py L77-L95](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L77-L95)

### Crop Extraction with Context Fallback

The `extract_crop` method demonstrates stateful parsing with context memory:

```

```

The fallback to `context.get("last_crop")` enables queries like:

* User: "¿Cuándo sembrar maíz?"
* Bot: "El maíz se siembra en marzo"
* User: "¿Dónde es más rentable?" (implicitly referring to maíz)

**Sources:** [app/chatbot/question_processor.py L58-L69](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L58-L69)

---

## Context Reset and Isolation

Each `QuestionProcessor` instance maintains its own context dictionary. In the current implementation:

* Context is **not explicitly reset** between user sessions
* A single `QuestionProcessor` instance is created at application startup and shared across all users
* Flask session provides isolation between users for location data
* The in-memory context may leak between users if crops are mentioned

### Potential Improvement

For multi-user production deployment, context should be stored in the Flask session rather than in the processor instance, ensuring complete isolation between users.

**Sources:** [app/chatbot/question_processor.py L28-L34](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L28-L34)

 [app/routes/routes.py L11](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/routes/routes.py#L11-L11)