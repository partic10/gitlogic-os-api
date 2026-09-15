# Wiring Guide - Complete Reference

A comprehensive guide on how to wire HTTP Transmitters and 16x16 Panels together in GitLogic OS.

## Quick Reference

### HTTP Transmitter Outputs
- **Response** - The data returned from the API (Text/JSON)
- **Status** - Success (True) or Failure (False)
- **Status Code** - HTTP status code (200, 404, 500, etc.)

### 16x16 Panel Inputs
- **Character** - Text to display (max 256 chars or 16 chars per line)
- **Position** - Where to place text (XXYY format: X=1-16, Y=1-16)

---

## Wiring Scenario 1: Basic GET Request → Panel Display

**Goal:** Fetch data and display it

```
HTTP Transmitter (GET)
    │
    ├─ Response (successful data)
    │     │
    │     └──→ 16x16 Panel Character Input
    │
    └─ Status (True/False)
          │
          └──→ Logic Gate (conditional display)
```

**Step-by-Step:**

1. **Configure HTTP Transmitter**
   - Website: `https://api.example.com/data`
   - Method: GET
   - Headers: `User-Agent: GitLogic-OS/1.0`

2. **Create 16x16 Panel**
   - Place in build area

3. **Wire Connection**
   - Drag from Transmitter → Panel
   - Select "Response" output
   - Connect to Panel "Character" input

4. **Set Position**
   - Panel "Position" input → Manual value `0101`

5. **Trigger**
   - Add Button or Timer to trigger transmitter

**Result:** When transmitter runs, response displays on panel starting at bottom-left.

---

## Wiring Scenario 2: Multi-Line Display (4 Lines)

**Goal:** Display 4 lines of different data from separate API calls

```
Transmitter1.Response → Panel Character (Position: 0104)
Transmitter2.Response → Panel Character (Position: 0103)
Transmitter3.Response → Panel Character (Position: 0102)
Transmitter4.Response → Panel Character (Position: 0101)
```

**Setup:**

```
┌─────────────────────────────────────────┐
│  Timer (5s trigger)                     │
└────────────────────┬────────────────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
        ↓            ↓            ↓
   Transmit1    Transmit2    Transmit3
   (API 1)      (API 2)      (API 3)
        │            │            │
        └────────────┼────────────┘
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
   Panel(Y=4)  Panel(Y=3)  Panel(Y=2)
```

**Position Reference:**
```
Y=4: Position 0104 (Line 4 - Top)
Y=3: Position 0103 (Line 3)
Y=2: Position 0102 (Line 2)
Y=1: Position 0101 (Line 1 - Bottom)
```

**Character Limits:**
- Each line: 16 characters maximum
- Total: 256 characters for full grid

---

## Wiring Scenario 3: Conditional Display (Error Handling)

**Goal:** Display different text based on API response

```
HTTP Transmitter.Response
    │
    ├─ "error" detected?
    │     ├─ YES → Panel: "ERROR" at 0101
    │     │
    │     └─ NO → Panel: "SUCCESS" at 0101
```

**Setup with Logic Gates:**

```
HTTP Transmitter.Response
    │
    └─→ String Comparison Gate
        ├─ Contains "error"?
        │    ├─ YES → Output "error_text"
        │    │         │
        │    │         └──→ Panel Character
        │    │
        │    └─ NO → Output "success_text"
        │             │
        │             └──→ Panel Character
        │
        └─→ Panel Position (fixed 0101)
```

**Gate Configuration:**

| Gate Type | Input | Condition | Output |
|-----------|-------|-----------|--------|
| String Contains | Response | "error" | True/False |
| If/Then | Status | True | "Success" |
| If/Then | Status | False | "Failed" |

---

## Wiring Scenario 4: Dynamic Position Updates

**Goal:** Display text at different positions based on conditions

