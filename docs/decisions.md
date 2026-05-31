# Architectural Decisions Log

This file records every major technical and product decision made for this project, with the reasoning behind each. **Read this before making any structural changes.** If you are an AI agent, this file explains why the code is the way it is — do not "fix" things documented here without understanding the tradeoff first.

---

## How to Use This File

- Decisions are listed chronologically, newest at the top within each section
- Each decision has: the choice made, the alternatives considered, and the reason
- If you want to revisit a decision, add a note — don't silently change it

---

## Project Context

- **What we're building:** Multi-tenant SaaS booking platform for experience-based operators (marinas, ziplines, tubing parks, farms, escape rooms, etc.)
- **Competitor we're beating:** Singenuity (singenuity.com)
- **Build style:** AI-assisted (vibe coding) with one primary developer and multiple Claude agents
- **Target market:** US-based operators only (for now)
- **Current phase:** Pre-code — foundation decisions being locked in
- **Pilot operator:** The developer acts as the first operator until real clients are onboarded
- **Timeline:** No hard deadline. Build the MVP right, not fast.

---

## Infrastructure & Hosting

### Hosting Stack
**Decision:** Vercel (booking site) + Railway (API) + Neon (Postgres) + Upstash (Redis) + Cloudflare R2 (files)

**Alternatives considered:**
- AWS full stack — too much DevOps overhead for a solo build
- Vercel for everything — can't run a long-lived Fastify API on Vercel serverless functions efficiently
- Railway for everything — Vercel is better for Next.js with edge caching
- Render instead of Railway — Railway has better DX and deployment speed

**Reason:** This combo gives managed infrastructure with zero DevOps, scales to hundreds of tenants without touching servers, and costs ~$50-100/month at early scale. Each service is best-in-class for its role.

---

### Custom Domains for Operators
**Decision:** Support operator custom domains (e.g., `book.theirmarina.com`) via Vercel's custom domain API, not just subdomains on our domain.

**Alternatives considered:**
- Subdomains only (`clientslug.ourplatform.com`) — simpler but operators can't brand the URL
- Self-hosted nginx with Let's Encrypt — too much operational burden

**Reason:** White-labeling is a core product requirement. Vercel handles wildcard SSL and custom domain provisioning via API. This needs to be planned from day one because it affects DNS architecture. Operators point a CNAME at our Vercel deployment; we provision the domain via Vercel API on operator onboarding.

---

## Backend

### API Framework
**Decision:** Fastify (not Express, not Next.js API routes)

**Alternatives considered:**
- Express — fine but slower and more boilerplate than Fastify
- Next.js API routes — couples the API to one frontend, bad when you have 4 apps hitting the same API
- Hono — newer, great DX, but less ecosystem maturity

**Reason:** One Fastify API serves all four frontends (booking site, admin, register, kiosk). Fastify is faster than Express, has first-class TypeScript support, and built-in schema validation via `fastify-zod` or `@fastify/type-provider-zod`. This is the right call for a shared backend.

---

### API Design: REST (not tRPC, not GraphQL)
**Decision:** REST with Zod validation. OpenAPI spec generated from routes.

**Alternatives considered:**
- tRPC — excellent type safety but creates tight coupling between frontend and backend. Hard to expose as a public API later (which is a planned feature).
- GraphQL — overkill for this domain, adds resolver complexity
- tRPC internally + REST for public API — best of both but too much to maintain

**Reason:** The roadmap includes a public REST API with webhooks for third-party integrations (QuickBooks, Zapier, channel managers). If we use tRPC internally, we'd have to build REST separately anyway. Starting with REST means the public API is already the same API. Zod schemas in `packages/types` serve as the shared contract between frontend and backend.

---

### ORM: Prisma
**Decision:** Prisma (not Drizzle, not raw SQL)

**Alternatives considered:**
- Drizzle — better raw query control, more lightweight, but weaker multi-tenant middleware ecosystem
- Raw SQL with `pg` — maximum control but too much boilerplate for a fast-moving codebase

**Reason:** Prisma's generated types are used as the shared type source across the monorepo. Prisma's middleware/extension system is used to enforce tenant isolation on every query automatically (see Multi-Tenancy section). Prisma Studio is also useful for debugging as the pilot operator.

---

## Database

### Multi-Tenancy Strategy: Row-Level Isolation + Double Enforcement
**Decision:** Single database, `clientId` on every tenant-scoped table. Enforced at TWO layers.

**Alternatives considered:**
- Schema-per-tenant — better isolation but complex migrations across hundreds of schemas
- Database-per-tenant — maximum isolation but operationally expensive and overkill at this scale
- Row-level only with manual WHERE clauses — dangerous, one missed clause leaks data across tenants

**Reason:** Row-level isolation is the right tradeoff for a SaaS at this stage. BUT it only works if enforced automatically. A single missed `WHERE clientId = X` clause could expose one operator's data to another — that is a business-ending bug.

**Enforcement layers (BOTH are required, neither is optional):**

