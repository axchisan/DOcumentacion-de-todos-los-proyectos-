# Application Layers

> **Relevant source files**
> * [app/chatbot/__init__.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py)
> * [docs/README.md](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md)

## Purpose and Scope

This document describes the layered architecture of the Agrobot system, detailing the separation of concerns across six distinct layers: the User Interface Layer, Application Layer, Chatbot Core, Data Layer, External Services Layer, and ML/NLP Infrastructure. Each layer encapsulates specific responsibilities and interacts with adjacent layers through well-defined interfaces.

For information about how requests flow through these layers, see [Request Flow](/axchisan/ProyectoAgroBot/3.2-request-flow). For details on data processing pipelines within the data layer, see [Data Processing Pipeline](/axchisan/ProyectoAgroBot/3.3-data-processing-pipeline). For comprehensive documentation of the chatbot core components, see [Chatbot Core System](/axchisan/ProyectoAgroBot/4-chatbot-core-system).

---

## Architectural Overview

The Agrobot system follows a strict layered architecture where each layer depends only on the layers below it. This design promotes modularity, testability, and maintainability by ensuring clear separation of concerns.

### High-Level Layer Diagram

```

```

**Sources:** High-level system diagrams (Diagram 1), [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)

---

## Layer 1: User Interface Layer

The User Interface Layer consists of HTML templates rendered by Flask's Jinja2 templating engine and static assets (CSS, JavaScript) that provide the visual and interactive components of the application.

### Components

| Component | File Path | Purpose |
| --- | --- | --- |
| Chat Interface | `app/templates/chat.html` | Main chatbot interaction interface with message input and response display |
| Analytics Dashboard | `app/templates/dashboard.html` | Overview dashboard with KPIs and summary statistics |
| National Crops View | `app/templates/national_crops.html` | Interactive charts and filters for national agricultural data |
| Municipal Data View | `app/templates/municipal_data.html` | Granular municipal-level agricultural analysis |
| Departmental Comparison | `app/templates/departmental_comparison.html` | Cross-region comparative analysis |
| Departmental Participation | `app/templates/departmental_participation.html` | Regional contribution analysis |
| Weather Interface | `app/templates/weather.html` | Weather information display with agricultural context |
| Recommendations Page | `app/templates/recommendations.html` | Crop recommendation interface |
| Crop Status Page | `app/templates/crop_status.html` | Current crop condition monitoring |
| Support Page | `app/templates/support.html` | Help and documentation for users |
| Landing Page | `app/templates/index.html` | Entry point with navigation to main features |
| Stylesheets | `app/static/css/` | Visual styling and layout definitions |
| Client Scripts | `app/static/js/` | Interactive behaviors and AJAX requests |

### Responsibilities

* **Presentation**: Render data in user-friendly formats (charts, tables, forms)
* **User Input**: Capture user queries, filter selections, and form submissions
* **Client-Side Logic**: Handle dynamic updates via JavaScript without full page reloads
* **Accessibility**: Ensure responsive design for various devices and screen sizes

### Interaction Patterns

The UI layer communicates with the Application Layer exclusively through HTTP requests:

* **GET** requests for page rendering
* **POST** requests for form submissions (e.g., `/chat` endpoint)
* **AJAX** requests for dynamic data updates (e.g., `/api/chart-data`)

**Sources:** High-level system diagrams (Diagram 1, Diagram 5), [docs/README.md L145-L153](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L145-L153)

---

## Layer 2: Application Layer (Flask)

The Application Layer implements the web server using Flask, handling HTTP routing, request processing, session management, and response generation. This layer acts as the gateway between the UI and business logic.

### Flask Application Structure

```

```

### Key Components

| Component | File | Responsibilities |
| --- | --- | --- |
| Flask Application | `app.py` or `main.py` | Application initialization, blueprint registration, configuration |
| Main Routes Blueprint | `app/routes/routes.py` | Handle chatbot, weather, recommendations, and general pages |
| Analytics Routes Blueprint | `app/routes/analytics_routes.py` | Handle analytics dashboard, data visualization, and export |
| Session Management | Flask session | Maintain conversation context and user preferences |
| Error Handlers | Within route files | Handle 404, 500, and other HTTP errors |

### Initialization Process

The Flask application initialization follows this sequence:

