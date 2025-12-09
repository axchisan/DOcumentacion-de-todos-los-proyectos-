# Project Structure

> **Relevant source files**
> * [app/chatbot/__init__.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py)
> * [docs/README.md](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md)

This document describes the physical organization of the Agrobot codebase, including the directory hierarchy, module layout, and file system structure. It explains how code, data, and configuration files are organized to support the system's layered architecture.

For information about how these components interact at runtime, see [System Architecture](/axchisan/ProyectoAgroBot/3-system-architecture). For details on specific subsystems, refer to [Chatbot Core System](/axchisan/ProyectoAgroBot/4-chatbot-core-system), [Data Management](/axchisan/ProyectoAgroBot/5-data-management), and [Web Interface](/axchisan/ProyectoAgroBot/7-web-interface).

---

## Overview

The Agrobot project follows a modular structure that separates concerns between application code, data assets, configuration, and deployment artifacts. The codebase is organized to support both development workflows (training models, processing data) and production deployment (Docker containers, web serving).

**Root Directory Structure**

```markdown
ProyectoAgroBot/
├── app/                    # Flask application and chatbot core
├── data/                   # Agricultural datasets and knowledge base
├── docs/                   # Documentation files
├── app.py                  # Flask application entry point
├── main.py                 # Alternative application entry point
├── train_intent_classifier.py    # ML model training script
├── convert_xlsx_to_csv.py        # Data conversion utility
├── requirements.txt        # Python dependencies
├── Dockerfile             # Docker image definition
├── docker-compose.yml     # Docker orchestration
└── .gitignore            # Version control exclusions
```

Sources: [docs/README.md L146-L153](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L146-L153)

---

## Application Directory (app/)

The `app/` directory contains all Flask application code, chatbot logic, machine learning models, and web interface assets.

### Directory Layout Diagram

```

```

Sources: High-level architecture diagrams, [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)

### Module Organization

#### Routes Module (app/routes/)

Contains Flask blueprint definitions for handling HTTP requests:

| File | Blueprint Name | Purpose |
| --- | --- | --- |
| `main.py` | `main` | Primary routes: landing page, chat interface, weather, recommendations, crop status, support pages |
| `analytics_routes.py` | `analytics` | Analytics dashboard routes, API endpoints for chart data, export functionality |

The blueprints are registered in the main Flask application during initialization.

#### Chatbot Module (app/chatbot/)

Core chatbot processing logic organized into specialized processors:

| File | Primary Classes/Functions | Purpose |
| --- | --- | --- |
| `__init__.py` | `init_chatbot()` | Initialization function that loads data and instantiates processors |
| `question_processor.py` | `QuestionProcessor` | Main query handler, intent routing, response generation |
| `dataset_processor.py` | `DatasetProcessor` | Agricultural data queries, crop recommendations, production statistics |
| `nlp_processor.py` | `NLPProcessor` | BERT-based intent classification |
| `location_handler.py` | `LocationHandler` | Geographic services, location extraction, regional recommendations |
| `data_loader.py` | `load_questions()`, `load_agricultural_data()`, `load_department_data()`, `load_dynamic_datasets()` | Data loading utilities |

The initialization flow follows this pattern:

```

```