1. **PostgreSQL Row-Level Security (RLS):** Every tenant table has an RLS policy enforced at the database level using `current_setting('app.current_client_id')`. This is the safety net — it catches bugs that slip through application code.

2. **Prisma tenant middleware:** A global Prisma extension that automatically injects `{ clientId }` into every `findMany`, `findFirst`, `update`, `delete`, and `create` query. It throws an error if a query is attempted without a tenant context set. This is the primary enforcement layer.

**Do not bypass either layer.** If you find yourself needing to query across tenants (e.g., for a super-admin view), use a separate Prisma client instance with a service-role that bypasses RLS, and explicitly document why.

---

### Soft Deletes on All Critical Models
**Decision:** Add `deletedAt DateTime?` to Booking, Order, Customer, Activity, User, Rate, Schedule, TimeSlot, Merchandise, GiftCard, Membership, Resource.

**Alternatives considered:**
- Hard deletes — simpler but operators will accidentally delete things and expect recovery
- Event sourcing — too complex for this stage

**Reason:** Operators make mistakes. A deleted booking is a dispute waiting to happen. `deletedAt` costs almost nothing and prevents a lot of support pain. The Prisma middleware should automatically filter `WHERE deletedAt IS NULL` on all reads by default, with an explicit opt-in to include deleted records.

---

### Audit Log from Day One
**Decision:** `AuditLog` table tracking who changed what, when, on which record.

**Reason:** Operators dispute bookings. Staff make mistakes. When someone asks "who cancelled this booking and when?" you need an answer. Building this after the fact is painful. The `AuditLog` model captures: `clientId`, `userId`, `action` (created/updated/cancelled/checked_in/refunded/etc.), `entityType`, `entityId`, `before` (JSON), `after` (JSON), `ip`, `createdAt`. Write to it via a helper, not inline in every handler.

---

## Payments

### Payment Processor: Square (primary) via Abstraction Layer
**Decision:** Square SDK for payments, but wrapped behind a `PaymentProvider` interface so Stripe can be added later without a rewrite.

**Alternatives considered:**
- Stripe only — better developer experience and subscription API, but operators in this market already have Square accounts and use Square card readers for in-person POS
- Square only with no abstraction — locks us in completely
- Stripe + Square both without abstraction — chaos

**Reason:** The target operators are US-based experience businesses who already use Square terminals at point of sale. Square's in-person card reader integration (Square Terminal, Square Reader) is a genuine requirement for the Register app. However, Square's subscription API is weaker than Stripe's, so we may want Stripe for membership auto-renewals later.

**The abstraction layer:**
```typescript
// packages/types/src/payments.ts
interface PaymentProvider {
  chargeCard(params: ChargeParams): Promise<PaymentResult>
  refund(params: RefundParams): Promise<RefundResult>
  createSubscription(params: SubscriptionParams): Promise<SubscriptionResult>
  cancelSubscription(id: string): Promise<void>
  getBalance(): Promise<BalanceResult>
}
// SquareProvider and StripeProvider both implement this
// Client model stores: paymentProvider: 'square' | 'stripe'
```

**Do not call the Square SDK directly from route handlers.** Always go through the provider interface.

---

## Authentication

### Auth Strategy: JWT + Refresh Tokens (not sessions, not NextAuth)
**Decision:** Short-lived JWTs (15 min) + long-lived refresh tokens (30 days) stored in httpOnly cookies.

**Alternatives considered:**
- Database sessions — simpler but requires a DB hit on every request
- NextAuth — designed for Next.js apps, awkward to share across 4 different frontends + a Fastify API
- Clerk/Auth0 — third-party auth adds cost and a vendor dependency for a core feature

**Reason:** Four separate frontend apps all authenticate against the same Fastify API. A self-contained JWT approach works cleanly across all of them. The JWT payload contains `{ userId, clientId, role }` — the three things every route needs. Refresh tokens are stored in the DB (`RefreshToken` table) so they can be revoked.

**JWT payload shape:**
```typescript
{
  sub: string        // userId
  clientId: string   // tenant
  role: UserRole     // OWNER | ADMIN | MANAGER | STAFF | GUIDE | READ_ONLY
  iat: number
  exp: number
}
```

---

## Frontend

### Shared Component Library: Deferred
**Decision:** Do NOT build `packages/ui` in Phase 0-2. Copy components between apps as needed. Extract to a shared package only when a component is stable and used in 3+ apps.

**Reason:** Shared component libraries add real friction early — you have to manage versioning, publishing, and imports across apps. In Phase 0-2 we're moving fast and components will change constantly. Premature extraction slows everything down. Extract when the pattern is proven, not before.

---

### Booking Site: Next.js App Router
**Decision:** `apps/web` (the public customer booking site) uses Next.js 14+ with App Router.

**Alternatives considered:**
- React SPA (like the other apps) — loses SEO, and the booking site needs to be crawlable (activity pages should rank in Google)
- Next.js Pages Router — App Router is the current standard, no reason to use the legacy router

**Reason:** The booking site has public-facing activity pages that should be indexed by search engines. Next.js with App Router gives us server-side rendering for SEO + client-side interactivity for the booking flow. All other apps (admin, register, kiosk, display) are private/staff-facing and can be plain React SPAs.

