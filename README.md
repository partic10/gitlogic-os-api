# GitLogic OS - API Documentation

**GitLogic OS** is a comprehensive control system for *Build Logic!* (a Roblox game by Tomtom4500) that enables programmatic control via HTTP transmitters and visual display via 16x16 character panels.

## Core Components

### 1. HTTP Transmitter
The HTTP Transmitter is the primary communication interface for sending requests to external servers or receiving data.

### 2. 16x16 Character Panel
A grid-based display system for rendering text output with 256 character slots (16×16).

---

## Table of Contents
- [HTTP Transmitter Guide](#http-transmitter-guide)
- [16x16 Panel Guide](#16x16-panel-guide)
- [API Examples](#api-examples)
- [Wiring Guide](#wiring-guide)

---

## HTTP Transmitter Guide

### Overview
The HTTP Transmitter allows you to make GET and POST requests to external endpoints with custom headers, parameters, and body data.

### Configuration Settings

#### 1. **Website URL**
- **Purpose**: The full endpoint URL for your API request
- **Format**: `https://example.com/api/endpoint`
- **Required**: Yes
- **Example**: `https://api.github.com/users/partic10`

#### 2. **Headers**
- **Purpose**: Custom HTTP headers to include in the request
- **Format**: `Key: Value` (one per line)
- **Examples**:
  - `Cookie: test=1`
  - `Authorization: Bearer YOUR_TOKEN`
  - `Content-Type: application/json`
  - `User-Agent: GitLogic-OS/1.0`

#### 3. **Request Method**
- **GET**: Retrieve data from server (no body)
- **POST**: Send data to server (with body)

#### 4. **GET Parameters**
- **Purpose**: Query parameters appended to URL
- **Format**: `key1=value1&key2=value2`
- **Full URL Example**: `https://api.example.com/data?key1=value1&key2=value2`

#### 5. **POST Body**
- **Purpose**: Data sent in request body (POST only)
- **Format**: JSON, Form Data, or Raw Text
- **Example**:
  ```json
  {
    "username": "player123",
    "action": "build",
    "coordinates": [10, 20, 30]
  }
  ```

#### 6. **Response Output**
- **Purpose**: Data received from the server
- **Format**: Usually JSON or plain text
- **Usage**: Connect to panel or other components

---

## 16x16 Panel Guide

### Panel Layout

The 16x16 panel is a 256-character display grid with a specific coordinate system:

```
Position Counter (1-256):
┌─────────────────────────────┐
│  241  242  ... ... ...  256  │  Y = 16 (top-right corner)
│  225  226  ... ... ...  240  │
│  ...  ...  ... ... ...  ...  │
│  17   18   ... ... ...   32  │
│  1    2   ... ... ...   16   │  Y = 1 (bottom-right corner)
└─────────────────────────────┘
X = 1             X = 16
(left)            (right)
```

### Character Input
- **Purpose**: The text to display on the panel
- **Maximum**: 256 characters (fills the entire grid left-to-right, bottom-to-top)
- **Format**: Plain ASCII text
- **Overflow**: Text beyond 256 characters is truncated

### Position Input (Dual Purpose)

The Position input uses a **split format** where:
- **Left Half (Bits 0-7)**: X-axis (column) - Range: 1-16
- **Right Half (Bits 8-15)**: Y-axis (row) - Range: 1-16

#### Position Encoding

**Format**: `XXYY` (4-digit decimal) or programmatically: `(Y × 16) + X`

**Examples**:
| Position | X | Y | Visual Location |
|----------|---|---|---|
| `0101` | 1 | 1 | Bottom-Left |
| `1001` | 16 | 1 | Bottom-Right |
| `0116` | 1 | 16 | Top-Left |
| `1616` | 16 | 16 | Top-Right |
| `0808` | 8 | 8 | Center |

#### Calculation Formula

```
Position_Code = (Y × 16) + X

Where:
  X = 1 to 16 (columns, left to right)
  Y = 1 to 16 (rows, bottom to top)
```

#### Reverse Calculation

```
X = Position_Code % 16
Y = Position_Code ÷ 16 (integer division)
```

### Panel Write Operation

**Single Character Write**:
1. Set Character input to your 1-character string
2. Set Position input to `XXYY` format
3. Panel updates that specific cell

**Full Grid Write**:
1. Set Character input to 256-character string
2. Panel fills from bottom-left (1) to top-right (256)

---

## API Examples

### Example 1: Simple Weather API

**HTTP Transmitter Setup**:
```
Website: https://api.weatherapi.com/v1/current.json
Method: GET
GET Parameters: key=YOUR_API_KEY&q=New_York&aqi=no
Headers: User-Agent: GitLogic-OS/1.0
```

**Response** (truncated for panel):
```
Temp: 72°F Humidity: 65%
```

**Panel Output**:
```
Position: 0101 (start bottom-left)
Character: "Temp: 72°F Humidity: 65%"
```

---

### Example 2: Discord Bot Integration (POST)

**HTTP Transmitter Setup**:
```
Website: https://discordapp.com/api/v10/channels/YOUR_CHANNEL_ID/messages
Method: POST
Headers:
  Authorization: Bot YOUR_BOT_TOKEN
  Content-Type: application/json

POST Body:
{
  "content": "Status: Server Online",
  "embeds": [{
    "title": "GitLogic OS",
    "description": "Build Logic Status"
  }]
}
```

---

### Example 3: GitHub User Info (GET)

**HTTP Transmitter Setup**:
```
Website: https://api.github.com/users/partic10
Method: GET
Headers: 
  User-Agent: GitLogic-OS
  Accept: application/vnd.github.v3+json
GET Parameters: (none)
```

**Response**:
```json
{
  "login": "partic10",
  "public_repos": 42,
  "followers": 100
}
```

**Panel Display**:
```
Position: 0101
Character: "partic10 | Repos: 42 | Followers: 100     "
```

---

### Example 4: Real-time Server Status (Repeated Requests)

**HTTP Transmitter Setup**:
```
Website: https://api.example.com/server/status
Method: GET
Headers: Authorization: Bearer YOUR_TOKEN
GET Parameters: server_id=build-logic-1
```

**Poll Interval**: Every 5 seconds

**Panel Display** (Updates continuously):
```
[Server Status Line]
Position: 0101 → Character: "STATUS: ONLINE | CPU: 45% | RAM: 2.1GB"
```

---

## Wiring Guide

### HTTP Transmitter → Panel Workflow

```
┌─────────────────────────────┐
│  HTTP Transmitter           │
│  ┌───────────────────────┐  │
│  │ Website URL Input     │  │
│  │ Headers Input         │  │
│  │ Method (GET/POST)     │  │
│  │ GET/POST Parameters   │  │
│  └───────────────────────┘  │
│           ↓                  │
│  ┌─────────────────────────┐ │
│  │ RESPONSE OUTPUT         │ │
│  │ (JSON/Plain Text)       │ │
│  └─────────────────────────┘ │
└─────────────┬─────────────────┘
              │
              └──→ ┌─────────────────────────┐
                   │   16x16 Character Panel │
                   │                         │
                   │ Character Input ←───────┤
                   │ Position Input  ←───────┤
                   │                         │
                   │ [Display Grid 16×16]    │
                   └─────────────────────────┘
```

### Step-by-Step Wiring (Example)

**Goal**: Display API response on panel

#### Step 1: Configure HTTP Transmitter
```
Website: https://api.example.com/status
Method: GET
Headers: Cookie: test=1
GET Parameters: format=text
```

#### Step 2: Connect Response to Panel
```
HTTP Transmitter.ResponseOutput 
  → Panel.CharacterInput
```

#### Step 3: Set Panel Position
```
Wire Panel.PositionInput ← [Manual: 0101] 
  (or from another logic component)
```

#### Step 4: Test
- Trigger HTTP Transmitter
- Observe panel for response display

---

### Advanced Wiring: Multi-Line Display

**Scenario**: Display 4 lines of status information

```
Line 1 (Y=4):  Position 0104 → 16 characters
Line 2 (Y=3):  Position 0103 → 16 characters
Line 3 (Y=2):  Position 0102 → 16 characters
Line 4 (Y=1):  Position 0101 → 16 characters

Wiring:
  Transmitter1.Response → Panel.Character (Position: 0104)
  Transmitter2.Response → Panel.Character (Position: 0103)
  Transmitter3.Response → Panel.Character (Position: 0102)
  Transmitter4.Response → Panel.Character (Position: 0101)
```

---

### Input/Output Mapping Reference

| Component | Input | Output | Purpose |
|-----------|-------|--------|---------|
| HTTP Transmitter | Website, Headers, Method, Parameters, Body | Response (Text/JSON) | Send HTTP request & receive data |
| 16x16 Panel | Character (text), Position (XXYY) | Visual Display | Render text on 256-char grid |
| Logic Gate | Various | True/False/Value | Condition logic |

---

## Common Patterns

### Pattern 1: Auto-Update Panel Every 5 Seconds
```
Timer (5s interval) 
  → HTTP Transmitter 
  → String Parser (extract relevant data) 
  → Panel.Character
```

### Pattern 2: Conditional Display
```
HTTP Transmitter 
  → Logic Gate (IF response contains "error") 
  → Display "ERROR" on panel 
  → ELSE display "SUCCESS"
```

### Pattern 3: Multi-Position Display
```
HTTP Transmitter 
  → String Splitter (split by newlines) 
  → Multiple Panel writes at different positions
```

---

## Best Practices

1. **Header Security**: Store sensitive tokens in environment variables or config files
2. **Rate Limiting**: Add delays between requests to avoid API throttling
3. **Error Handling**: Use logic gates to detect failed responses (empty, errors, etc.)
4. **Character Encoding**: Stick to ASCII for panel compatibility
5. **Position Precision**: Double-check XXYY encoding to avoid display corruption

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Panel shows scrambled text | Check position encoding (XXYY format) |
| HTTP request returns 401 | Verify headers, especially Authorization token |
| Panel only shows first 16 chars | Make sure you're using full 256-char string for full grid |
| Position appears off-grid | Ensure X (1-16) and Y (1-16) are within bounds |
| Response not displaying | Check if transmitter actually ran (add timer or trigger) |

---

## License

This documentation is for Build Logic! game by Tomtom4500.

For more info: Visit the [Build Logic! Wiki](https://roblox.fandom.com/wiki/Build_Logic!)
