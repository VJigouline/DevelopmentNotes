# Create a Static Website on Azure

This guide explains how to deploy a static website to Azure using three approaches:
1. **Azure Static Web Apps** (SWA) — recommended for modern SPAs with CI/CD
2. **Azure Storage + Azure CDN** — simple, cost-effective file hosting
3. **Azure App Service** — for more control or if you need server-side logic

We'll cover setup via Azure Portal, Azure CLI, and GitHub Actions automation.

---

## Prerequisites

- An Azure account (free tier available at https://azure.microsoft.com/free/)
- Azure CLI installed (download from https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows)
- VS Code or your preferred editor
- A static site (HTML/CSS/JS, or a built SPA like Angular, React, Vue)

---

## Approach 1: Azure Static Web Apps (Recommended)

Azure Static Web Apps (SWA) is purpose-built for hosting modern static sites and SPAs. It includes:
- Free SSL/TLS certificates
- Built-in CI/CD via GitHub Actions
- APIs (Azure Functions backend)
- Authentication support
- Global CDN

### Step 1a: Create a Static Web App (Azure Portal)

1. Go to https://portal.azure.com
2. Click "Create a resource"
3. Search for "Static Web App" and select it
4. Click "Create"
5. Fill in:
   - **Resource Group**: Create new (e.g., `my-rg`) or use existing
   - **Name**: e.g., `my-static-site`
   - **Hosting plan**: Free tier (or Standard)
   - **Region**: Choose closest to your users
   - **Source**: GitHub (you'll authorize GitHub and select your repo)
6. After authorizing GitHub, select:
   - **Organization**: your GitHub account
   - **Repository**: your site repo
   - **Branch**: `main` (or your preferred branch)
   - **Build presets**: Angular, React, Vue, etc. (or Custom if plain HTML)
   - **App location**: `./` (or path to your built app, e.g., `./dist`)
   - **API location**: (leave blank if no backend)
   - **Output location**: `./` (or `dist`, `build`, etc.)
7. Click "Review + Create" → "Create"

Azure generates a GitHub Actions workflow and deploys your site. Monitor in the **Actions** tab on GitHub.

Your site is live at: `https://<random-name>.azurestaticapps.net`

### Step 1b: Deploy via Azure CLI (non-GitHub)

If your repo isn't on GitHub, use the CLI:

```powershell
# Login to Azure
az login

# Create a resource group
az group create --name my-rg --location eastus

# Create a Static Web App (upload local files)
az staticwebapp create `
  --name my-static-site `
  --resource-group my-rg `
  --location eastus `
  --sku Free

# Get the deployment token
$token = az staticwebapp secrets list --name my-static-site --resource-group my-rg --query properties.apiKey -o tsv

# Deploy your files
npm install -g @azure/static-web-apps-cli
swa deploy --app-location ./dist --api-location ./api --deployment-token $token
```

### Step 1c: Add a custom domain

1. In the Azure Portal, go to your Static Web App resource
2. Click "Custom domains"
3. Click "Add custom domain"
4. Enter your domain (e.g., `www.example.com`)
5. Azure will provide DNS records to add to your domain registrar
6. Add the DNS records and wait for verification

---

## Approach 2: Azure Storage + CDN (Simple & Cost-Effective)

Good for simple static HTML sites without CI/CD requirements.

### Step 2a: Create a Storage Account

```powershell
az login
az group create --name my-rg --location eastus

# Create storage account
az storage account create `
  --name mystorageaccount `
  --resource-group my-rg `
  --location eastus `
  --sku Standard_LRS

# Enable static website hosting
az storage blob service-properties update `
  --account-name mystorageaccount `
  --static-website `
  --index-document index.html `
  --404-document 404.html
```

### Step 2b: Upload your files

```powershell
# Get storage account connection string
$connString = az storage account show-connection-string `
  --name mystorageaccount `
  --resource-group my-rg `
  --query connectionString -o tsv

# Upload files
az storage blob upload-batch `
  --connection-string $connString `
  --destination '$web' `
  --source ./dist
```

### Step 2c: Get the storage URL

```powershell
$storageUrl = az storage account show `
  --name mystorageaccount `
  --resource-group my-rg `
  --query primaryEndpoints.web -o tsv

Write-Host "Site URL: $storageUrl"
```

Your site is now live at that URL (e.g., `https://mystorageaccount.z13.web.core.windows.net/`).

### Step 2d: Add CDN (optional, for performance)

```powershell
# Create a CDN profile
az cdn profile create `
  --resource-group my-rg `
  --name my-cdn-profile `
  --sku Standard_Microsoft

# Create a CDN endpoint
az cdn endpoint create `
  --resource-group my-rg `
  --profile-name my-cdn-profile `
  --name my-cdn-endpoint `
  --origin $storageUrl
```

Your CDN URL: `https://my-cdn-endpoint.azureedge.net`

---

## Approach 3: Azure App Service (Web App)

Useful if you need server-side logic or more control.

### Step 3a: Create an App Service plan and Web App

```powershell
# Create App Service plan (Free tier)
az appservice plan create `
  --name my-app-plan `
  --resource-group my-rg `
  --sku F1

# Create web app
az webapp create `
  --resource-group my-rg `
  --plan my-app-plan `
  --name my-static-site-app
```

### Step 3b: Deploy files

Option 1: Upload via ZIP (PowerShell):

```powershell
# Compress your site
Compress-Archive -Path ./dist/* -DestinationPath site.zip

# Deploy via CLI
az webapp deployment source config-zip `
  --resource-group my-rg `
  --name my-static-site-app `
  --src site.zip
```

Option 2: Use GitHub Actions (same pattern as SWA).

Your site: `https://my-static-site-app.azurewebsites.net`

---

## Deployment automation with GitHub Actions

Recommended for all approaches: automate builds and deployments.

### SWA GitHub Actions (Auto-generated)

Azure SWA generates this automatically when you select GitHub as the source. It's in `.github/workflows/azure-static-web-apps-*.yml`.

### Storage + CDN GitHub Actions (manual)

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Azure Storage

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Build site (optional)
      run: |
        npm install
        npm run build
    
    - name: Azure login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - name: Upload to Azure Storage
      uses: azure/CLI@v1
      with:
        inlineScript: |
          az storage blob upload-batch \
            --account-name ${{ secrets.STORAGE_ACCOUNT }} \
            --destination '$web' \
            --source ./dist \
            --overwrite
    
    - name: Purge CDN cache
      run: |
        az cdn endpoint purge \
          --resource-group my-rg \
          --profile-name my-cdn-profile \
          --name my-cdn-endpoint \
          --content-paths "/*"
```

Add Azure credentials to GitHub Secrets:

```powershell
# Generate service principal credentials
az ad sp create-for-rbac `
  --name "github-actions" `
  --role contributor `
  --scopes /subscriptions/<subscription-id>/resourceGroups/my-rg `
  --json-auth
```

Copy the JSON output and add it as a GitHub Secret called `AZURE_CREDENTIALS`.

---

## Custom domains and SSL

All Azure approaches support custom domains with free HTTPS certificates.

### For Static Web Apps

1. Portal → Static Web App → Custom domains → Add custom domain
2. Follow the DNS verification steps

### For Storage + CDN

1. CDN Endpoint → Custom domains → Add custom domain
2. Configure DNS records

---

## Monitoring and troubleshooting

- **Site not loading**: check the resource status in Azure Portal. Verify files are uploaded (check Storage > Containers for SWA/Storage approaches).
- **Old content showing**: purge CDN cache via Portal or CLI.
- **Build failed**: check GitHub Actions logs or Azure Portal Activity.
- **Performance issues**: enable CDN, set appropriate cache headers in your HTML/headers config.

---

## Cost estimates (Free tier)

- **Azure Static Web Apps**: Free tier (very limited) or ~$9/month Standard
- **Azure Storage**: ~$0.50/month for a small site
- **Azure CDN**: ~$0.17 per GB (minimum charge)
- **App Service**: Free tier (F1) has limitations; ~$5–$10/month for production

---

## Comparison

| Approach | Best for | Cost | Effort |
|----------|----------|------|--------|
| **Static Web Apps** | Modern SPAs, CI/CD | Free–$9/month | Low (auto CI/CD) |
| **Storage + CDN** | Simple static sites | $0.50–$1/month | Low (manual upload) |
| **App Service** | More control, backend logic | $0–$100+/month | Medium |

---

## Quick reference commands

```powershell
# Login
az login

# Create resource group
az group create --name my-rg --location eastus

# Create Static Web App
az staticwebapp create --name my-site --resource-group my-rg --location eastus

# Create storage account and enable static hosting
az storage account create --name myaccount --resource-group my-rg --location eastus
az storage blob service-properties update --account-name myaccount --static-website

# Upload files
az storage blob upload-batch --account-name myaccount --destination '$web' --source ./dist

# List resources
az staticwebapp list --resource-group my-rg
az storage account list --resource-group my-rg

# Delete resource group (and all resources)
az group delete --name my-rg
```

---

## Resources

- Azure Static Web Apps: https://learn.microsoft.com/en-us/azure/static-web-apps/
- Azure Storage static website: https://learn.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website
- Azure App Service: https://learn.microsoft.com/en-us/azure/app-service/
- Azure CLI: https://learn.microsoft.com/en-us/cli/azure/

---

**Recommendation**: Use **Azure Static Web Apps** for modern SPAs with GitHub integration; use **Storage + CDN** for simple static sites you update manually.