---

### Admin, Register, Kiosk, Display: Vite + React SPA
**Decision:** All internal apps use Vite + React (not Next.js).

**Reason:** These apps don't need SEO. They're authenticated, staff-facing tools. Vite is faster to build with than Next.js for SPAs, no need for server-side rendering complexity.

---

## Feature Flags

### Typed Feature Flags (not raw JSON)
**Decision:** Feature flags stored as JSON on the `Client` model but always accessed through a typed interface — never as raw strings.

**Reason:** An untyped JSON blob means typos silently disable features with no error. The canonical feature list lives in `packages/types/src/features.ts` as a const array. A `hasFeature(client, feature)` helper is used everywhere in the codebase. If a feature name changes, TypeScript catches every callsite.

```typescript
// packages/types/src/features.ts
export const FEATURES = [
  'WAIVERS', 'MERCHANDISE', 'GIFT_CARDS', 'MEMBERSHIPS',
  'KIOSK', 'DISPLAY_APP', 'GUIDE_SCHEDULING', 'TIPPING',
  'DISCOUNTS', 'PROMO_CODES', 'AFFILIATES', 'PRIORITY_BOOKING',
  'RESOURCES', 'RENTALS', 'TRANSPORTATION', 'TICKETING', 'PHOTOS'
] as const
export type Feature = typeof FEATURES[number]
export type FeatureFlags = Partial<Record<Feature, boolean>>
```

---

## Offline Mode (Register App)

### Offline Scope: Manifest Cache + Check-in Queue Only
**Decision:** The Register app's offline capability is scoped to: (1) manifest loads from cache, and (2) check-in taps queue locally and sync when reconnected. Nothing else.

**Explicitly out of scope for offline mode:**
- Creating new bookings offline
- Processing card payments offline (Square requires connectivity)
- Refunds offline

**Reason:** "True offline mode" for a full POS is extremely complex and Square's SDK requires connectivity for payment processing. Promising more than this leads to an unreliable feature that damages trust. The two scoped capabilities (manifest cache + check-in queue) solve the actual pain point — staff at docks and fields with spotty LTE can still run their day. This is implemented via Service Worker + IndexedDB queue.

---

## Schema Decisions

### Models Added Beyond the Original Spec
These models were added during the pre-build review because they were referenced in the original schema but not defined, or identified as critical missing pieces:

| Model | Why Added |
|---|---|
| `OrderLineItem` | Referenced in Order but never defined. Needed for itemized receipts and order totals. |
| `Refund` | Referenced in Order but never defined. Needed for any cancellation or return flow. |
| `Addon` | Referenced in Activity but never defined. Needed for upsells at booking (wetsuit, parking, etc.). |
| `CancellationPolicy` | Not in original spec. Operators need to configure refund windows (e.g., full refund 48h+, 50% 24-48h, none under 24h). Without this model, cancellation logic is hardcoded. |
| `AuditLog` | Not in original spec. Required for dispute resolution and staff accountability. |
| `RefreshToken` | Not in original spec. Required for JWT refresh token revocation. |

`TransportBooking` (referenced in Booking) is left undefined intentionally — it belongs to Phase 13 (Transportation module). The FK reference in `Booking` is nullable and will be wired up when that phase is built.

---

## Email

### Email Provider: Resend
**Decision:** Resend (not SendGrid, not Postmark)

**Alternatives considered:**
- SendGrid — industry standard but complex dashboard, aggressive spam filtering on new accounts
- Postmark — excellent deliverability but more expensive
- AWS SES — cheapest but requires more setup and DNS configuration

**Reason:** Resend has the best developer experience of any transactional email provider, generous free tier (3,000 emails/month), React Email for template building, and excellent deliverability. For a new SaaS with low initial volume, this is the right call. Can migrate to SES behind an abstraction layer if volume grows.

**Note on deliverability:** Before sending any transactional email, configure SPF, DKIM, and DMARC DNS records for the sending domain. Skipping this means confirmation emails go to spam.

---

## What NOT to Do (Common Agent Mistakes)

This section exists specifically for AI agents who might try to "improve" the codebase. Do not do the following without a deliberate decision and update to this file:

- **Do not add tRPC.** The API is REST intentionally. tRPC would conflict with the public API roadmap.
- **Do not call the Square or Stripe SDK directly from route handlers.** Use the `PaymentProvider` interface.
- **Do not query the database without going through the tenant-aware Prisma client.** Bypassing tenant middleware is a data leak.
- **Do not hard-delete records** on Booking, Order, Customer, Activity, or any other model with `deletedAt`. Use soft deletes.
- **Do not create `packages/ui`** until components are stable and shared across 3+ apps.
- **Do not add `any` types.** If you don't know the type, use `unknown` and narrow it.
- **Do not store secrets in code or commit `.env` files.** Use Railway/Vercel environment variable dashboards.
- **Do not use `npm`.** This monorepo uses `pnpm` with workspaces. Running `npm install` in a subdirectory will corrupt the lockfile.
