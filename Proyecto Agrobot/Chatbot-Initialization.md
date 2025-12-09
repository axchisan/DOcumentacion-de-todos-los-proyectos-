# Chatbot Initialization

> **Relevant source files**
> * [app/chatbot/__init__.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py)
> * [app/chatbot/data_loader.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py)

## Purpose and Scope

This document details the chatbot initialization process in Agrobot, specifically the `init_chatbot` function that serves as the entry point for setting up the chatbot system. This initialization process loads all necessary data sources, configures external API integrations, and instantiates the core processors required for query handling.

For information about how the initialized chatbot processes user queries, see [Question Processing](/axchisan/ProyectoAgroBot/4.2-question-processing). For details on the data structures being loaded, see [Knowledge Base](/axchisan/ProyectoAgroBot/5.1-knowledge-base) and [Data Loading](/axchisan/ProyectoAgroBot/5.2-data-loading). For information about the NLP intent classification system, see [Intent Classification](/axchisan/ProyectoAgroBot/4.3-intent-classification).

---

## Overview

The chatbot initialization process orchestrates the loading of multiple data sources and the instantiation of specialized processor components. The `init_chatbot` function in [app/chatbot/__init__.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py)

 coordinates this process, returning a fully configured `QuestionProcessor` instance ready to handle user queries.

**Sources:** [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)

---

## Initialization Flow

The following diagram illustrates the complete initialization sequence, from function invocation through component instantiation:

```

```

