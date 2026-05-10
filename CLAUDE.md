# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm install          # Install dependencies
npm start            # Dev server at localhost:8080 (hot reload)
npm run build        # Production build → dist/build.js
```

There is no test suite and no linter configured.

The `bin/start.js` script is the `npx ynab-api-starter-kit` CLI entry point — it copies the project template, installs deps, and starts the dev server. It is not used for normal development.

## Architecture

This is a Vue 3 single-page application bundled with webpack. Entry point is `src/main.js`, which mounts `src/App.vue` onto `#app` in `public/index.html`. The built bundle is output to `dist/build.js`, which `public/index.html` loads.

### App state machine (App.vue)

All application logic lives in `src/App.vue`. A `loading` boolean overlays all states; aside from that, the template renders one of three mutually exclusive states:

1. **No token** — shows an "Authorize with YNAB" button. Clicking it redirects to YNAB's OAuth endpoint using the `clientId` and `redirectUri` from `src/config.json`.
2. **Token present, no budget selected** — renders `<Budgets>` with the list fetched via `this.api.budgets.getBudgets()`.
3. **Budget selected** — renders `<Transactions>` with data from `this.api.transactions.getTransactions(budgetId)`.

An **error** state (`this.error`) can appear in place of states 2–3, showing the error message with a "Try Again" button that clears the token.

The OAuth flow is **Implicit Grant** — the access token arrives in `window.location.hash` after redirect and is persisted to `sessionStorage` under the key `ynab_access_token`.

### Configuration

`src/config.json` holds the two required OAuth values:
```json
{
  "clientId": "<YNAB OAuth Client ID>",
  "redirectUri": "<URL of the deployed app>"
}
```
This file must be updated before the app can authenticate. The `redirectUri` must also be registered in YNAB Developer Settings.

### YNAB SDK

The `ynab` npm package provides the API client and utilities. Key usage patterns:
- `new ynab.api(token)` — creates an authenticated client
- `utils.convertMilliUnitsToCurrencyAmount(milliUnits)` — YNAB stores all monetary values as milliunits (integer × 1000); always use this utility when displaying amounts (see `Transactions.vue`)

### Deployment

Pushing to `main` triggers `.github/workflows/gh-deploy.yaml`, which runs `npm install && npm run build`, copies `dist/` into `public/`, then publishes `public/` to the `gh-pages` branch via `peaceiris/actions-gh-pages`. The live app is served from that branch by GitHub Pages.

To use a custom domain, add a `cname: your-domain.com` field under the `with:` block of the `peaceiris/actions-gh-pages` step in `gh-deploy.yaml`, then update `redirectUri` in `src/config.json` and register the new URL in YNAB Developer Settings.
