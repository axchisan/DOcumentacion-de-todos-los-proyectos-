# Dataset Processing

> **Relevant source files**
> * [app/chatbot/dataset_processor.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py)
> * [app/chatbot/question_processor.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py)

## Purpose and Scope

The Dataset Processing subsystem is responsible for loading, querying, and analyzing agricultural CSV data to provide data-driven recommendations and statistics to users. The `DatasetProcessor` class serves as the primary query engine for agricultural datasets, enabling the chatbot to answer questions about crop recommendations, production statistics, and profitability comparisons across Colombian departments and municipalities.

This document covers the internal workings of the `DatasetProcessor` class. For information about how datasets are loaded into memory, see [Data Loading](/axchisan/ProyectoAgroBot/5.2-data-loading). For details about the structure and schema of the CSV files, see [Agricultural Data Structure](/axchisan/ProyectoAgroBot/5.4-agricultural-data-structure).

---

## Class Architecture

The `DatasetProcessor` class provides a unified interface for querying agricultural data organized by department, crop, or municipality. It abstracts the complexity of multiple dataset types and provides normalized access through specialized query methods.

```

```

**Sources:** [app/chatbot/dataset_processor.py L1-L250](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L1-L250)

 [app/chatbot/question_processor.py L10-L13](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L10-L13)

---

## Initialization and Dataset Loading

### Constructor Parameters

The `DatasetProcessor` can be initialized in two ways:

| Parameter | Type | Description |
| --- | --- | --- |
| `datasets` | `str` | Path to directory containing CSV files (e.g., `"data/processed"`) |
| `datasets` | `Dict[str, pd.DataFrame]` | Pre-loaded dictionary of DataFrames keyed by dataset name |

```

```

**Sources:** [app/chatbot/dataset_processor.py L7-L24](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L7-L24)

 [app/chatbot/dataset_processor.py L31-L58](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L31-L58)

### Dataset Loading Process

The `_load_all_datasets()` method recursively walks the data directory and loads all CSV files:

1. **File Discovery**: Uses `os.walk()` to find all `.csv` files in the directory tree
2. **Key Generation**: Creates dataset keys from filenames (e.g., `"antioquia.csv"` → `"antioquia"`)
3. **Column Normalization**: Strips whitespace from column names
4. **Data Normalization**: Applies `_normalize_name()` to `Producto`, `Departamento`, and `Municipio` columns
5. **Storage**: Stores DataFrames in `self.datasets` dictionary

**Sources:** [app/chatbot/dataset_processor.py L31-L58](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L31-L58)

---

## Data Normalization

### Crop Name Mappings

The processor maintains a mapping dictionary to handle crop name variations:

```

```

**Sources:** [app/chatbot/dataset_processor.py L14-L17](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L14-L17)

### Normalization Function

The `_normalize_name()` method handles text normalization:

| Step | Operation | Example |
| --- | --- | --- |
| 1 | Remove accents using `unidecode()` | `"café"` → `"cafe"` |
| 2 | Convert to lowercase | `"Café"` → `"cafe"` |
| 3 | Strip whitespace | `" cafe "` → `"cafe"` |
| 4 | Apply crop mapping | `"cafe"` → `"café"` |

This ensures consistent matching regardless of user input variations or data inconsistencies.

**Sources:** [app/chatbot/dataset_processor.py L26-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L26-L29)

---

## Query Methods

### Most and Least Favorable Departments

These methods identify the best and worst performing departments for a specific crop based on a given metric.

```

```

**Method Signatures:**

| Method | Parameters | Returns |
| --- | --- | --- |
| `get_most_favorable_department()` | `crop: str, metric: str = "Rendimiento (ha/ton)"` | `Dict` with `department`, `value`, `year` or `None` |
| `get_least_favorable_department()` | `crop: str, metric: str = "Rendimiento (ha/ton)"` | `Dict` with `department`, `value`, `year` or `None` |

**Sources:** [app/chatbot/dataset_processor.py L60-L124](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L60-L124)

---

### Crop Recommendations by Department

The `get_recommended_crops()` method returns the top 3 crops for a given department based on average yield.

```

```

**Return Structure:**

```

```

**Sources:** [app/chatbot/dataset_processor.py L126-L149](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L126-L149)

---

### Production Queries by Location

The `get_production_by_location()` method retrieves production data for a specific crop, location, and year, supporting both department-level and municipality-level queries.

```

```

**Sources:** [app/chatbot/dataset_processor.py L151-L191](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L151-L191)

---

### Crop Profitability Comparison

The `compare_crops_profitability()` method compares two crops either within a specific department or at the national level.

| Comparison Level | Data Source | Calculation |
| --- | --- | --- |
| Department-specific | Departmental dataset filtered by `Producto` | Mean yield for each crop in that department |
| National | Average yield from `get_average_yield_by_crop()` | Overall average across all departments |