1. **Application Creation**: Instantiate Flask app with configuration
2. **Blueprint Registration**: Register main and analytics blueprints with appropriate URL prefixes
3. **Chatbot Initialization**: Call `init_chatbot()` to set up chatbot processors (see [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44) )
4. **Static File Configuration**: Configure static assets and template directories
5. **Session Configuration**: Set up session management for conversation state

### Route Responsibilities

**Main Routes** (`routes.py`):

* Render page templates with initial data
* Handle chatbot POST requests by calling `QuestionProcessor.process_question()`
* Manage session state for conversation context
* Integrate weather API responses into templates

**Analytics Routes** (`analytics_routes.py`):

* Load and filter agricultural datasets
* Generate aggregated statistics using `DatasetProcessor`
* Serve JSON data for client-side chart rendering
* Export filtered data in CSV/JSON formats

**Sources:** High-level system diagrams (Diagram 1, Diagram 3, Diagram 5), [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

---

## Layer 3: Chatbot Core

The Chatbot Core layer contains the business logic for processing user questions, classifying intents, querying datasets, and generating responses. This is the most complex layer with multiple specialized processors working in coordination.

### Chatbot Core Component Diagram

```

```

### Core Processors

#### QuestionProcessor

**Location**: `app/chatbot/question_processor.py`

**Primary Responsibilities**:

* Orchestrate the question-answering pipeline
* Maintain conversation context across multiple turns
* Route requests to appropriate specialized handlers based on intent
* Implement multi-tier response strategy (pattern matching → data queries → AI fallback)
* Format responses for user display

**Key Methods**:

* `process_question(user_input, location)`: Main entry point for query processing
* `_match_question_pattern()`: Match user input against known question patterns
* `_handle_intent()`: Route classified intent to appropriate handler
* `_get_weather_response()`: Handle weather-related queries
* `_get_location_based_recommendation()`: Process location-specific crop recommendations
* `_call_external_api()`: Fallback to OpenAI for complex queries

#### NLPProcessor

**Location**: `app/chatbot/nlp_processor.py`

**Primary Responsibilities**:

* Load and manage the trained intent classification model
* Classify user queries into one of 14 intent types
* Tokenize Spanish text using BERT tokenizer
* Provide confidence scores for classifications

**Key Methods**:

* `classify_intent(text)`: Classify user input and return intent label with confidence
* `_load_model()`: Load saved BERT model and label mapping
* `_preprocess_text()`: Prepare text for model input

**Intent Types Supported**:

* `theoretical`: General agricultural knowledge questions
* `weather`, `weather_impact`, `weather_forecast`: Weather-related queries
* `location_based_recommendation`, `location_specific_production`: Geographic queries
* `recommendation_by_profitability`, `recommendation_by_season`: Crop recommendations
* `crop_timing`, `irrigation_advice`: Timing and scheduling queries
* `production_trends`, `yield_analysis`: Data analysis queries

#### DatasetProcessor

**Location**: `app/chatbot/dataset_processor.py`

**Primary Responsibilities**:

* Query agricultural CSV datasets
* Filter data by department, municipality, crop, year
* Calculate aggregations (totals, averages, trends)
* Generate crop recommendations based on production data
* Provide metadata (available crops, departments, years)

**Key Methods**:

* `get_recommended_crops(location, criteria)`: Recommend crops for a specific region
* `query_production_data(filters)`: Retrieve filtered production statistics
* `get_yield_analysis(crop, location)`: Analyze yield trends
* `get_available_crops()`: List all crops in datasets
* `get_departments_list()`: List all Colombian departments

#### LocationHandler

**Location**: `app/chatbot/location_handler.py`

**Primary Responsibilities**:

* Extract location entities from natural language text
* Geocode location names using Nominatim API
* Map Colombian departments and municipalities
* Provide location-specific crop recommendations
* Handle ambiguous location references

**Key Methods**:

* `extract_location(text)`: Identify location mentions in user input
* `geocode_location(location_name)`: Convert location name to coordinates
* `get_crop_recommendations(department)`: Get crops suitable for a department
* `resolve_ambiguous_location()`: Handle multiple location matches

### Initialization Flow

The `init_chatbot()` function in [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

 orchestrates the initialization:

1. **Load Data**: Call data loader functions to load questions, agricultural data, and department data
2. **Load Dynamic Datasets**: Load all CSV files from `data/processed/` directory
3. **Retrieve API Keys**: Get API keys from environment variables or function parameters
4. **Initialize DatasetProcessor**: Pass dynamic datasets to processor
5. **Initialize QuestionProcessor**: Pass all loaded data, processors, and API keys
6. **Return Processor**: Return configured `QuestionProcessor` instance

**Sources:** High-level system diagrams (Diagram 1, Diagram 2, Diagram 3), [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)

---

## Layer 4: Data Layer

The Data Layer provides abstraction over all data sources, including structured datasets (CSV), knowledge bases (JSON), and reference data. This layer ensures consistent data access patterns across the application.

### Data Layer Architecture

```

```

### Data Loader Functions

**Module**: `app/chatbot/data_loader.py`

| Function | Parameters | Return Type | Purpose |
| --- | --- | --- | --- |
| `load_questions()` | `filepath: str` | `dict` | Load Q&A knowledge base from JSON |
| `load_agricultural_data()` | `filepath: str` | `pandas.DataFrame` | Load single agricultural CSV file |
| `load_department_data()` | `filepath: str` | `pandas.DataFrame` | Load Colombian department reference data |
| `load_dynamic_datasets()` | `directory: str` | `dict[str, DataFrame]` | Load all CSV files from directory tree |

### Data Source Structure

#### questions.json Schema

```

```

#### Agricultural CSV Schema

**National Production Files** (`data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/*.csv`):

| Column | Data Type | Description |
| --- | --- | --- |
| `Año` | Integer | Year of data collection |
| `Departamento` | String | Colombian department name |
| `Cultivo` | String | Crop name |
| `Area_Sembrada` | Float | Planted area in hectares |
| `Area_Cosechada` | Float | Harvested area in hectares |
| `Produccion` | Float | Total production in tons |
| `Rendimiento` | Float | Yield in tons/hectare |

**Municipal Production Files** (`data/processed/Area_Produccion_y_Rendimiento_Municipal_por_Cultivo/*.csv`):

| Column | Data Type | Description |
| --- | --- | --- |
| `Año` | Integer | Year of data collection |
| `Departamento` | String | Colombian department name |
| `Municipio` | String | Municipality name |
| `Cultivo` | String | Crop name |
| `Area_Sembrada` | Float | Planted area in hectares |
| `Area_Cosechada` | Float | Harvested area in hectares |
| `Produccion` | Float | Total production in tons |
| `Rendimiento` | Float | Yield in tons/hectare |

#### Department Data Schema

**File**: `data/raw/departments_data.csv`

| Column | Data Type | Description |
| --- | --- | --- |
| `department_id` | Integer | Unique department identifier |
| `department_name` | String | Official department name |
| `capital` | String | Department capital city |
| `region` | String | Geographic region (Andina, Caribe, etc.) |
| `latitude` | Float | Approximate latitude |
| `longitude` | Float | Approximate longitude |

### Dynamic Dataset Loading

The `load_dynamic_datasets()` function scans the `data/processed/` directory recursively and loads all CSV files, organizing them by their directory structure. This allows the system to automatically discover and load new datasets without code changes.

**Example structure**:

```

```

**Sources:** High-level system diagrams (Diagram 2), [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)

---

## Layer 5: External Services Layer

The External Services Layer handles integration with third-party APIs that provide weather data, geocoding services, and AI-powered responses. Each service is encapsulated to isolate external dependencies.

### External Services Integration

```

```

### OpenWeatherMap Integration

**Purpose**: Provide real-time weather data and forecasts for agricultural decision-making

**Endpoints Used**:

* **Current Weather**: `/weather?q={city}&appid={api_key}&units=metric&lang=es`
* **5-Day Forecast**: `/forecast?q={city}&appid={api_key}&units=metric&lang=es`

**Integration Points**:

* `QuestionProcessor._get_weather_response()`: Fetch weather data when intent is classified as `weather`
* Weather route in `routes.py`: Display weather page with detailed forecasts

**Response Processing**:

* Extract temperature, humidity, precipitation
* Provide agricultural context (e.g., "Good conditions for planting" vs. "Delay planting due to heavy rain")
* Cache responses to reduce API calls

**API Key Configuration**:

* Environment variable: `OPENWEATHERMAP_API_KEY`
* Passed to `QuestionProcessor` during initialization (see [app/chatbot/__init__.py L28](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L28-L28) )

### Nominatim Geocoding Integration

**Purpose**: Convert location names to coordinates and resolve Colombian geographic entities

**Endpoints Used**:

* **Search**: `/search?q={location}&format=json&countrycodes=co`
* **Reverse**: `/reverse?lat={lat}&lon={lon}&format=json`

**Integration Points**:

* `LocationHandler.geocode_location()`: Convert user-provided location names to coordinates
* `LocationHandler.extract_location()`: Identify and validate location mentions in text

**Colombian-Specific Features**:

* Filter results to Colombia (`countrycodes=co`)
* Match against known department and municipality names
* Handle common misspellings and aliases

**Rate Limiting**:

* 1 request per second limit (per Nominatim usage policy)
* Implement exponential backoff for failures

### OpenAI API Integration

**Purpose**: Provide intelligent fallback responses for complex agricultural questions not covered by pattern matching or data queries

**Model Used**: GPT-3.5-turbo or GPT-4 (configurable)

**Integration Points**:

* `QuestionProcessor._call_external_api()`: Called when no pattern match or specialized handler applies
* Maintains conversation history for context-aware responses

**Prompt Engineering**:

* System prompt: "You are an agricultural advisor for Colombian small farmers..."
* Include conversation context and user location in prompt
* Request responses in Spanish

**API Key Configuration**:

* Environment variable: `OPENAI_API_KEY`
* Fallback value: "Api Key" (see [app/chatbot/__init__.py L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L29-L29) )

**Cost Optimization**:

* Use OpenAI only as last resort (after pattern matching and data queries fail)
* Limit response token count
* Cache common responses

**Sources:** High-level system diagrams (Diagram 1, Diagram 3), [app/chatbot/__init__.py L28-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L28-L29)

---

## Layer 6: ML/NLP Infrastructure

The ML/NLP Infrastructure layer provides the machine learning capabilities for intent classification, enabling the system to understand user queries and route them to appropriate handlers.

### ML Pipeline Components

```

```

### Intent Classification Model

**Base Model**: `dccuchile/bert-base-spanish-wwm-cased` (Chilean Spanish BERT)

**Model Type**: Sequence classification with 14 output classes

**Training Configuration**:

* **Epochs**: 15
* **Batch Size**: 4
* **Learning Rate**: 2e-5
* **Optimizer**: AdamW
* **Max Sequence Length**: 128 tokens
* **Warmup Steps**: 10% of total steps

**Model Storage**: `app/models/intent_classifier/`

**Files**:

* `pytorch_model.bin`: Model weights
* `config.json`: Model configuration
* `vocab.txt`: Tokenizer vocabulary
* `tokenizer_config.json`: Tokenizer settings
* `special_tokens_map.json`: Special token mappings

**Label Mapping**: `label_map.pkl` stores the bidirectional mapping between intent names and numeric IDs

### Supported Intent Types

| Intent ID | Intent Name | Description | Example Queries |
| --- | --- | --- | --- |
| 0 | `theoretical` | General agricultural knowledge | "¿Qué es el pH del suelo?" |
| 1 | `weather` | Current weather queries | "¿Cómo está el clima?" |
| 2 | `weather_impact` | Weather effects on crops | "¿Cómo afecta la lluvia al maíz?" |
| 3 | `weather_forecast` | Future weather predictions | "¿Lloverá mañana?" |
| 4 | `location_based_recommendation` | Crop suggestions by location | "¿Qué sembrar en Antioquia?" |
| 5 | `location_specific_production` | Production data by region | "¿Cuánto café produce Caldas?" |
| 6 | `recommendation_by_profitability` | Most profitable crops | "¿Qué cultivo da más ganancia?" |
| 7 | `recommendation_by_season` | Seasonal crop recommendations | "¿Qué sembrar en enero?" |
| 8 | `crop_timing` | Planting and harvest timing | "¿Cuándo cosechar tomate?" |
| 9 | `irrigation_advice` | Watering recommendations | "¿Cuánto riego necesita el arroz?" |
| 10 | `pest_management` | Pest control guidance | "¿Cómo controlar plagas en café?" |
| 11 | `fertilization_advice` | Fertilizer recommendations | "¿Qué fertilizante usar?" |
| 12 | `production_trends` | Historical production analysis | "¿Cómo ha variado la producción?" |
| 13 | `yield_analysis` | Yield performance analysis | "¿Cuál es el rendimiento promedio?" |

### Training Process

**Script**: `train_intent_classifier.py`

**Training Steps**:

1. Load training data from `data/training_data/` directory
2. Preprocess and tokenize questions
3. Split into training and validation sets (80/20)
4. Initialize BERT model with classification head
5. Fine-tune using Hugging Face Trainer
6. Evaluate on validation set
7. Save model to `app/models/intent_classifier/`
8. Save label mapping to `label_map.pkl`

**Training Utilities**: `training_utils.py` provides helper functions for data preparation, model training, and evaluation

### Runtime Inference

**NLPProcessor Integration**:

1. Initialize `NLPProcessor` with model directory path
2. Load saved model and tokenizer during initialization
3. Load `label_map.pkl` for intent name resolution
4. Expose `classify_intent(text)` method for query classification

**Classification Process**:

1. Tokenize input text using loaded tokenizer
2. Pass tokens through BERT model
3. Apply softmax to get probability distribution
4. Return intent with highest probability and confidence score
5. Use confidence threshold (e.g., 0.7) to determine if classification is reliable

**Performance Characteristics**:

* **Inference Time**: ~50-100ms per query on CPU
* **Memory Usage**: ~500MB for loaded model
* **Accuracy**: >90% on validation set

**Sources:** High-level system diagrams (Diagram 6), [docs/README.md L118-L131](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L118-L131)

---

## Layer Interactions and Data Flow

### Cross-Layer Communication Patterns

The following table summarizes how each layer interacts with others:

| Source Layer | Target Layer | Communication Pattern | Example |
| --- | --- | --- | --- |
| UI → Application | HTTP Requests | POST `/chat` with form data | User submits question |
| Application → Chatbot Core | Method Calls | `processor.process_question(input, location)` | Route handler calls processor |
| Chatbot Core → Data Layer | Function Calls | `load_questions(filepath)` | Processor loads knowledge base |
| Chatbot Core → External Services | HTTP APIs | OpenWeatherMap API call | Fetch weather data |
| Chatbot Core → ML Infrastructure | Model Inference | `nlp.classify_intent(text)` | Classify user intent |
| Data Layer → File System | File I/O | `pd.read_csv(filepath)` | Load CSV datasets |
| ML Infrastructure → File System | Model Loading | `torch.load(model_path)` | Load trained model |

### Request Processing Flow

A typical user query flows through the layers as follows:

1. **User Interface**: User types question in chat interface
2. **HTTP POST**: Browser sends request to `/chat` endpoint
3. **Application Layer**: Flask route receives request, extracts form data
4. **Chatbot Initialization**: Route handler calls `init_chatbot()` if needed
5. **Question Processing**: `QuestionProcessor.process_question()` is invoked
6. **Intent Classification**: `NLPProcessor.classify_intent()` determines intent
7. **Data Access**: Based on intent, query appropriate data sources via `DatasetProcessor`
8. **External APIs**: If needed, call weather or geocoding APIs
9. **Response Generation**: Format response based on data and intent
10. **Template Rendering**: Flask renders chat template with new message
11. **HTTP Response**: Browser receives HTML with updated chat
12. **UI Update**: JavaScript updates chat display

### Dependency Flow

```

```

### Layer Isolation and Testability

Each layer is designed to be independently testable:

* **UI Layer**: Can be tested with browser automation tools (Selenium, Playwright)
* **Application Layer**: Can be tested with Flask test client and mock processors
* **Chatbot Core**: Can be tested with mock data loaders and API responses
* **Data Layer**: Can be tested with sample CSV/JSON files
* **External Services**: Can be mocked using libraries like `responses` or `vcr.py`
* **ML Infrastructure**: Can be tested with sample model and known inputs

This layered architecture ensures that changes to one layer do not necessitate changes to others, promoting maintainability and scalability.

**Sources:** High-level system diagrams (Diagram 1, Diagram 3), [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

 [docs/README.md L1-L173](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L1-L173)