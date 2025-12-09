# Technology Stack

> **Relevant source files**
> * [.gitignore](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/.gitignore)
> * [docs/README.md](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md)
> * [requirements.txt](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt)

## Purpose and Scope

This document details the complete technology stack used in the Agrobot system, including frameworks, libraries, external services, and deployment infrastructure. It covers both development and production environments, specifying versions and explaining the role of each component in the system architecture.

For information about how these technologies are organized in the codebase, see [Project Structure](/axchisan/ProyectoAgroBot/1.1-project-structure). For details on setting up the development environment, see [Installation and Setup](/axchisan/ProyectoAgroBot/2.1-installation-and-setup). For deployment configurations, see [Docker Deployment](/axchisan/ProyectoAgroBot/2.4-docker-deployment).

---

## Core Technology Overview

Agrobot is built on Python 3.11.2 and uses a modern stack combining web development, machine learning, and data processing capabilities. The system is designed for both local development using Anaconda and production deployment using Docker containers.

### Technology Stack Diagram

```

```

**Sources:** [requirements.txt](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt)

 [docs/README.md L4-L6](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L4-L6)

---

## Web Framework Stack

### Flask Ecosystem

The application uses **Flask 3.1.1** as its web framework, providing routing, request handling, and template rendering capabilities.

| Component | Version | Role in System |
| --- | --- | --- |
| Flask | 3.1.1 | Core web framework, blueprint registration |
| Werkzeug | 3.1.3 | WSGI toolkit, URL routing, request/response objects |
| Jinja2 | 3.1.6 | Server-side template engine for HTML rendering |
| Gunicorn | Latest | Production-grade WSGI HTTP server |

**Key Flask Components:**

* `app.py` / `main.py` - Application initialization and configuration
* `routes.py` - Main application routes (chat, weather, recommendations)
* `analytics_routes.py` - Analytics dashboard routes
* Template rendering using Jinja2 for all HTML pages

**Sources:** [requirements.txt L10-L68](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L10-L68)

 [docs/README.md L6](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L6-L6)

### Request Handling Flow

```

```

**Sources:** [requirements.txt L10-L68](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L10-L68)

---

## Machine Learning Stack

### PyTorch and Transformers

The ML stack is centered on **PyTorch 2.7.0** and **Hugging Face Transformers 4.52.3**, providing deep learning capabilities for intent classification.

| Component | Version | Purpose |
| --- | --- | --- |
| PyTorch | 2.7.0 | Deep learning framework, tensor operations |
| Transformers | 4.52.3 | BERT model architecture, training utilities |
| Accelerate | 1.7.0 | Distributed training, mixed precision |
| Hugging Face Hub | 0.32.0 | Model repository access |
| Tokenizers | 0.21.1 | Fast text tokenization |
| SafeTensors | 0.5.3 | Secure model weight serialization |

**BERT Model Specification:**

* Base model: `dccuchile/bert-base-spanish-wwm-cased`
* Task: Sequence classification (intent classification)
* Output: 14 intent categories
* Training: 15 epochs, batch size 4
* Model files stored in: `app/models/intent_classifier/`

**Sources:** [requirements.txt L15-L61](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L15-L61)

### CUDA Support

The system includes NVIDIA CUDA libraries for GPU acceleration during training and inference:

```

```

**CUDA Components:**

* `nvidia-cuda-runtime-cu12==12.6.77` - Core CUDA runtime
* `nvidia-cudnn-cu12==9.5.1.17` - Deep neural network library
* `nvidia-cublas-cu12==12.6.4.1` - Basic linear algebra subprograms
* `nvidia-cufft-cu12==11.3.0.4` - Fast Fourier transform library
* `nvidia-nccl-cu12==2.26.2` - Multi-GPU communication

**Sources:** [requirements.txt L27-L40](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L27-L40)

### Training Infrastructure

The training pipeline uses specialized utilities:

| Component | Version | Role |
| --- | --- | --- |
| Transformers | 4.52.3 | `Trainer` API for model training |
| Accelerate | 1.7.0 | Training optimization, device management |
| tqdm | 4.67.1 | Progress bars during training |
| psutil | 7.0.0 | System resource monitoring |

**Training Process:**

* Script: `train_intent_classifier.py`
* Training logic: `training_utils.py`
* Output: Trained model + `label_map.pkl`

