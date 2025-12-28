# CFCap
Secure, R2-backed deployment of [Cap](https://github.com/tiagozip/cap) captcha on Cloudflare Workers.

## Features
- **Secure Access Control**: Domain whitelisting.
- **R2 Storage**: Serverless, scalable object storage for challenges (1-day auto-expiry).
- **Configurable TTL**: Customize expiration for challenges (300s) and tokens (330s).

---

## 1. GUI Based Deployment (Cloudflare Dashboard)

**Refined for Ease:** This project includes a setup script that automatically builds the worker and creates the necessary R2 storage buckets for you.

### Step A: Deployment
1.  **Fork** this repository to your GitHub account.
2.  **Update Configuration** (Important):
    *   Open `wrangler.toml` in your repository.
    *   Find the `[vars]` section.
    *   Update the `ALLOWED` variable to restrict access to your domains.
    *   **Examples**:
        *   **Allow Single Domain** (Matches `example.com` ONLY, no subdomains):
            ```toml
            ALLOWED = "example.com"
            ```
        *   **Allow Subdomains** (Matches `any.example.com`, but NOT `example.com`):
            ```toml
            ALLOWED = "*.example.com"
            ```
        *   **Allow Root & Subdomains** (Common Setup):
            ```toml
            ALLOWED = "example.com, *.example.com"
            ```
        *   **Allow Multiple Domains**:
            ```toml
            ALLOWED = "example.com, another-site.org"
            ```
        *   **Allow All** (Public Access):
            ```toml
            ALLOWED = ""
            ```
3.  **Connect & Deploy**:
    *   Go to **Cloudflare Dashboard** > **Workers & Pages**.
    *   Click **Create Application** > **Connect to Git**.
    *   Select your forked repository.
    *   Click **Save and Deploy**.

The build system will automatically run `npm run setup`, creating your `cap-challenges` and `cap-tokens` buckets with the required **1-Day Lifecycle Rule** to auto-delete old data.


---

## 2. CLI Based Deployment (Wrangler)

Use this method if you are comfortable with the command line.

### Step A: Setup
1.  Clone the repo and install dependencies:
    ```bash
    npm install
    ```
2.  Run the automated setup script:
    ```bash
    npm run setup
    ```
    *This will auto-create R2 buckets, Lifecycle rules, and set default Secrets (`ALLOWED`, `TTLs`) ONLY if they don't already exist.*

### Step B: Deploy
```bash
npm run deploy
```

### Step C: Configuration
To verify or change variables for a deploy:
```bash
# Example: Set allowed domains
wrangler vars set ALLOWED "example.com"
```
Or check `wrangler.toml` (note: it does not contain default variables to avoid overwriting your Dashboard settings).
