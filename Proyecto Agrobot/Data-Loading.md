# Data Loading

> **Relevant source files**
> * [app/chatbot/__init__.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py)
> * [app/chatbot/data_loader.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py)

## Purpose and Scope

The data loading subsystem provides an abstraction layer for loading different types of data from files into memory structures that the Agrobot chatbot can use. This module handles loading the knowledge base, agricultural datasets, geographic data, and dynamically discovers CSV files in the processed data directory.

This page documents the `data_loader` module and its integration with chatbot initialization. For details about the structure of the knowledge base file, see [Knowledge Base](/axchisan/ProyectoAgroBot/5.1-knowledge-base). For information about how loaded data is processed and queried, see [Dataset Processing](/axchisan/ProyectoAgroBot/5.3-dataset-processing). For the structure of CSV files being loaded, see [Agricultural Data Structure](/axchisan/ProyectoAgroBot/5.4-agricultural-data-structure).

## Module Overview

The `data_loader` module ([app/chatbot/data_loader.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py)

) provides four primary loader functions that abstract file I/O operations and return structured data objects. These functions are called during chatbot initialization to prepare all necessary data before the system begins processing user queries.

```

```

**Sources:** [app/chatbot/data_loader.py L1-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L1-L46)

## Data Loading Functions

### load_questions

The `load_questions` function ([app/chatbot/data_loader.py L7-L15](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L7-L15)

) loads the knowledge base from a JSON file. This file contains predefined questions with their answers or response templates.

**Function Signature:**

```

```

**Parameters:**

* `file_path`: Path to the JSON questions file

**Returns:**

* Dictionary containing `theoretical` and `dynamic` question arrays
* Empty dictionary with default keys if file not found

**Error Handling:**

* Catches `FileNotFoundError` and prints error message
* Returns safe default: `{"theoretical": [], "dynamic": []}`

The function opens the file with UTF-8 encoding to properly handle Spanish text with accents and special characters.

**Sources:** [app/chatbot/data_loader.py L7-L15](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L7-L15)

### load_agricultural_data

The `load_agricultural_data` function ([app/chatbot/data_loader.py L17-L22](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L17-L22)

) loads a single CSV file containing agricultural production data. This is typically used for a default or primary crop dataset.

**Function Signature:**

```

```

**Parameters:**

* `file_path`: Path to the agricultural CSV file

**Returns:**

* Pandas DataFrame containing the CSV data
* Empty DataFrame if file not found

**Error Handling:**

* Catches `FileNotFoundError` silently
* Returns empty DataFrame as safe default

This function is primarily used during initialization with a default coffee crop dataset ([app/chatbot/__init__.py L6](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L6)

).

**Sources:** [app/chatbot/data_loader.py L17-L22](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L17-L22)

 [app/chatbot/__init__.py L6](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L6)

### load_department_data

The `load_department_data` function ([app/chatbot/data_loader.py L24-L30](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L24-L30)

) loads geographic and agricultural data for Colombian departments (states). This data includes production statistics and notable crops by region.

**Function Signature:**

```

```

**Parameters:**

* `file_path`: Path to the departments CSV file

**Returns:**

* Pandas DataFrame with department information
* Empty DataFrame if file not found

**Error Handling:**

* Catches `FileNotFoundError` and prints error message
* Returns empty DataFrame as safe default

This data is used by the location handler for providing region-specific crop recommendations.

**Sources:** [app/chatbot/data_loader.py L24-L30](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L24-L30)

### load_dynamic_datasets

The `load_dynamic_datasets` function ([app/chatbot/data_loader.py L32-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L32-L46)

) performs a recursive directory walk to discover and load all CSV files in the processed data directory. This enables the system to automatically incorporate new datasets without code changes.

**Function Signature:**

```

```

**Parameters:**

* `data_dir`: Root directory to scan for CSV files (default: `"data/processed"`)

**Returns:**

* Dictionary mapping dataset keys to DataFrames
* Keys are derived from filename without `.csv` extension, converted to lowercase

**Processing Logic:**

```

```

**Error Handling:**