**Sources:** [requirements.txt L1-L61](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L1-L61)

 [docs/README.md L118-L131](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L118-L131)

---

## Data Processing Stack

### Core Data Libraries

| Library | Version | Primary Use Cases |
| --- | --- | --- |
| Pandas | 2.2.3 | CSV loading, DataFrame operations, agricultural data queries |
| NumPy | 2.2.6 | Numerical arrays, mathematical operations |
| Scikit-learn | 1.6.1 | Data splitting, metrics, preprocessing utilities |

**Data Processing Components:**

* `DatasetProcessor` class - Uses Pandas for querying agricultural CSVs
* `data_loader.py` - CSV loading with Pandas `read_csv()`
* `convert_xlsx_to_csv.py` - Excel to CSV conversion using Openpyxl

**Sources:** [requirements.txt L26-L53](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L26-L53)

### File Format Support

| Library | Version | Supported Formats |
| --- | --- | --- |
| Openpyxl | 3.1.5 | Excel files (.xlsx, .xlsm) |
| Pandas | 2.2.3 | CSV, JSON, Excel |

**Data Flow:**

```

```

**Sources:** [requirements.txt L41-L43](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L41-L43)

---

## Natural Language Processing

### NLP Libraries

| Component | Version | Purpose |
| --- | --- | --- |
| NLTK | 3.9.1 | Text tokenization, stopwords, linguistic utilities |
| Unidecode | 1.4.0 | Unicode to ASCII transliteration, text normalization |
| Regex | 2024.11.6 | Advanced pattern matching |

**NLP Processing Pipeline:**

1. Text normalization with Unidecode
2. Tokenization using NLTK
3. Intent classification via Transformers BERT model
4. Pattern matching for specific query types

**Code Integration:**

* `NLPProcessor` class uses Transformers for intent classification
* Text preprocessing before model inference
* Spanish language support via `dccuchile/bert-base-spanish-wwm-cased`

**Sources:** [requirements.txt L25-L66](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L25-L66)

---

## External Service Integrations

### API Clients

The system integrates with three external APIs using the **Requests 2.32.3** library:

```

```

| Service | API | Authentication | Rate Limits |
| --- | --- | --- | --- |
| OpenWeatherMap | REST | API Key | Free tier: 60 calls/minute |
| OpenAI | REST | API Key | Model-dependent |
| Nominatim | REST | User-Agent required | 1 request/second |

**Configuration:**

* API keys stored in environment variables
* Loaded via `python-dotenv` from `.env` file
* Accessed through `os.getenv()` in application code

**Sources:** [requirements.txt L51-L69](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L51-L69)

---

## Analytics and Visualization

### Data Visualization Stack

| Library | Version | Purpose |
| --- | --- | --- |
| Matplotlib | 3.10.3 | Chart generation for analytics dashboard |
| NumPy | 2.2.6 | Numerical data for plotting |
| Pandas | 2.2.3 | Data aggregation and preparation |

**Matplotlib Dependencies:**

* `fonttools==4.58.0` - Font management
* `pillow==11.2.1` - Image processing
* `kiwisolver==1.4.8` - Constraint solver for layouts
* `cycler==0.12.1` - Color cycle management
* `contourpy==1.3.2` - Contour plotting

**Analytics Routes:**

* `/dashboard` - Overview with KPI visualizations
* `/national-crops` - Crop production charts
* `/departmental-comparison` - Regional comparison graphs
* `/api/chart-data` - JSON endpoints for dynamic charts

**Sources:** [requirements.txt L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L6-L44)

---

## Development Environment

### Anaconda Distribution

**Version:** 24.9.2+
**Python Version:** 3.11.2

The development environment uses Anaconda for package and environment management:

| Feature | Details |
| --- | --- |
| Environment name | `agrobot` |
| Python version | 3.11.2 |
| Package manager | conda + pip |
| Activation | `conda activate agrobot` |

**Environment Creation:**

```

```

**Sources:** [docs/README.md L4-L86](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L4-L86)

### Development vs Production Environments

```

```

**Sources:** [docs/README.md L4-L142](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L4-L142)

---

## Deployment Infrastructure

### Docker Stack

**Base Image:** `python:3.11.2-slim`

The production deployment uses Docker for containerization:

| Component | Purpose |
| --- | --- |
| Dockerfile | Application container definition |
| docker-compose.yaml | Service orchestration |
| Gunicorn | Production WSGI server |
| Volume mounts | Hot-reloading during development |

