# Message Routing

> **Relevant source files**
> * [N8N_CONFIGURATION_GUIDE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md)
> * [N8N_INTEGRATION_GUIDE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md)

**Purpose**: This document describes the n8n Switch node logic used to route incoming messages based on their source channel (app, WhatsApp, Telegram). It covers channel detection rules, `remoteJid` format parsing, and user verification expressions for each communication channel.

For information about webhook configuration and incoming message formats, see [Webhook Configuration](/axchisan/AxIA/7.1-webhook-configuration). For AI processing and response formatting after routing, see [Response Generation](/axchisan/AxIA/7.3-response-generation).

---

## Channel Detection Strategy

The n8n workflow uses a **Switch node** immediately after the webhook to detect the message source channel. This node examines the `channel` field and routes messages to channel-specific processing branches.

### Switch Node Configuration

| Setting | Value |
| --- | --- |
| Node Type | Switch |
| Mode | Rules |
| Name | "Detectar Canal de Origen" |
| Evaluation | Execute in order, stop at first match |

**Sources**: [N8N_CONFIGURATION_GUIDE.md L69-L93](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L69-L93)

### Detection Rules

The Switch node implements three routing rules executed in order:

```
// Rule 1 - WhatsApp Detection
{{ $json.channel === undefined || $json.channel === 'whatsapp' }}

// Rule 2 - Telegram Detection  
{{ $json.channel === 'telegram' }}

// Rule 3 - App Detection
{{ $json.channel === 'app' }}
```

**Sources**: [N8N_CONFIGURATION_GUIDE.md L77-L93](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L77-L93)

### Channel Detection Flow

```mermaid
flowchart TD

WH["Webhook Node<br>(Entry Point)"]
SW["Switch Node<br>'Detectar Canal de Origen'"]
WA_ROUTE["WhatsApp Branch<br>(channel === undefined or 'whatsapp')"]
TG_ROUTE["Telegram Branch<br>(channel === 'telegram')"]
APP_ROUTE["App Branch<br>(channel === 'app')"]
WA_LOGIC["WhatsApp Processing"]
TG_LOGIC["Telegram Processing"]
APP_LOGIC["App Processing"]

WH --> SW
SW --> WA_ROUTE
SW --> TG_ROUTE
SW --> APP_ROUTE
WA_ROUTE --> WA_LOGIC
TG_ROUTE --> TG_LOGIC
APP_ROUTE --> APP_LOGIC
```

**Sources**: [N8N_CONFIGURATION_GUIDE.md L204-L223](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L204-L223)

---

## RemoteJid Format Analysis

Each channel uses a different `remoteJid` format to identify users. Understanding these formats is critical for proper user verification.

### Format Comparison

| Channel | RemoteJid Format | Example | Extraction Pattern |
| --- | --- | --- | --- |
| **WhatsApp** | `{phone}:{suffix}@s.whatsapp.net` | `573183038190:24@s.whatsapp.net` | Split on `@`, then `:` |
| **App** | `app:{username}@{domain}` | `app:AxchiSan@axia.app` | Split on `:`, extract username |
| **Telegram** | `telegram:{user_id}@{domain}` | `telegram:123456@tg.com` | Split on `:`, extract user_id |

**Sources**: [N8N_INTEGRATION_GUIDE.md L63-L71](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L63-L71)

 [N8N_CONFIGURATION_GUIDE.md L27](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L27-L27)

 [N8N_CONFIGURATION_GUIDE.md L50](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L50-L50)

### RemoteJid Structure Diagram

```mermaid
flowchart TD

APP_RJ["app:Unsupported markdown: link"]
APP_PREFIX["Prefix: app"]
APP_USER["Username: AxchiSan"]
APP_DOMAIN["Domain: axia.app"]
WA_RJ["573183038190:Unsupported markdown: link"]
WA_PHONE["Phone: 573183038190"]
WA_SUFFIX["Suffix: 24"]
WA_DOMAIN["Domain: s.whatsapp.net"]

subgraph subGraph1 ["App Format"]
    APP_RJ
    APP_PREFIX
    APP_USER
    APP_DOMAIN
    APP_RJ --> APP_PREFIX
    APP_RJ --> APP_USER
    APP_RJ --> APP_DOMAIN
end

subgraph subGraph0 ["WhatsApp Format"]
    WA_RJ
    WA_PHONE
    WA_SUFFIX
    WA_DOMAIN
    WA_RJ --> WA_PHONE
    WA_RJ --> WA_SUFFIX
    WA_RJ --> WA_DOMAIN
end
```

