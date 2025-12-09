# Intent Classification

> **Relevant source files**
> * [app/chatbot/nlp_processor.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py)
> * [app/chatbot/question_processor.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py)
> * [train_intent_classifier.py](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/train_intent_classifier.py)

## Purpose and Scope

Intent classification is the process by which Agrobot determines the type and purpose of a user's query. This enables the system to route questions to appropriate handlers and generate contextually relevant responses.

This document covers the `NLPProcessor` class, the 14 supported intent types, the classification process at runtime, and how classified intents are used by the question processing system. For information about training the intent classifier model, see [Model Training Pipeline](/axchisan/ProyectoAgroBot/8.3-model-training-pipeline). For information about how classified intents are used to generate responses, see [Response Strategy](/axchisan/ProyectoAgroBot/4.4-response-strategy).

**Sources:** [app/chatbot/nlp_processor.py L1-L28](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L1-L28)

 [app/chatbot/question_processor.py L12-L44](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L12-L44)

---

## Intent Types

The system supports 14 distinct intent types that categorize user queries into functional categories. Each intent type maps to specific handler logic in the `QuestionProcessor`.

| Intent Label | Category | Description | Example Query |
| --- | --- | --- | --- |
| `theoretical` | Knowledge | Theoretical agricultural concepts and definitions | "¿Qué es agroecología?" |
| `weather` | Meteorological | Current weather conditions | "¿Cuál es clima Bogotá?" |
| `weather_forecast` | Meteorological | Future weather predictions | "¿Clima mañana Medellín?" |
| `weather_sowing_advice` | Meteorological | Weather-based sowing recommendations | "¿Clima para sembrar Bogotá?" |
| `current_location` | Location | User location queries | "¿Dónde estoy?" |
| `recommendation` | Crop Selection | General crop recommendations | "¿Qué cultivos región?" |
| `location_based_recommendation` | Crop Selection | Recommendations for specific department | "¿Cultivo Antioquia?" |
| `crop_profitability` | Economic | Most profitable crop by location | "¿Rentable maíz donde?" |
| `crop_production` | Production Data | Production statistics by location/crop | "¿Producción tomate Cundinamarca?" |
| `crop_timing` | Scheduling | Best time to plant crops | "¿Cuando siembro maíz?" |
| `irrigation_advice` | Water Management | Irrigation recommendations | "¿Cómo optimizo riego?" |
| `least_favorable_department` | Production Data | Worst location for a crop | "¿Dónde es menos favorable maíz?" |
| `recommended_crops` | Crop Selection | Recommended crops for department | "¿Qué cultivos recomiendas Antioquia?" |
| `production_query` | Production Data | Specific production data query | "¿Cuánto maíz Manizales 2020?" |