* Catches general exceptions during CSV reading
* Prints error message with file path and exception details
* Continues processing other files rather than failing completely

**Example:**

* File: `data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Cafe.csv`
* Key: `"cafe"`
* Value: DataFrame with coffee production data

**Sources:** [app/chatbot/data_loader.py L32-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L32-L46)

## Integration with Chatbot Initialization

The loader functions are invoked by the `init_chatbot` function ([app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

) during system startup. This function orchestrates the complete data loading process.

```

```

**Default File Paths:**

| Data Type | Default Path | Parameter Name |
| --- | --- | --- |
| Questions | `data/questions.json` | `questions_file` |
| Agricultural | `data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Cafe.csv` | `agricultural_file` |
| Departments | `data/raw/departments_data.csv` | `department_file` |
| Dynamic Datasets | `data/processed` | `dynamic_data_dir` |

**Sources:** [app/chatbot/__init__.py L6-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L6-L44)

## Data Flow Architecture

The following diagram illustrates how data flows from the file system through the loading layer to the chatbot's processing components:

```

```

**Sources:** [app/chatbot/__init__.py L21-L43](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L21-L43)

 [app/chatbot/data_loader.py L1-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L1-L46)

## Loaded Data Structures

### Questions Data Structure

The dictionary returned by `load_questions` has the following structure:

| Key | Type | Description |
| --- | --- | --- |
| `theoretical` | `List[Dict]` | Predefined questions with fixed answers |
| `dynamic` | `List[Dict]` | Template-based questions requiring data injection |

Each question object contains patterns, answers/templates, and metadata. See [Knowledge Base](/axchisan/ProyectoAgroBot/5.1-knowledge-base) for detailed schema.

### DataFrame Structures

All CSV loader functions return `pandas.DataFrame` objects with the following characteristics:

**Agricultural Data DataFrame:**

* Columns depend on specific CSV file
* Typically includes: crop name, period, area, production, yield
* Used for production statistics and crop analysis

**Department Data DataFrame:**

* Columns: department name, production values, notable crops
* Used for geographic crop recommendations
* Indexed by department name

**Dynamic Datasets Dictionary:**

* Keys: Lowercase filename without extension
* Values: DataFrames with dataset-specific schemas
* Enables flexible querying across multiple datasets

**Sources:** [app/chatbot/data_loader.py L7-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L7-L46)

## Error Handling Strategy

The data loader module implements a graceful degradation strategy for error handling:

```

```

**Safe Defaults:**

| Function | Safe Default | Type |
| --- | --- | --- |
| `load_questions` | `{"theoretical": [], "dynamic": []}` | `Dict` |
| `load_agricultural_data` | `pd.DataFrame()` | Empty DataFrame |
| `load_department_data` | `pd.DataFrame()` | Empty DataFrame |
| `load_dynamic_datasets` | `{}` | Empty Dict |

This strategy ensures that:

1. The system can start even if some data files are missing
2. Error messages provide diagnostic information
3. Downstream components receive valid (albeit empty) data structures
4. The chatbot can fall back to alternative response strategies

**Sources:** [app/chatbot/data_loader.py L10-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L10-L46)

## Dynamic Discovery Mechanism

The `load_dynamic_datasets` function implements a discovery pattern that automatically finds and loads CSV files without requiring explicit configuration:

```

```

**Key Features:**

* **Recursive traversal**: Uses `os.walk()` to explore nested directories
* **Automatic key generation**: Converts filename to lowercase key
* **Flexible schema**: No assumptions about CSV column structure
* **Isolated error handling**: One corrupt file doesn't prevent loading others

This mechanism enables easy dataset expansion by simply adding new CSV files to the processed data directory without code modifications.

**Sources:** [app/chatbot/data_loader.py L32-L46](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/data_loader.py#L32-L46)

## Usage Example

The following code demonstrates typical usage of the data loader module during chatbot initialization:

```

```

The loaded data is then available throughout the chatbot's lifecycle for query processing and response generation.

**Sources:** [app/chatbot/__init__.py L21-L43](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/__init__.py#L21-L43)