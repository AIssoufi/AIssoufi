# Tooling: build, test and deploy

Each project keeps its own toolchain. The versions below are those of issoufi.fr, read from its lockfile on 2026-10-20.

| Tool | Version | Used for |
| --- | --- | --- |
| [pnpm](https://pnpm.io) | 10.34.7, pinned by `packageManager` | Installs |
| [Vite](https://vite.dev) | 8.3.4 | Dev server and build |
| [Vitest](https://vitest.dev) | 4.1.11 | Unit and component tests, in jsdom |
| [Playwright](https://playwright.dev) | 1.64.0 | Browser tests in Chromium, screenshot comparisons, review screenshots |
| [Cloudflare](https://www.cloudflare.com) Pages and Workers | | Hosting, with a preview for each branch |

## pnpm

- Each new worktree runs `pnpm install --frozen-lockfile`, from its [Worktrunk hook](tooling-worktrunk.md#project-hooks).
- pnpm installs from one content-addressable store on the disk, so a new worktree downloads only what the store lacks.
- `minimumReleaseAge: 10080` installs only versions published at least seven days ago, the same delay as the Dependabot cooldown: a guard against a compromised release.

## Vite

- Each worktree serves on its own `PORT`, with `strictPort`: a taken port stops the server instead of moving it, so a test never reaches another worktree's app.
- Every worker runs with `BROWSER=none`, so a dev server never opens a tab in the owner's browser.
- Vitest reads the `test` block of the same `vite.config.ts`, so tests and build resolve modules the same way.

## Vitest

- Unit and component tests sit next to the code, and run in jsdom.
- Vitest 4 detects that an agent runs it and switches to a compact reporter, which hides the console output of passing tests. An agent passes `--reporter=default` to see a warning.

## Playwright

- After a build, browser tests in Chromium check what needs a layout: overflow, hover, keyboard navigation, forced colors, hydration, and accessibility with axe-core.
- On a pull request, the CI compares the screenshots of every page with the base branch, which only informs.
- The visual tester's [`screenshots.mjs`](tooling-scripts.md#review-script) runs the project's own Playwright.

## GitHub Actions

On every pull request of issoufi.fr, the CI runs the formatting, lint and type checks, the unit tests with coverage, the build and its size, the browser tests over three parallel jobs, a check of the commit messages and of new dependencies, then the deployment and its smoke test.

## Cloudflare

- **Pages** hosts the static projects. Each branch and pull request gets a preview URL, which the visual tests and the owner's validations use. On issoufi.fr, the CI deploys the tested build with Wrangler (`pages deploy dist --branch=<branch>`) and Cloudflare's own builds stay paused. `main` is production, updated each morning by a scheduled workflow that merges the tested `develop`.
- **Workers**, with D1, KV or R2 for the data, are the host for a project that needs a backend.
- **One driver of the dashboard**: one browser drives the Cloudflare dashboard, so the super master alone makes the changes there, on a master's `REQUEST: cloudflare`. A worker prepares the repository side: the build output, `_redirects` and `_headers`, and a `wrangler.toml` for a Worker. See [Safety](safety.md#cloudflare).