Sources: [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

#### Models Directory (app/models/)

Stores trained machine learning models and associated artifacts:

```markdown
app/models/
└── intent_classifier/
    ├── config.json          # Model configuration
    ├── pytorch_model.bin    # Trained BERT weights
    ├── tokenizer_config.json
    ├── vocab.txt
    └── label_map.pkl        # Intent label mappings
```

This directory is populated by running [train_intent_classifier.py

1](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/train_intent_classifier.py#L1-LNaN)

Sources: High-level ML pipeline diagram, training documentation

#### Templates Directory (app/templates/)

Jinja2 HTML templates for the web interface:

| Template | Route | Purpose |
| --- | --- | --- |
| `index.html` | `/` | Landing page |
| `chat.html` | `/chat` | Interactive chat interface |
| `weather.html` | `/weather` | Weather information page |
| `recommendations.html` | `/recommendations` | Crop recommendations page |
| `crop_status.html` | `/crop-status` | Crop status monitoring |
| `support.html` | `/support` | User support and documentation |
| `dashboard.html` | `/dashboard` | Analytics overview |
| `national_crops.html` | `/national-crops` | National crop analysis |
| `municipal_data.html` | `/municipal-data` | Municipal-level data |
| `departmental_comparison.html` | `/departmental-comparison` | Cross-departmental comparison |
| `departmental_participation.html` | `/departmental-participation` | Regional participation analysis |

Sources: High-level system architecture, web interface documentation

#### Static Assets (app/static/)

Client-side resources:

```markdown
app/static/
├── css/
│   ├── style.css           # Global styles
│   ├── chat.css            # Chat interface styles
│   └── analytics.css       # Analytics dashboard styles
├── js/
│   ├── chat.js             # Chat interaction logic
│   ├── charts.js           # Chart rendering
│   └── analytics.js        # Analytics functionality
└── images/
    └── [image assets]
```

---

## Data Directory (data/)

The `data/` directory contains all datasets, knowledge bases, and data processing artifacts.

### Data Directory Structure

```

```

Sources: [app/chatbot/__init__.py L6-L26](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L26)

 data processing pipeline diagram

### Data File Organization

#### Knowledge Base

| File | Format | Purpose |
| --- | --- | --- |
| `data/questions.json` | JSON | Static knowledge base with theoretical and dynamic questions, answer templates |

Structure includes:

* `questions_teoricas`: Theoretical agricultural knowledge
* `questions_dinamicas`: Dynamic questions requiring data lookup
* Each question has patterns, intents, and response templates

Sources: [app/chatbot/__init__.py L22](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L22-L22)

#### Raw Data (data/raw/)

Original, unprocessed data files:

| File/Directory | Format | Content |
| --- | --- | --- |
| `departments_data.csv` | CSV | Colombian department information, coordinates, climate zones |
| `training_data/` | Directory | Intent classification training examples (400+ questions, 14 intent types) |
| `*.xlsx` | Excel | Original agricultural statistics from government sources |

Sources: [app/chatbot/__init__.py L24](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L24-L24)

 training data documentation

#### Processed Data (data/processed/)

CSV files converted from Excel, organized by data category:

| Directory | Content |
| --- | --- |
| `Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/` | National-level data: area planted, production volume, yield per crop |
| `Produccion_Municipal/` | Municipal-level production statistics |
| `Produccion_Departamental/` | Departmental-level production aggregates |
| `Rendimiento_Nacional/` | National yield data by crop and year |

Each category contains individual CSV files per crop (e.g., `Cafe.csv`, `Maiz.csv`, `Arroz.csv`).

The conversion from XLSX to CSV is handled by [convert_xlsx_to_csv.py

1](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/convert_xlsx_to_csv.py#L1-LNaN)

Dynamic dataset loading is performed by `load_dynamic_datasets()` in [app/chatbot/data_loader.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#LNaN-LNaN)

 which scans these directories and makes data available to `DatasetProcessor`.

Sources: [app/chatbot/__init__.py L6](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L6)

 data processing pipeline diagram

---

## Root-Level Files

### Application Entry Points

| File | Purpose | Usage |
| --- | --- | --- |
| `app.py` | Flask application entry point | `python app.py` to start development server |
| `main.py` | Alternative entry point | Used by some deployment configurations |

Both files initialize the Flask application, register blueprints, and configure the chatbot system via `init_chatbot()`.

Sources: [docs/README.md L137-L142](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L137-L142)

### Training and Utilities

| File | Purpose | Usage |
| --- | --- | --- |
| `train_intent_classifier.py` | Trains the BERT-based intent classifier | `python train_intent_classifier.py` before first run |
| `convert_xlsx_to_csv.py` | Converts Excel datasets to CSV format | Run when updating agricultural data |

The training script generates the model files stored in `app/models/intent_classifier/` and creates checkpoints in a `results/` directory (which should be deleted after training).

Sources: [docs/README.md L118-L131](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L118-L131)

### Configuration Files

| File | Purpose |
| --- | --- |
| `requirements.txt` | Python package dependencies (Flask, PyTorch, Transformers, pandas, etc.) |
| `Dockerfile` | Docker image definition based on `python:3.11.2-slim` |
| `docker-compose.yml` | Docker Compose configuration, exposes port 5017, mounts code as volume |
| `.gitignore` | Excludes virtual environments, `__pycache__`, model checkpoints, API keys |

Sources: [docs/README.md L112-L116](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L112-L116)

 deployment architecture diagram

---

## Module Import Hierarchy

The following diagram shows how modules are imported and initialized at application startup:

```

```

Sources: [app/chatbot/__init__.py L1-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L1-L44)

---

## File Type Summary

The codebase contains the following file types:

| Extension | Count Range | Purpose | Location |
| --- | --- | --- | --- |
| `.py` | 10-20 | Python source code | `app/`, `app/chatbot/`, `app/routes/` |
| `.html` | 10-15 | Jinja2 templates | `app/templates/` |
| `.css` | 5-10 | Stylesheets | `app/static/css/` |
| `.js` | 5-10 | Client-side scripts | `app/static/js/` |
| `.json` | 2-5 | Configuration, knowledge base, model config | `data/`, `app/models/` |
| `.csv` | 50+ | Agricultural datasets | `data/processed/`, `data/raw/` |
| `.pkl` | 1-2 | Serialized Python objects (label maps) | `app/models/` |
| `.bin` | 1 | PyTorch model weights | `app/models/intent_classifier/` |
| `.txt` | 2-3 | Requirements, vocabulary | Root, `app/models/` |
| `.md` | 1-2 | Documentation | `docs/` |
| `.yml` | 1 | Docker Compose configuration | Root |
| `Dockerfile` | 1 | Docker image definition | Root |

Sources: Repository structure analysis

---

## Development vs. Production Structure

The project structure supports both development and production workflows:

**Development Mode:**

* Direct execution via `python app.py`
* Anaconda environment with `conda activate agrobot`
* Source code edited directly in `app/`
* Flask development server on port 5000

**Production Mode (Docker):**

* Containerized deployment via `docker-compose up`
* Gunicorn WSGI server
* Volume mount from host to container for hot-reloading
* Exposed on port 5017 (host) → 5000 (container)

The same directory structure serves both modes, with Docker using volume mounts to access the codebase.

Sources: [docs/README.md L42-L162](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L42-L162)

 deployment architecture diagram

---

## Summary

The Agrobot project structure follows a clear separation of concerns:

1. **Application code** in `app/` with Flask routes, chatbot processors, and ML models
2. **Data assets** in `data/` with raw sources and processed CSVs
3. **Configuration** at the root level for dependencies, deployment, and version control
4. **Entry points** (`app.py`, `train_intent_classifier.py`) for different workflows

This organization supports the layered architecture described in [System Architecture](/axchisan/ProyectoAgroBot/3-system-architecture), with each directory corresponding to specific layers: UI templates for the interface layer, routes for the application layer, chatbot processors for the core logic layer, and data files for the data layer.

Sources: All sections above