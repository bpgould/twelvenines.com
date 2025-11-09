---
layout: default.html
title: How I Built My Fourth Blog Site
description: A complete walkthrough of building a static blog with Cobalt (Rust), GitHub Pages, GitHub Actions, AWS Route 53, and modern development practices.
published_date: 2025-11-09 00:00:00 +0000
---

After building three previous blogs (React, Hugo, Jekyll), I decided to start fresh with a focus on simplicity, speed, and modern tooling. This is the story of how I built this site - a static website powered by Rust, deployed to GitHub Pages, with a custom domain and full CI/CD automation.

## Why Another Blog?

Third time's the charm, right? I wanted:

- **Speed**: Static sites are fast. No database, no server-side processing.
- **Reliability**: Simple architecture means fewer points of failure.
- **Modern tooling**: Rust-based static site generator, GitHub Actions for CI/CD.
- **Learning**: An excuse to explore Cobalt and improve my DevOps skills.

## Technology Stack

- **[Cobalt](https://cobalt-org.github.io/)** - Rust-based static site generator
- **GitHub Pages** - Free, reliable hosting from the `docs/` directory
- **GitHub Actions** - Automated builds, tests, and deployments
- **[prek](https://github.com/axodotdev/cargo-dist)** - Pre-commit hooks for code quality
- **AWS Route 53** - DNS management for custom domain
- **markdownlint** - Consistent markdown formatting

## Local Development Setup

### Prerequisites

- [Rust](https://www.rust-lang.org/tools/install) installed on your system
- Git installed and configured
- Node.js (for markdownlint)

### Installation

The project uses a `.tool-versions` file to track the Cobalt version, ensuring consistency between local and CI environments.

```bash
# Install Cobalt (cross-platform)
VERSION=$(grep cobalt-bin .tool-versions | awk '{print $2}')
cargo install cobalt-bin --version ${VERSION}
cobalt --version

# Install prek (pre-commit hooks)
brew install prek

# Install markdownlint
npm install -g markdownlint-cli2

# Set up pre-commit hooks
prek install
```

### Running Locally

Cobalt includes a built-in development server with live reload:

```bash
cd src
cobalt serve
```

Visit `http://localhost:1024` to see your site. The server watches for changes and automatically rebuilds.

### Building

To build the site without running the dev server:

```bash
cd src
cobalt build
```

This generates the static site in the `docs/` directory.

## GitHub Pages Setup

GitHub Pages makes hosting static sites trivial. Here's how I configured it:

1. **Push code to GitHub**:

   ```bash
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

2. **Configure GitHub Pages**:
   - Navigate to **Settings** → **Pages**
   - Source: **Deploy from a branch**
   - Branch: **main**
   - Folder: **/docs**
   - Click **Save**

3. **Add custom domain**:
   - In the same Pages settings
   - Custom domain: `twelvenines.com`
   - Click **Save**
   - Check **Enforce HTTPS** once DNS is configured

## AWS Route 53 DNS Configuration

Setting up a custom domain requires DNS configuration. Here's my step-by-step process:

### Create Hosted Zone

1. Log in to AWS Console → Route 53
2. Click **Hosted zones** → **Create hosted zone**
3. Enter domain: `twelvenines.com`
4. Type: **Public hosted zone**
5. Click **Create**

### Update Name Servers

After creating the hosted zone, Route 53 provides 4 NS (Name Server) records:

- `ns-1234.awsdns-12.org`
- `ns-5678.awsdns-34.com`
- etc.

If registered with AWS Route 53:

1. Go to **Registered domains** → click your domain
2. Verify name servers match the hosted zone

If registered elsewhere (GoDaddy, Namecheap, etc.):

1. Log in to your registrar
2. Update DNS/Name Server settings
3. Replace with the 4 NS records from Route 53
4. Save changes (propagation takes up to 48 hours)

### Create DNS Records

**A Records (apex domain):**

1. Click **Create record**
2. Leave **Record name** blank
3. Type: **A record**
4. Value (add all 4 GitHub Pages IPs):

   ```bash
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```

5. TTL: 300
6. Click **Create records**

**CNAME Record (www subdomain):**

1. Click **Create record**
2. Record name: `www`
3. Type: **CNAME**
4. Value: `yourusername.github.io` (replace with your GitHub username)
5. TTL: 300
6. Click **Create records**

### Verify DNS

Wait a few minutes, then verify:

```bash
dig twelvenines.com
dig www.twelvenines.com
```

You should see the GitHub Pages IPs for the apex domain and a CNAME for www.

## GitHub Actions CI/CD

I set up two workflows to automate everything:

### PR Checks

Runs on every pull request:

- Markdown linting with markdownlint-cli2
- Build validation to ensure the site builds successfully

```yaml
name: PR Checks
on:
  pull_request:
    branches: [main]
jobs:
  markdown-lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm install -g markdownlint-cli2
      - run: markdownlint-cli2 "**/*.md"
```

### Build and Deploy

Runs on every push to main:

- Downloads pre-built Cobalt binary (~10 seconds)
- Builds the site
- Auto-commits to `docs/` directory

```yaml
name: Build and Deploy
on:
  push:
    branches: [main]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Install Cobalt
        run: |
          VERSION=$(grep cobalt-bin .tool-versions | awk '{print $2}')
          wget https://github.com/cobalt-org/cobalt.rs/releases/download/v${VERSION}/cobalt-v${VERSION}-x86_64-unknown-linux-gnu.tar.gz
          tar xf cobalt-v${VERSION}-x86_64-unknown-linux-gnu.tar.gz
          sudo mv cobalt /usr/local/bin/
      - name: Build site
        run: cd src && cobalt build
      - uses: stefanzweifel/git-auto-commit-action@v5
        with:
          commit_message: "Auto-build site from source [skip ci]"
          file_pattern: "docs/*"
```

## Pre-commit Hooks

I use prek to enforce code quality before commits:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: end-of-file-fixer
      - id: trailing-whitespace
      - id: mixed-line-ending
        args: ['--fix=lf']

  - repo: https://github.com/DavidAnson/markdownlint-cli2
    rev: v0.12.1
    hooks:
      - id: markdownlint-cli2
        args: ['--fix']
```

## Design Choices

### Windows XP-Inspired Theme

I went with a retro navy blue and gray theme reminiscent of Windows XP:

- Monospace fonts (Courier New) throughout
- Navy gradient header
- Light gray content areas
- Muted colors to reduce eye strain

The nostalgia factor was too strong to resist.

### Version Management

Using a `.tool-versions` file to track Cobalt version:

```bash
cobalt-bin 0.20.0
```

This ensures local development and CI use the same version.

### Fast CI Builds

Instead of compiling Cobalt from source (5-10 minutes), I download pre-built binaries:

- **CI build time**: ~10 seconds
- **Local install**: Uses `cargo install` for cross-platform compatibility

## Project Structure

```text
├── .github/workflows/    # GitHub Actions
├── src/                  # Cobalt source files
│   ├── _cobalt.yml      # Configuration
│   ├── _layouts/        # HTML templates
│   ├── assets/          # Images, favicons
│   ├── posts/           # Blog posts
│   └── index.md         # Homepage
├── docs/                 # Build output (GitHub Pages)
├── .tool-versions       # Version tracking
├── .pre-commit-config.yaml
└── .markdownlint-cli2.yaml
```

## Licensing

I chose a dual-license approach:

- **Code/Infrastructure**: MIT License
- **Content**: CC BY 4.0 (allows sharing with attribution)

This lets people reuse the site setup while ensuring I get credit for my writing.

## Resources

- [Cobalt Documentation](https://cobalt-org.github.io/docs/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [AWS Route 53 Documentation](https://docs.aws.amazon.com/route53/)
- [Source code for this site](https://github.com/bgould/twelvenines.com)