**Port Configuration:**

* Container internal: 5000
* Host mapping: 5017
* Configured in `docker-compose.yaml`

**Sources:** [docs/README.md L4](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L4-L4)

### Configuration Management

**Environment Variables:**

* Managed via `python-dotenv==1.0.1`
* Loaded from `.env` file (ignored by git)
* Required variables: * `OPENWEATHER_API_KEY` * `OPENAI_API_KEY` * Other configuration settings

**Sources:** [requirements.txt L69](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L69-L69)

 [.gitignore L40](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/.gitignore#L40-L40)

---

## Supporting Libraries

### Utility Libraries

| Library | Version | Purpose |
| --- | --- | --- |
| click | 8.2.1 | Command-line interface creation |
| tqdm | 4.67.1 | Progress bar displays |
| PyYAML | 6.0.2 | YAML configuration parsing |
| python-dateutil | 2.9.0.post0 | Date/time parsing |
| pytz | 2025.2 | Timezone handling |

### File System and IO

| Library | Version | Purpose |
| --- | --- | --- |
| filelock | 3.18.0 | File locking for concurrent access |
| fsspec | 2025.5.1 | Filesystem abstraction |
| joblib | 1.5.1 | Efficient serialization |

### Text Processing

| Library | Version | Purpose |
| --- | --- | --- |
| unicode | 2.9 | Unicode utilities |
| Unidecode | 1.4.0 | ASCII transliteration |
| charset-normalizer | 3.4.2 | Character encoding detection |

**Sources:** [requirements.txt L4-L66](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L4-L66)

---

## Dependency Management

### Requirements File Structure

The `requirements.txt` file specifies exact versions for reproducibility:

```
Flask==3.1.1
torch==2.7.0
transformers==4.52.3
pandas==2.2.3
...
```

**Installation Process:**

1. Create Anaconda environment with Python 3.11.2
2. Install all dependencies: `pip install -r requirements.txt`
3. Train ML model: `python train_intent_classifier.py`
4. Run application: `python app.py`

**Dependency Categories:**

* **Core (7):** Flask, PyTorch, Transformers, Pandas, NumPy, NLTK, Requests
* **ML Support (5):** Accelerate, Hugging Face Hub, Tokenizers, SafeTensors, Scikit-learn
* **CUDA (13):** NVIDIA GPU acceleration libraries
* **Utilities (10+):** File handling, configuration, visualization, text processing

**Sources:** [requirements.txt](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt)

 [docs/README.md L112-L116](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L112-L116)

---

## Technology Selection Rationale

### Why These Technologies?

| Technology | Rationale |
| --- | --- |
| Python 3.11.2 | Latest stable, strong ML/data science ecosystem |
| Flask | Lightweight, flexible, perfect for chatbot API |
| PyTorch | Industry standard for NLP, excellent Transformers support |
| BERT Spanish | Pre-trained on Spanish corpus, ideal for Colombian farmers |
| Pandas | Best-in-class for CSV/tabular agricultural data |
| Anaconda | Simplifies dependency management for ML projects |
| Docker | Ensures consistent production environment |

**Colombian Context:**

* Spanish language model (`dccuchile/bert-base-spanish-wwm-cased`)
* Regional agricultural data support (Colombian departments)
* Geocoding for Colombian cities via Nominatim
* Weather data for Colombian regions via OpenWeatherMap

**Sources:** [docs/README.md L1-L11](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L1-L11)

---

## Version Compatibility Matrix

| Component | Minimum Version | Tested Version | Python Requirement |
| --- | --- | --- | --- |
| Python | 3.11.0 | 3.11.2 | N/A |
| Flask | 3.1.0 | 3.1.1 | ≥3.8 |
| PyTorch | 2.0.0 | 2.7.0 | ≥3.8 |
| Transformers | 4.30.0 | 4.52.3 | ≥3.8 |
| Pandas | 2.0.0 | 2.2.3 | ≥3.9 |
| Anaconda | 24.0.0 | 24.9.2 | N/A |

**Compatibility Notes:**

* CUDA libraries require NVIDIA GPU with compute capability 3.5+
* Transformers 4.52.3 requires PyTorch ≥2.1.0
* Anaconda environment ensures consistent dependency versions

**Sources:** [requirements.txt](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt)

 [docs/README.md L4-L38](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L4-L38)