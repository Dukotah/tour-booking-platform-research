# Tour Booking Platform Research

> Competitive analysis and feature research for building a superior tour & attraction booking SaaS platform.

---

## What This Is

This repository contains the complete product specification and roadmap for building a best-in-class booking platform for experience-based businesses: marinas, ziplines, tubing parks, farm tours, kayak rentals, escape rooms, axe throwing, ski schools, ghost tours, and more.

The goal is to build a better, more modern alternative to existing platforms — with superior UX, true offline support, open APIs, and operator-friendly features that existing tools are missing.

---

## Repository Structure

```
/
├── CLAUDE.md              # AI coding context (architecture, tech stack, dev commands)
├── README.md              # This file
└── docs/
    ├── roadmap.md                    # Phased feature roadmap (15 phases, 52 weeks)
    ├── competitive-analysis.md       # Feature-by-feature gap analysis
    ├── admin-features.md             # Admin dashboard full spec
    ├── register-features.md          # Register/POS app full spec
    ├── booking-flow.md               # Customer booking flow spec
    ├── data-models.md                # Database schema & entity relationships
    └── improvements-over-competitor.md  # Where and how to beat the competition
```

---

## Quick Links

| Document | Purpose |
|----------|---------|
| [CLAUDE.md](./CLAUDE.md) | Start here for AI-assisted development |
| [Roadmap](./docs/roadmap.md) | 15-phase, 52-week build plan with checkboxes |
| [Competitive Analysis](./docs/competitive-analysis.md) | Feature matrix vs existing platforms |
| [Admin Features](./docs/admin-features.md) | Full admin dashboard spec |
| [Register / POS](./docs/register-features.md) | Staff tablet/desktop app spec |
| [Booking Flow](./docs/booking-flow.md) | Customer-facing booking experience |
| [Data Models](./docs/data-models.md) | PostgreSQL/Prisma schema |
| [Improvements](./docs/improvements-over-competitor.md) | 14 specific ways to build better |

---

## Platform Architecture

Four primary applications, one backend API:

| App | URL Pattern | Users |
|-----|------------|-------|
| Booking Site | book.[domain].com | Customers |
| Admin Dashboard | manage.[domain].com | Owners, Managers |
| Register / POS | register.[domain].com | Staff, Guides |
| Kiosk | kiosk.[domain].com | Self-service guests |

**Tech Stack:** React + TypeScript + Tailwind (frontend), Node.js/Fastify (API), PostgreSQL + Prisma (database), Square SDK (payments), AWS S3 (files), SendGrid (email), Twilio (SMS)

---

## Roadmap Summary

| Phase | Focus | Timeline |
|-------|-------|----------|
| 0 | Foundation (monorepo, auth, multi-tenant) | Weeks 1-3 |
| 1 | MVP Core Booking (activities, scheduling, payments) | Weeks 4-8 |
| 2 | Register / POS App | Weeks 9-12 |
| 3 | Waivers | Weeks 13-15 |
| 4 | Merchandise & POS | Weeks 16-18 |
| 5 | Gift Cards & Certificates | Weeks 19-21 |
| 6 | Packages & Discounts | Weeks 22-24 |
| 7 | Memberships & Season Passes | Weeks 25-28 |
| 8 | Resources & Rentals | Weeks 29-31 |
| 9 | Reporting & Analytics | Weeks 32-34 |
| 10 | Kiosk App | Weeks 35-36 |
| 11 | Display App | Weeks 37-38 |
| 12 | Photos | Weeks 39-41 |
| 13 | Transportation | Weeks 42-44 |
| 14 | Guide Scheduling | Weeks 45-47 |
| 15 | Multi-Location & Enterprise | Weeks 48-52 |

---

## Key Differentiators

1. **Customer self-service portal** — customers can reschedule/cancel online
2. **True offline mode** — Register app works without internet at docks/fields
3. **Dynamic pricing** — demand-based, early-bird, group-tier pricing
4. **Open API + webhooks** — connect QuickBooks, Mailchimp, Zapier, channel managers
5. **Product variants** — size/color support for merchandise
6. **Modern UX** — Apple Pay, single-page booking flow, mobile-first design
7. **Superior reporting** — custom builder, scheduled email delivery, accounting export
