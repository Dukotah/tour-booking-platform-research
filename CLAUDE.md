# CLAUDE.md - Tour & Attraction Booking Platform

## Project Overview

You are building a **tour and attraction booking software platform** — a SaaS product for operators like marinas, ziplines, tubing parks, ski resorts, farms, zoos, aquariums, and any experience-based business. This is a competitive alternative to Singenuity (singenuity.com).

The goal is to build something BETTER than the existing market with more features, better UX, and more flexibility.

## Architecture

### Application Modules

1. **Customer Booking Site** (`/booking` or `book.[domain].com`) — Public-facing booking widget/site
2. **Admin Dashboard** (`manage.[domain].com` or `/admin`) — Full back-office management
3. **Register / POS App** (`register.[domain].com` or `/register`) — Staff-facing tablet/desktop app for check-in, POS, waivers
4. **Kiosk App** — Self-service guest kiosk
5. **Display App** — TV display showing upcoming reservations

### Tech Stack Recommendations

- **Frontend**: React (TypeScript) + Tailwind CSS + shadcn/ui
- **Backend**: Node.js (Express or Fastify) OR Next.js full-stack
- **Database**: PostgreSQL (primary), Redis (sessions/cache)
- **Payments**: Square SDK (primary), Stripe fallback
- **Auth**: JWT + refresh tokens; role-based (Admin, Staff, Guide, Customer)
- **File Storage**: AWS S3 or Cloudflare R2
- **Email**: SendGrid or Postmark
- **SMS**: Twilio
- **ORM**: Prisma or Drizzle
- **Queue**: BullMQ (for email/SMS jobs)
- **Real-time**: Socket.io (for display app, live manifest)

### Monorepo Structure

```
/
├── apps/
│   ├── web/          # Customer booking site (Next.js)
│   ├── admin/        # Admin dashboard (React SPA)
│   ├── register/     # Register/POS app (React SPA, PWA)
│   ├── kiosk/        # Self-service kiosk (React, kiosk-mode)
│   └── display/      # TV display app (React, auto-refresh)
├── packages/
│   ├── api/          # Shared API client
│   ├── db/           # Prisma schema + migrations
│   ├── ui/           # Shared component library
│   ├── email/        # Email templates
│   └── types/        # Shared TypeScript types
├── services/
│   └── api/          # Backend API (Express/Fastify)
├── docs/             # Research and documentation
├── CLAUDE.md         # This file
└── package.json      # Workspace root
```

## Development Commands

```bash
# Install dependencies
npm install

# Run all apps in dev mode
npm run dev

# Run specific app
npm run dev --workspace=apps/admin

# Database
npx prisma migrate dev
npx prisma studio

# Tests
npm test

# Build
npm run build
```

## Key Design Principles

1. **Multi-tenant**: Each operator is a "client" with their own subdomain
2. **Feature flags**: Enable/disable features per client
3. **White-label**: Operators can brand their booking site
4. **Mobile-first**: All interfaces work on tablets and phones
5. **Offline-capable**: Register app works with spotty internet
6. **Real-time**: Manifest updates live across all devices

## Core Data Models

See `/docs/data-models.md` for full schema definitions.

Key entities:
- `Client` (the operator/business)
- `Location` (physical location, a client may have multiple)
- `Activity` (bookable experience)
- `Rate` (pricing tier for an activity)
- `Schedule` / `Timeslot` (availability)
- `Booking` (a reservation)
- `Order` (transaction containing one or more bookings)
- `Customer` (guest/booker)
- `Waiver` (digital liability waiver)
- `Resource` (shared physical asset: boat, vehicle, court)
- `Membership` (season pass, punch pass)
- `GiftCard`
- `Merchandise`
- `User` (staff member with roles)
- `Guide` (staff who leads activities)

## Feature Flags / Modules

Each feature below can be enabled/disabled per client:
- `BOOKINGS` (core, always on)
- `WAIVERS`
- `MERCHANDISE`
- `GIFT_CARDS`
- `GIFT_CERTIFICATES`
- `PACKAGES`
- `MEMBERSHIPS`
- `PHOTOS`
- `TRANSPORTATION`
- `RENTALS`
- `RESOURCES`
- `TICKETING`
- `KIOSK`
- `DISPLAY_APP`
- `GUIDE_SCHEDULING`
- `TIPPING`
- `DISCOUNTS`
- `PROMO_CODES`
- `AFFILIATES`
- `PRIORITY_BOOKING`

## What to Build First (MVP Priority Order)

1. Auth + multi-tenant setup
2. Activity/Rate/Schedule management (admin)
3. Customer booking flow (web)
4. Order + payment processing (Square)
5. Register app - manifest/check-in
6. Waivers
7. Reports (sales, taxes)
8. Email confirmations
9. Merchandise/POS
10. Gift cards
11. Packages
12. Memberships

## Important Notes

- Read `/docs/competitive-analysis.md` for full feature breakdown
- Read `/docs/admin-features.md` for admin dashboard spec
- Read `/docs/register-features.md` for Register/POS app spec
- Read `/docs/booking-flow.md` for customer-facing booking spec
- Read `/docs/data-models.md` for database schema
- Read `/docs/improvements-over-competitor.md` for features to build BETTER
