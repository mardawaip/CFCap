# CFCap Authentication Guide

This document explains how to configure and use Basic Authentication in CFCap.

## Configuration

Authentication is configured in `wrangler.toml` using environment variables:

### Option 1: Basic Auth (Username & Password)

```toml
[vars]
BASIC_AUTH_USERNAME = "admin"
BASIC_AUTH_PASSWORD = "your_secure_password"
```

### Option 2: API Key (Bearer Token)

```toml
[vars]
ALLOWED_API_KEYS = "key1,key2,key3"
```

You can use multiple API keys separated by commas.

### Option 3: Multiple API Keys

```toml
[vars]
ALLOWED_API_KEYS = "sk_prod_abc123,sk_dev_xyz789,sk_test_uvw456"
```

## Usage

### With Basic Auth

Include the `Authorization` header with Base64-encoded credentials:

```bash
# Using curl
curl -X POST https://your-api.com/api/challenge \
  -H "Authorization: Basic $(echo -n 'admin:your_secure_password' | base64)"
```

**JavaScript/Fetch Example:**
```javascript
const username = "admin";
const password = "your_secure_password";
const credentials = btoa(`${username}:${password}`);

fetch('https://your-api.com/api/challenge', {
  method: 'POST',
  headers: {
    'Authorization': `Basic ${credentials}`,
    'Content-Type': 'application/json'
  }
});
```

### With API Key (Bearer Token)

Include the `Authorization` header with your API key:

```bash
# Using curl
curl -X POST https://your-api.com/api/challenge \
  -H "Authorization: Bearer sk_prod_abc123"
```

**JavaScript/Fetch Example:**
```javascript
fetch('https://your-api.com/api/challenge', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer sk_prod_abc123',
    'Content-Type': 'application/json'
  }
});
```

## Security Notes

1. **Store credentials in environment variables** - Never hardcode credentials in your code
2. **For production**, use Cloudflare's Secrets feature for sensitive data:
   ```bash
   wrangler secret put BASIC_AUTH_PASSWORD
   wrangler secret put ALLOWED_API_KEYS
   ```
3. **Use strong passwords** - Ensure `BASIC_AUTH_PASSWORD` is sufficiently complex
4. **API Keys** - Generate and rotate API keys regularly
5. **HTTPS only** - Always use HTTPS to prevent credentials from being sent in plain text

## Protected Endpoints

Authentication is required for:
- `POST /api/challenge`
- `POST /api/redeem`

These endpoints are **NOT** protected (server-to-server calls):
- `POST /api/validate`
- `POST /api/verify`
- `POST /api/delete`

## Disabling Authentication

Leave the authentication variables empty to disable authentication:

```toml
[vars]
BASIC_AUTH_USERNAME = ""
BASIC_AUTH_PASSWORD = ""
ALLOWED_API_KEYS = ""
```

## Error Responses

**Missing/Invalid Credentials:**
```json
HTTP 401 Unauthorized
{
  "error": "Unauthorized"
}
```

**Access Control (Origin/Referer) Failure:**
```
HTTP 403 Forbidden
```
