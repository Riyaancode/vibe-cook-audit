# Baseline checks

Detect the package manager from the lockfile: `bun.lock`/`bun.lockb` → bun,
`pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, otherwise npm. If the detected
tool is not installed, fall back to npm and note in the audit that the
lockfile was not honoured.

Run every row. Capture exit code and the last 40 lines of output. Never
skip a row silently: if a script does not exist or the tool is missing,
record "could not run: <reason>".

| Check | npm | pnpm | yarn | bun |
|---|---|---|---|---|
| Install | `npm install --ignore-scripts --no-audit --no-fund` | `pnpm install --ignore-scripts` | `yarn install --ignore-scripts` | `bun install --ignore-scripts` |
| Typecheck | `npx tsc --noEmit -p tsconfig.json` (if `tsconfig.json`) | same via `pnpm exec` | same via `yarn` | same via `bunx` |
| Lint | `npm run lint` if script exists, else `npx eslint .` if `eslint.config.*`/`.eslintrc*` | | | |
| Unit tests | `npm test` if script exists, else `npx vitest run` / `npx jest` by config presence | | | |
| Build | `npm run build` if script exists | | | |
| Vulnerabilities | `npm audit --json` | `pnpm audit --json` | `yarn npm audit --json` | `bun audit` (if available) else `npm audit --json` |
| Outdated | `npm outdated` | `pnpm outdated` | `yarn outdated` | `bun outdated` |

Python projects: `pip install -r requirements.txt`, `mypy .` / `pyright` if
configured, `ruff check .` or `flake8`, `pytest -q`, `pip-audit`.

## Secret scan of the built output

After a successful build, grep the client output directory (`dist/`,
`.output/public/`, `build/`, `.next/static/`, `out/`) for these names and
patterns. Any hit is Critical until proven to be a harmless string
reference (open the chunk and look at the context):

```
SERVICE_ROLE|SUPABASE_SERVICE|STRIPE_SECRET|STRIPE_WEBHOOK|sk_live_|rk_live_|whsec_|
AUTH_TOKEN|API_SECRET|PRIVATE_KEY|-----BEGIN|AKIA[0-9A-Z]{16}|sb_secret_
```

Also grep the source for hard-coded live keys: `sk_live_`, `rk_live_`,
`AIza[0-9A-Za-z_-]{35}`, `ghp_`, `xox[baprs]-`, `-----BEGIN (RSA|EC|OPENSSH) PRIVATE KEY`.

## Repo hygiene checks

- Is `.env` (or any `.env.*` other than `.env.example`) present and not in
  `.gitignore`? List every variable name it contains (never the values).
- Stray files at the root: `*.log`, screenshots, `*.bak`, editor files.
- Is there git history? If not, say so; commit-pattern analysis is skipped.
- Lockfile present and matches the package manager in use?
- `README` is boilerplate? `.env.example` exists? Setup steps documented?

## Size and shape metrics (for the code-quality section)

- Total lines of source by language (`find` + `wc`, excluding generated,
  vendored and lockfiles).
- Files over 700 lines, with line counts.
- Counts of `any`, `@ts-ignore`/`@ts-expect-error`, `eslint-disable`,
  `console.log`, `TODO|FIXME|HACK`, `dangerouslySetInnerHTML`.
- Test files count and test count from the runner output.
- Largest built client chunks (`du -k`, top 10) and total client JS bytes.
