---
name: api-health-check
description: Check health of API endpoints with response time, status codes, and body validation in a dashboard-style report
---

# API Health Check

Monitor the health of your API endpoints by checking response time, status codes, and optionally validating response bodies. Outputs a clear dashboard-style report. Useful for morning checks, post-deploy verification, or debugging connectivity issues.

## When to Use

- Morning production health check before starting work
- After deploying a new version to verify all endpoints respond correctly
- When users report issues and you need to quickly assess API status
- Periodic monitoring of third-party APIs your app depends on

## How It Works

### Step 1: Gather Endpoints

**Option A: From a config file**

Look for endpoint lists in common locations:
```bash
# Check for common config files
ls -la api-health.json health-check.json .api-endpoints.json 2>/dev/null
```

Expected config format:
```json
{
  "endpoints": [
    { "name": "Auth API", "url": "https://api.example.com/health", "method": "GET", "expect_status": 200 },
    { "name": "User Service", "url": "https://api.example.com/v1/users/me", "method": "GET", "headers": { "Authorization": "Bearer $API_TOKEN" }, "expect_status": 200 }
  ]
}
```

**Option B: Manual input**

Accept a list of URLs from the user, e.g.:
```
Check these APIs:
- https://api.example.com/health
- https://api.example.com/v1/status
- https://staging.example.com/ping
```

### Step 2: Check Each Endpoint

For each endpoint, run a curl command:

```bash
curl -s -o /dev/null -w "%{http_code} %{time_total}" -X GET "{url}" -H "Accept: application/json" --max-time 10
```

Capture:
- **HTTP status code** (200, 404, 500, etc.)
- **Response time** in milliseconds
- **Response body** (first 500 chars for validation)

For endpoints requiring authentication:
- Check environment variables for tokens
- If no token available, skip auth endpoints and note them as "skipped (no credentials)"

### Step 3: Validate Responses

For each response, check:
1. **Status code** matches expected (default: 200)
2. **Response time** is under threshold (default: 2000ms)
3. **Response body** contains expected fields (if specified in config)

Classify each endpoint:
- `HEALTHY` — correct status, fast response, valid body
- `DEGRADED` — correct status but slow response (>1000ms)
- `DOWN` — wrong status code or connection failure
- `TIMEOUT` — no response within 10 seconds
- `SKIPPED` — could not test (missing credentials, etc.)

### Step 4: Generate Dashboard Report

Output format: Overview table (status counts), Endpoint Details table (name, status, code, response time, notes), Issues Requiring Attention (list DOWN/DEGRADED with diagnosis), Recommended Actions (actionable checklist).

If a previous health check exists in the conversation, highlight status changes and response time trends.

### Error Handling

- If curl is not available, fall back to other HTTP tools available in the environment
- If an endpoint requires a VPN or internal network, note it as "unreachable (network)" not "DOWN"
- If all endpoints fail, check internet connectivity first (`curl -s https://www.google.com`)
- Rate-limit checks to avoid triggering DDoS protection (1 request per second)
- Never log or display full authentication tokens in the report — mask them as `Bearer ***`
- If the config file contains secrets, warn the user not to commit it

## Example

**User input:**
```
Check if our production APIs are healthy:
- https://api.gugenlab.com/health
- https://api.gugenlab.com/v1/pods/status
- https://egg-os.gugenlab.com/api/ping
```

**Output:**
```markdown
# API Health Check — 2026-04-11 09:00 JST

## Overview
| Status | Count |
|--------|-------|
| HEALTHY | 2 |
| DEGRADED | 1 |

## Endpoint Details

| # | Endpoint | Status | Code | Response Time |
|---|----------|--------|------|--------------|
| 1 | Main API Health | HEALTHY | 200 | 127ms |
| 2 | Pod Status API | HEALTHY | 200 | 245ms |
| 3 | EGG OS Ping | DEGRADED | 200 | 1,520ms |

## Issues Requiring Attention
1. **EGG OS Ping (DEGRADED):** 1,520ms response time. May indicate high server load.

All critical endpoints operational. No immediate action required.
```
