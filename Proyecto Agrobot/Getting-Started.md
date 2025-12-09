# Getting Started

> **Relevant source files**
> * [.gitignore](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/.gitignore)
> * [docs/README.md](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md)
> * [requirements.txt](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt)

This page provides an overview of setting up and running the Agrobot system for the first time. It covers the prerequisites, high-level setup process, and two deployment paths: local development and Docker-based production deployment.

For detailed system architecture, see [System Architecture](/axchisan/ProyectoAgroBot/3-system-architecture). For information about the underlying technology choices, see [Technology Stack](/axchisan/ProyectoAgroBot/1.3-technology-stack).

---

## Purpose and Scope

This guide walks through the complete setup process from a clean machine to a running Agrobot instance. The setup involves:

1. Establishing the Python environment (Anaconda or Docker)
2. Installing required dependencies from `requirements.txt`
3. Training the intent classification model using `train_intent_classifier.py`
4. Running the Flask application via `app.py` or Docker Compose

For step-by-step installation instructions, see [Installation and Setup](/axchisan/ProyectoAgroBot/2.1-installation-and-setup). For model training details, see [Training the Intent Classifier](/axchisan/ProyectoAgroBot/2.2-training-the-intent-classifier). For runtime configuration, see [Running the Application](/axchisan/ProyectoAgroBot/2.3-running-the-application) and [Docker Deployment](/axchisan/ProyectoAgroBot/2.4-docker-deployment).

**Sources:** [docs/README.md L1-L173](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L1-L173)

---

## Prerequisites

The following table summarizes the system requirements for running Agrobot:

| Component | Requirement | Purpose |
| --- | --- | --- |
| **Python** | 3.11.2 | Core runtime environment |
| **Anaconda** | 24.9.2+ | Package and environment management (local development) |
| **Docker** | Latest | Container runtime (production deployment) |
| **RAM** | 4GB minimum, 8GB recommended | Model training and inference |
| **Storage** | 2GB free space | Dependencies, models, and datasets |
| **Operating System** | Windows (64-bit), macOS, Linux | Cross-platform support |
| **Internet** | Required during setup | Downloading dependencies and pretrained models |
| **Browser** | Modern browser (Chrome, Firefox, Edge) | Accessing web interface |

**Sources:** [docs/README.md L34-L41](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L34-L41)

---

## Setup Flow Overview

The following diagram illustrates the complete setup process from initial prerequisites to a running application:

```

```

**Sources:** [docs/README.md L46-L143](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L46-L143)

---

## Development Environment Options

Agrobot supports two primary deployment configurations, each with distinct use cases:

### Local Development (Anaconda)

The Anaconda-based setup is optimized for development and experimentation:

| Aspect | Configuration |
| --- | --- |
| **Environment Manager** | `conda` with virtual environment `agrobot` |
| **Python Version** | 3.11.2 (enforced by conda) |
| **Server** | Flask development server |
| **Port** | 5000 (default Flask port) |
| **Hot Reload** | Enabled via Flask debug mode |
| **Dependency Management** | `pip` within conda environment |
| **Use Case** | Development, model training, debugging |

**Setup Command Sequence:**

```

```

**Sources:** [docs/README.md L69-L143](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L69-L143)

### Production Deployment (Docker)

The Docker-based setup provides a production-ready environment:

| Aspect | Configuration |
| --- | --- |
| **Base Image** | `python:3.11.2-slim` |
| **WSGI Server** | Gunicorn with 4 workers |
| **Port** | 5017 (mapped from container port 5000) |
| **Volume Mounting** | Application code mounted for updates |
| **Orchestration** | Docker Compose |
| **Use Case** | Production deployment, consistent environments |

**Setup Command Sequence:**

```

```

For detailed Docker configuration, see [Docker Deployment](/axchisan/ProyectoAgroBot/2.4-docker-deployment).

**Sources:** Deployment diagrams from system overview

---

## Setup Process to Code Mapping

The following diagram maps each setup step to the actual files and commands involved:

```

```

**Key Files:**

* [requirements.txt L1-L70](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L1-L70) : Python package dependencies
* [train_intent_classifier.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/train_intent_classifier.py) : Model training entry point
* [app.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app.py)  or [main.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/main.py) : Flask application entry point
* [app/models/intent_classifier/](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/models/intent_classifier/) : Directory containing trained model artifacts
* [app/data/questions.json](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/data/questions.json) : Knowledge base for theoretical questions
* [data/processed/](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/data/processed/) : Agricultural datasets in CSV format

