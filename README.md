# agentify-metrics

A real-time code coverage metrics dashboard for the [agentify](https://github.com/jonparker/agentify) project, deployed on Cloudflare Pages.

## Overview

This project visualizes time-series code coverage data with interactive charts tracking:
- **Overall coverage trends** — lines, statements, branches, and functions coverage over time
- **Directory-level breakdowns** — per-directory statement coverage with toggleable visualization
- **Latest metrics** — live stats cards showing current coverage percentages

The dashboard is built with vanilla HTML/JavaScript and [Chart.js](https://www.chartjs.org/), consuming JSON data files (`data/latest.json` and `data/history.json`) that are updated via automated workflows.

## Technology Stack

- **Frontend**: HTML5, JavaScript (ES6+), Chart.js 4 + date-fns adapter
- **Hosting**: Cloudflare Pages
- **Deployment**: GitHub Actions + wrangler
- **Data format**: JSON (history snapshots with per-commit metrics)

## Getting Started

### Prerequisites

- Node.js (for wrangler CLI)
- Cloudflare account with Pages project already created
- GitHub repository with deployment secrets configured

### Local Development

```bash
# Install dependencies
npm install

# Run locally (if using wrangler)
wrangler pages dev .

# Or simply open index.html in your browser
# JSON data files are served from ./data/
```

### Data Format

Coverage data is stored in JSON files:

**data/history.json**: Array of coverage snapshots
```json
[
  {
    "timestamp": "2026-03-30T15:10:12.425Z",
    "commit": "abc123...",
    "commitMessage": "...",
    "total": {
      "statements": 97.05,
      "branches": 92.59,
      "functions": 92.1,
      "lines": 98.07
    },
    "directories": {
      "src/utilities": { "statements": 98.18, ... },
      "src/engines": { "statements": 100, ... }
    }
  }
]
```

**data/latest.json**: Current metrics snapshot (same structure, single object)

## Deployment

### Manual Setup (One-time)

1. Create the Cloudflare Pages project manually:
   - Go to **Workers & Pages → Create → Pages → Direct Upload**
   - Name it `agentify-metrics` (must match `--project-name` in workflow)

2. Create an Account API token:
   - **Manage Account → Account API Tokens**
   - Required permission: **Developer Platform → Pages → Edit** only

3. Add GitHub secrets:
   - `CLOUDFLARE_API_TOKEN` — your API token
   - `CLOUDFLARE_ACCOUNT_ID` — from Workers & Pages dashboard sidebar

### Automatic Deployment

Push to `main` branch triggers GitHub Actions workflow (`.github/workflows/cloudflare-pages.yml`):
- Checks out code
- Deploys to Cloudflare Pages using wrangler
- Actions are pinned to commit SHAs for supply chain security

## Architecture Notes

- **Pages & Workers convergence**: Cloudflare unified Pages and Workers under a single platform. Pages is in maintenance mode; new projects should favor Workers.
- **Wrangler limitations**: The `wrangler-action` can only deploy to existing projects — manual creation required before first deployment.
- **Security**: Third-party GitHub Actions are pinned to full commit SHAs (not version tags) to prevent supply chain attacks.

## Customization

### Adding New Metrics

1. Update the data source to include new coverage types in history snapshots
2. Add new dataset definitions in `chartOptions()` section
3. Add color mappings in the `COLORS` object

### Styling

The dashboard uses a dark GitHub-inspired theme. Modify CSS in `<style>` block to customize colors, spacing, or layout.

## License

See LICENSE file.
