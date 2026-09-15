# Example: GitHub Integration

This example shows how to fetch GitHub data and display it on a 16x16 panel.

## Setup

### HTTP Transmitter Configuration

**Website URL:**
```
https://api.github.com/users/partic10
```

**Method:** GET

**Headers:**
```
User-Agent: GitLogic-OS/1.0
Accept: application/vnd.github.v3+json
```

**GET Parameters:** (none required for basic user info)

**Authentication (Optional):**
If you want higher rate limits, add:
```
Authorization: token YOUR_GITHUB_TOKEN
```

### Expected Response:

```json
{
  "login": "partic10",
  "id": 123456789,
  "avatar_url": "https://avatars.githubusercontent.com/u/123456789?v=4",
  "gravatar_id": "",
  "url": "https://api.github.com/users/partic10",
  "html_url": "https://github.com/partic10",
  "followers_url": "https://api.github.com/users/partic10/followers",
  "following_url": "https://api.github.com/users/partic10/following{/other_user}",
  "gists_url": "https://api.github.com/users/partic10/gists{/gist_id}",
  "starred_url": "https://api.github.com/users/partic10/starred{/owner}{/repo}",
  "subscriptions_url": "https://api.github.com/users/partic10/subscriptions",
  "organizations_url": "https://api.github.com/users/partic10/orgs",
  "repos_url": "https://api.github.com/users/partic10/repos",
  "events_url": "https://api.github.com/users/partic10/events{/privacy}",
  "received_events_url": "https://api.github.com/users/partic10/received_events",
  "type": "User",
  "site_admin": false,
  "name": "Participant",
  "company": "DevTeam",
  "blog": "https://example.com",
  "location": "USA",
  "email": null,
  "bio": "Building awesome projects",
  "twitter_username": null,
  "public_repos": 42,
  "public_gists": 5,
  "followers": 150,
  "following": 75,
  "created_at": "2020-01-15T10:30:00Z",
  "updated_at": "2024-01-15T10:30:00Z"
}
```

## Panel Display Examples

### Example 1: Simple User Info (1 Line)

**Position:** `0101`

**Character:** (max 16 chars)
```
"partic10 | 42r  "
```

**Breakdown:**
- `partic10` = username
- `42r` = 42 repositories

---

### Example 2: Multi-Line User Profile (4 Lines)

**Line 1 (Y=1)** - Username & Repos
```
Position: 0101
Character: "partic10  42repo"
```

**Line 2 (Y=2)** - Followers
```
Position: 0102
Character: "Followers: 150  "
```

**Line 3 (Y=3)** - Following
```
Position: 0103
Character: "Following: 75   "
```

**Line 4 (Y=4)** - Location
```
Position: 0104
Character: "Location: USA   "
```

---

## API Endpoints

### Get User Info

```
GET https://api.github.com/users/{username}
```

**Variables:**
- `{username}` = GitHub username (e.g., "partic10")

---

### Get User Repos

```
GET https://api.github.com/users/{username}/repos
```

**GET Parameters:**
```
sort=updated&direction=desc&per_page=10
```

**Response:** Array of 10 most recently updated repos

---

### Get Repo Info

```
GET https://api.github.com/repos/{owner}/{repo}
```

**Example:**
```
GET https://api.github.com/repos/partic10/gitlogic-os-api
```

**Response:**
```json
{
  "name": "gitlogic-os-api",
  "full_name": "partic10/gitlogic-os-api",
  "description": "GitLogic OS - API Documentation",
  "url": "https://github.com/partic10/gitlogic-os-api",
  "stargazers_count": 25,
  "watchers_count": 25,
  "language": "Markdown",
  "forks_count": 5,
  "open_issues_count": 2,
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-01-20T15:45:00Z",
  "pushed_at": "2024-01-20T15:45:00Z"
}
```

---

### Search Repositories

```
GET https://api.github.com/search/repositories
```

**GET Parameters:**
```
q=language:lua+stars:>1000&sort=stars&order=desc
```

---

## Complete Wiring Example

### Scenario: Display User Stats Dashboard

```
┌──────────────────────────────┐
│  Button (Fetch User)         │
└──────────────┬───────────────┘
               │
               ↓
┌──────────────────────────────┐
│  HTTP Transmitter            │
│  GET /users/{username}       │
│  Response: JSON User Data    │
└──────────────┬───────────────┘
               │
        ┌──────┴──────────┐
        │                 │
        ↓                 ↓
   String Parser    String Parser
   (Extract Name)   (Extract Repos)
        │                 │
        └────────┬────────┘
                 │
        ┌────────┴────────┐
        │                 │
        ↓                 ↓
   Panel (0101)     Panel (0102)
   Display Name     Display Repos
```

---

## Testing Steps

### Test 1: Basic User Lookup

