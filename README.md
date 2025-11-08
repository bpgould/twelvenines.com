# twelvenines.com

A static website built with Cobalt (Rust static site generator) and deployed to GitHub Pages with a custom domain.

## Overview

This project demonstrates modern static site development with:

- **Cobalt** - Fast, flexible Rust-based static site generator
- **GitHub Pages** - Reliable hosting from the `docs/` directory
- **GitHub Actions** - Automated CI/CD for linting and building
- **Pre-commit hooks** - Code quality with prek (markdown linting, whitespace fixing)
- **AWS Route 53** - Custom domain DNS management

## Quick Start

### Prerequisites

- Rust toolchain installed
- Node.js (for markdownlint)

### Installation

```bash
# Install Cobalt
cargo install cobalt-bin

# Install prek globally via Homebrew (for pre-commit hooks)
brew install prek

# Install markdownlint
npm install -g markdownlint-cli2

# Set up pre-commit hooks
prek install
```

### Running Locally

Cobalt includes a built-in development server with **hot reloading** - changes to your source files will automatically trigger a rebuild and refresh your browser.

```bash
cd src
cobalt serve
```

Visit `http://localhost:3000` to see your site. The server will watch for changes and automatically rebuild when you edit files.

### Building

To build the site without running the development server:

```bash
cd src
cobalt build
```

The site will be built to the `docs/` directory.

## Deployment

The site automatically deploys via GitHub Actions:

- **On Pull Request**: Runs markdown linting and build validation
- **On Push to Main**: Builds the site and auto-commits to `docs/` directory

GitHub Pages serves the site from the `docs/` directory on the `main` branch.

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
├── docs/                  # Build output (served by GitHub Pages)
├── .pre-commit-config.yaml # Pre-commit hooks configuration
└── .markdownlint-cli2.yaml # Markdown linting config
```

## License

MIT

## Contributing

Contributions welcome! Please open an issue or pull request.