**Sources**: [N8N_INTEGRATION_GUIDE.md L63-L71](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L63-L71)

 [N8N_CONFIGURATION_GUIDE.md L18-L63](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L18-L63)

---

## User Verification Expressions

Each channel requires different n8n expressions to extract and verify the user identifier.

### WhatsApp User Extraction

The WhatsApp verification extracts the phone number from the `remoteJid`:

```
// Expression
{{ $if($('Webhook').isExecuted, 
   $('Webhook').item.json.body.data.key.remoteJid.split("@")[0].split(":")[0], 
   '') 
}}

// Expected result: '573183038190'
// Validation: Equals to '573183038190'
```

**Processing steps**:

1. Split on `@` → `['573183038190:24', 's.whatsapp.net']`
2. Take first element → `'573183038190:24'`
3. Split on `:` → `['573183038190', '24']`
4. Take first element → `'573183038190'`

**Sources**: [N8N_CONFIGURATION_GUIDE.md L97-L102](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L97-L102)

### App User Extraction

The app verification extracts the username after the `app:` prefix:

```
// Expression - Method 1: Full extraction
{{ $if($('Webhook').isExecuted, 
   $('Webhook').item.json.data.key.remoteJid.split(":")[1].split("@")[0], 
   '') 
}}

// Expected result: 'AxchiSan'
// Validation: Equals to 'AxchiSan@axia.app' (or just 'AxchiSan')
```

**Processing steps**:

1. Split on `:` → `['app', 'AxchiSan@axia.app']`
2. Take second element → `'AxchiSan@axia.app'`
3. Split on `@` → `['AxchiSan', 'axia.app']`
4. Take first element → `'AxchiSan'`

**Sources**: [N8N_CONFIGURATION_GUIDE.md L104-L108](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L104-L108)

### Prefix-Based Detection

A simpler approach for app messages uses prefix detection:

```
// Expression - Method 2: Prefix check
{{ $json.data.key.remoteJid.startsWith('app:') }}

// Returns: true for app messages, false otherwise
```

**Sources**: [N8N_CONFIGURATION_GUIDE.md L110-L113](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L110-L113)

 [N8N_INTEGRATION_GUIDE.md L83-L84](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L83-L84)

### Combined Verification Logic

For workflows handling multiple channels, use a unified verification expression:

```javascript
// Multi-channel user verification
{{
  $if($('Webhook').isExecuted, 
    (() => {
      const remoteJid = $('Webhook').item.json.body.data.key.remoteJid;
      const channel = $('Webhook').item.json.body.channel;
      
      // App channel
      if (channel === 'app' || remoteJid.startsWith('app:')) {
        return remoteJid.split(':')[1].split('@')[0];
      }
      
      // WhatsApp channel
      return remoteJid.split('@')[0].split(':')[0];
    })(), 
    ''
  )
}}
```

**Sources**: [N8N_INTEGRATION_GUIDE.md L99-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L99-L117)

### Verification Flow Diagram

```mermaid
flowchart TD

MSG["Incoming Message<br>data.key.remoteJid"]
CHECK_CHANNEL["Check<br>channel field"]
APP_CHECK["Channel === 'app'<br>OR<br>remoteJid.startsWith('app:')"]
WA_CHECK["Channel === undefined<br>OR<br>channel === 'whatsapp'"]
APP_EXTRACT["Split on ':'<br>Take [1]<br>Split on '@'<br>Take [0]<br>Result: 'AxchiSan'"]
WA_EXTRACT["Split on '@'<br>Take [0]<br>Split on ':'<br>Take [0]<br>Result: '573183038190'"]
APP_VERIFY["Verify against<br>authorized app users"]
WA_VERIFY["Verify against<br>authorized phone numbers"]
ALLOW["Allow processing"]
DENY["Reject message"]

MSG --> CHECK_CHANNEL
CHECK_CHANNEL --> APP_CHECK
CHECK_CHANNEL --> WA_CHECK
APP_CHECK --> APP_EXTRACT
WA_CHECK --> WA_EXTRACT
APP_EXTRACT --> APP_VERIFY
WA_EXTRACT --> WA_VERIFY
APP_VERIFY --> ALLOW
APP_VERIFY --> DENY
WA_VERIFY --> ALLOW
WA_VERIFY --> DENY
```