**Sources:** [docs/README.md L105-L143](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L105-L143)

 [.gitignore L26-L34](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/.gitignore#L26-L34)

---

## Critical Setup Steps

### 1. Environment Activation

The conda environment must be activated before any Python commands:

```

```

If the environment is not activated, Python commands will use the system Python instead of the project-specific version, leading to dependency conflicts.

**Sources:** [docs/README.md L82-L86](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L82-L86)

### 2. Model Training Requirement

The intent classifier model **must** be trained before launching the application. The application will fail to start if the model directory does not exist:

```

```

This command generates:

* Trained model weights in [app/models/intent_classifier/](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/models/intent_classifier/)
* Label mapping file [app/models/intent_classifier/label_map.pkl](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/models/intent_classifier/label_map.pkl)
* Intermediate checkpoints in `results/` (can be deleted after training)

The training process typically takes 5-15 minutes depending on hardware. See [Training the Intent Classifier](/axchisan/ProyectoAgroBot/2.2-training-the-intent-classifier) for details.

**Sources:** [docs/README.md L118-L132](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L118-L132)

 [.gitignore L26-L34](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/.gitignore#L26-L34)

### 3. Cleanup After Training

After successful training, the `results/` directory contains training checkpoints that are no longer needed:

```

```

This directory can contain several hundred megabytes of checkpoint data and should be removed to conserve disk space.

**Sources:** [docs/README.md L127-L131](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L127-L131)

 [.gitignore L26-L27](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/.gitignore#L26-L27)

---

## Verification Steps

After completing the setup, verify the installation using the following checks:

### Environment Verification

```

```

Expected output:

* Python: `Python 3.11.2`
* Flask: `Flask: 3.1.1` (or higher)
* PyTorch: `PyTorch: 2.7.0` (or compatible)
* Transformers: `Transformers: 4.52.3` (or compatible)

**Sources:** [docs/README.md L88-L94](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L88-L94)

 [requirements.txt L10-L62](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L10-L62)

### Model Training Verification

Verify that the model artifacts were created:

```

```

If any files are missing, re-run `train_intent_classifier.py`.

**Sources:** [.gitignore L33](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/.gitignore#L33-L33)

### Application Launch Verification

Start the application and verify it responds:

```

```

Open browser to `http://127.0.0.1:5000` and verify:

* Landing page loads successfully
* Navigation to `/chat` shows chat interface
* Sending a test message returns a response

**Sources:** [docs/README.md L133-L143](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L133-L143)

---

## Development Workflow

Once the initial setup is complete, the typical development workflow follows this pattern:

```

```

**Notes:**

* Flask's debug mode enables hot-reloading for template and static file changes
* Python code changes (routes, chatbot logic) require manual server restart
* The conda environment must remain activated throughout the development session

**Sources:** [docs/README.md L156-L162](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L156-L162)

---

## Common Setup Issues

| Issue | Symptom | Solution |
| --- | --- | --- |
| **Wrong Python Version** | Module import errors, compatibility warnings | Verify conda environment is activated: `conda activate agrobot` |
| **Missing Model** | `FileNotFoundError` when starting app | Run `python train_intent_classifier.py` |
| **Dependency Conflicts** | Import errors or version warnings | Recreate environment: `conda env remove -n agrobot` then reinstall |
| **Port Already in Use** | `OSError: [Errno 48] Address already in use` | Kill existing process or change port in app.py |
| **Out of Memory** | Crash during model training | Reduce batch size in training script or add more RAM |
| **CUDA Errors** | GPU-related errors during training | Install CPU-only PyTorch or ensure CUDA drivers are current |

For detailed troubleshooting, see the subsection pages under this section.

**Sources:** [requirements.txt L1-L70](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/requirements.txt#L1-L70)

 [docs/README.md L95-L104](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/docs/README.md#L95-L104)

---

## Next Steps

After completing the initial setup, proceed to:

1. **[Installation and Setup](/axchisan/ProyectoAgroBot/2.1-installation-and-setup)**: Detailed installation instructions with platform-specific guidance
2. **[Training the Intent Classifier](/axchisan/ProyectoAgroBot/2.2-training-the-intent-classifier)**: In-depth explanation of the model training process
3. **[Running the Application](/axchisan/ProyectoAgroBot/2.3-running-the-application)**: Configuration options and runtime behavior
4. **[Docker Deployment](/axchisan/ProyectoAgroBot/2.4-docker-deployment)**: Production deployment with Docker Compose

For understanding how the system works internally, see:

* **[Chatbot Core System](/axchisan/ProyectoAgroBot/4-chatbot-core-system)**: How queries are processed and responses generated
* **[Data Management](/axchisan/ProyectoAgroBot/5-data-management)**: How knowledge base and agricultural data are loaded
* **[Machine Learning Pipeline](/axchisan/ProyectoAgroBot/8-machine-learning-pipeline)**: Intent classification model architecture and training

**Sources:** Table of contents structure