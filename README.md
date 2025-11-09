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
# Install cargo-binstall (faster than cargo install)
cargo install cargo-binstall

# Install Cobalt (using version from .tool-versions)
VERSION=$(grep cobalt-bin .tool-versions | awk '{print $2}')
cargo binstall cobalt-bin@${VERSION} -y

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

## License

### Code

The code and configuration files in this repository (HTML templates, build scripts, etc.) are available under the MIT License.

### Content

All blog posts and written content are licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

This means you are free to:

- **Share** — copy and redistribute the content in any medium or format
- **Adapt** — remix, transform, and build upon the content

Under the following terms:

- **Attribution** — You must give appropriate credit, provide a link to the license, and indicate if changes were made.
