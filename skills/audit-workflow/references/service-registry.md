# Service registry

How to use: match `Dependency patterns` against every dependency name in the
manifest (`package.json` dependencies and devDependencies, `requirements.txt`,
`pyproject.toml`, `Podfile`, `pubspec.yaml`, `go.mod`) and `Signals` against
top-level files and folders. Every match contributes one row to the stack
table (module → provider) and, when `Reviewer` is set, activates that
reviewer. When `Plugin id` is set, the pre-flight check verifies it is
installed and otherwise prints the install commands.

Install command format: one line for plugins on the pre-registered
`claude-plugins-official` marketplace, two lines otherwise.

```
/plugin install <plugin id>                      # pre-registered marketplace
```
```
/plugin marketplace add <marketplace source>     # any other marketplace
/plugin install <plugin id>
```

Only major services belong here. UI kits, icon packs, date libraries, form
libraries, linters and test runners are never listed in the stack table.

## Where to look, and where not to

Read the real manifests only: `package.json`, `requirements.txt`,
`pyproject.toml`, `Podfile`, `pubspec.yaml`, `go.mod`, `composer.json`.
Ignore anything under `node_modules`, `.output`, `dist`, `build`, `.next`,
`.nuxt`, `.wrangler`, `.vinxi`, `.vercel`, `.svelte-kit`, `coverage`.
Ignore stray files such as `firebase-debug.log`; a log is not a signal.

## Three ways a service can match

1. **Dependency patterns** in the manifest.
2. **Signals**: top-level files and folders.
3. **Source signals**: environment-variable names and API hostnames grepped
   in the source tree (`grep -rE "<pattern>" src app lib server` and the
   `.env*` files, names only). This catches services called over raw HTTP
   with no SDK. A source-signal match counts the same as a dependency match
   for detection, but it is labelled "(source signal only)" in the stack table
   and placed in the module the surrounding code implies (an `EXPO_ACCESS_TOKEN`
   in a push-dispatch file is a Push row, not a Mobile row).

## Package manager

Record the package manager alongside the stack table (lockfile detection
in `baseline-checks.md`). If two lockfiles exist, say so; it is a hygiene
finding.

## One module, several providers

Keep one row per module and list every provider in the Provider cell,
separated by " + ", primary first (the one most code paths use). Example:
`Email | Lovable managed email (system mail) + Resend (coach-authored mail)`.
When a platform package supplies several modules (for example Lovable:
hosting, OAuth broker, email), it appears in each of those module rows,
not as its own row.

## Framework suppression

`react`/`react-dom` and `vue` are shown only when no meta-framework row
matched (Next.js, TanStack Start, Remix/React Router framework, Nuxt,
SvelteKit, Astro, Expo). `vite` is shown only when no framework row matched.

