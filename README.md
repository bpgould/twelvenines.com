# twelvenines.com

A static website built with Cobalt (Rust static site generator) and deployed to GitHub Pages with a custom domain.

## Overview

This project demonstrates modern static site development with:

- **Cobalt** - Fast, flexible Rust-based static site generator
- **GitHub Pages** - Reliable hosting from the `site/` directory
- **GitHub Actions** - Automated CI/CD for linting and building
- **Pre-commit hooks** - Code quality with prek (markdown linting, whitespace fixing)
- **AWS Route 53** - Custom domain DNS management

## Quick Start

### Prerequisites

- Rust toolchain installed
- Node.js (for markdownlint)

### Development

#### Option 1: Using Cargo (Recommended)

This project includes a `Cargo.toml` to manage tool versions:

```bash
# Install prek globally via Homebrew (for pre-commit hooks)
brew install prek

# Build the project (installs cobalt-bin)
cargo build

# Install markdownlint
npm install -g markdownlint-cli2

# Set up pre-commit hooks
prek install

# Serve locally with live reload
cd src
cargo cobalt serve
```

#### Option 2: Global Installation

```bash
# Install prek via Homebrew
brew install prek

# Install Cobalt globally
cargo install cobalt-bin

# Install markdownlint
npm install -g markdownlint-cli2

# Set up pre-commit hooks
prek install

# Serve locally with live reload
cd src
cobalt serve
```

Visit `http://localhost:3000` to see your site.

### Building

Using Cargo:

```bash
cd src
cargo cobalt build
```

Or with global installation:

```bash
cd src
cobalt build
```

The site will be built to the `site/` directory.

## Deployment

The site automatically deploys via GitHub Actions:

- **On Pull Request**: Runs markdown linting and build validation
- **On Push to Main**: Builds the site and auto-commits to `site/` directory

GitHub Pages serves the site from the `site/` directory on the `main` branch.

## Setup Instructions

For detailed setup instructions including GitHub Pages configuration and AWS Route 53 DNS setup, see [SETUP.md](SETUP.md).

## Project Structure

```text
├── .github/workflows/     # GitHub Actions workflows
├── src/                   # Cobalt source files
│   ├── _cobalt.yml       # Cobalt configuration
│   ├── _layouts/         # HTML templates
│   ├── posts/            # Blog posts
│   └── index.md          # Homepage
├── site/                  # Build output (served by GitHub Pages)
├── .pre-commit-config.yaml # Pre-commit hooks configuration
└── .markdownlint-cli2.yaml # Markdown linting config
```

## License

MIT

## Contributing

Contributions welcome! Please open an issue or pull request.