**Sources:** [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

---

## Function Signature and Parameters

The `init_chatbot` function is defined in [app/chatbot/__init__.py L6-L19](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L19)

 with the following signature:

```

```

### Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `weather_api_key` | `str` (optional) | `None` | OpenWeatherMap API key for weather queries |
| `openai_api_key` | `str` (optional) | `None` | OpenAI API key for AI fallback responses |
| `questions_file` | `str` | `"data/questions.json"` | Path to JSON file containing theoretical and dynamic questions |
| `agricultural_file` | `str` | `"data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Cafe.csv"` | Path to CSV with agricultural data (legacy parameter) |
| `department_file` | `str` | `"data/raw/departments_data.csv"` | Path to CSV with Colombian department data |
| `dynamic_data_dir` | `str` | `"data/processed"` | Directory path for dynamically loaded agricultural datasets |

**Sources:** [app/chatbot/__init__.py L6-L16](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L16)

---

## Data Loading Process

The initialization function loads four distinct data sources using functions from the `data_loader` module:

```

```

### Questions Data

Loaded via `load_questions()` at [app/chatbot/data_loader.py L7-L15](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L7-L15)

 this function reads the JSON file containing predefined questions and their answers or templates. If the file is not found, it returns an empty dictionary with default keys `{"theoretical": [], "dynamic": []}`.

### Agricultural Data

Loaded via `load_agricultural_data()` at [app/chatbot/data_loader.py L17-L22](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L17-L22)

 this function reads a CSV file containing agricultural information such as crops, planting months, and yields. If the file is missing, it returns an empty pandas DataFrame.

### Department Data

Loaded via `load_department_data()` at [app/chatbot/data_loader.py L24-L30](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L24-L30)

 this function reads a CSV file with Colombian department information including production data and featured crops. Returns an empty DataFrame if not found.

### Dynamic Datasets

Loaded via `load_dynamic_datasets()` at [app/chatbot/data_loader.py L32-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L32-L46)

 this function walks through the specified directory and all subdirectories, loading every CSV file it finds. Each file is stored in a dictionary with the filename (without `.csv` extension) as the key and the DataFrame as the value. This enables flexible addition of new datasets without code changes.

**Sources:** [app/chatbot/__init__.py L21-L25](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L21-L25)

 [app/chatbot/data_loader.py L1-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L1-L46)

---

## API Key Configuration

The initialization process handles API keys with a fallback mechanism, prioritizing explicitly provided keys over environment variables:

```

```

The API key resolution logic is implemented at [app/chatbot/__init__.py L27-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L27-L29)

:

* **Weather API Key**: Uses `os.getenv("OPENWEATHERMAP_API_KEY")` as fallback if no key is provided as a parameter
* **OpenAI API Key**: Uses `os.getenv("OPENAI_API_KEY", "Api Key")` as fallback, with a temporary default value of `"Api Key"`

**Sources:** [app/chatbot/__init__.py L27-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L27-L29)

---

## Processor Initialization

After loading all data sources and configuring API keys, the initialization function instantiates two core processor components:

### DatasetProcessor Initialization

The `DatasetProcessor` is created at [app/chatbot/__init__.py L31-L32](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L31-L32)

 with the dynamically loaded datasets:

```

```

This processor provides methods for querying agricultural data, generating crop recommendations, and retrieving production statistics. For detailed information about its capabilities, see [Dataset Processing](/axchisan/ProyectoAgroBot/5.3-dataset-processing).

### QuestionProcessor Initialization

The `QuestionProcessor` is the central orchestrator for query handling, instantiated at [app/chatbot/__init__.py L35-L43](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L35-L43)

:

```

```

This processor receives all loaded data and both API keys. The `api_type` parameter is hardcoded to `"openai"`, specifying the external AI service to use for fallback responses.

**Sources:** [app/chatbot/__init__.py L31-L43](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L31-L43)

---

## Component Dependency Graph

The following diagram shows how the initialized components depend on each other and the data they consume:

```

```

**Sources:** [app/chatbot/__init__.py L31-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L31-L44)

---

## Return Value

The `init_chatbot` function returns a fully initialized `QuestionProcessor` instance at [app/chatbot/__init__.py L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L44-L44)

 This instance is ready to:

* Process user queries via its `process_question()` method
* Classify intent using integrated NLP capabilities
* Query agricultural datasets through the embedded `DatasetProcessor`
* Fetch weather information using the configured API key
* Generate AI-powered responses using the OpenAI API
* Maintain conversation context and state

The returned processor serves as the primary interface for all chatbot interactions in the Flask application routes.

**Sources:** [app/chatbot/__init__.py L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L44-L44)

---

## Usage Example

While this page does not show implementation code, the typical usage pattern in the Flask application involves calling `init_chatbot()` once during application startup and storing the returned processor for subsequent request handling:

```

```

The processor is then used in route handlers to process incoming chat messages. For details on how routes use the processor, see [Main Routes](/axchisan/ProyectoAgroBot/7.2-main-routes).

**Sources:** [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

---

## Error Handling

The initialization process includes error handling at the data loading layer:

* **Missing Files**: If any of the data files (`questions.json`, department CSV, agricultural CSV) are not found, the respective loader functions return empty data structures (empty dictionary or empty DataFrame) rather than raising exceptions
* **CSV Loading Errors**: The `load_dynamic_datasets()` function catches exceptions during CSV loading and prints error messages without stopping the initialization process
* **Partial Initialization**: The system can initialize even with missing data files, though functionality may be limited

This graceful degradation approach ensures the chatbot can start up even in environments with incomplete data, though proper configuration is recommended for full functionality.

**Sources:** [app/chatbot/data_loader.py L14-L15](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L14-L15)

 [app/chatbot/data_loader.py L22](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L22-L22)

 [app/chatbot/data_loader.py L29-L30](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L29-L30)

 [app/chatbot/data_loader.py L44-L45](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L44-L45)

---

## Integration Points

The `init_chatbot` function is called from:

* **Flask Application**: During application startup to create a global processor instance
* **Route Handlers**: In some implementations, may be called per-request or stored in application context
* **Testing**: Unit tests may call this function to create isolated processor instances

For information on how the Flask application integrates this initialization, see [Flask Application Structure](/axchisan/ProyectoAgroBot/7.1-flask-application-structure).

**Sources:** [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)