**Sources**: [N8N_INTEGRATION_GUIDE.md L99-L138](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L99-L138)

 [N8N_CONFIGURATION_GUIDE.md L97-L113](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L97-L113)

---

## Channel-Specific Processing Branches

After routing, each channel has dedicated processing logic before converging at the AI processing stage.

### WhatsApp Branch Logic

```mermaid
flowchart TD

WA_IN["WhatsApp Route"]
WA_VERIFY["User Verification<br>(phone number)"]
WA_FORMAT["Format Message<br>(WhatsApp structure)"]
WA_CONV["AI Processing"]

WA_IN --> WA_VERIFY
WA_VERIFY --> WA_FORMAT
WA_FORMAT --> WA_CONV
```

### App Branch Logic

```mermaid
flowchart TD

APP_IN["App Route"]
APP_VERIFY["User Verification<br>(username)"]
APP_SRC["Check source field<br>'flutter_app'"]
APP_FORMAT["Format Message<br>(app structure)"]
APP_CONV["AI Processing"]

APP_IN --> APP_VERIFY
APP_VERIFY --> APP_SRC
APP_SRC --> APP_FORMAT
APP_FORMAT --> APP_CONV
```

**Sources**: [N8N_CONFIGURATION_GUIDE.md L204-L223](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L204-L223)

 [N8N_INTEGRATION_GUIDE.md L162-L174](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L162-L174)

---

## Complete Routing Architecture

The complete message routing system connects incoming webhooks through channel detection, user verification, and processing branches.

### End-to-End Routing Flow

```mermaid
flowchart TD

WH["Webhook Node<br>/webhook/axia"]
SWITCH1["Switch Node 1<br>'Detectar Canal'<br>Channel Detection"]
WA_BRANCH["WhatsApp Branch"]
WA_VERIFY["IF Node<br>remoteJid.split('@')[0].split(':')[0]<br>== '573183038190'"]
WA_PROCESS["WhatsApp Logic<br>Format & Validate"]
TG_BRANCH["Telegram Branch"]
TG_VERIFY["IF Node<br>Telegram User Verification"]
TG_PROCESS["Telegram Logic<br>Format & Validate"]
APP_BRANCH["App Branch"]
APP_VERIFY["IF Node<br>remoteJid.split(':')[1].split('@')[0]<br>== 'AxchiSan'"]
APP_PROCESS["App Logic<br>Check source == 'flutter_app'"]
MERGE["Merge Node<br>Combine all channels"]
AI["AI Processing<br>OpenAI/Anthropic"]
SWITCH2["Switch Node 2<br>'Route Response'<br>Channel Detection"]
WA_RESP["Evolution API<br>Send WhatsApp"]
TG_RESP["Telegram API<br>Send Message"]
APP_RESP["HTTP Request<br>POST /app-message"]

WH --> SWITCH1
SWITCH1 --> WA_BRANCH
SWITCH1 --> TG_BRANCH
SWITCH1 --> APP_BRANCH
WA_PROCESS --> MERGE
TG_PROCESS --> MERGE
APP_PROCESS --> MERGE
MERGE --> AI
AI --> SWITCH2
SWITCH2 --> WA_RESP
SWITCH2 --> TG_RESP
SWITCH2 --> APP_RESP

subgraph subGraph1 ["Response Routing"]
    WA_RESP
    TG_RESP
    APP_RESP
end

subgraph subGraph0 ["Channel-Specific Branches"]
    WA_BRANCH
    WA_VERIFY
    WA_PROCESS
    TG_BRANCH
    TG_VERIFY
    TG_PROCESS
    APP_BRANCH
    APP_VERIFY
    APP_PROCESS
    WA_BRANCH --> WA_VERIFY
    WA_VERIFY --> WA_PROCESS
    TG_BRANCH --> TG_VERIFY
    TG_VERIFY --> TG_PROCESS
    APP_BRANCH --> APP_VERIFY
    APP_VERIFY --> APP_PROCESS
end
```