```
Counter/Logic
    │
    └─→ Position Calculator
        │
        ├─ Counter = 1 → Position 0101
        ├─ Counter = 2 → Position 0102
        ├─ Counter = 3 → Position 0103
        └─ Counter = 4 → Position 0104
             │
             └──→ Panel Position Input
                  │
                  ↑
                  │
HTTP Transmitter.Response ──→ Panel Character Input
```

**Formula for Position:**
```
If Counter = 1: Position = 0101
If Counter = 2: Position = 0102
If Counter = 3: Position = 0103
Etc.
```

Or use mathematical formula:
```
Position_Code = (Counter × 16) + 1
```

---

## Wiring Scenario 5: Auto-Scrolling Display

**Goal:** Rotate through multiple lines automatically

```
┌─────────────────────────────────────────┐
│  Timer (2s interval)                    │
└──────────────┬──────────────────────────┘
               │
               ↓
        ┌──────────────┐
        │  Counter     │
        │  Increments  │
        │  1→2→3→4→1   │
        └──────┬───────┘
               │
        ┌──────┴────────────┬─────────────┐
        │                   │             │
        ↓                   ↓             ↓
    API1 Request      Counter→Position   Panel
    (GET data)        Calculator     Display
        │                   │             ↑
        └───────────────────┴─────────────┘
```

**Cycle Pattern:**
```
Timer triggers → Counter increments (1, 2, 3, 4)
Counter value → Position Calculator (0101, 0102, 0103, 0104)
Panel updates position → Text scrolls up line by line
```

---

## Wiring Scenario 6: Header Input from Dynamic Source

**Goal:** Use generated data as HTTP headers

```
Logic Component
    │
    └─→ Format as Header
        ├─ "Authorization: Bearer " + token
        │
        └──→ HTTP Transmitter Header Input
             │
             └─→ Request sent with dynamic header
```

**Example:**

```
Token Generator (from login)
    │
    └─→ String Concatenation
        ├─ Input 1: "Authorization: Bearer "
        ├─ Input 2: token_value
        │
        └─→ HTTP Transmitter (Headers field)
            │
            └─→ Request with auth header
```

---

## Position Encoding Quick Reference

### XXYY Format Breakdown

```
Position Code: XXYY

XX = Column (1-16)
YY = Row (1-16)

Examples:
0101 = X1, Y1 (Bottom-Left)
1601 = X16, Y1 (Bottom-Right)
0116 = X1, Y16 (Top-Left)
1616 = X16, Y16 (Top-Right)
0808 = X8, Y8 (Center)
```

### Grid Layout with Positions

```
┌──────────────────────────────────┐
│ 0101 0201 0301 ... 1501 1601     │  Row 16 (Y=16) - Top
│ 0102 0202 0302 ... 1502 1602     │  Row 15 (Y=15)
│ 0103 0203 0303 ... 1503 1603     │  Row 14 (Y=14)
│  ...  ...  ...     ...  ...      │  ...
│ 0115 0215 0315 ... 1515 1615     │  Row 2 (Y=2)
│ 0116 0216 0316 ... 1516 1616     │  Row 1 (Y=1) - Bottom
└──────────────────────────────────┘
  X=1  X=2  X=3     X=15 X=16
  Left                      Right
```

---

## Common Wiring Mistakes & Fixes

### Mistake 1: Wrong Position Format

❌ **Wrong:**
```
Position Input: "1,1" or "16" or "Top-Left"
```

✅ **Correct:**
```
Position Input: "0101" (XXYY format)
```

---

### Mistake 2: Character Overflow

❌ **Wrong:**
```
Character: "This is a very long line that exceeds 16 characters"
Panel Position: 0101
Result: Text wraps incorrectly or truncates
```

✅ **Correct:**
```
Character: "Long text here" (max 16 chars per line)
Panel Position: 0101 (line 1)
Panel Position: 0102 (line 2)
(Split into multiple panel writes)
```

---

### Mistake 3: Forgetting to Trigger Transmitter

