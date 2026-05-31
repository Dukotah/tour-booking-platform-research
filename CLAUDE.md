# CLAUDE.md - Tour & Attraction Booking Platform

> **For AI agents:** Read `docs/decisions.md` before making any structural changes. It explains why the code is structured the way it is and what not to touch.

---

## Project Overview

You are building a **tour and attraction booking software platform** — a multi-tenant SaaS product for operators like marinas, ziplines, tubing parks, ski resorts, farms, zoos, aquariums, and any experience-based business. This is a direct competitor to Singenuity (singenuity.com), built to be meaningfully better.

**Current state:** Pre-build. Architecture is decided. Starting Phase 0 (foundation).

**Build style:** AI-assisted (vibe coding) with one developer and multiple Claude agents. Code must be extremely well-structured and typed so agents can navigate and extend it reliably.

---

## Application Modules

| App | URL Pattern | Users | Framework |
|-----|------------|-------|-----------|
| Booking Site | `book.[domain].com` | Customers (public) | Next.js 14 App Router |
| Admin Dashboard | `manage.[domain].com` | Owners, Managers | Vite + React SPA |
| Register / POS | `register.[domain].com` | Staff, Guides | Vite + React SPA (PWA) |
| Kiosk | `kiosk.[domain].com` | Self-service guests | Vite + React SPA |
| Display App | `display.[domain].com` | Staff (TV screens) | Vite + React SPA |

All frontends talk to a single **Fastify API** (`services/api`).

---

## Finalized Tech Stack

### Frontend
- **React 18** + **TypeScript** (strict mode)
- **Tailwind CSS** + **shadcn/ui** components
- **Vite** for admin, register, kiosk, display apps
- **Next.js 14** (App Router) for the public booking site only
- **React Query (TanStack Query)** for server state
- **React Hook Form** + **Zod** for forms and validation

### Backend
- **Fastify** (Node.js) — one API, all apps
- **Zod** for request/response validation
- **Prisma** ORM with tenant-enforcing middleware
- **BullMQ** for background jobs (email, SMS, sync)

### Database & Storage
- **Neon** — managed PostgreSQL with Row-Level Security enabled
- **Upstash** — serverless Redis (sessions, rate limiting, queues)
- **Cloudflare R2** — file storage (waiver PDFs, activity images, photos)

### Hosting
- **Vercel** — booking site (`apps/web`) + custom operator domains
- **Railway** — Fastify API (`services/api`) + BullMQ workers

### Payments
- **Square SDK** — primary processor (in-person + online, US)
- Wrapped behind a `PaymentProvider` interface — do not call Square SDK directly
- Stripe can be added as a second provider without code changes to route handlers

### Communication
- **Resend** — transactional email (booking confirmations, reminders, waivers)
- **React Email** — email templates
- **Twilio** — SMS (booking confirmations, reminders, 2-way)

### Auth
- **JWT** (15-min expiry) + **refresh tokens** (30-day, stored in DB, httpOnly cookie)
- Roles: `OWNER | ADMIN | MANAGER | STAFF | GUIDE | READ_ONLY`
- JWT payload: `{ sub: userId, clientId, role }`

### Real-time
- **Socket.io** — live manifest updates, Display App, live availability

---

## Monorepo Structure

```
/
├── apps/
│   ├── web/          # Customer booking site (Next.js)
│   ├── admin/        # Admin dashboard (Vite + React)
│   ├── register/     # Register/POS app (Vite + React, PWA)
│   ├── kiosk/        # Self-service kiosk (Vite + React)
│   └── display/      # TV display app (Vite + React)
├── packages/
│   ├── types/        # Shared TypeScript types + Zod schemas (source of truth)
│   ├── api-client/   # Generated API client used by all frontends
│   └── config/       # Shared ESLint, TypeScript, Tailwind configs
├── services/
│   └── api/          # Fastify backend (routes, middleware, workers)
├── packages/db/      # Prisma schema + migrations + seeding
├── docs/             # All research and decision documents
├── CLAUDE.md         # This file
└── package.json      # pnpm workspace root
```

**Package manager: `pnpm` with workspaces. Never use `npm` or `yarn`.**

**Do not create `packages/ui`** until a component is stable and needed in 3+ apps.

---

## Development Commands

```bash
# Install all dependencies
pnpm install

# Run everything in dev mode
pnpm dev

# Run a specific app
pnpm --filter apps/admin dev
pnpm --filter services/api dev

# Database
pnpm --filter packages/db migrate:dev   # run migrations
pnpm --filter packages/db studio        # open Prisma Studio
pnpm --filter packages/db generate      # regenerate Prisma client

# Type check all packages
pnpm typecheck

# Lint
pnpm lint

# Build everything
pnpm build
```

---

## Multi-Tenancy Rules (Critical)

