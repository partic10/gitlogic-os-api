# Quick Reference Cheat Sheet

Fast lookup guide for GitLogic OS HTTP Transmitters and 16x16 Panels.

---

## HTTP Transmitter Cheat Sheet

### Configuration Quick Fill

```
Website URL:        https://api.example.com/endpoint
Method:             GET or POST
Headers:            Key: Value (one per line)
GET Parameters:     key1=value1&key2=value2
POST Body:          {"key": "value"} (JSON format)
```

### Common Headers

```
Authorization: Bearer YOUR_TOKEN
Cookie: sessionid=abc123
Content-Type: application/json
User-Agent: GitLogic-OS/1.0
Accept: application/json
```

### Output Pins

```
┌─ Response (Text/JSON data)
├─ Status (True = Success, False = Failed)
└─ Status Code (200, 404, 500, etc.)
```

### GET vs POST

| GET | POST |
|-----|------|
| Retrieve data | Send data |
| No body needed | Body required |
| Parameters in URL | Parameters in body |
| Cached by servers | Not cached |
| Max 2000 chars URL | Larger data allowed |

---

## 16x16 Panel Cheat Sheet

### Panel Grid (256 characters)

```
Top-Left     Top-Right
0116 ......... 1616

....................

Bottom-Left  Bottom-Right
0101 ......... 1601
```

### Position Encoding (XXYY Format)

```
First 2 digits:  X-axis (1-16, left to right)
Last 2 digits:   Y-axis (1-16, bottom to top)

Formula: Position = (Y × 16) + X
```

### Position Examples

```
0101 = Bottom-Left      1601 = Bottom-Right
0102 = Line 2          1602 = Line 2 Right
0103 = Line 3          1603 = Line 3 Right
0104 = Line 4          1604 = Line 4 Right
0116 = Top-Left        1616 = Top-Right
```

### Input Pins

```
┌─ Character (text to display, max 256 chars)
└─ Position (XXYY format: X=1-16, Y=1-16)
```

### Character Limits per Line

```
16 characters maximum per line
256 characters maximum for full grid
Excess text = truncated
```

---

## Common Wiring Patterns

### Pattern 1: API → Panel

```
HTTP Transmitter.Response → Panel.Character
(Manual Position)         ← Panel.Position
```

### Pattern 2: Conditional Display

```
Transmitter.Response → Logic Gate → Panel.Character
                        (if error)
```

### Pattern 3: Multi-Line Display

```
Transmitter1.Response → Panel (Position 0101)
Transmitter2.Response → Panel (Position 0102)
Transmitter3.Response → Panel (Position 0103)
Transmitter4.Response → Panel (Position 0104)
```

### Pattern 4: Auto-Update

```
Timer → Transmitter → Panel (repeating cycle)
```

---

## Popular APIs & Headers

### GitHub API

```
Website: https://api.github.com/users/{username}
Headers: User-Agent: GitLogic-OS
Method: GET
```

### Weather API

```
Website: https://api.weatherapi.com/v1/current.json
Headers: User-Agent: GitLogic-OS/1.0
Method: GET
Parameters: key=YOUR_KEY&q=CITY&aqi=no
```

### Discord

```
Website: https://discord.com/api/v10/channels/{CHANNEL_ID}/messages
Headers: Authorization: Bot YOUR_BOT_TOKEN
         Content-Type: application/json
Method: POST
Body: {"content": "message here"}
```

### OpenWeatherMap

```
Website: https://api.openweathermap.org/data/2.5/weather
Method: GET
Parameters: q=CITY&appid=YOUR_KEY&units=metric
```

---

## Position Calculator

### Quick Conversions

```
Row (Y)  Positions
1        0101 0201 0301 ... 1601
2        0102 0202 0302 ... 1602
3        0103 0203 0303 ... 1603
4        0104 0204 0304 ... 1604
...
16       0116 0216 0316 ... 1616
```

### Math Formula

```
Position = (Y × 16) + X

Example: X=5, Y=3
Position = (3 × 16) + 5 = 48 + 5 = 53
Format: 0553
```

### Reverse Formula

```
Given Position 0553:
X = 53 % 16 = 5
Y = 53 ÷ 16 = 3 (integer)
Result: X=5, Y=3
```

---

## Character Count Helper

### Text Formatting

```
1 Line (16 chars):  "Hello World!    " (pad with spaces)
2 Lines (32 chars): "Line 1         " + "Line 2         "
3 Lines (48 chars): "Line 1         " + "Line 2         " + "Line 3         "
4 Lines (64 chars): Repeat above pattern

Full Grid (256):    All 16 lines × 16 chars each
```

