# Example: Discord Bot Integration

This example demonstrates how to send messages to Discord via HTTP POST requests.

## Setup

### HTTP Transmitter Configuration

**Website URL:**
```
https://discord.com/api/v10/channels/YOUR_CHANNEL_ID/messages
```

**Method:** POST

**Headers:**
```
Authorization: Bot YOUR_BOT_TOKEN
Content-Type: application/json
User-Agent: GitLogic-OS/1.0
```

**GET Parameters:** (none)

**POST Body:**
```json
{
  "content": "Build Logic Status Update",
  "embeds": [
    {
      "title": "GitLogic OS Status",
      "description": "Real-time status from Build Logic!",
      "color": 3447003,
      "fields": [
        {
          "name": "Status",
          "value": "ONLINE",
          "inline": true
        },
        {
          "name": "CPU Usage",
          "value": "45%",
          "inline": true
        },
        {
          "name": "Memory",
          "value": "2.1 GB / 4 GB",
          "inline": false
        }
      ],
      "footer": {
        "text": "Updated at 14:30 UTC"
      }
    }
  ]
}
```

## Setup Instructions

### Step 1: Create Discord Bot

1. Go to [Discord Developer Portal](https://discord.com/developers/applications)
2. Click "New Application"
3. Name it "GitLogic OS"
4. Go to "Bot" section → "Add Bot"
5. Copy the TOKEN (this is your Bot Token)

### Step 2: Invite Bot to Server

1. Go to "OAuth2" → "URL Generator"
2. Select scopes: `bot`
3. Select permissions: `Send Messages`, `Embed Links`
4. Copy generated URL
5. Paste in browser to invite bot to your server

### Step 3: Get Channel ID

1. Enable Developer Mode in Discord (Settings → Advanced → Developer Mode)
2. Right-click channel → Copy Channel ID
3. Use this as `YOUR_CHANNEL_ID` in the URL

### Step 4: Configure HTTP Transmitter

In Build Logic!, create HTTP Transmitter with:
- Website: `https://discord.com/api/v10/channels/CHANNEL_ID/messages`
- Bot Token in Authorization header
- POST body with your message

## 16x16 Panel Feedback Display

Display confirmation on the panel after sending:

**Position Input:** `0101`

**Character Input:**
```
Message sent to Discord!  ✓
```

Or show status:
```
Status: ONLINE | Discord: ✓
```

## Complete Wiring

```
┌──────────────────────────────┐
│  Button/Trigger              │
└──────────────┬───────────────┘
               │
               ↓
┌──────────────────────────────┐
│  HTTP Transmitter (POST)     │
│  Discord API Endpoint        │
│  Response: {"id": "..."}     │
└──────────────┬───────────────┘
               │
         ┌─────┴──────────┐
         ↓                ↓
    Logic Gate        16x16 Panel
    (Check Success)   Show Confirmation
         │
         ├─ Success → Display "✓ Sent"
         └─ Failure → Display "✗ Error"
```

## Message Variations

### Simple Text Message

```json
{
  "content": "Build Logic Server is Online!"
}
```

### With Embed (Rich Formatting)

```json
{
  "content": "Status Update",
  "embeds": [
    {
      "title": "Server Status",
      "description": "All systems operational",
      "color": 65280
    }
  ]
}
```

### With Fields (Structured Data)

```json
{
  "embeds": [
    {
      "title": "Build Logic Stats",
      "fields": [
        {"name": "Players Online", "value": "42", "inline": true},
        {"name": "CPU", "value": "45%", "inline": true},
        {"name": "Uptime", "value": "24 hours", "inline": false}
      ]
    }
  ]
}
```

### Ping Users

```json
{
  "content": "<@USER_ID> Server needs attention!",
  "embeds": [{"description": "Critical error detected"}]
}
```

Replace `USER_ID` with Discord user ID or use `<@&ROLE_ID>` for roles.

## Auto-Update to Discord (Every 5 minutes)

```
┌──────────────────────────┐
│  Timer (5min interval)   │
└──────────┬───────────────┘
           │
           ↓
┌──────────────────────────┐
│  Gather Status Data      │
│  (CPU, Memory, etc)      │
└──────────┬───────────────┘
           │
           ↓
┌──────────────────────────┐
│  Format JSON for Discord │
└──────────┬───────────────┘
           │
           ↓
┌──────────────────────────┐
│  HTTP POST Transmitter   │
│  → Discord Channel       │
└──────────┬───────────────┘
           │
           ↓
┌──────────────────────────┐
│  16x16 Panel Displays    │
│  "Last update: 14:30"    │
└──────────────────────────┘
```

## Error Handling

### Check Response Status

```
If Response is Empty:
  → Display "ERROR: No Response" on panel
  
If Response contains "error":
  → Display "Discord Error" on panel
  
If Response contains "unauthorized":
  → Display "Auth Failed" on panel
  
If Response successful:
  → Display "✓ Message Sent" on panel
```

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid bot token | Verify token in Authorization header |
| 403 Forbidden | Bot lacks permissions | Add "Send Messages" permission in OAuth2 |
| 404 Not Found | Wrong channel ID | Copy correct Channel ID from Discord |
| 429 Too Many Requests | Rate limited | Add delay between requests (1-5 seconds) |
| 400 Bad Request | Invalid JSON body | Validate JSON syntax in POST body |

## Testing

### Step 1: Simple Message Test
```json
{
  "content": "Test message from GitLogic OS"
}
```

### Step 2: Check Panel
```
Position: 0101
Character: "Message sent to Discord ✓"
```

### Step 3: Verify in Discord
- Check Discord channel for message
- Confirm it appears in real-time

### Step 4: Error Test (Optional)
- Disable bot permissions
- Try sending message
- Should see error on panel

## Rate Limiting

Discord limits message posting:
- **Default**: 5 messages per 5 seconds per channel
- **Add delay**: Use Timer between requests (minimum 1 second)
- **Monitor**: Check response headers for rate limit info

```
Recommended Intervals:
- Status updates: 5 minutes
- Alerts: Immediate (but max 1/second)
- Logs: 30 seconds
```

## Advanced: Interactive Messages

### With Buttons (Discord API v10)

```json
{
  "content": "Ready?",
  "components": [
    {
      "type": 1,
      "components": [
        {
          "type": 2,
          "label": "Start",
          "style": 1,
          "custom_id": "build_start"
        },
        {
          "type": 2,
          "label": "Stop",
          "style": 4,
          "custom_id": "build_stop"
        }
      ]
    }
  ]
}
```

*(Note: Buttons require additional webhook setup for interactions)*

## Security Notes

1. **Never share your bot token** - Keep it secret!
2. **Use environment variables** - Store token in a safe location
3. **Limit permissions** - Only grant necessary Discord permissions
4. **Rate limit** - Don't spam Discord API
5. **Validate data** - Ensure JSON is properly formatted

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Message not appearing | Check channel ID and bot permissions |
| "Unauthorized" error | Verify bot token is correct |
| Formatting looks wrong | Check JSON syntax and Discord markdown |
| Messages too frequent | Add delay between requests |
| Panel shows error | Verify HTTP response in transmitter logs |

## Resources

- [Discord API Docs](https://discord.com/developers/docs)
- [Bot Permissions](https://discord.com/developers/docs/topics/permissions)
- [Message Format](https://discord.com/developers/docs/resources/message)
- [Embed Structure](https://discord.com/developers/docs/resources/message#embed-object)
