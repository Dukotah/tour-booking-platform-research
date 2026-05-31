# Tour Booking Platform — Product Roadmap

> Building a best-in-class SaaS platform for tour & attraction operators. A superior alternative to existing booking platforms.

---

## Vision

A modern, flexible, white-label booking platform that empowers experience-based businesses (marinas, ziplines, tubing parks, ski resorts, farms, zoos, kayak rentals, escape rooms, and more) to sell, manage, and grow.

---

## Release Milestones

### Phase 0 — Foundation (Weeks 1-3)
**Goal:** Repo, CI/CD, auth, multi-tenant scaffolding

- [ ] Monorepo setup (Turborepo + pnpm workspaces)
- [ ] Shared ESLint, Prettier, TypeScript config
- [ ] CI/CD pipeline (GitHub Actions -> Vercel/Railway)
- [ ] PostgreSQL + Prisma schema (core entities)
- [ ] Multi-tenant middleware (subdomain -> client resolver)
- [ ] Auth system: JWT + refresh tokens, role-based (Admin, Staff, Guide, Customer)
- [ ] Basic admin shell (login, dashboard skeleton)
- [ ] Environment/secrets management

**Deliverable:** Authenticated admin portal with multi-tenant routing

---

### Phase 1 — MVP Core Booking (Weeks 4-8)
**Goal:** Operators can create activities and accept bookings online

#### Admin Dashboard
- [ ] Activity CRUD (name, description, images, duration, capacity)
- [ ] Rate management (per-person pricing tiers: adult/child/senior)
- [ ] Schedule builder (recurring + one-off time slots)
- [ ] Availability rules (blackout dates, override capacity)
- [ ] Basic order list / booking list views

#### Customer Booking Flow
- [ ] Public booking site (white-labeled per client)
- [ ] Activity listing page with date picker
- [ ] Participant count + rate selection
- [ ] Customer info form (name, email, phone)
- [ ] Order summary + checkout
- [ ] Square payment integration (card present + online)
- [ ] Booking confirmation page + email

**Deliverable:** Live booking site that takes real payments

---

### Phase 2 — Register / POS App (Weeks 9-12)
**Goal:** Staff can manage the day from a tablet or desktop