**Sources**: [N8N_CONFIGURATION_GUIDE.md L204-L223](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L204-L223)

 [N8N_INTEGRATION_GUIDE.md L162-L189](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L162-L189)

---

## Message Content Extraction

Each channel requires different n8n expressions to extract message content based on message type.

### Text Message Extraction

```
// App source detection and text extraction
{{ $json.data.source === 'flutter_app' ? 
   $json.data.message.conversation : 
   $json.body.data.message.conversation 
}}
```

**Sources**: [N8N_CONFIGURATION_GUIDE.md L118-L124](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L118-L124)

### Audio Message Detection

```
// Condition to detect audio message type
{{ $json.data.messageType === 'audioMessage' }}

// Audio data extraction (Base64)
{{ $json.data.messageType === 'audioMessage' ? 
   $json.data.message.base64 : 
   null 
}}
```

**Sources**: [N8N_CONFIGURATION_GUIDE.md L126-L143](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L126-L143)

### Message Type Routing

```mermaid
flowchart TD

MSG["Message Data"]
TYPE_CHECK["messageType"]
TEXT_ROUTE["messageType === 'conversation'"]
AUDIO_ROUTE["messageType === 'audioMessage'"]
TEXT_EXTRACT["Extract:<br>data.message.conversation"]
AUDIO_EXTRACT["Extract:<br>data.message.base64"]
TEXT_PROCESS["Text Processing<br>Send to AI"]
AUDIO_PROCESS["Audio Processing<br>Transcribe then AI"]

MSG --> TYPE_CHECK
TYPE_CHECK --> TEXT_ROUTE
TYPE_CHECK --> AUDIO_ROUTE
TEXT_ROUTE --> TEXT_EXTRACT
AUDIO_ROUTE --> AUDIO_EXTRACT
TEXT_EXTRACT --> TEXT_PROCESS
AUDIO_EXTRACT --> AUDIO_PROCESS
```

**Sources**: [N8N_CONFIGURATION_GUIDE.md L132-L143](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L132-L143)

---

## Implementation Checklist

When implementing message routing in n8n, verify the following components:

| Component | Configuration Required | Validation |
| --- | --- | --- |
| **Switch Node 1** | Channel detection rules | 3 outputs (WhatsApp, Telegram, App) |
| **WhatsApp IF** | Phone number verification | Split `@` then `:`, check first segment |
| **App IF** | Username verification | Split `:` then `@`, check segments |
| **Telegram IF** | User ID verification | Split `:`, extract user_id |
| **Merge Node** | Combine branches | Before AI processing |
| **Switch Node 2** | Response routing | 3 outputs (same channels) |

**Sources**: [N8N_CONFIGURATION_GUIDE.md L314-L324](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L314-L324)

---

## Troubleshooting Routing Issues

### Messages Not Routing to App Branch

**Symptoms**: App messages processed as WhatsApp messages

**Diagnosis**:

1. Verify `channel: "app"` field exists in webhook payload
2. Check Switch node rule order (app rule should be Rule 3)
3. Verify `remoteJid` has `app:` prefix

**Sources**: [N8N_CONFIGURATION_GUIDE.md L340-L344](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L340-L344)

### User Verification Failing

**Symptoms**: Messages rejected despite valid user

**Diagnosis**:

1. Log `remoteJid` value: `{{ $json.data.key.remoteJid }}`
2. Test extraction expression in n8n's expression editor
3. Verify format matches expected pattern

**Common mistakes**:

* Using wrong split order (`:` before `@` or vice versa)
* Accessing wrong array index after split
* Case sensitivity in username comparison

**Sources**: [N8N_INTEGRATION_GUIDE.md L88-L115](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L88-L115)

### Channel Field Missing

**Symptoms**: WhatsApp rule catches app messages

**Solution**: Update webhook payload to always include `channel` field. WhatsApp messages from Evolution API do not include this field, so detect them with `channel === undefined`.

**Sources**: [N8N_CONFIGURATION_GUIDE.md L77-L81](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_CONFIGURATION_GUIDE.md#L77-L81)

 [N8N_INTEGRATION_GUIDE.md L63-L71](https://github.com/axchisan/AxIA/blob/1fe26c44/N8N_INTEGRATION_GUIDE.md#L63-L71)