| Dependency patterns | Signals | Source signals (env names / hostnames) | Service | Module | Plugin id | Marketplace source | Reviewer |
|---|---|---|---|---|---|---|---|
| `stripe`, `@stripe/*` | — | `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, `api.stripe.com` | Stripe | Payments | `stripe@claude-plugins-official` | (pre-registered) | stripe-reviewer |
| `@paypal/*`, `paypal-*` | — | — | PayPal | Payments | `paypal@claude-plugins-official` | (pre-registered) | — |
| `react-native-purchases`, `@revenuecat/*`, `purchases-*` | — | — | RevenueCat | Payments (in-app) | `revenuecat@claude-plugins-official` | (pre-registered) | mobile-reviewer |
| `@supabase/*` | `supabase/` | `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `*.supabase.co` | Supabase | Database, Auth, File storage | `supabase@supabase-community-supabase-plugin` | `supabase-community/supabase-plugin` | supabase-reviewer |
| `convex` | `convex/` | — | Convex | Database, Backend | `convex@claude-plugins-official` | (pre-registered) | convex-reviewer |
| `firebase`, `firebase-admin`, `@react-native-firebase/*` (or FCM source signal alone → Push row only) | `firebase.json`, `.firebaserc` | `FIREBASE_*`, `fcm.googleapis.com`, `firebaseio.com`, `GOOGLE_APPLICATION_CREDENTIALS` | Firebase | Database, Auth, Push, Hosting | `firebase@firebase` | `firebase/firebase-tools` | firebase-reviewer |
| `@neondatabase/*`, `@neon/*` | — | `*.neon.tech` | Neon | Database | `neon@claude-plugins-official` | (pre-registered) | sql-orm-reviewer |
| `@prisma/client`, `prisma` | `prisma/` | — | Prisma | Database (ORM) | `prisma@claude-plugins-official` | (pre-registered) | sql-orm-reviewer |
| `drizzle-orm`, `drizzle-kit` | `drizzle/`, `drizzle.config.*` | — | Drizzle | Database (ORM) | — | — | sql-orm-reviewer |
| `pg`, `postgres`, `@vercel/postgres`, `knex`, `typeorm`, `sequelize` | — | — | Postgres | Database | — | — | sql-orm-reviewer |
| `@planetscale/*` | — | — | PlanetScale | Database | `planetscale@claude-plugins-official` | (pre-registered) | sql-orm-reviewer |
| `mongodb`, `mongoose` | — | `MONGODB_URI`, `mongodb+srv://` | MongoDB | Database | `mongodb@claude-plugins-official` | (pre-registered) | mongodb-reviewer |
| `redis`, `ioredis`, `@upstash/redis` | — | `REDIS_URL`, `UPSTASH_REDIS_*` | Redis / Upstash | Cache / Queue | `redis-development@claude-plugins-official` | (pre-registered) | — |
| `@clerk/*` | — | `CLERK_SECRET_KEY`, `CLERK_PUBLISHABLE_KEY` | Clerk | Auth | `core@clerk-skills` | `clerk/skills` | auth-provider-reviewer |
| `@auth0/*`, `auth0` | — | `AUTH0_*` | Auth0 | Auth | `auth0@claude-plugins-official` | (pre-registered) | auth-provider-reviewer |
| `next-auth`, `@auth/*` | — | — | Auth.js / NextAuth | Auth | — | — | auth-provider-reviewer |
| `@workos-inc/*` | — | — | WorkOS | Auth | `workos@claude-plugins-official` | (pre-registered) | auth-provider-reviewer |
| `expo`, `expo-*` | `app.json`, `app.config.*` | `EXPO_ACCESS_TOKEN`, `exp.host` | Expo | Mobile | `expo@claude-plugins-official` | (pre-registered) | mobile-reviewer |
| `@capacitor/*` | `capacitor.config.*` | — | Capacitor | Mobile (add Push row if `@capacitor/push-notifications` present) | — | — | mobile-reviewer |
| `react-native` | `ios/`, `android/` | — | React Native | Mobile | — | — | mobile-reviewer |
| `twilio`, `@twilio/*` | — | `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN`, `api.twilio.com` | Twilio | SMS / Voice | `twilio-developer-kit@twilio` | `twilio/ai` | messaging-reviewer |
| `@sendgrid/*` | — | `SENDGRID_API_KEY`, `api.sendgrid.com` | SendGrid | Email | `twilio-developer-kit@twilio` | `twilio/ai` | messaging-reviewer |
| `resend`, `@react-email/*` | — | `RESEND_API_KEY`, `api.resend.com` | Resend / React Email | Email | `resend@claude-plugins-official` | (pre-registered) | messaging-reviewer |
| `postmark`, `nodemailer`, `mailgun*`, `@mailchimp/*` | — | `POSTMARK_*`, `MAILGUN_*`, `SMTP_HOST` | (named provider) | Email | — | — | messaging-reviewer |
| `@aws-sdk/*`, `aws-sdk`, `aws-cdk-lib` | `serverless.yml`, `cdk.json`, `template.yaml` | `AWS_ACCESS_KEY_ID`, `AWS_REGION`, `amazonaws.com` | AWS | Cloud / Storage / Hosting | `aws-core@agent-toolkit-for-aws` | `https://github.com/aws/agent-toolkit-for-aws.git` | cloud-reviewer |
| `@google-cloud/*` | — | `GOOGLE_APPLICATION_CREDENTIALS`, `googleapis.com` (non-FCM) | Google Cloud | Cloud | — | — | cloud-reviewer |
| `@azure/*` | — | `AZURE_*` | Azure | Cloud | `azure@claude-plugins-official` | (pre-registered) | cloud-reviewer |
| `@vercel/*`, `vercel` | `vercel.json` | `VERCEL_*` | Vercel | Hosting | `vercel@claude-plugins-official` | (pre-registered) | — |
| `wrangler`, `@cloudflare/*`, `nitro` (cloudflare preset) | `wrangler.toml`, `wrangler.json`, `.wrangler/` | `CLOUDFLARE_*`, `cloudflare-module` in nitro config | Cloudflare | Hosting | `cloudflare@claude-plugins-official` | (pre-registered) | — |
| `netlify-cli`, `@netlify/*` | `netlify.toml` | — | Netlify | Hosting | `netlify-skills@claude-plugins-official` | (pre-registered) | — |
| `@lovable.dev/*` | `.lovable/` | `LOVABLE_API_KEY`, `LOVABLE_CRON_SECRET`, `*.lovable.app` | Lovable | Hosting / Platform | `lovable@claude-plugins-official` | (pre-registered) | — |
| `cloudinary`, `@cloudinary/*` | — | `CLOUDINARY_URL`, `res.cloudinary.com` | Cloudinary | File storage / Media | `cloudinary@claude-plugins-official` | (pre-registered) | — |
| `uploadthing`, `@uploadthing/*` | — | — | UploadThing | File storage | — | — | — |
| `@sentry/*` | `sentry.*.config.*` | `SENTRY_DSN`, `ingest.sentry.io` | Sentry | Monitoring | `sentry@claude-plugins-official` | (pre-registered) | — |
| `posthog-js`, `posthog-node` | — | `POSTHOG_*` | PostHog | Analytics | `posthog@claude-plugins-official` | (pre-registered) | — |
| `@datadog/*`, `dd-trace` | — | — | Datadog | Monitoring | `datadog@claude-plugins-official` | (pre-registered) | — |
| `openai` | — | `OPENAI_API_KEY`, `api.openai.com` | OpenAI | AI | — | — | — |
| `@anthropic-ai/*` | — | `ANTHROPIC_API_KEY`, `api.anthropic.com` | Anthropic | AI | — | — | — |
| `@pinecone-database/*` | — | — | Pinecone | Vector DB | `pinecone@claude-plugins-official` | (pre-registered) | — |
| `mapbox-gl`, `@mapbox/*` | — | `MAPBOX_*` | Mapbox | Maps | `mapbox@claude-plugins-official` | (pre-registered) | — |
| `@googlemaps/*`, `@react-google-maps/*` | — | `*GOOGLE_MAPS*`, `maps.googleapis.com` | Google Maps | Maps | — | — | — |
| `@shopify/*` | — | — | Shopify | Commerce | `shopify-ai-toolkit@claude-plugins-official` | (pre-registered) | — |
| `intuit-oauth`, `node-quickbooks` | — | `QUICKBOOKS_*`, `quickbooks.api.intuit.com` | QuickBooks | Accounting | `intuit-quickbooks@claude-plugins-official` | (pre-registered) | — |
| `@slack/*` | — | `SLACK_*`, `hooks.slack.com` | Slack | Integrations | `slack@claude-plugins-official` | (pre-registered) | — |
| `@notionhq/*` | — | — | Notion | Integrations | `notion@claude-plugins-official` | (pre-registered) | — |
| `@playwright/test`, `playwright` | `playwright.config.*` | — | Playwright | Testing (E2E) | `playwright@claude-plugins-official` | (pre-registered) | — |

## Framework rows (stack table only, never a plugin recommendation)

| Dependency patterns | Signals | Module row |
|---|---|---|
| `next` | `next.config.*` | Frontend + Backend: Next.js |
| `react`, `react-dom` (without next) | — | Frontend: React |
| `@tanstack/react-start` | — | Frontend + Backend: TanStack Start |
| `@remix-run/*`, `react-router` (framework mode) | — | Frontend + Backend: Remix / React Router |
| `vue`, `nuxt` | `nuxt.config.*` | Frontend: Vue / Nuxt |
| `svelte`, `@sveltejs/kit` | `svelte.config.*` | Frontend: Svelte / SvelteKit |
| `@angular/core` | `angular.json` | Frontend: Angular |
| `express`, `fastify`, `hono`, `koa`, `@nestjs/core` | — | Backend: (named) |
| `django`, `flask`, `fastapi` | `manage.py` | Backend: (named) |
| `laravel/framework` | `artisan` | Backend: Laravel |
| `vite` | `vite.config.*` | Build: Vite (mention only if no framework row) |

## Adding a service

Add one row. Keep patterns as exact package names or `prefix/*`. If there
is no official plugin, put `—` in Plugin id and Marketplace source; the stack
table still gets the row and a generic reviewer covers it.
