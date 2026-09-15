# Example: Basic Weather Display

This example shows how to fetch weather data and display it on a 16x16 panel.

## Setup

### HTTP Transmitter Configuration

**Website URL:**
```
https://api.weatherapi.com/v1/current.json
```

**Method:** GET

**Headers:**
```
User-Agent: GitLogic-OS/1.0
Accept: application/json
```

**GET Parameters:**
```
key=YOUR_API_KEY&q=New_York&aqi=no
```

### Expected Response:
```json
{
  "location": {
    "name": "New York",
    "region": "New York",
    "country": "United States",
    "lat": 40.71,
    "lon": -74.01,
    "tz_id": "America/New_York",
    "localtime_epoch": 1234567890,
    "localtime": "2024-01-15 14:30"
  },
  "current": {
    "last_updated_epoch": 1234567890,
    "last_updated": "2024-01-15 14:30",
    "temp_c": 22.2,
    "temp_f": 72,
    "is_day": 1,
    "condition": {
      "text": "Partly cloudy",
      "icon": "//cdn.weatherapi.com/weather/64x64/day/116.png",
      "code": 1003
    },
    "wind_mph": 12.3,
    "wind_kph": 19.8,
    "wind_degree": 180,
    "wind_dir": "S",
    "pressure_mb": 1013,
    "pressure_in": 29.91,
    "precip_mm": 0,
    "precip_in": 0,
    "humidity": 65,
    "cloud": 50,
    "feelslike_c": 21,
    "feelslike_f": 70,
    "vis_km": 10,
    "vis_miles": 6.2,
    "uv": 3,
    "gust_mph": 15.6,
    "gust_kph": 25.1
  }
}
```

## Panel Display Setup

### 16x16 Panel Configuration

**Character Input:** (Connect from HTTP Transmitter response)
```
Format the response to fit 256 characters max:
"New York: 72F | Humidity 65% | Partly Cloudy"
```

**Position Input:** 
```
0101  (Display at bottom-left corner, extends right)
```

### Multi-Line Weather Display

For a more detailed 4-line display:

**Line 1 (Bottom)** - City & Temperature
```
Position: 0101
Character: "NYC        72F  Humidity65%"
```

**Line 2** - Condition
```
Position: 0102
Character: "Partly Cloudy              "
```

**Line 3** - Wind Information
```
Position: 0103
Character: "Wind: 12mph S              "
```

**Line 4** - Pressure & Visibility
```
Position: 0104
Character: "Press:1013mb  Vis:6.2mi   "
```

## Complete Wiring

```
┌──────────────────────────────┐
│  Timer (30s interval)        │
└──────────────┬───────────────┘
               │
               ↓
┌──────────────────────────────┐
│  HTTP Transmitter            │
│  Website: weatherapi.com     │
│  Method: GET                 │
│  Response: JSON              │
└──────────────┬───────────────┘
               │
               ↓
┌──────────────────────────────┐
│  String Parser/Formatter     │
│  Extract temp, humidity, etc │
└──────────────┬───────────────┘
               │
         ┌─────┴─────┐
         ↓           ↓
    Line1Input   Line2Input
         │           │
         ↓           ↓
    Panel(0101) Panel(0102)
```

## Testing Steps

1. **Create HTTP Transmitter**
   - Set website URL
   - Add API key in headers or parameters
   - Set method to GET

2. **Create Timer**
   - Set interval to 30 seconds (or your preference)
   - Connect timer → transmitter trigger

3. **Create 16x16 Panel**
   - Add to your build area

4. **Wire Transmitter → Panel**
   - Response → Character Input
   - Manual Position → 0101 (or chain from logic)

5. **Test**
   - Wait for timer to trigger
   - Check panel displays weather data
   - Verify updates every 30 seconds

## Error Handling

Add logic gates to detect failures:

```
If HTTP Transmitter Response is empty:
  → Display "ERROR: No Data" at Position 0101
  
If HTTP Transmitter Response contains "error":
  → Display "API ERROR" at Position 0101
  
Otherwise:
  → Display weather data as configured
```

## Customization

- **Update Frequency**: Change timer interval
- **Location**: Modify GET parameter `q=` to any city
- **Display Format**: Adjust text in string formatter
- **Additional Data**: Add more lines (Y axis 1-16)

## Notes

- Free API key from: https://www.weatherapi.com
- Rate limits apply (check API docs)
- Responses are typically 200-300 characters
- Panel wraps/truncates text beyond 16 chars per line