1. **Configure Transmitter:**
   - Website: `https://api.github.com/users/octocat`
   - Method: GET
   - Headers: User-Agent: GitLogic-OS/1.0

2. **Connect to Panel:**
   - Transmitter Response → Panel Character Input
   - Panel Position: 0101

3. **Trigger:**
   - Add Button to trigger transmitter
   - Click button

4. **Expected Result:**
   - Panel displays GitHub user data

---

### Test 2: Extract Specific Fields

Use String Parser/Formatter:

```
Input: (Full JSON response)
Extract: "login" field → username
Format: username + " repos: " + public_repos
Output: "octocat repos: 42"
```

---

### Test 3: Display Repository List

```
Website: https://api.github.com/users/octocat/repos
Parameters: sort=updated&per_page=5
```

Parse array and display top 5 repos on lines 1-5.

---

## GitHub API Rate Limits

### Without Authentication
```
Rate Limit: 60 requests per hour
Per IP Address
```

### With Authentication
```
Rate Limit: 5,000 requests per hour
Per User Account
```

### Recommended Intervals
```
Without token: 1 request per minute (60/hr)
With token: 1 request per 30 seconds
```

---

## Getting a GitHub Token (Optional)

1. Go to: https://github.com/settings/tokens
2. Click "Generate new token"
3. Select scopes: `public_repo`, `read:user`
4. Copy token
5. Add to Authorization header:
   ```
   Authorization: token YOUR_TOKEN_HERE
   ```

---

## Example Queries

### Query 1: GitHub User Statistics

```
Website: https://api.github.com/users/partic10
Headers: User-Agent: GitLogic-OS/1.0
```

**Display on Panel:**
```
Line 1: Username + Public Repos
Line 2: Followers + Following
Line 3: Location + Company
Line 4: Created Date
```

---

### Query 2: Repository Details

```
Website: https://api.github.com/repos/partic10/gitlogic-os-api
```

**Display:**
```
Line 1: Repo Name
Line 2: Stars + Forks
Line 3: Language
Line 4: Last Updated
```

---

### Query 3: Recent Activity

```
Website: https://api.github.com/users/partic10/events/public
Parameters: per_page=1
```

**Display:**
```
Latest action by user (e.g., "Pushed code to repo X")
```

---

## Error Handling

### Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| 404 Not Found | Username doesn't exist | Verify username spelling |
| 403 Forbidden | Rate limit exceeded | Add delay or get token |
| 401 Unauthorized | Invalid token | Check token validity |
| 422 Validation Failed | Bad parameters | Verify query format |

### Panel Error Display

```
If Response contains "message":
  → Extract error message
  → Display on Panel: "ERROR: {message}"
  
If Response is empty:
  → Display: "No Data Available"
  
If Status is not 200:
  → Display: "API Error {status_code}"
```

---

## Advanced: Commit History

### Get Recent Commits

```
Website: https://api.github.com/repos/partic10/gitlogic-os-api/commits
Parameters: per_page=5
```

**Response:** Array of 5 most recent commits

**Parse and Display:**
```
Line 1: Latest Commit Message
Line 2: Commit Author
Line 3: Commit Date
Line 4: Commit SHA (first 8 chars)
```

---

## Advanced: Issues & Pull Requests

### Get Open Issues

```
Website: https://api.github.com/repos/partic10/gitlogic-os-api/issues
Parameters: state=open&per_page=10
```

**Display Count on Panel:**
```
"Open Issues: 5      "
```

---

### Get Pull Requests

```
Website: https://api.github.com/repos/partic10/gitlogic-os-api/pulls
Parameters: state=open&per_page=10
```

**Display on Panel:**
```
"Pull Requests: 3   "
```

---

## Security Notes

1. **Public Data Only:** GitHub API returns public data by default
2. **Token Security:** Never commit tokens to repos
3. **Rate Limiting:** Respect API rate limits
4. **Caching:** Cache responses to reduce API calls

---

## Useful GitHub API Endpoints

```
User Info:           /users/{username}
User Repos:          /users/{username}/repos
Repo Details:        /repos/{owner}/{repo}
Commits:             /repos/{owner}/{repo}/commits
Issues:              /repos/{owner}/{repo}/issues
Pull Requests:       /repos/{owner}/{repo}/pulls
Releases:            /repos/{owner}/{repo}/releases
Stargazers:          /repos/{owner}/{repo}/stargazers
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| No data displayed | Check username is correct |
| 404 error | Verify endpoint URL format |
| Rate limit error | Add delay between requests |
| Wrong character encoding | Use ASCII-safe characters |
| Panel truncates data | Limit response to 16 chars per line |

---

## Resources

- [GitHub API Docs](https://docs.github.com/en/rest)
- [API Rate Limits](https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting)
- [Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- [Endpoints Reference](https://docs.github.com/en/rest/reference)