### Padding Guide

```
16-char line needs padding:
"Status: OK" = 10 chars → add 6 spaces → "Status: OK     "
```

---

## Common Errors & Fixes

| Error | Fix |
|-------|-----|
| 401 Unauthorized | Check Authorization header & token |
| 404 Not Found | Verify website URL is correct |
| 429 Too Many Requests | Add delay between requests (rate limit) |
| Blank panel | Check if response is connected to Character input |
| Wrong position | Use XXYY format (0101, not 1,1) |
| Text truncated | Limit to 16 chars per line |
| No response | Add trigger (Button/Timer) to transmitter |
| Scrambled display | Verify position encoding |

---

## Trigger Types

### Timer
```
Interval: 1-3600 seconds
Use: Repeated API calls, auto-refresh
```

### Button
```
Manual activation
Use: On-demand API requests
```

### Logic Output
```
Conditional triggering
Use: If-then scenarios, error handling
```

---

## String Manipulation Blocks

### Format Text for Panel

```
Concatenation: "Text1" + "Text2" = "Text1Text2"
Substring: Extract 10 chars starting at position 0
Replace: Replace "old" with "new"
Length: Get character count
Uppercase/Lowercase: Change case
Trim: Remove leading/trailing spaces
```

---

## JSON Basics for POST

### Simple Object

```json
{
  "key": "value",
  "number": 42,
  "flag": true
}
```

### Array

```json
{
  "items": ["item1", "item2", "item3"]
}
```

### Nested

```json
{
  "user": {
    "name": "John",
    "age": 30
  }
}
```

---

## Testing Checklist

- [ ] HTTP Transmitter configured correctly
- [ ] Website URL valid & accessible
- [ ] Headers formatted properly (Key: Value)
- [ ] GET/POST method matches endpoint
- [ ] 16x16 Panel placed in world
- [ ] Response wired to Panel Character
- [ ] Position set to XXYY format
- [ ] Trigger (Button/Timer) connected
- [ ] Character text ≤ 16 chars per line
- [ ] Position coordinates within 1-16 range
- [ ] No typos in API endpoints
- [ ] API token/key valid and not expired

---

## Performance Tips

### Optimize API Calls

```
✓ Use GET for read-only data
✓ Add delays between requests (avoid rate limits)
✓ Request only needed fields if API supports filtering
✓ Cache responses if data doesn't change frequently
✓ Use smaller response formats (XML vs JSON if lighter)
```

### Optimize Panel Display

```
✓ Limit to necessary lines (don't fill all 16)
✓ Use fixed positions instead of dynamic when possible
✓ Batch multiple writes into single trigger
✓ Pad text efficiently (avoid extra spaces)
```

---

## Security Tips

```
🔒 Never hardcode API tokens
🔒 Use environment variables for sensitive data
🔒 Validate API responses before displaying
🔒 Sanitize user input before sending to API
🔒 Use HTTPS URLs only
🔒 Keep bot tokens private
🔒 Limit API permissions to minimum required
🔒 Monitor rate limit headers
```

---

## Debugging Steps

### Step 1: Test Transmitter Alone
```
Add Panel → Manual position → Trigger transmitter
Check if response appears on panel
```

### Step 2: Verify API Response
```
Add String display block → Show transmitter response
Check for expected format (JSON, text, etc.)
```

### Step 3: Check Headers
```
Verify Authorization header is formatted correctly
Test with Postman/curl first if needed
```

### Step 4: Validate Position
```
Test each position (0101, 1601, 0116, 1616)
Verify text appears in correct location
```

### Step 5: Check Triggers
```
Verify button/timer actually triggers transmitter
Add visual feedback (panel message) on trigger
```

---

## Keyboard Shortcuts (In Game)

```
[G] - Open GitLogic OS menu
[R] - Rotate component
[D] - Duplicate component
[Del] - Delete component
[C] - Copy settings
[V] - Paste settings
```

*(Verify with game instructions)*

---

## Useful Resources

- **API Testing**: [Postman](https://www.postman.com/)
- **JSON Validator**: [jsonlint.com](https://www.jsonlint.com/)
- **Position Calculator**: Online XXYY converter
- **GitHub API Docs**: [api.github.com](https://api.github.com/)
- **Discord Bot Docs**: [discord.com/developers](https://discord.com/developers/)

---

## Contact & Support

For issues or questions:
- Check repository: [gitlogic-os-api](https://github.com/partic10/gitlogic-os-api)
- Review examples in `/examples/` folder
- Read full guide in main README.md

---

**Last Updated:** September 2026  
**Version:** 1.0  
**For:** Build Logic! (Roblox) by Tomtom4500
