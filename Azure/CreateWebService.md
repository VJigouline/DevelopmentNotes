# Create a Web Service on Azure — Quick Guide

This guide explains how to create and deploy a web service on Azure. It covers common hosting options, copy-ready PowerShell/Azure CLI commands, simple example apps (Node.js), CI/CD with GitHub Actions, scaling, monitoring, and troubleshooting.

Supported hosting options covered here:
- Azure App Service (managed web apps)
- Azure Functions (serverless)
- Azure Container Apps (serverless containers)
- Azure Kubernetes Service (AKS) — brief notes

---

## Before you begin
- Install Azure CLI and sign in:

```powershell
# Install / update Azure CLI (if needed)
# Windows: https://learn.microsoft.com/cli/azure/install-azure-cli-windows
az version
az login
```

- Create a resource group for your resources:

```powershell
az group create --name my-rg --location eastus
```

- Ensure you have your application code ready. Examples below use a minimal Node.js app.

---

## 1) Azure App Service (recommended for managed web apps)

App Service hosts web apps (Linux/Windows) with built-in scaling, TLS, and easy deployments.

### 1.1 Create an App Service plan and Web App

```powershell
# Create a plan (Linux example, Free tier for testing)
az appservice plan create `
  --name my-app-plan `
  --resource-group my-rg `
  --sku B1 `
  --is-linux

# Create a web app (Node.js runtime)
az webapp create `
  --resource-group my-rg `
  --plan my-app-plan `
  --name my-webapp-name `
  --runtime "NODE|18-lts"
```

### 1.2 Deploy options

Option A — `az webapp up` (quick deploy from folder):

```powershell
# From your app folder. This creates the app and deploys in one step.
az webapp up --name my-webapp-name --resource-group my-rg --location eastus
```

Option B — ZIP deploy (recommended for reproducible deployments):

```powershell
# Build your app into ./dist (or ensure files are ready)
Compress-Archive -Path ./ -DestinationPath site.zip -Force

# Deploy ZIP
az webapp deployment source config-zip `
  --resource-group my-rg `
  --name my-webapp-name `
  --src site.zip
```

Option C — GitHub Actions (see CI/CD section below)

### 1.3 App Settings and connection strings

Set environment variables via CLI:

```powershell
az webapp config appsettings set `
  --resource-group my-rg `
  --name my-webapp-name `
  --settings NODE_ENV=production PORT=8080
```

### 1.4 Test the site

```powershell
# Get the URL
$site = az webapp show --resource-group my-rg --name my-webapp-name --query defaultHostName -o tsv
Write-Host "https://$site"
```

---

## 2) Azure Functions (serverless)

Azure Functions is ideal for event-driven, small HTTP APIs. Use the Functions Core Tools locally for development.

### 2.1 Create a Function App

```powershell
# Create a storage account required by functions
az storage account create --name mystorage$(Get-Random) --location eastus --resource-group my-rg --sku Standard_LRS

# Create function app (Linux, Node runtime)
az functionapp create `
  --resource-group my-rg `
  --consumption-plan-location eastus `
  --name my-func-app-name `
  --storage-account <your-storage-account-name> `
  --runtime node --runtime-version 18
```

### 2.2 Deploy from local project (Functions Core Tools)

Install Functions Core Tools, then:

```powershell
# From your functions project folder
func azure functionapp publish my-func-app-name
```

Or use ZIP deploy similar to App Service.

---

## 3) Azure Container Apps (serverless containers)

Container Apps let you run containers with automatic scaling (including KEDA-based event-driven scaling) without managing Kubernetes directly.

### 3.1 Prereqs: Azure CLI extensions

```powershell
az extension add --name containerapp
az provider register --namespace Microsoft.Web
```

### 3.2 Create environment and deploy container

```powershell
# Create Container Apps environment
az containerapp env create --name my-env --resource-group my-rg --location eastus

# Build and push container to ACR or Docker Hub
# Example using Docker Hub (tag and push):
# docker build -t username/myapp:1.0 .
# docker push username/myapp:1.0

# Create container app
az containerapp create `
  --name my-container-app `
  --resource-group my-rg `
  --environment my-env `
  --image username/myapp:1.0 `
  --ingress external --target-port 8080
```

### 3.3 Deploy from GitHub Actions (Container registry build + push)

See CI/CD section for a workflow example that builds Docker image and deploys to Container Apps.

---

