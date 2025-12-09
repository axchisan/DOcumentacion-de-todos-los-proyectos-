# Agricultural Data Structure

> **Relevant source files**
> * [app/chatbot/dataset_processor.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py)
> * [data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Acelga.csv](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Acelga.csv)
> * [data/processed/Comparativo_de_Area_Produccion_Rendimiento_y_Participacion_Departamental_por_Cultivo/achicoria.csv](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/data/processed/Comparativo_de_Area_Produccion_Rendimiento_y_Participacion_Departamental_por_Cultivo/achicoria.csv)

This document describes the structure, organization, and schemas of the agricultural CSV datasets used by the Agrobot system. These datasets contain production, yield, and area data for various crops across Colombian departments and municipalities.

For information about how this data is loaded into memory, see [Data Loading](/axchisan/ProyectoAgroBot/5.2-data-loading). For information about how this data is queried and processed, see [Dataset Processing](/axchisan/ProyectoAgroBot/5.3-dataset-processing). For information about converting source Excel files to these CSV structures, see [Data Conversion Utilities](/axchisan/ProyectoAgroBot/5.5-data-conversion-utilities).

---

## Overview

The Agrobot system relies on processed agricultural datasets stored as CSV files in the `data/processed/` directory. These datasets contain historical agricultural production data for Colombia, organized by crop, department, municipality, and year. The data supports queries about crop recommendations, production statistics, yield comparisons, and regional agricultural analysis.

**Sources:** [app/chatbot/dataset_processor.py L1-L58](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L1-L58)

---

## Directory Structure

The processed agricultural data is organized in a two-level directory hierarchy:

```

```

**Dataset Types:**

| Type | Directory Pattern | Key Pattern | Scope | Use Case |
| --- | --- | --- | --- | --- |
| Type 1 | `Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/` | `{crop}.csv` | National, by crop | National statistics for specific crops |
| Type 2 | `Comparativo_de_Area_Produccion_Rendimiento_y_Participacion_Departamental_por_Cultivo/` | `{crop}.csv` | Departmental, by crop | Cross-departmental comparisons |
| Type 3 | Various | `{department}-{crop}.csv` | Municipal, by department and crop | Municipality-level granular data |