- [ ] Daily manifest view (all bookings for today)
- [ ] Check-in flow (tap to check in a booking)
- [ ] Walk-up / in-person booking creation
- [ ] POS cash + card payment (Square Reader integration)
- [ ] Booking search (name, confirmation #, phone)
- [ ] Booking modification (reschedule, upgrade, notes)
- [ ] Cancellation + refund flow
- [ ] Offline mode (PWA with local queue sync)
- [ ] Printer support (receipt / ticket printing)

**Deliverable:** Staff can run a full day from the Register app

---

### Phase 3 — Waivers & Compliance (Weeks 13-15)
**Goal:** Digital waiver collection, legally sound

- [ ] Waiver template builder (rich text, custom fields)
- [ ] Waiver assignment to activities
- [ ] Pre-booking waiver signing (email link)
- [ ] On-site waiver signing (Register app / Kiosk)
- [ ] Waiver storage + retrieval (linked to booking/customer)
- [ ] Group waiver (one signer covers party)
- [ ] Minor waiver (parent/guardian signature)
- [ ] Waiver status on manifest
- [ ] PDF export / archival (S3)

**Deliverable:** Full digital waiver system replacing paper

---

### Phase 4 — Merchandise & POS (Weeks 16-18)
**Goal:** Operators can sell physical items at point of sale

- [ ] Product catalog (name, SKU, price, image, category)
- [ ] Inventory tracking (stock levels, low-stock alerts)
- [ ] POS merchandise add-on during booking checkout
- [ ] Standalone merchandise sale (no booking required)
- [ ] Barcode scanner support
- [ ] Merchandise reports (sales by item, by category)

**Deliverable:** Full retail POS capability

---

### Phase 5 — Gift Cards & Certificates (Weeks 19-21)
**Goal:** Gift card sales and redemption

- [ ] Physical gift card (swipe / code entry at POS)
- [ ] Digital gift card (purchase online, email delivery)
- [ ] Gift certificate (dollar-value voucher for specific activity)
- [ ] Custom designs per operator
- [ ] Partial redemption + balance tracking
- [ ] Gift card reports

**Deliverable:** Complete gift card/certificate system

---

### Phase 6 — Packages & Discounts (Weeks 22-24)
**Goal:** Bundled experiences and promotional pricing

- [ ] Package builder (combine multiple activities + merchandise)
- [ ] Package pricing (bundle discount)
- [ ] Package booking flow (auto-creates sub-bookings)
- [ ] Promo codes (percentage off, dollar off, free item)
- [ ] Discount rules (first-time, seasonal, group size)
- [ ] Affiliate links with tracking
- [ ] Priority booking (members / VIPs get early access)

**Deliverable:** Promotions and bundled upsell capability

---

### Phase 7 — Memberships & Season Passes (Weeks 25-28)
**Goal:** Recurring revenue and loyalty programs

- [ ] Membership plan builder (name, price, billing interval, benefits)
- [ ] Punch pass (N uses, expiry date)
- [ ] Season pass (unlimited visits, date-bound)
- [ ] Membership portal (customer self-service)
- [ ] Membership check-in at Register (scan/lookup)
- [ ] Auto-renewal billing (Stripe/Square subscriptions)
- [ ] Membership reports + churn tracking

**Deliverable:** Full membership / season pass system

---

### Phase 8 — Resources & Rentals (Weeks 29-31)
**Goal:** Manage shared physical assets (boats, bikes, equipment)

- [ ] Resource catalog (name, type, capacity, image)
- [ ] Resource assignment to activities/bookings
- [ ] Conflict detection (double-booking prevention)
- [ ] Rental-only bookings (hourly/daily rate, no guide)
- [ ] Resource availability calendar
- [ ] Damage / maintenance tracking

**Deliverable:** Resource management for equipment-based operators

---

### Phase 9 — Reporting & Analytics (Weeks 32-34)
**Goal:** Operators can understand their business

- [ ] Sales dashboard (revenue, transactions, average order)
- [ ] Booking report (by activity, by date range, by status)
- [ ] Customer report (new vs returning, source)
- [ ] Tax report (collected, by rate)
- [ ] Capacity utilization report
- [ ] Guide performance report
- [ ] Merchandise sales report
- [ ] Gift card liability report
- [ ] Membership report
- [ ] Export to CSV / PDF
- [ ] Scheduled email reports

**Deliverable:** Complete reporting suite

---

### Phase 10 — Kiosk App (Weeks 35-36)
**Goal:** Self-service check-in and booking for guests

- [ ] Kiosk mode (fullscreen, locked-down browser)
- [ ] Self check-in by confirmation # or name lookup
- [ ] Waiver signing on kiosk
- [ ] New booking purchase on kiosk
- [ ] Gift card redemption
- [ ] Idle screen with branding/promotions
- [ ] Receipt printing from kiosk

**Deliverable:** Deployable self-service kiosk

---

### Phase 11 — Display App (Weeks 37-38)
**Goal:** TV display showing live manifest/upcoming trips

- [ ] Live manifest feed (Socket.io real-time)
- [ ] Upcoming departures view (by activity, by time)
- [ ] Check-in status (X/Y checked in)
- [ ] Custom branding + layout per operator
- [ ] Auto-refresh fallback
- [ ] Weather widget (optional)

**Deliverable:** Staff-facing TV display

---

### Phase 12 — Photos & Upsells (Weeks 39-41)
**Goal:** Post-experience photo sales

- [ ] Photo upload by guide/staff (linked to booking)
- [ ] Guest photo gallery (access via confirmation link)
- [ ] Photo purchase flow (individual or package)
- [ ] Automated delivery (email/download link)
- [ ] Photo storage (S3 + CDN)

**Deliverable:** Photo sales system

---

### Phase 13 — Transportation (Weeks 42-44)
**Goal:** Shuttle/transport management for pickup-based activities

- [ ] Pickup location management
- [ ] Pickup time windows per location
- [ ] Guest pickup assignment at booking
- [ ] Driver manifest (by vehicle/route)
- [ ] Transportation capacity management
- [ ] Guest notifications (pickup reminders, delays)

**Deliverable:** Transportation/shuttle module

---

### Phase 14 — Guide Scheduling (Weeks 45-47)
**Goal:** Assign guides to activities and manage their schedules

- [ ] Guide profile management
- [ ] Guide assignment to time slots / activities
- [ ] Guide availability (set working hours, days off)
- [ ] Guide mobile view (their schedule for the day)
- [ ] Guide tip tracking (collected at Register)
- [ ] Guide payroll report

**Deliverable:** Full guide management system

---

### Phase 15 — Multi-Location & Enterprise (Weeks 48-52)
**Goal:** Support operators with multiple physical locations

- [ ] Location management (each with own schedule/activities)
- [ ] Cross-location reporting
- [ ] Location-specific branding
- [ ] Staff permissions by location
- [ ] Centralized multi-location admin

**Deliverable:** Enterprise-grade multi-location support

---

## Ongoing / Parallel Work

- **Security**: SOC 2 prep, pen testing, PCI compliance (Square handles card data)
- **Performance**: Load testing, CDN setup, DB query optimization
- **Accessibility**: WCAG 2.1 AA compliance across all apps
- **Mobile**: Native wrapper (Capacitor) for Register/Kiosk apps
- **Integrations**: Google Analytics, Meta Pixel, Mailchimp, QuickBooks, Xero
- **API**: Public REST API + webhooks for third-party integrations
- **White-label**: Custom domain + SSL per operator
- **Onboarding**: In-app guided setup wizard for new operators

---

## Key Differentiators

See [improvements-over-competitor.md](./improvements-over-competitor.md) for the full breakdown.

1. **Better UX** - Modern, fast, mobile-first design throughout
2. **True offline mode** - Register app works without internet
3. **Flexible pricing** - More rate types, dynamic pricing support
4. **Real-time everything** - Live manifest, live availability
5. **Open integrations** - Webhook/API first, plays well with others
6. **Superior reporting** - More depth, better export options
7. **Transparent pricing** - No per-booking fees, flat SaaS pricing

---

## File Index

| File | Contents |
|------|----------|
| [CLAUDE.md](../CLAUDE.md) | AI coding context - architecture, stack, dev commands |
| [roadmap.md](./roadmap.md) | This file - phased feature roadmap |
| [competitive-analysis.md](./competitive-analysis.md) | Feature-by-feature competitor breakdown |
| [admin-features.md](./admin-features.md) | Admin dashboard full feature spec |
| [register-features.md](./register-features.md) | Register/POS app full feature spec |
| [booking-flow.md](./booking-flow.md) | Customer-facing booking flow spec |
| [data-models.md](./data-models.md) | Database schema and entity relationships |
| [improvements-over-competitor.md](./improvements-over-competitor.md) | Where to beat the competition |