**Return Structure:**

```

```

**Sources:** [app/chatbot/dataset_processor.py L209-L250](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L209-L250)

 [app/chatbot/dataset_processor.py L193-L207](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L193-L207)

---

## Integration with Question Processor

The `DatasetProcessor` is instantiated and used by the `QuestionProcessor` to handle data-driven queries.

```

```

**Sources:** [app/chatbot/question_processor.py L234-L239](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L234-L239)

 [app/chatbot/question_processor.py L318-L325](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L318-L325)

---

## Dataset Query Patterns

The `DatasetProcessor` supports three distinct dataset organization patterns:

### Pattern 1: Departmental Datasets

Files named by department (e.g., `antioquia.csv`, `cundinamarca.csv`) containing all crops for that region.

**Columns:** `Año`, `Producto`, `Departamento`, `Area sembrada (ha)`, `Area cosechada (ha)`, `Produccion (ton)`, `Rendimiento (ha/ton)`

**Usage:** `get_recommended_crops("antioquia")` → loads `datasets["antioquia"]`

### Pattern 2: Departmental Participation Files

Files containing multi-department data for analysis (e.g., `participacion_departamental.csv`).

**Columns:** Same as Pattern 1, but with multiple departments per file

**Usage:** `get_most_favorable_department("maíz")` searches these when crop-specific files aren't found

### Pattern 3: Municipal Datasets by Crop

Files named as `{department}-{crop}.csv` (e.g., `antioquia-maíz.csv`) with municipality-level data.

**Columns:** `Año`, `Municipio`, `Departamento`, `Area sembrada (ha)`, `Area cosechada (ha)`, `Produccion (ton)`, `Rendimiento (ha/ton)`

**Usage:** `get_production_by_location("maíz", "medellín", 2020, "municipality")` → searches `datasets["antioquia-maíz"]`

**Sources:** [app/chatbot/dataset_processor.py L60-L191](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L60-L191)

---

## Integration Points in Question Processing

The following table maps user intents to `DatasetProcessor` methods:

| Intent | QuestionProcessor Method Call | DatasetProcessor Method | Example Query |
| --- | --- | --- | --- |
| `recommendation` | Line 234 | `get_recommended_crops(department)` | "¿Qué sembrar en Antioquia?" |
| `location_based_recommendation` | Line 242 | `get_recommended_crops(department)` | "Recomienda cultivos para mi región" |
| `crop_profitability` | Line 256, 261 | `compare_crops_profitability()`, `get_most_favorable_department()` | "¿Maíz o café es más rentable?" |
| `crop_production` | Line 273, 281, 291 | `get_least_favorable_department()`, `get_most_favorable_department()`, `get_production_by_location()` | "¿Dónde se produce más café?" |
| `recommended_crops` | Line 319 | `get_recommended_crops(department)` | "¿Qué cultivos recomiendas?" |
| `production_query` | Line 334 | `get_production_by_location()` | "Producción de maíz en Manizales en 2020" |

**Sources:** [app/chatbot/question_processor.py L232-L338](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L232-L338)

---

## Complete Data Flow

```

```

**Sources:** [app/chatbot/dataset_processor.py L7-L250](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L7-L250)

 [app/chatbot/question_processor.py L132-L343](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L132-L343)

---

## Error Handling and Edge Cases

The `DatasetProcessor` implements defensive programming patterns:

| Scenario | Handling Strategy | Example |
| --- | --- | --- |
| Missing dataset | Return `None` or empty list | Department not in `datasets` dict |
| Invalid metric values | Filter `metric > 0` and `metric.notna()` | Negative or null yields excluded |
| Crop name variations | Apply `crop_mappings` and `unidecode()` | "cafe" → "café", "maiz" → "maíz" |
| No matching data | Return `None` or empty list | Crop not grown in specified department |
| CSV loading errors | Catch exception, print error, continue | Corrupted CSV file skipped |
| Multiple data sources | Search crop-specific first, then departmental | Hierarchical search strategy |

**Sources:** [app/chatbot/dataset_processor.py L44-L58](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L44-L58)

 [app/chatbot/dataset_processor.py L68-L90](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L68-L90)

---

## Performance Considerations

### Memory Usage

All datasets are loaded into memory during initialization. For the current dataset size (~30 CSV files, typical size 10-500 KB each), this results in approximately 5-10 MB of memory usage, which is acceptable for the application scale.

### Query Performance

| Operation | Complexity | Performance Notes |
| --- | --- | --- |
| Dataset lookup | O(1) | Dictionary key access |
| DataFrame filtering | O(n) | Pandas optimized operations |
| Grouping and aggregation | O(n log n) | Pandas groupby implementation |
| Name normalization | O(m) | Where m = string length |

Most queries complete in under 50ms for typical dataset sizes.

**Sources:** [app/chatbot/dataset_processor.py L31-L58](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L31-L58)