**Sources:** [app/chatbot/dataset_processor.py L31-L58](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L31-L58)

 [data/processed/Comparativo_de_Area_Produccion_Rendimiento_y_Participacion_Departamental_por_Cultivo/achicoria.csv L1-L5](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/data/processed/Comparativo_de_Area_Produccion_Rendimiento_y_Participacion_Departamental_por_Cultivo/achicoria.csv#L1-L5)

 [data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Acelga.csv L1-L127](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Acelga.csv#L1-L127)

---

## CSV Schema

All agricultural CSV files follow a standardized schema with the following columns:

### Standard Column Schema

```

```

### Column Definitions

| Column Name | Data Type | Unit | Description | Required |
| --- | --- | --- | --- | --- |
| `Año` | Integer | Year | Calendar year of the data record | Yes |
| `Departamento` | String | - | Name of Colombian department (normalized) | Yes |
| `Municipio` | String | - | Name of municipality (municipal datasets only) | Conditional |
| `Producto` | String | - | Name of crop or agricultural product (normalized) | Yes |
| `Area (ha)` | Float | Hectares | Total cultivated area | Yes |
| `Produccion (ton)` | Float | Metric Tons | Total production output | Yes |
| `Rendimiento (ha/ton)` | Float | Hectares/Ton | Yield efficiency ratio | Yes |
| `Produccion Nacional (ton)` | Float | Metric Tons | Total national production for comparison | Yes |
| `Area Nacional (ha)` | Float | Hectares | Total national cultivated area for comparison | Yes |

**Example Record (from Acelga.csv):**

```
Año,Departamento,Producto,Area (ha),Produccion (ton),Rendimiento (ha/ton),Produccion Nacional (ton),Area Nacional (ha)
2007,CUNDINAMARCA,ACELGA,153.5,2760.0,17.98,97.49,95.64
```

**Sources:** [data/processed/Comparativo_de_Area_Produccion_Rendimiento_y_Participacion_Departamental_por_Cultivo/achicoria.csv L1-L2](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/data/processed/Comparativo_de_Area_Produccion_Rendimiento_y_Participacion_Departamental_por_Cultivo/achicoria.csv#L1-L2)

 [data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Acelga.csv L1-L11](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/data/processed/Area_Produccion_y_Rendimiento_Nacional_por_Cultivo/Acelga.csv#L1-L11)

---

## Data Normalization

The `DatasetProcessor` applies normalization to ensure consistent querying across datasets. This normalization happens during CSV loading and affects product names, department names, and municipality names.

```

```

### Normalization Steps

1. **Remove Accents:** Uses `unidecode()` to convert accented characters (á, é, í, ó, ú, ñ) to ASCII equivalents
2. **Convert to Lowercase:** All text converted to lowercase for case-insensitive matching
3. **Strip Whitespace:** Leading and trailing whitespace removed
4. **Apply Crop Mappings:** Common variations mapped to standard names

### Crop Mappings

The system maintains a mapping dictionary for crop name variations:

| Input Variation | Normalized Output |
| --- | --- |
| `maiz` | `maíz` |
| `cafe` | `café` |
| `platano` | `plátano` |
| `guayaba manzana` | `guayaba` |
| `guayaba pera` | `guayaba` |
| `cana azucarera` | `caña de azúcar` |
| `cana panelera` | `caña de azúcar` |

**Sources:** [app/chatbot/dataset_processor.py L14-L29](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L14-L29)

 [app/chatbot/dataset_processor.py L46-L54](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L46-L54)

---

## Dataset Loading and Organization

The `DatasetProcessor` loads all CSV files from the `data/processed` directory and organizes them by file name keys.

### Loading Process

```

```

### Dataset Key Structure

The system uses lowercase, normalized file names as dictionary keys to access datasets:

| File Path | Dataset Key | Type |
| --- | --- | --- |
| `.../Acelga.csv` | `acelga` | Type 1 (National) |
| `.../achicoria.csv` | `achicoria` | Type 2 (Departmental) |
| `.../antioquia-cafe.csv` | `antioquia-cafe` | Type 3 (Municipal) |
| `.../cundinamarca-maiz.csv` | `cundinamarca-maiz` | Type 3 (Municipal) |

**Sources:** [app/chatbot/dataset_processor.py L31-L58](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L31-L58)

---

## Dataset Type Identification

The `DatasetProcessor` uses key patterns to identify dataset types when processing queries:

```

```

### Type Detection Logic

**Type 1 (National by Crop):**

* Key pattern: `{crop}` (e.g., `"maiz"`, `"cafe"`)
* Identified when: `key == crop` or `key.endswith(f"-{crop}")`
* Usage: National-level statistics for a specific crop

**Type 2 (Departmental Comparison):**

* Key pattern: `{crop}` or `{department}`
* Identified when: DataFrame contains `"Producto"` column
* Usage: Comparing multiple crops within a department or departments for a crop

**Type 3 (Municipal Granular):**

* Key pattern: `{department}-{crop}` (e.g., `"antioquia-cafe"`)
* Identified when: Key ends with `-{crop}` pattern
* Usage: Municipality-level data for specific department-crop combinations

**Sources:** [app/chatbot/dataset_processor.py L60-L124](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L60-L124)

 [app/chatbot/dataset_processor.py L126-L149](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L126-L149)

 [app/chatbot/dataset_processor.py L151-L191](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L151-L191)

---

## Data Access Patterns

The `DatasetProcessor` provides specialized methods for querying agricultural data based on dataset types:

### Query Method Mapping

```

```

### Query Examples by Dataset Type

**Type 1 - National Crop Statistics:**

```

```

**Type 2 - Departmental Recommendations:**

```

```

**Type 3 - Municipal Production:**

```

```

**Sources:** [app/chatbot/dataset_processor.py L60-L91](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L60-L91)

 [app/chatbot/dataset_processor.py L93-L124](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L93-L124)

 [app/chatbot/dataset_processor.py L126-L149](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L126-L149)

 [app/chatbot/dataset_processor.py L151-L191](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L151-L191)

---

## Data Quality and Filtering

The `DatasetProcessor` applies consistent data quality filters when querying datasets to ensure valid results:

### Filtering Criteria

| Filter Type | Condition | Purpose |
| --- | --- | --- |
| **Positive Values** | `metric > 0` | Exclude zero/negative values that indicate no data |
| **Non-Null** | `metric.notna()` | Exclude missing/NaN values |
| **Valid Years** | Implicit through data | Ensure temporal relevance |

### Example Filtering Logic

```

```

**Example from Code:**

```

```

**Sources:** [app/chatbot/dataset_processor.py L68-L69](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L68-L69)

 [app/chatbot/dataset_processor.py L100-L102](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L100-L102)

 [app/chatbot/dataset_processor.py L133-L135](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L133-L135)

 [app/chatbot/dataset_processor.py L200-L201](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L200-L201)

---

## Metrics and Calculations

The agricultural datasets support multiple metrics for agricultural analysis:

### Primary Metrics

| Metric Name | Column | Unit | Calculation | Use Case |
| --- | --- | --- | --- | --- |
| **Area** | `Area (ha)` | Hectares | Direct value | Land usage analysis |
| **Production** | `Produccion (ton)` | Metric Tons | Direct value | Output quantity |
| **Yield** | `Rendimiento (ha/ton)` | ha/ton | `Area / Production` | Efficiency comparison |
| **National Production** | `Produccion Nacional (ton)` | Metric Tons | Sum across regions | Benchmark |
| **National Area** | `Area Nacional (ha)` | Hectares | Sum across regions | Benchmark |

### Aggregation Methods

```

```

**Common Aggregations:**

* **Average Yield:** `groupby("Producto")["Rendimiento (ha/ton)"].mean()`
* **Maximum Production:** `.loc[df[metric].idxmax()]`
* **Minimum Production:** `.loc[df[metric].idxmin()]`
* **Top N Crops:** `.sort_values(by=metric, ascending=False).head(n)`

**Sources:** [app/chatbot/dataset_processor.py L133-L148](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L133-L148)

 [app/chatbot/dataset_processor.py L199-L206](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L199-L206)

 [app/chatbot/dataset_processor.py L227-L228](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L227-L228)

---

## Data Consistency and Standards

### Naming Conventions

All CSV files follow these naming standards:

**Department Names:**

* Capitalized in source files (e.g., `CUNDINAMARCA`, `ANTIOQUIA`)
* Normalized to lowercase during processing (e.g., `cundinamarca`, `antioquia`)
* Returned capitalized in query results using `.capitalize()`

**Crop/Product Names:**

* Mixed case in source files (e.g., `ACELGA`, `Maíz`, `café`)
* Normalized to lowercase during processing
* Returned capitalized in query results

**Municipality Names:**

* Similar normalization pattern as departments and crops

### Time Series Data

All datasets include temporal data with the following characteristics:

| Aspect | Standard |
| --- | --- |
| **Column Name** | `Año` (Year) |
| **Data Type** | Integer |
| **Range** | Typically 2006-2023 |
| **Granularity** | Annual (one record per year) |
| **Completeness** | May have gaps for specific department-crop combinations |

### Missing Data Handling

```

```

The system handles missing data by:

1. Checking for empty DataFrames after filtering
2. Validating metric values are positive and non-null
3. Returning `None` or empty lists when no valid data exists
4. Logging errors during CSV loading but continuing with available data

**Sources:** [app/chatbot/dataset_processor.py L46-L58](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L46-L58)

 [app/chatbot/dataset_processor.py L68-L75](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L68-L75)

 [app/chatbot/dataset_processor.py L100-L108](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L100-L108)

 [app/chatbot/dataset_processor.py L159-L172](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/dataset_processor.py#L159-L172)