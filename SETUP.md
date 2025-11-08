# Setup Guide for twelvenines.com

This guide walks you through setting up the development environment, configuring GitHub Pages, and setting up the custom domain with AWS Route 53.

## Local Development Setup

### Prerequisites

- [Rust](https://www.rust-lang.org/tools/install) installed on your system
- Git installed and configured

### Installation

1. **Install Cobalt** (static site generator):

   ```bash
   cargo install cobalt-bin
   ```

2. **Install prek** (for pre-commit hooks):

   ```bash
   brew install prek
   ```

3. **Install markdownlint-cli2** (for markdown linting):

   ```bash
   npm install -g markdownlint-cli2
   ```

   Or if you prefer using npx without global installation:

   ```bash
   npx markdownlint-cli2 "**/*.md"
   ```

4. **Set up pre-commit hooks**:

   ```bash
   prek install
   ```

### Working with the Site

**Serve the site locally** (with live reload):

```bash
cd src
cobalt serve
```

The site will be available at `http://localhost:3000`

**Build the site**:

```bash
cd src
cobalt build
```

This will generate the static site in the `docs/` directory.

**Run markdown linting**:

```bash
markdownlint-cli2 "**/*.md"
```

## GitHub Pages Setup

Follow these steps to configure GitHub Pages for your repository:

1. **Push your code to GitHub** (if not already done):

   ```bash
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

2. **Configure GitHub Pages**:
   - Go to your repository on GitHub
   - Navigate to **Settings** → **Pages** (in the left sidebar)
   - Under **Source**, select:
     - **Deploy from a branch**
     - Branch: **main**
     - Folder: **/docs**
   - Click **Save**

3. **Configure Custom Domain**:
   - In the same **Pages** settings page
   - Under **Custom domain**, enter: `twelvenines.com`
   - Click **Save**
   - Wait for the DNS check to complete (this will happen after you set up Route 53)
   - Check **Enforce HTTPS** once the SSL certificate is provisioned (may take a few minutes after DNS is configured)

## AWS Route 53 Setup

These are the step-by-step instructions for configuring your custom domain in AWS Route 53:

### Step 1: Create a Hosted Zone

1. Log in to the **AWS Console**
2. Navigate to **Route 53** service
3. Click on **Hosted zones** in the left sidebar
4. Click **Create hosted zone**
5. Enter your domain name: `twelvenines.com`
6. Type: Select **Public hosted zone**
7. Click **Create hosted zone**

### Step 2: Note the Name Servers

After creating the hosted zone, you'll see 4 NS (Name Server) records. They will look something like:

- `ns-1234.awsdns-12.org`
- `ns-5678.awsdns-34.com`
- `ns-9012.awsdns-56.net`
- `ns-3456.awsdns-78.co.uk`

**Important**: Keep these handy for the next step.

### Step 3: Update Domain Registrar Name Servers

If you registered your domain with AWS Route 53:

1. Go to **Route 53** → **Registered domains**
2. Click on `twelvenines.com`
3. The name servers should already be set to your hosted zone's NS records
4. If not, click **Add or edit name servers** and update them

If you registered your domain elsewhere (GoDaddy, Namecheap, etc.):

1. Log in to your domain registrar
2. Find the DNS or Name Server settings for `twelvenines.com`
3. Replace the existing name servers with the 4 NS records from Route 53
4. Save the changes

**Note**: Name server updates can take up to 48 hours to propagate, but usually complete within a few hours.

### Step 4: Create DNS Records for GitHub Pages

Back in the Route 53 hosted zone for `twelvenines.com`:

#### Create A Records (for apex domain)

1. Click **Create record**
2. Leave **Record name** blank (for the apex domain)
3. **Record type**: Select **A - Routes traffic to an IPv4 address**
4. **Value**: Enter the first GitHub Pages IP: `185.199.108.153`
5. **TTL**: 300 (or leave default)
6. Click **Add another record** and repeat for the other 3 IPs:
   - `185.199.109.153`
   - `185.199.110.153`
   - `185.199.111.153`
7. Click **Create records**

Alternatively, you can create one A record with all four IPs (one per line):

```text
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

#### Create CNAME Record (for www subdomain)

1. Click **Create record**
2. **Record name**: Enter `www`
3. **Record type**: Select **CNAME - Routes traffic to another domain name**
4. **Value**: Enter your GitHub Pages domain (replace `YOURUSERNAME` with your actual GitHub username):
   - If this is a user/organization site: `YOURUSERNAME.github.io`
   - If this is a project site: `YOURUSERNAME.github.io`
5. **TTL**: 300 (or leave default)
6. Click **Create records**

**Example**: If your GitHub username is `bgould`, enter: `bgould.github.io`

### Step 5: Verify DNS Configuration

Wait a few minutes for DNS propagation, then verify:

**Check DNS records**:

```bash
dig twelvenines.com
dig www.twelvenines.com
```

You should see the GitHub Pages IPs for the apex domain and a CNAME for www.

**Check the site**:

Visit `https://twelvenines.com` in your browser. You should see your site!

### Step 6: Enable HTTPS on GitHub Pages

1. Go back to your GitHub repository **Settings** → **Pages**
2. Once the DNS check passes, you'll see a checkmark next to your custom domain
3. Check the **Enforce HTTPS** checkbox
4. GitHub will automatically provision an SSL certificate (this may take a few minutes)

## Troubleshooting

### DNS Issues

- **DNS not resolving**: Wait longer. DNS propagation can take up to 48 hours, though it's usually much faster.
- **Check propagation status**: Use online tools like [WhatsMyDNS.net](https://www.whatsmydns.net/) to check DNS propagation worldwide.

### GitHub Pages Issues

- **404 errors**: Make sure the `docs/` directory contains an `index.html` file after building.
- **CNAME file missing**: The build process should include `docs/CNAME`. Check that it exists and contains `twelvenines.com`.
- **Changes not appearing**: GitHub Pages can take a few minutes to update after pushing changes.

### Build Issues

- **Cobalt not found**: Make sure you've installed it with `cargo install cobalt-bin` and that `~/.cargo/bin` is in your PATH.
- **Build fails**: Check the GitHub Actions logs in the **Actions** tab of your repository.

## GitHub Actions Workflows

This project includes two automated workflows:

### PR Checks (`pr-checks.yml`)

Runs on every pull request:

- Markdown linting with markdownlint-cli2
- Build validation to ensure the site builds successfully

### Build and Deploy (`deploy.yml`)

Runs on every push to the `main` branch:

- Installs Cobalt
- Builds the site from `src/` to `docs/`
- Auto-commits the built site to the repository

## Project Structure

```text
twelvenines.com/
├── .github/
│   └── workflows/
│       ├── pr-checks.yml       # PR validation workflow
│       └── deploy.yml          # Build and deploy workflow
├── src/                        # Cobalt source files
│   ├── _cobalt.yml            # Cobalt configuration
│   ├── _layouts/              # HTML templates
│   │   └── default.html
│   ├── _includes/             # Reusable partials
│   ├── posts/                 # Blog posts (optional)
│   └── index.md               # Homepage
├── docs/                       # Build output (GitHub Pages serves from here)
│   ├── .nojekyll              # Disable Jekyll processing
│   ├── CNAME                  # Custom domain configuration
│   └── index.html             # Generated files
├── .pre-commit-config.yaml    # Pre-commit hooks configuration
├── .markdownlint-cli2.yaml    # Markdown linting configuration
├── .gitignore                 # Git ignore patterns
├── README.md                  # Project overview
└── SETUP.md                   # This file
```

## Additional Resources

- [Cobalt Documentation](https://cobalt-org.github.io/docs/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [AWS Route 53 Documentation](https://docs.aws.amazon.com/route53/)
- [Configuring a custom domain for GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
