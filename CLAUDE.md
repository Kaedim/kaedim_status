# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository is an Upptime-based status monitoring system for Kaedim services. Upptime is an open-source uptime monitor and status page powered entirely by GitHub Actions, Issues, and Pages. The system automatically monitors endpoints, tracks response times, opens/closes issues for incidents, and generates a public status page.

## Architecture

### Core Components

**Configuration-Driven System**
- `.upptimerc.yml` is the single source of truth for all monitoring configuration
- Defines monitored sites, status website settings, and monitoring behavior
- Changes to this file automatically regenerate GitHub Actions workflows via the template update mechanism

**Automated Workflows (.github/workflows/)**
- `uptime.yml`: Runs every 5 minutes to check endpoint status
- `response-time.yml`: Records response times every 6 hours
- `graphs.yml`: Generates response time graphs daily
- `summary.yml`: Updates summary statistics
- `site.yml`: Builds and deploys the status page to GitHub Pages
- `update-template.yml`: Auto-updates workflows when Upptime template changes (weekly by default)
- All workflow files are auto-generated - DO NOT edit them directly

**Data Storage**
- `history/`: Contains commit history tracking endpoint status over time
- `api/`: JSON files with uptime/response time metrics for each monitored endpoint
- `graphs/`: Generated PNG graphs of response time trends
- All data is committed to git, creating an immutable audit trail

**Status Website**
- Built with Svelte/Sapper and hosted on GitHub Pages
- Fetches data from this repository via GitHub API
- Accessible at the baseUrl defined in `.upptimerc.yml`

### Monitored Services

The system currently monitors:
- Customer Web App (https://app.kaedim3d.com)
- Backend API (https://api.kaedim3d.com)
- Google (https://www.google.com) - used as a reference/test endpoint

## Configuration Changes

**Adding/Removing Monitored Sites**

Edit `.upptimerc.yml` under the `sites:` section:

```yaml
sites:
  - name: Service Name
    url: https://example.com
    expectedStatusCodes:  # Optional
      - 200
      - 201
```

**Customizing the Status Website**

Modify the `status-website:` section in `.upptimerc.yml`:
- `name`: Status page title
- `introTitle` / `introMessage`: Homepage text
- `logoUrl`: Custom logo URL
- `navbar` / `footer`: Navigation and footer content
- `baseUrl`: Path for GitHub Pages deployment (format: `/repo-name`)

**Advanced Configuration**

Upptime supports additional features configurable in `.upptimerc.yml`:
- Notifications (Slack, Discord, email, etc.)
- Assignees for incident issues
- Custom check intervals
- Authentication for private endpoints
- Custom HTTP methods and headers

Refer to https://upptime.js.org/docs/configuration for full options.

## Common Tasks

**Triggering Manual Checks**
```bash
# Via GitHub CLI
gh workflow run uptime.yml

# Or use the Actions tab in GitHub web interface
```

**Viewing Status History**
```bash
# Check commit history for a specific site
git log --oneline -- history/customer-web-app.yml

# View historical metrics
cat api/customer-web-app/uptime.json
```

**Forcing Workflow Regeneration**
```bash
# Manually trigger template update
gh workflow run update-template.yml
```

**Testing Configuration Locally**

There is no local development environment. All operations run via GitHub Actions. To test changes:
1. Make configuration changes in `.upptimerc.yml`
2. Commit and push to trigger workflows
3. Monitor workflow runs in GitHub Actions tab

## Repository Ownership

- Owner: `Kaedim` (GitHub organization)
- Repository: `kaedim_status`
- These values in `.upptimerc.yml` affect GitHub API calls and must match the actual repository location

## Important Notes

- Workflows are auto-generated from the Upptime template - manual edits to `.github/workflows/*.yml` will be overwritten
- Data files (`api/`, `graphs/`, `history/`) are automatically managed by workflows - do not edit manually
- The system relies on GitHub Actions, so quota/rate limits may affect monitoring frequency
- Issues are automatically opened for downtime incidents and closed when services recover
- Response time data is stored in git commits, enabling long-term trend analysis

## Documentation

Official Upptime documentation: https://upptime.js.org/docs
