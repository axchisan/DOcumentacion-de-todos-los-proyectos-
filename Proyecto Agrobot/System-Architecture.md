# System Architecture

> **Relevant source files**
> * [app/chatbot/__init__.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py)
> * [app/chatbot/question_processor.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py)
> * [docs/README.md](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md)

## Purpose and Scope

This document provides a comprehensive overview of the Agrobot system architecture, describing the organization of major components, their relationships, and how they interact to provide agricultural assistance to Colombian farmers. The architecture follows a layered design with clear separation between the web interface, application logic, chatbot intelligence, data management, and external service integrations.

For specific implementation details of individual layers, see [Application Layers](/axchisan/ProyectoAgroBot/3.1-application-layers). For detailed request processing workflows, see [Request Flow](/axchisan/ProyectoAgroBot/3.2-request-flow). For data transformation pipelines, see [Data Processing Pipeline](/axchisan/ProyectoAgroBot/3.3-data-processing-pipeline).

---

## Architectural Overview

The Agrobot system implements a **multi-layered architecture** where each layer has distinct responsibilities and communicates through well-defined interfaces. The design prioritizes modularity, allowing individual components to be developed, tested, and maintained independently.

### High-Level System Diagram

```

```

**Sources:** [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)

 [app/chatbot/question_processor.py L1-L344](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L1-L344)

---

## Component Architecture

### Presentation Layer Components

The presentation layer consists of Jinja2 templates and static assets that render the user interface. Key templates include:

| Template | Purpose | Route |
| --- | --- | --- |
| `chat.html` | Real-time chatbot interface | `/chat` |
| `dashboard.html` | Analytics overview | `/dashboard` |
| `weather.html` | Weather information display | `/weather` |
| `recommendations.html` | Crop recommendations | `/recommendations` |
| `crop_status.html` | Crop status tracking | `/crop-status` |

**Sources:** [docs/README.md L1-L173](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L1-L173)

### Application Layer Components

The application layer is implemented using Flask with a blueprint-based modular structure:

```

```

The Flask application initializes the chatbot subsystem by calling `init_chatbot()` at startup, which returns a configured `QuestionProcessor` instance stored in the application context.