Every operator is a `Client`. All data is scoped to a `clientId`.

1. **Never query the database without tenant context.** Use the tenant-aware Prisma client exported from `packages/db`. It automatically injects `clientId` on every query and throws if the context is missing.

2. **PostgreSQL RLS is the safety net.** Row-Level Security policies are on every tenant table. These enforce `clientId` at the DB level even if application code has a bug.

3. **The JWT tells you the tenant.** Every authenticated request has `req.user.clientId`. Set it in the Fastify tenant middleware before any route handler runs.

4. **Never hard-code a clientId.** Never bypass the tenant middleware. Never use a raw Prisma client without tenant context except in explicitly marked super-admin routes.

---

## Key Design Rules

1. **Soft deletes everywhere.** Critical models have `deletedAt DateTime?`. Never hard-delete. The Prisma middleware filters deleted records automatically.
2. **Audit everything.** Mutations on Booking, Order, Payment, Refund, Customer write to `AuditLog`.
3. **Typed feature flags.** Use `hasFeature(client, 'WAIVERS')` — never check raw JSON strings.
4. **Payment abstraction.** Call `paymentProvider.chargeCard()` — never `squareClient.paymentsApi.createPayment()` directly from a route handler.
5. **Zod is the contract.** Request bodies, response shapes, and shared types are all Zod schemas in `packages/types`. Infer TypeScript types from Zod, not the other way around.
6. **No `any`.** Use `unknown` and narrow it. Strict TypeScript throughout.

---

## Core Data Models

Full schema in `packages/db/prisma/schema.prisma` and documented in `docs/data-models.md`.

Key entities:
- `Client` — the operator/business (tenant root)
- `Location` — physical location
- `Activity → Rate → Schedule → TimeSlot` — the bookable product hierarchy
- `Customer → Order → Booking` — the purchase hierarchy
- `Payment`, `Refund`, `OrderLineItem` — financial records
- `Waiver`, `WaiverTemplate` — digital liability system
- `Resource` — shared physical assets (boats, kayaks)
- `Merchandise`, `MerchVariant` — retail products with size/color variants
- `GiftCard` — physical and digital gift cards
- `MembershipPlan`, `CustomerMembership` — season passes and punch passes
- `PromoCode`, `Discount` — promotional pricing
- `User` — staff with roles
- `Guide` — activity guides
- `CancellationPolicy` — operator-configured refund windows
- `AuditLog` — immutable record of all mutations
- `RefreshToken` — JWT refresh token store

---

## Feature Flags

Each feature can be enabled/disabled per `Client`. Always check via `hasFeature(client, feature)`.

Available flags: `WAIVERS`, `MERCHANDISE`, `GIFT_CARDS`, `GIFT_CERTIFICATES`, `PACKAGES`, `MEMBERSHIPS`, `PHOTOS`, `TRANSPORTATION`, `RENTALS`, `RESOURCES`, `TICKETING`, `KIOSK`, `DISPLAY_APP`, `GUIDE_SCHEDULING`, `TIPPING`, `DISCOUNTS`, `PROMO_CODES`, `AFFILIATES`, `PRIORITY_BOOKING`

`BOOKINGS` is always on and is not a flag.

---

## MVP Build Order (Phase 0 → Phase 1)

**Phase 0 — Foundation (do this first, nothing else):**
1. Monorepo scaffold (Turborepo + pnpm)
2. Prisma schema — all core models
3. Neon Postgres + RLS policies + Prisma tenant middleware
4. Fastify API skeleton with auth routes (`/auth/login`, `/auth/refresh`, `/auth/logout`, `/auth/me`)
5. JWT + refresh token system
6. Multi-tenant subdomain middleware
7. Admin app: login page → blank authenticated dashboard
8. Deploy: Railway (API) + Vercel (admin)

**Phase 1 — MVP Booking:**
1. Activity / Rate / Schedule / TimeSlot CRUD (admin)
2. Public booking site — activity listing, date picker, participant selection, checkout
3. Square payment integration (online card)
4. Order confirmation + booking confirmation email (Resend)
5. Basic order and booking list views (admin)

**Deliverable:** A real operator can create experiences and take real payments from customers online.

See `docs/roadmap.md` for all 15 phases.

---

## Reference Documents

| File | Contents |
|------|----------|
| `docs/decisions.md` | **Read first.** All architectural decisions and why. |
| `docs/roadmap.md` | 15-phase, 52-week build plan |
| `docs/competitive-analysis.md` | Feature gap analysis vs existing platforms |
| `docs/admin-features.md` | Admin dashboard full spec |
| `docs/register-features.md` | Register/POS app spec |
| `docs/booking-flow.md` | Customer booking flow spec |
| `docs/data-models.md` | Database schema reference |
| `docs/improvements-over-competitor.md` | Where and how to beat the competition |
