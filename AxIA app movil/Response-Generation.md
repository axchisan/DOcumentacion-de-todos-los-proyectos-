# Response Generation

> **Relevant source files**
> * [N8N_CONFIGURATION_FINAL.md](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_FINAL.md)
> * [N8N_CONFIGURATION_GUIDE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md)

## Purpose and Scope

This document describes how n8n generates and formats responses after processing user messages. It covers AI integration, response format determination (text vs audio), audio synthesis with ElevenLabs, Base64 encoding, and transmission to the AxIA app via the `/app-message` endpoint.

For information about receiving and routing incoming messages from the app, see [Webhook Configuration](/axchisan/AxIA/7.1-webhook-configuration) and [Message Routing](/axchisan/AxIA/7.2-message-routing). For backend WebSocket handling, see [WebSocket Communication](/axchisan/AxIA/5.1-websocket-communication).

---

## Response Generation Flow

The following diagram shows the complete response generation pipeline from AI processing to app delivery:

```

```

**Sources:** N8N_CONFIGURATION_FINAL.md:1-182, N8N_CONFIGURATION_GUIDE.md:1-353

---

## Data Extraction from Incoming Message

Before generating a response, n8n extracts required fields from the webhook payload:

| Field | Extraction Method | Purpose |
| --- | --- | --- |
| `username` | `remoteJid.split(':')[1].split('@')[0]` | Identifies app user |
| `session_id` | `data.key.id` | Links request to response |
| `messageType` | `data.messageType` | Determines input format |
| `input_text` | `data.message.conversation` or transcribed audio | AI processing input |

### Code Node: Extract Message Data

```

```

**Sources:** N8N_CONFIGURATION_FINAL.md:52-74, N8N_CONFIGURATION_GUIDE.md:19-63

---

## AI Processing Integration

After extracting the message data, n8n sends it to AI models for processing:

```

```

The AI processing node receives the extracted input and returns a text response in the `output` field. This text forms the basis for both text and audio responses.

**Sources:** N8N_CONFIGURATION_FINAL.md:42-62, N8N_CONFIGURATION_GUIDE.md:232-247

---

## Response Format Decision

The workflow determines whether to return text or audio based on the `debe_ser_audio` flag:

### Decision Logic

```

```

The `debe_ser_audio` flag is typically set to `true` when:

* The original input was an audio message
* User preferences indicate audio responses
* The message type requires voice output

**Sources:** N8N_CONFIGURATION_FINAL.md:62-73, N8N_CONFIGURATION_GUIDE.md:249-258

---

## Text Response Generation

Text responses support full Markdown formatting for rich display in the Flutter app.

### Markdown Formatting

| Element | Syntax | Usage |
| --- | --- | --- |
| Headers | `### Title` | Section titles |
| Bold | `**text**` | Emphasis |
| Italic | `_text_` | Details |
| Code | ``code`` | Technical references |
| Lists | `✅ Item` | Structured data with emojis |

### Set Node: Prepare Text Response

```

```

### Example Text Response

```

```

**Sources:** N8N_CONFIGURATION_FINAL.md:184-196, N8N_CONFIGURATION_GUIDE.md:152-160, N8N_CONFIGURATION_GUIDE.md:280-311

---

## Audio Response Generation

Audio responses require two-step processing: text-to-speech conversion and Base64 encoding.

### Step 1: ElevenLabs Text-to-Speech

```

```

#### HTTP Request Configuration

**Endpoint:** `POST https://api.elevenlabs.io/v1/text-to-speech/{voice_id}`

**Headers:**

```

```

**Body:**

```

```

**Response Format:** Binary file (set in n8n HTTP node)

**Sources:** N8N_CONFIGURATION_FINAL.md:76-107, N8N_CONFIGURATION_GUIDE.md:264-276

### Step 2: Base64 Encoding

The binary audio file is converted to Base64 for JSON transmission:

#### Code Node: Convert Audio to Base64

```

```

This encoding allows audio data to be transmitted as a JSON string through the WebSocket connection.

**Sources:** N8N_CONFIGURATION_FINAL.md:108-120

### Step 3: Prepare Audio Response

```

```

**Sources:** N8N_CONFIGURATION_FINAL.md:62-74

---

## App-Message Endpoint Integration

The final step sends the formatted response to the FastAPI backend via the `/app-message` endpoint.

### Endpoint Specification

```

```

**URL:** `https://apiaxia.axchisan.com/app-message`

**Method:** `POST`

**Authentication:** None (called by n8n, not user)

**Content-Type:** `application/json`

### HTTP Request Node Configuration

```

```

**Sources:** N8N_CONFIGURATION_FINAL.md:8-19, N8N_CONFIGURATION_FINAL.md:122-149

---

## Response Data Structure

The complete response payload sent to `/app-message`:

### Text Response Schema

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `username` | `string` | Yes | App user identifier (from `remoteJid`) |
| `session_id` | `string` | Yes | Original message ID for correlation |
| `output` | `string` | Yes | AI-generated response text (Markdown) |
| `type` | `string` | Yes | Always `"text"` for text responses |
| `debe_ser_audio` | `boolean` | Yes | Always `false` for text responses |
| `audio_url` | `string \| null` | Yes | Always `null` for text responses |
| `audio_base64` | `string \| null` | Yes | Always `null` for text responses |

### Audio Response Schema

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `username` | `string` | Yes | App user identifier (from `remoteJid`) |
| `session_id` | `string` | Yes | Original message ID for correlation |
| `output` | `string` | Yes | Text version of audio content |
| `type` | `string` | Yes | Always `"audio"` for audio responses |
| `debe_ser_audio` | `boolean` | Yes | Always `true` for audio responses |
| `audio_url` | `string \| null` | No | Optional CDN URL for audio |
| `audio_base64` | `string` | Yes | Base64-encoded audio data (primary) |

**Sources:** N8N_CONFIGURATION_FINAL.md:138-149, N8N_CONFIGURATION_FINAL.md:184-210

---

## Complete Response Generation Flow

The following diagram maps the entire pipeline from AI output to app delivery, with specific n8n node types:

```

```

**Sources:** N8N_CONFIGURATION_FINAL.md:151-182, N8N_CONFIGURATION_GUIDE.md:203-223

---

## Response Delivery Behavior

### Username Matching

The backend matches the `username` field to active WebSocket connections:

```

```

If no connections are active, the endpoint returns:

```

```

**Sources:** N8N_CONFIGURATION_FINAL.md:232-237

---

## Testing Response Generation

### Manual Testing with curl

```

```

### Expected Response (Success)

```

```

### Expected Response (No Connections)

```

```

**Sources:** N8N_CONFIGURATION_FINAL.md:213-230

---

## Error Handling

### Common Issues

| Issue | Cause | Solution |
| --- | --- | --- |
| No response received | WebSocket disconnected | Check connection status in Flutter |
| Audio not playing | Invalid Base64 or format | Verify ElevenLabs output format |
| Wrong user receives message | Username mismatch | Verify `remoteJid` extraction logic |
| Response timeout | n8n workflow stuck | Check n8n execution logs |

### Response Validation

Before sending to `/app-message`, validate:

* `username` is extracted correctly from `remoteJid`
* `session_id` matches original message ID
* `type` is either `"text"` or `"audio"`
* `audio_base64` is present when `debe_ser_audio: true`
* JSON structure matches schema

**Sources:** N8N_CONFIGURATION_FINAL.md:232-237, N8N_CONFIGURATION_GUIDE.md:327-344