## 4) Azure Kubernetes Service (AKS) — brief notes

Use AKS for full Kubernetes control. It's more complex and suited for production-grade microservices.

High-level steps:
- Create AKS cluster with `az aks create`
- Configure kubectl: `az aks get-credentials`
- Deploy manifests with `kubectl apply -f` or use Helm
- Consider Azure Monitor for containers for monitoring

---

## Example: Minimal Node.js app (index.js)

If you need a sample app to deploy quickly, create `index.js` and `package.json`.

index.js:

```javascript
const http = require('http');
const port = process.env.PORT || 8080;
http.createServer((req, res) => {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello from Azure Web Service');
}).listen(port);
console.log('Listening on', port);
```

package.json (minimal):

```json
{
  "name": "azure-sample",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  }
}
```

Deploy using `az webapp up` or zip deploy as shown above.

---

## CI/CD: GitHub Actions examples

### 1) Deploy Node app to App Service using official action

Create `.github/workflows/deploy-appservice.yml`:

```yaml
name: Build and deploy Node.js app to Azure Web App
on:
  push:
    branches: [ main ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Set up Node
      uses: actions/setup-node@v3
      with:
        node-version: '18'
    - name: Install dependencies
      run: npm ci
    - name: Build
      run: npm run build --if-present
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with:
        app-name: 'my-webapp-name'
        slot-name: 'production'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

To obtain `AZURE_WEBAPP_PUBLISH_PROFILE`, export publish profile from the Azure Portal (Web App → Get publish profile) and add it as a GitHub secret.

### 2) Build & deploy container to Azure Container Apps

Create `.github/workflows/containerapp-deploy.yml`:

```yaml
name: Build and deploy to Azure Container Apps
on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Build and push image
      run: |
        docker build -t myregistry.azurecr.io/myapp:${{ github.sha }} .
        echo ${{ secrets.ACR_PASSWORD }} | docker login myregistry.azurecr.io -u ${{ secrets.ACR_USERNAME }} --password-stdin
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}
    - name: Deploy to Container Apps
      run: |
        az containerapp update --name my-container-app --resource-group my-rg --image myregistry.azurecr.io/myapp:${{ github.sha }}
```

For this workflow create an Azure service principal and store its JSON as `AZURE_CREDENTIALS` in GitHub Secrets.

---

## Monitoring, logging, and scaling

- App Service: enable Application Insights for request tracing, performance, and logs.
  - Turn on from Portal or CLI: `az monitor app-insights component create ...`
- Functions: use built-in monitoring (Application Insights) and live metrics.
- Container Apps & AKS: use Azure Monitor for containers and Log Analytics workspace.

Scale
- App Service: scale up (plan SKU) or scale out (instances) via `az appservice plan update --number-of-workers` or set autoscale rules in Portal.
- Container Apps: configure KEDA triggers (concurrency, CPU, queue length) to scale to zero when idle.
- AKS: use Cluster Autoscaler.

---

## Security & networking

- Use Managed Identities for Azure resource access rather than embedding secrets.
- Store secrets in Azure Key Vault; reference them from App Service or Container Apps.
- Use Application Gateway or Azure Front Door for WAF and global routing.
- Place services in a VNet if needed for private networking.

---

## Troubleshooting common issues

- App not starting: check logs:

```powershell
az webapp log tail --name my-webapp-name --resource-group my-rg
```

- Deployment fails: inspect GitHub Actions logs, or run `az webapp deployment source show`.
- Port binding: ensure your app listens on `process.env.PORT` for App Service and Container Apps.
- Storage/permission errors for Functions: ensure the created storage account exists and is accessible.

---

## Clean up resources

To avoid unwanted charges, delete the resource group when you're done.

```powershell
az group delete --name my-rg --yes --no-wait
```

---

## Further reading
- App Service docs: https://learn.microsoft.com/azure/app-service/
- Azure Functions docs: https://learn.microsoft.com/azure/azure-functions/
- Azure Container Apps: https://learn.microsoft.com/azure/container-apps/
- AKS docs: https://learn.microsoft.com/azure/aks/
- Azure CLI reference: https://learn.microsoft.com/cli/azure/

---

If you'd like, I can:
- Scaffold a minimal Node.js app and add deployment scripts for App Service or Container Apps in this repo, or
- Create a sample GitHub Actions workflow file in `.github/workflows/` tailored to your project.

Which would you like me to do next?