❌ **Wrong:**
```
HTTP Transmitter (no trigger)
→ Panel displays nothing (transmitter never runs)
```

✅ **Correct:**
```
Button/Timer → HTTP Transmitter → Panel
(Transmitter needs input trigger to execute)
```

---

### Mistake 4: Wrong Header Format

❌ **Wrong:**
```
Headers: "Authorization=Bearer token123"
Headers: "Authorization: Bearer token123, Content-Type: application/json"
```

✅ **Correct:**
```
Headers:
Authorization: Bearer token123
Content-Type: application/json
(One header per line)
```

---

### Mistake 5: Mixing GET Parameters with POST

❌ **Wrong:**
```
Method: POST
GET Parameters: key=value&foo=bar
(Should use POST Body instead)
```

✅ **Correct:**
```
Method: POST
POST Body: {"key": "value", "foo": "bar"}
```

---

## Advanced Wiring Patterns

### Pattern: Parallel Requests with Aggregation

```
┌──────────────────────────────────┐
│  Data Aggregator                 │
└──────────────────────────────────┘
         ↑          ↑         ↑
         │          │         │
    API1.Response API2.Response API3.Response
         │          │         │
         └──────────┼─────────┘
                    │
            String Formatter
                    │
                    ↓
            Panel Character Input
```

**Use Case:** Combine results from 3 APIs into single panel display

---

### Pattern: Feedback Loop

```
User Input (Button)
    │
    ↓
HTTP Transmitter
    │
    ├─ Response → Panel (Display result)
    │
    └─ Status → Logic Gate
        │
        ├─ If Success → Next Step
        └─ If Failure → Retry or Display Error
```

---

### Pattern: Real-time Status Monitor

```
Timer (1s interval)
    │
    ├─→ HTTP Transmitter 1 (CPU)
    ├─→ HTTP Transmitter 2 (Memory)
    ├─→ HTTP Transmitter 3 (Network)
    │
    └─→ Panel Updates
        Position 0101: CPU Stats
        Position 0102: Memory Stats
        Position 0103: Network Stats
```

---

## Testing Your Wiring

### Test 1: Visual Verification

1. Trigger transmitter manually
2. Check panel for text appearance
3. Verify position encoding (should start at bottom-left if 0101)

### Test 2: Response Validation

1. Print transmitter response to panel
2. Check for expected data format
3. Verify no error messages

### Test 3: Position Testing

```
Wire Panel Character: "POSITION TEST"
Test each position:
  0101 → Should appear bottom-left
  1601 → Should appear bottom-right
  0116 → Should appear top-left
  1616 → Should appear top-right
```

### Test 4: Multi-Line Testing

```
Line 1 (Y=1): 0101 → "LINE 1        "
Line 2 (Y=2): 0102 → "LINE 2        "
Line 3 (Y=3): 0103 → "LINE 3        "
Line 4 (Y=4): 0104 → "LINE 4        "

Verify: Text appears as 4 separate lines, not overlapping
```

---

## Troubleshooting Wiring Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| Panel blank after transmitter runs | No connection between Response and Character | Re-wire transmitter to panel |
| Text appears in wrong position | Position format incorrect | Use XXYY format (e.g., 0101) |
| Text overlaps or wraps oddly | Character string too long for position | Limit to 16 chars per line, use multiple positions |
| Transmitter never runs | No trigger connected | Add Button/Timer to trigger input |
| Headers not working | Format incorrect | Use "Key: Value" per line, not JSON |
| GET params not appending | Wrong syntax | Use "key1=value1&key2=value2" format |

---

## Resources & Links

- [HTTP Transmitter Documentation](../README.md#http-transmitter-guide)
- [16x16 Panel Documentation](../README.md#16x16-panel-guide)
- [API Examples](../examples/)
- [Discord Integration](../examples/discord-bot-integration.md)
- [Weather Display](../examples/basic-weather-display.md)