**Sources:** [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

### Business Logic Layer Components

The business logic layer contains specialized processors that handle different aspects of agricultural intelligence:

#### QuestionProcessor Class

The `QuestionProcessor` is the central orchestrator that:

* Receives user queries from route handlers
* Maintains conversation context (location, last crop, coordinates)
* Routes queries to appropriate handlers based on intent classification
* Manages fallback to external AI services when necessary

**Key attributes:**

* `questions_data`: Loaded knowledge base from JSON
* `agricultural_data`: Pandas DataFrame with crop data
* `department_data`: Geographic reference data
* `dataset_processor`: Instance for querying agricultural datasets
* `nlp_processor`: Instance for intent classification
* `context`: Dictionary storing conversation state

**Sources:** [app/chatbot/question_processor.py L12-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L12-L44)

#### Component Initialization Flow

```

```

The initialization sequence ensures all data dependencies are loaded before the processor becomes active. Environment variables for API keys are retrieved with fallback defaults defined in [app/chatbot/__init__.py L28-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L28-L29)

**Sources:** [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

---

## Core Processing Architecture

### Request Processing Pipeline

User requests flow through multiple processing stages before generating a response:

```

```

**Processing stages:**

1. **Text Normalization** ([question_processor.py L45-L56](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/question_processor.py#L45-L56) ): Removes accents, corrects common typos, standardizes format
2. **Context Update** ([question_processor.py L77-L94](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/question_processor.py#L77-L94) ): Extracts and stores location from text or coordinates
3. **Intent Classification** ([question_processor.py L142](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/question_processor.py#L142-L142) ): Uses BERT model to classify user intent into 14 categories
4. **Routing Logic** ([question_processor.py L144-L342](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/question_processor.py#L144-L342) ): Directs query to appropriate handler based on intent

**Sources:** [app/chatbot/question_processor.py L45-L344](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L45-L344)

### Intent Classification System

The system supports 14 distinct intent types organized into functional categories:

| Intent Label | Handler | Example Query |
| --- | --- | --- |
| `theoretical` | Pattern matching | "¿Qué es la rotación de cultivos?" |
| `weather` | `get_weather()` | "¿Cómo está el clima?" |
| `weather_forecast` | Weather API | "¿Va a llover mañana?" |
| `weather_sowing_advice` | `get_weather_for_sowing()` | "¿Es buen día para sembrar?" |
| `current_location` | Context retrieval | "¿Dónde estoy?" |
| `recommendation` | `get_recommended_crops()` | "¿Qué cultivo me recomiendas?" |
| `location_based_recommendation` | `get_recommended_crops()` | "¿Qué sembrar en Antioquia?" |
| `crop_profitability` | `get_most_favorable_department()` | "¿Dónde es más rentable el café?" |
| `crop_production` | `get_production_by_location()` | "¿Cuánto maíz se produce?" |
| `crop_timing` | Agricultural data lookup | "¿Cuándo sembrar papa?" |
| `irrigation_advice` | Weather-based recommendation | "¿Debo regar hoy?" |
| `least_favorable_department` | `get_least_favorable_department()` | "¿Dónde no sembrar arroz?" |
| `recommended_crops` | `get_recommended_crops()` | "Mejores cultivos para mi región" |
| `production_query` | `get_production_by_location()` | "Producción de café en 2020" |

Intent labels are defined in [question_processor.py L22-L27](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/question_processor.py#L22-L27)

 and used for routing decisions throughout the processing pipeline.

**Sources:** [app/chatbot/question_processor.py L22-L27](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L22-L27)

### Context Management System

The `QuestionProcessor` maintains conversational context to enable multi-turn interactions:

```

```

Context is initialized in [question_processor.py L28-L34](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/question_processor.py#L28-L34)

 and updated on every query via [question_processor.py L77-L94](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/question_processor.py#L77-L94)

 This allows subsequent queries to reference previous context (e.g., "¿Y en mi región?" after establishing location).

**Sources:** [app/chatbot/question_processor.py L28-L94](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L28-L94)

---

## Data Flow Architecture

### Data Loading and Processing

The system loads data from multiple sources at initialization:

```

```

**Data structures:**

* **questions_data**: Dictionary with `"theoretical"` and `"dynamic"` keys containing question-answer pairs
* **agricultural_data**: Pandas DataFrame loaded from CSV with crop timing data
* **department_data**: DataFrame with Colombian geographic data
* **dynamic_datasets**: Dictionary mapping location keys to DataFrames with production/yield data

**Sources:** [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

### Dataset Processing Architecture

The `DatasetProcessor` class provides methods for querying agricultural datasets:

```

```

Each method queries the internal `datasets` dictionary, which maps department/municipality names to their respective DataFrames. The processor handles normalization of location names and crop names to match dataset keys.

**Sources:** [app/chatbot/__init__.py L31-L32](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L31-L32)

 [app/chatbot/question_processor.py L16-L17](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L16-L17)

---

## External Service Integration

### API Integration Architecture

The system integrates with three external APIs to enhance capabilities:

```

```

**API Configuration:**

| Service | Key Location | Usage |
| --- | --- | --- |
| OpenWeatherMap | `OPENWEATHERMAP_API_KEY` env var | Current weather, forecasts, agricultural advice |
| OpenAI | `OPENAI_API_KEY` env var | Complex query fallback, max 400 tokens |
| Nominatim | No key required | Reverse geocoding, location extraction |

API keys are retrieved from environment variables in [app/chatbot/__init__.py L28-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L28-L29)

 with fallback defaults for development.

**Sources:** [app/chatbot/__init__.py L28-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L28-L29)

 [app/chatbot/question_processor.py L7-L8](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L7-L8)

 [app/chatbot/question_processor.py L96-L131](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L96-L131)

### OpenAI Integration Details

When no predefined pattern matches and the query is complex (>6 words), the system falls back to OpenAI:

**Configuration:**

* **Model**: `gpt-4o-mini` (cost-effective, Spanish-capable)
* **Max tokens**: 400 (limit response length)
* **Temperature**: 0.3 (deterministic responses)
* **System prompt**: Defined in [question_processor.py L35-L43](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/question_processor.py#L35-L43)  instructs model to act as agricultural advisor for Colombian farmers

The response is post-processed to remove repeated characters and normalize whitespace before returning to the user.

**Sources:** [app/chatbot/question_processor.py L96-L131](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L96-L131)

---

## Deployment Architecture

### Container Structure

The application is containerized using Docker for consistent deployment:

```

```

The Docker configuration enables:

* **Hot reloading**: Volume mounts allow code changes without container rebuild
* **Environment isolation**: Dependencies managed via `requirements.txt`
* **Port mapping**: Container port 5000 exposed as host port 5017
* **Resource efficiency**: Slim base image minimizes container size

**Sources:** [docs/README.md L1-L173](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L1-L173)

### Application Startup Sequence

```

```

The startup sequence ensures all dependencies are loaded before accepting requests. The BERT model loading can take several seconds on first startup.

**Sources:** [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

---

## Machine Learning Infrastructure

### Model Architecture

The NLP intent classification uses a fine-tuned Spanish BERT model:

```

```

**Model specifications:**

* **Base model**: `dccuchile/bert-base-spanish-wwm-cased`
* **Task**: Sequence classification with 14 intent labels
* **Training**: 15 epochs, batch size 4, learning rate auto
* **Output**: Softmax probabilities over intent classes
* **Artifacts**: Saved to `app/models/intent_classifier/`

The model is loaded once at application startup by `NLPProcessor.__init__()` and reused for all inference requests.

**Sources:** [app/chatbot/question_processor.py L18](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L18-L18)

---

## Summary

The Agrobot architecture demonstrates a well-structured design with clear separation of concerns:

* **Layered organization** enables independent development and testing of components
* **Modular processors** handle specialized tasks (NLP, datasets, location, weather)
* **Context management** enables natural multi-turn conversations
* **Multi-tier response strategy** ensures high answer coverage (pattern → data → AI)
* **External service integration** enhances capabilities beyond static knowledge
* **Containerized deployment** provides consistent runtime environment

The architecture supports the primary use case of providing agricultural advice to Colombian farmers through an intelligent chatbot interface with data-driven recommendations, weather integration, and AI-powered fallback responses.

**Sources:** [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)

 [app/chatbot/question_processor.py L1-L344](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L1-L344)

 [docs/README.md L1-L173](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L1-L173)