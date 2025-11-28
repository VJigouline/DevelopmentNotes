# Create a GitHub Pages Website

This guide explains how to create and deploy a website using GitHub Pages, including static HTML sites, Jekyll blogs, custom domains, and automated CI/CD with GitHub Actions.

## Quick overview

GitHub Pages is a free static site hosting service that deploys directly from a GitHub repository. You can:
- Host a personal/organization site (`username.github.io`)
- Host project documentation (on a branch or `/docs` folder)
- Use Jekyll (built-in) or bring your own static site generator (Hugo, MkDocs, Next.js, etc.)

---

## 1) Create a GitHub Pages repository (user/org site)

### Step 1a: Create a new repository

1. Go to https://github.com/new
2. Name it `<username>.github.io` (e.g., `john-doe.github.io`)
3. Keep it **Public** (required for free GitHub Pages)
4. Initialize with a README

### Step 1b: Clone and add content

```powershell
git clone https://github.com/username/username.github.io.git
cd username.github.io
# Add an index.html file
echo "<h1>Hello, World!</h1>" > index.html
git add index.html
git commit -m "Add index.html"
git push origin main
```

### Step 1c: View your site

Your site is now live at **`https://username.github.io`**

Within a few moments, you'll see your HTML rendered.

---

## 2) Project site (docs in a repository)

If you have an existing project and want to host documentation, create a `docs/` folder and enable Pages for it.

### Step 2a: Create docs folder

```powershell
mkdir docs
echo "<h1>Project Documentation</h1>" > docs/index.html
git add docs/
git commit -m "Add documentation"
git push origin main
```

### Step 2b: Enable GitHub Pages in repo settings

1. Go to your repository on GitHub
2. Settings → Pages
3. Under "Build and deployment", set "Source" to "Deploy from a branch"
4. Select branch: `main` (or `master`)
5. Folder: `/ (root)` or `/docs`
6. Click Save

Your site will be available at **`https://username.github.io/project-name`**

---

## 3) Using Jekyll (GitHub Pages default)

GitHub Pages has built-in Jekyll support. Just add a `_config.yml` and Markdown files.

### Create a basic Jekyll site

```powershell
mkdir my-jekyll-site
cd my-jekyll-site
echo "title: My Site" > _config.yml
echo "# Home" > index.md
git init
git add .
git commit -m "Initial Jekyll site"
git remote add origin https://github.com/username/my-jekyll-site.git
git push -u origin main
```

Then enable GitHub Pages (as in Step 2b) and GitHub will automatically build and serve your site.

### Jekyll front matter (metadata)

Each Markdown file needs YAML front matter:

```markdown
---
layout: page
title: About Me
permalink: /about/
---

# About Me

This is my about page.
```

### Popular Jekyll themes

1. Go to repo Settings → Pages → Themes
2. Pick a built-in theme (Minimal, Dark, Cayman, etc.)
3. GitHub auto-generates `_config.yml` with the theme

Or use a remote theme in `_config.yml`:

```yaml
remote_theme: pages-themes/minimal@v0.2.0
```

See https://pages.github.com/themes/ for more.

---

## 4) Custom domain

If you own a domain, point it to GitHub Pages.

### 4a: DNS configuration (Namecheap, Route53, Google Domains, etc.)

For `example.com`, create DNS records pointing to GitHub's servers:

- For `example.com` (apex domain), use 4 A records (IP addresses provided by GitHub):
  - `185.199.108.153`
  - `185.199.109.153`
  - `185.199.110.153`
  - `185.199.111.153`

- Or, for `www.example.com` (www subdomain), use a CNAME record:
  - CNAME → `username.github.io`

Check GitHub's docs for current IPs: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site

### 4b: GitHub Pages settings

1. Go to repo Settings → Pages
2. Under "Custom domain", enter `example.com` or `www.example.com`
3. Click Save
4. GitHub creates a `CNAME` file in your repo with your custom domain

### 4c: Enforce HTTPS

After adding a custom domain, wait a few minutes for the DNS to propagate. Then:

1. In repo Settings → Pages
2. Check "Enforce HTTPS"

---

## 5) Use a static site generator (Hugo, MkDocs, Next.js, etc.)

GitHub Pages can host any static HTML site, not just Jekyll. Build locally or use GitHub Actions.

### Option A: Build locally and commit

```powershell
# Example with MkDocs
mkdocs build
# output is in site/
git add site/
git commit -m "Build site"
git push
```

Then enable Pages to deploy from the `site/` folder or `/docs` folder if you rename it.

### Option B: Use GitHub Actions (recommended)

GitHub Actions can automatically build your site on every push.

Example workflow file (`.github/workflows/build.yml`):

```yaml
name: Build and Deploy

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Install dependencies
      run: |
        pip install mkdocs
    
    - name: Build site
      run: mkdocs build
    
    - name: Deploy
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./site
```

This workflow:
1. Checks out your repo
2. Installs dependencies
3. Builds the site (e.g., `mkdocs build`)
4. Deploys to `gh-pages` branch

Then, in repo Settings → Pages, set the source to the `gh-pages` branch.

---

## 6) Example workflows

### Static HTML blog

```
.
├── index.html
├── posts/
│   ├── post1.html
│   └── post2.html
└── styles.css
```

Enable Pages for root folder, and you're done.

### Jekyll blog

```
.
├── _config.yml
├── _posts/
│   ├── 2025-01-01-first-post.md
│   └── 2025-01-05-another.md
├── index.md
└── about.md
```

Enable Pages, and GitHub builds Jekyll automatically.

### MkDocs documentation site

```
.
├── mkdocs.yml
├── docs/
│   ├── index.md
│   ├── guide.md
│   └── api.md
```

Use GitHub Actions (as shown above) to build and deploy to `gh-pages` branch.

### Angular / React SPA

Build your app with `npm run build` (output in `dist/` or `build/`), then deploy via GitHub Actions:

```yaml
- name: Build app
  run: npm run build

- name: Deploy
  uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./dist
```

**Important**: For SPA routing, configure a fallback to `index.html`. Add to GitHub Pages settings or use a `404.html` redirect.

---

## 7) Troubleshooting

- **Site not loading**: wait a few minutes for GitHub to build and deploy. Check Actions tab for build errors.
- **Custom domain not working**: verify DNS records are correct and propagated. Use `nslookup example.com` or a DNS checker.
- **Old content showing**: clear browser cache or do a hard refresh (Ctrl+Shift+R in Windows).
- **Build failed**: check the Actions tab and read the error log. Common issues: Jekyll theme not found, bad front matter, or missing dependencies.

---

## 8) Common commands

```powershell
# Clone your Pages repo
git clone https://github.com/username/username.github.io.git

# Test Jekyll locally
gem install bundler jekyll
jekyll serve  # localhost:4000

# Test MkDocs locally
mkdocs serve  # localhost:8000

# Check GitHub Actions logs
# Go to repo → Actions → Latest run
```

---

## 9) Resources

- GitHub Pages docs: https://pages.github.com/
- GitHub Pages with custom domain: https://docs.github.com/en/pages
- Jekyll documentation: https://jekyllrb.com/
- GitHub Actions: https://docs.github.com/en/actions

---

**Next steps:**
1. Create a `<username>.github.io` repository.
2. Add an `index.html` or `index.md` and push.
3. Visit `https://username.github.io` and verify your site is live.
4. (Optional) Set up a custom domain or add a GitHub Actions workflow for automated builds.
