# Service registry

How to use: match `Dependency patterns` against every dependency name in the
manifest (`package.json` dependencies and devDependencies, `requirements.txt`,
`pyproject.toml`, `Podfile`, `pubspec.yaml`, `go.mod`) and `Signals` against
top-level files and folders. Every match contributes one row to the stack
table (module → provider) and, when `Reviewer` is set, activates that
reviewer. When `Plugin id` is set, the pre-flight check verifies it is
installed and otherwise prints the install commands.

Install command format (two lines, always printed together):

```
/plugin marketplace add <marketplace source>
/plugin install <plugin id>
```

`claude-plugins-official` is pre-registered in Claude Code, so for those
plugins print only the second line.

Only major services belong here. UI kits, icon packs, date libraries, form
libraries, linters and test runners are never listed in the stack table.

| Dependency patterns | Signals | Service | Module | Plugin id | Marketplace source | Reviewer |
|---|---|---|---|---|---|---|
| `stripe`, `@stripe/*` | — | Stripe | Payments | `stripe@claude-plugins-official` | (pre-registered) | stripe-reviewer |
| `@paypal/*`, `paypal-*` | — | PayPal | Payments | `paypal@claude-plugins-official` | (pre-registered) | — |
| `react-native-purchases`, `@revenuecat/*`, `purchases-*` | — | RevenueCat | Payments (in-app) | `revenuecat@claude-plugins-official` | (pre-registered) | mobile-reviewer |
| `@supabase/*` | `supabase/` | Supabase | Database, Auth, File storage | `supabase@supabase-community-supabase-plugin` | `supabase-community/supabase-plugin` | supabase-reviewer |
| `convex` | `convex/` | Convex | Database, Backend | `convex@claude-plugins-official` | (pre-registered) | convex-reviewer |
| `firebase`, `firebase-admin`, `@react-native-firebase/*` | `firebase.json`, `.firebaserc` | Firebase | Database, Auth, Push, Hosting | `firebase@firebase` | `firebase/firebase-tools` | firebase-reviewer |
| `@neondatabase/*`, `@neon/*` | — | Neon | Database | `neon@claude-plugins-official` | (pre-registered) | sql-orm-reviewer |
| `@prisma/client`, `prisma` | `prisma/` | Prisma | Database (ORM) | `prisma@claude-plugins-official` | (pre-registered) | sql-orm-reviewer |
| `drizzle-orm`, `drizzle-kit` | `drizzle/`, `drizzle.config.*` | Drizzle | Database (ORM) | — | — | sql-orm-reviewer |
| `pg`, `postgres`, `@vercel/postgres`, `knex`, `typeorm`, `sequelize` | — | Postgres | Database | — | — | sql-orm-reviewer |
| `@planetscale/*` | — | PlanetScale | Database | `planetscale@claude-plugins-official` | (pre-registered) | sql-orm-reviewer |
| `mongodb`, `mongoose` | — | MongoDB | Database | `mongodb@claude-plugins-official` | (pre-registered) | mongodb-reviewer |
| `redis`, `ioredis`, `@upstash/redis` | — | Redis / Upstash | Cache / Queue | `redis-development@claude-plugins-official` | (pre-registered) | — |
| `@clerk/*` | — | Clerk | Auth | `core@clerk-skills` | `clerk/skills` | auth-provider-reviewer |
| `@auth0/*`, `auth0` | — | Auth0 | Auth | `auth0@claude-plugins-official` | (pre-registered) | auth-provider-reviewer |
| `next-auth`, `@auth/*` | — | Auth.js / NextAuth | Auth | — | — | auth-provider-reviewer |
| `@workos-inc/*` | — | WorkOS | Auth | `workos@claude-plugins-official` | (pre-registered) | auth-provider-reviewer |
| `expo`, `expo-*` | `app.json`, `app.config.*` | Expo | Mobile | `expo@claude-plugins-official` | (pre-registered) | mobile-reviewer |
| `@capacitor/*` | `capacitor.config.*` | Capacitor | Mobile | — | — | mobile-reviewer |
| `react-native` | `ios/`, `android/` | React Native | Mobile | — | — | mobile-reviewer |
| `twilio`, `@twilio/*` | — | Twilio | SMS / Voice | `twilio-developer-kit@twilio` | `twilio/ai` | messaging-reviewer |
| `@sendgrid/*` | — | SendGrid | Email | `twilio-developer-kit@twilio` | `twilio/ai` | messaging-reviewer |
| `resend`, `@react-email/*` | — | Resend / React Email | Email | `resend@claude-plugins-official` | (pre-registered) | messaging-reviewer |
| `postmark`, `nodemailer`, `mailgun*`, `@mailchimp/*` | — | (named provider) | Email | — | — | messaging-reviewer |
| `@aws-sdk/*`, `aws-sdk`, `aws-cdk-lib` | `serverless.yml`, `cdk.json`, `template.yaml` | AWS | Cloud / Storage / Hosting | `aws-core@agent-toolkit-for-aws` | `https://github.com/aws/agent-toolkit-for-aws.git` | cloud-reviewer |
| `@google-cloud/*` | — | Google Cloud | Cloud | — | — | cloud-reviewer |
| `@azure/*` | — | Azure | Cloud | `azure@claude-plugins-official` | (pre-registered) | cloud-reviewer |
| `@vercel/*`, `vercel` | `vercel.json` | Vercel | Hosting | `vercel@claude-plugins-official` | (pre-registered) | — |
| `wrangler`, `@cloudflare/*`, `nitro` (cloudflare preset) | `wrangler.toml`, `wrangler.json` | Cloudflare | Hosting | `cloudflare@claude-plugins-official` | (pre-registered) | — |
| `netlify-cli`, `@netlify/*` | `netlify.toml` | Netlify | Hosting | `netlify-skills@claude-plugins-official` | (pre-registered) | — |
| `@lovable.dev/*` | `.lovable/` | Lovable | Hosting / Platform | `lovable@claude-plugins-official` | (pre-registered) | — |
| `cloudinary`, `@cloudinary/*` | — | Cloudinary | File storage / Media | `cloudinary@claude-plugins-official` | (pre-registered) | — |
| `uploadthing`, `@uploadthing/*` | — | UploadThing | File storage | — | — | — |
| `@sentry/*` | `sentry.*.config.*` | Sentry | Monitoring | `sentry@claude-plugins-official` | (pre-registered) | — |
| `posthog-js`, `posthog-node` | — | PostHog | Analytics | `posthog@claude-plugins-official` | (pre-registered) | — |
| `@datadog/*`, `dd-trace` | — | Datadog | Monitoring | `datadog@claude-plugins-official` | (pre-registered) | — |
| `openai` | — | OpenAI | AI | — | — | — |
| `@anthropic-ai/*` | — | Anthropic | AI | — | — | — |
| `@pinecone-database/*` | — | Pinecone | Vector DB | `pinecone@claude-plugins-official` | (pre-registered) | — |
| `mapbox-gl`, `@mapbox/*` | — | Mapbox | Maps | `mapbox@claude-plugins-official` | (pre-registered) | — |
| `@googlemaps/*`, `@react-google-maps/*` | — | Google Maps | Maps | — | — | — |
| `@shopify/*` | — | Shopify | Commerce | `shopify-ai-toolkit@claude-plugins-official` | (pre-registered) | — |
| `intuit-oauth`, `node-quickbooks` | — | QuickBooks | Accounting | `intuit-quickbooks@claude-plugins-official` | (pre-registered) | — |
| `@slack/*` | — | Slack | Integrations | `slack@claude-plugins-official` | (pre-registered) | — |
| `@notionhq/*` | — | Notion | Integrations | `notion@claude-plugins-official` | (pre-registered) | — |
| `@playwright/test`, `playwright` | `playwright.config.*` | Playwright | Testing (E2E) | `playwright@claude-plugins-official` | (pre-registered) | — |

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
