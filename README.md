# gas-template

[![CI](https://github.com/h13/gas-template/actions/workflows/ci.yml/badge.svg)](https://github.com/h13/gas-template/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Node.js](https://img.shields.io/badge/Node.js-%3E%3D24-green.svg)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-strict-blue.svg)](https://www.typescriptlang.org/)
[![Google Apps Script](https://img.shields.io/badge/Google%20Apps%20Script-Template-4285F4.svg)](https://developers.google.com/apps-script)

[日本語](README.ja.md)

A production-ready Google Apps Script project template. Write GAS in TypeScript with modern tooling, automated testing, and CI/CD — all set up and ready to go.

## Why This Template?

Google Apps Script projects tend to become hard to maintain as they grow. This template solves that by providing:

- **TypeScript** — Catch bugs before deployment with strict type checking
- **Local development** — Write and test code in your editor, not the GAS web editor
- **Automated quality checks** — Linting, formatting, and tests run automatically on every commit
- **CI/CD pipeline** — Push to `main` and your code deploys to GAS automatically
- **Dependency management** — Renovate keeps everything up to date

## What's Included

| Category     | Tools                                            |
| ------------ | ------------------------------------------------ |
| Language     | TypeScript (strict mode)                         |
| Bundler      | Rollup (GAS-compatible ESM output)               |
| Deployment   | clasp (dev / prod environments)                  |
| Testing      | Jest (100% coverage threshold)                   |
| Linting      | ESLint, Prettier, Stylelint (HTML CSS), HTMLHint |
| Git hooks    | husky + lint-staged (auto-fix on commit)         |
| CI/CD        | GitHub Actions (CI on PR, CD on merge)           |
| Dependencies | Renovate (auto-update with automerge)            |

## Getting Started

### Prerequisites

Choose one of the following setups:

#### Option A: Local (mise)

If you use [mise](https://mise.jdx.dev/), Node.js and pnpm are configured automatically via `.mise.toml`.

```bash
mise install   # installs Node.js >= 24 and pnpm 10
```

Or install manually:

| Tool                           | Version | Install                         |
| ------------------------------ | ------- | ------------------------------- |
| [Node.js](https://nodejs.org/) | >= 24   | [download](https://nodejs.org/) |
| [pnpm](https://pnpm.io/)       | 10      | `npm install -g pnpm`           |

#### Option B: Dev Container / GitHub Codespaces

No local setup needed. Everything is pre-configured in `.devcontainer/`.

- **VS Code**: Open the project and click "Reopen in Container" when prompted
- **GitHub Codespaces**: Click **"Code" → "Codespaces" → "Create codespace on main"** on GitHub

> **Note**: For `clasp login` inside a container, use `pnpm exec clasp login --no-localhost` and follow the URL-based auth flow.

### 1. Create Your Repository

Click **"Use this template"** on GitHub, then clone your new repository:

```bash
git clone https://github.com/<your-org>/<your-project>.git
cd <your-project>
```

### 2. Install Dependencies

```bash
pnpm install
```

### 3. Set Up clasp

Log in to your Google account:

```bash
pnpm exec clasp login
```

Create a new GAS project (or use an existing one):

```bash
pnpm exec clasp create --type webapp --title "My GAS App"
```

Then create environment-specific config files (these are gitignored):

**`.clasp-dev.json`** — Development environment:

```json
{
  "scriptId": "YOUR_DEV_SCRIPT_ID",
  "rootDir": "dist"
}
```

**`.clasp-prod.json`** — Production environment:

```json
{
  "scriptId": "YOUR_PROD_SCRIPT_ID",
  "rootDir": "dist"
}
```

> **Tip**: You can find the Script ID in the GAS editor under Project Settings.

### 4. Verify Everything Works

```bash
pnpm run check    # lint + typecheck + test
pnpm run build    # bundle TypeScript + copy assets to dist/
```

### 5. Deploy

```bash
pnpm run deploy       # check → build → push to dev
pnpm run deploy:prod  # check → build → push to production
```

## Project Structure

```text
gas-template/
├── src/
│   ├── index.ts            # GAS entry points (doGet, etc.)
│   └── index.html          # Web UI served by the GAS webapp
├── test/
│   └── index.test.ts       # Unit tests
├── .github/
│   └── workflows/
│       ├── ci.yml             # CI: lint → typecheck → test → build
│       ├── cd.yml             # CD: deploy on CI success
│       └── sync-template.yml # Sync updates from template repo
├── appsscript.json         # GAS manifest (OAuth scopes, runtime config)
├── rollup.config.mjs       # Bundler config
├── tsconfig.json           # TypeScript config (strict mode)
├── jest.config.json        # Test config (100% coverage)
├── eslint.config.mjs       # ESLint 10 flat config
├── .prettierrc             # Prettier config
├── .stylelintrc.json       # Stylelint config (HTML CSS support)
├── .htmlhintrc             # HTMLHint config
├── .devcontainer/
│   └── devcontainer.json   # Dev Container / Codespaces config
├── .mise.toml              # mise tool versions (Node.js, pnpm)
├── .templatesyncignore     # Files excluded from template sync
└── renovate.json           # Renovate auto-update config
```

## Development Workflow

### Daily Development

1. Edit source files in `src/`
2. Run checks locally:

   ```bash
   pnpm run check
   ```

3. Deploy to dev and verify:

   ```bash
   pnpm run deploy
   ```

4. Open the Web App URL from the GAS editor to test

### Making Changes (PR Flow)

1. Create a feature branch
2. Make your changes and commit — husky auto-runs lint-staged
3. Push and create a Pull Request
4. CI automatically runs lint, typecheck, test, and build
5. Merge to `main` — CD auto-deploys to production

### Available Commands

| Command                    | Description                                |
| -------------------------- | ------------------------------------------ |
| `pnpm run lint`            | ESLint with auto-fix                       |
| `pnpm run lint:css`        | Stylelint (including CSS in HTML)          |
| `pnpm run lint:html`       | HTMLHint                                   |
| `pnpm run format`          | Prettier format all files                  |
| `pnpm run typecheck`       | TypeScript type checking                   |
| `pnpm run test`            | Jest with coverage report                  |
| `pnpm run test -- --watch` | Jest in watch mode                         |
| `pnpm run check`           | Run lint + typecheck + test                |
| `pnpm run build`           | Bundle TypeScript + copy assets to `dist/` |
| `pnpm run deploy`          | check + build + deploy to dev              |
| `pnpm run deploy:prod`     | check + build + deploy to production       |

## Testing

Tests live in `test/` and run with Jest. Coverage threshold is set to **100%** for all metrics (branches, functions, lines, statements).

`src/index.ts` is excluded from coverage because it contains GAS-specific globals (`HtmlService`, etc.) that can't run in Node.js.

```bash
pnpm run test              # Run with coverage
pnpm run test -- --watch   # Watch mode for development
```

### Writing Tests

Add your test files in `test/` with the `.test.ts` extension. Example:

```typescript
import { myFunction } from "../src/myModule.js";

describe("myFunction", () => {
  it("should return expected value", () => {
    expect(myFunction("input")).toBe("expected");
  });
});
```

## CI/CD

### How It Works

```text
Push / PR  →  CI (ci.yml)  →  CD (cd.yml)
               ├── Lint          └── Build
               ├── Typecheck         └── clasp push
               ├── Test                  └── clasp deploy
               └── Build
```

CI runs on every push and PR. CD uses `workflow_run` and only triggers after CI succeeds on `dev` or `main`.

| Trigger        | Pipeline       | Behavior                                              |
| -------------- | -------------- | ----------------------------------------------------- |
| PR to `main`   | CI only        | lint → typecheck → test → build                       |
| Push to `dev`  | CI → CD (dev)  | cancel-in-progress: new pushes cancel running deploys |
| Push to `main` | CI → CD (prod) | queued: deploys run sequentially, never skipped       |

### Required GitHub Settings

Configure these in your repository's Settings:

**Repository Secret (shared across environments):**

| Name           | Value                                                  |
| -------------- | ------------------------------------------------------ |
| `CLASPRC_JSON` | Contents of `~/.clasprc.json` (clasp auth credentials) |

**Environment: `development`**

| Type     | Name            | Value                                         |
| -------- | --------------- | --------------------------------------------- |
| Secret   | `CLASP_JSON`    | `{"scriptId":"YOUR_DEV_ID","rootDir":"dist"}` |
| Variable | `DEPLOYMENT_ID` | Your dev deployment ID                        |

**Environment: `production`**

| Type     | Name            | Value                                          |
| -------- | --------------- | ---------------------------------------------- |
| Secret   | `CLASP_JSON`    | `{"scriptId":"YOUR_PROD_ID","rootDir":"dist"}` |
| Variable | `DEPLOYMENT_ID` | Your prod deployment ID                        |

> **How to get `CLASPRC_JSON`**: Run `pnpm exec clasp login`, then copy the contents of `~/.clasprc.json`.
>
> **How to get `DEPLOYMENT_ID`**: Run `pnpm exec clasp deployments` and copy the deployment ID.

## Customization

### Adding OAuth Scopes

Edit `oauthScopes` in `appsscript.json`:

```json
{
  "oauthScopes": [
    "https://www.googleapis.com/auth/script.external_request",
    "https://www.googleapis.com/auth/spreadsheets",
    "https://www.googleapis.com/auth/drive",
    "https://www.googleapis.com/auth/gmail.send"
  ]
}
```

### Adding New Source Files

1. Create your module in `src/` (e.g., `src/utils.ts`)
2. Import it from `src/index.ts` — Rollup bundles everything into a single file
3. Add tests in `test/`

> **Important**: GAS only sees functions defined at the top level of `src/index.ts`. Any function you want to call from GAS (or from HTML via `google.script.run`) must be exported from `index.ts`.

### Adjusting Coverage Threshold

Edit `coverageThreshold` in `jest.config.json`:

```json
{
  "coverageThreshold": {
    "global": {
      "branches": 100,
      "functions": 100,
      "lines": 100,
      "statements": 100
    }
  }
}
```

## Dependency Management

[Renovate](https://docs.renovatebot.com/) is configured via the shared preset [`h13/renovate-config:node`](https://github.com/h13/renovate-config):

- **minor / patch** updates are automerged automatically
- **major** updates create PRs for manual review (labeled `breaking`)
- **devDependencies** are grouped and automerged
- **eslint** and **@types/** packages are grouped into dedicated PRs
- New releases must be at least **7 days old** before updating (stability buffer)
- Runs weekly on **Sunday after 9pm**

## Keeping Up with the Template

This template includes a GitHub Actions workflow (`.github/workflows/sync-template.yml`) that automatically checks for updates from the upstream template repository every week.

When updates are found, a PR is created with the `template-sync` label. You can review the changes and merge at your discretion. You can also trigger the sync manually from the Actions tab.

Project-specific files (`src/`, `test/`, `README.md`, etc.) are excluded from sync via `.templatesyncignore`, so your code won't be overwritten. Only tooling and configuration updates are synced.

> **Note**: This workflow only runs in repositories created from this template — it does not run in the template repository itself.

## License

[MIT](LICENSE)