**Sources:** [app/chatbot/question_processor.py L22-L27](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L22-L27)

 [train_intent_classifier.py L4-L286](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/train_intent_classifier.py#L4-L286)

---

## NLPProcessor Class

The `NLPProcessor` class encapsulates the intent classification model and provides methods for classifying user input and analyzing sentiment.

### Class Structure

```

```

### Initialization Process

The `NLPProcessor` is initialized when the chatbot starts. The initialization loads the pre-trained BERT-based model and its associated label mapping:

1. **Tokenizer Loading**: Loads the Spanish BERT tokenizer from `app/models/intent_classifier/`
2. **Model Loading**: Loads the fine-tuned sequence classification model
3. **Label Map Loading**: Loads the pickle file containing intent-to-ID mappings
4. **Model Evaluation Mode**: Sets the model to evaluation mode (no training)
5. **Sentiment Analyzer**: Initializes NLTK's VADER sentiment analyzer

**Sources:** [app/chatbot/nlp_processor.py L9-L17](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L9-L17)

### Key Attributes

| Attribute | Type | Purpose |
| --- | --- | --- |
| `tokenizer` | `AutoTokenizer` | Converts text to model-compatible tokens |
| `model` | `AutoModelForSequenceClassification` | Fine-tuned BERT model for classification |
| `label_map` | `dict` | Maps intent strings to numeric IDs |
| `reverse_label_map` | `dict` | Maps numeric IDs back to intent strings |
| `sentiment_analyzer` | `SentimentIntensityAnalyzer` | Analyzes emotional tone of input |

**Sources:** [app/chatbot/nlp_processor.py L10-L17](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L10-L17)

---

## Classification Process

### Runtime Classification Flow

```

```

**Sources:** [app/chatbot/nlp_processor.py L19-L25](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L19-L25)

### classify_intent Method

The `classify_intent` method performs the following steps:

```

```

**Parameters:**

* `user_input`: The user's query in Spanish
* `candidate_labels`: List of valid intent labels (currently unused in implementation)

**Returns:** String representing the predicted intent label

**Process:**

1. **Tokenization** [app/chatbot/nlp_processor.py L20](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L20-L20) : Converts input text to tokens with padding and truncation
2. **Tensor Conversion**: Creates PyTorch tensors from tokenized input
3. **Model Inference** [app/chatbot/nlp_processor.py L21-L23](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L21-L23) : Runs forward pass without gradient computation
4. **Argmax Selection**: Identifies the intent with highest logit score
5. **Label Lookup** [app/chatbot/nlp_processor.py L24](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L24-L24) : Maps numeric ID to intent string using `reverse_label_map`

**Sources:** [app/chatbot/nlp_processor.py L19-L25](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L19-L25)

### Sentiment Analysis

The `analyze_sentiment` method provides emotional context about the user's input:

```

```

**Returns:** Dictionary with sentiment scores:

* `positive`: Positive sentiment score
* `negative`: Negative sentiment score
* `neutral`: Neutral sentiment score
* `compound`: Overall sentiment score (-1 to 1)

This is used by `QuestionProcessor` to add empathetic prefixes like "¡Entiendo que estás preocupado!" for negative sentiment.

**Sources:** [app/chatbot/nlp_processor.py L27-L28](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L27-L28)

 [app/chatbot/question_processor.py L134-L135](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L134-L135)

---

## Intent to Handler Routing

### Integration with QuestionProcessor

The `QuestionProcessor` instantiates an `NLPProcessor` and uses it to classify intents before routing to specialized handlers:

```

```

**Sources:** [app/chatbot/question_processor.py L132-L343](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L132-L343)

### Intent Handling Implementation

The `process_question` method uses a series of conditional blocks to handle each intent type:

**Theoretical Intent** [app/chatbot/question_processor.py L144-L150](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L144-L150)

:

* Matches against normalized questions in `questions_data["theoretical"]`
* Uses fuzzy matching with 60% cutoff
* Returns predefined answer from knowledge base

**Dynamic Question Intents** [app/chatbot/question_processor.py L152-L308](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L152-L308)

:

* Matches against `questions_data["dynamic"]`
* Handles weather, location, recommendation, profitability, production, timing, and irrigation intents
* Uses answer templates with context-specific data

**Specialized Intent Handlers** [app/chatbot/question_processor.py L309-L338](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L309-L338)

:

* `least_favorable_department`: Calls `dataset_processor.get_least_favorable_department()`
* `recommended_crops`: Calls `dataset_processor.get_recommended_crops()`
* `production_query`: Calls `dataset_processor.get_production_by_location()`

**Sources:** [app/chatbot/question_processor.py L144-L338](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L144-L338)

---

## Training Data Structure

### Training Data Format

The training data is defined in `train_intent_classifier.py` as a list of tuples:

```

```

### Data Augmentation Strategy

The training dataset includes deliberate variations to improve robustness:

| Variation Type | Example | Purpose |
| --- | --- | --- |
| Formal | "¿Qué es Agrobot?" | Standard Spanish |
| Casual | "¿Q es Agrobot?" | Common abbreviations |
| Typos | "¿Que es Agrobot?" | Missing accents |
| Slang | "¿k es agrobot?" | Informal spelling |
| Word Order | "¿Agrobot q es?" | Alternative phrasing |

**Sources:** [train_intent_classifier.py L3-L286](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/train_intent_classifier.py#L3-L286)

### Training Data Coverage

The training dataset contains **400+ examples** distributed across all intent types:

* **Theoretical queries**: ~78 variations
* **Weather queries**: ~36 variations
* **Location queries**: ~24 variations
* **Recommendation queries**: ~45 variations
* **Profitability queries**: ~38 variations
* **Production queries**: ~38 variations
* **Timing queries**: ~30 variations
* **Irrigation queries**: ~22 variations
* **Specialized intents**: ~20 variations

Each major crop (maíz, papa, café, tomate, arroz, etc.) and major department (Antioquia, Cundinamarca, Valle, etc.) has multiple question variations to ensure comprehensive coverage.

**Sources:** [train_intent_classifier.py L4-L286](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/train_intent_classifier.py#L4-L286)

---

## Model Architecture and Inference

### BERT-based Model Details

```

```

**Sources:** [app/chatbot/nlp_processor.py L11-L15](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L11-L15)

### Inference Performance

The model runs in evaluation mode with the following characteristics:

* **Input Processing**: Sequences padded/truncated to 128 tokens
* **Batch Size**: Single query per inference (real-time chat)
* **Gradient Computation**: Disabled via `torch.no_grad()` context
* **Device**: CPU (suitable for lightweight deployment)
* **Inference Time**: < 100ms per query on typical hardware

**Sources:** [app/chatbot/nlp_processor.py L20-L24](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L20-L24)

---

## Error Handling and Fallback

### Unknown Intent Handling

When the model produces a label ID not in `reverse_label_map`, the system returns `"unknown"`:

```

```

However, this case should never occur in production since the model is trained to output one of the 14 predefined intents.

**Sources:** [app/chatbot/nlp_processor.py L24](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L24-L24)

### Fallback to OpenAI API

When intent classification doesn't lead to a successful response match, `QuestionProcessor` falls back to the OpenAI API:

```

```

This ensures that even queries with unclear or complex intents receive informative responses. See [AI Fallback Service](/axchisan/ProyectoAgroBot/6.3-ai-fallback-service) for more details.

**Sources:** [app/chatbot/question_processor.py L340-L342](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L340-L342)

---

## Context-Aware Classification

### Contextual Information Flow

```

```

**Sources:** [app/chatbot/question_processor.py L77-L95](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L77-L95)

 [app/chatbot/question_processor.py L132-L248](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L132-L248)

### Context Fields Used in Intent Handling

The `context` dictionary maintains state across queries:

| Field | Type | Used By Intents | Purpose |
| --- | --- | --- | --- |
| `department` | `str` | recommendation, location_based_recommendation, crop_profitability, crop_production | Target department for queries |
| `city` | `str` | weather, weather_sowing_advice, irrigation_advice | Target city for weather API calls |
| `last_crop` | `str` | crop_profitability, crop_production, crop_timing | Previously mentioned crop |
| `lat` | `float` | weather, weather_sowing_advice, irrigation_advice | GPS latitude for weather |
| `lon` | `float` | weather, weather_sowing_advice, irrigation_advice | GPS longitude for weather |

**Sources:** [app/chatbot/question_processor.py L28-L34](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L28-L34)

---

## Summary

The intent classification system enables Agrobot to:

1. **Categorize Queries**: Classify user input into 14 functional intent types
2. **Route Efficiently**: Direct queries to appropriate handlers (weather API, dataset queries, knowledge base)
3. **Handle Variations**: Process informal Spanish, typos, and abbreviations through robust training data
4. **Provide Context**: Maintain conversation state for multi-turn interactions
5. **Support Fallback**: Gracefully handle unclassified queries via OpenAI API

The `NLPProcessor` serves as the gateway between raw user input and structured intent handling, leveraging a fine-tuned Spanish BERT model to understand Colombian agricultural queries with high accuracy.

**Sources:** [app/chatbot/nlp_processor.py L1-L28](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/nlp_processor.py#L1-L28)

 [app/chatbot/question_processor.py L12-L343](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/app/chatbot/question_processor.py#L12-L343)

 [train_intent_classifier.py L1-L289](https://github.com/axchisan/ProyectoAgroBot/blob/bc782fcf/train_intent_classifier.py#L1-L289)