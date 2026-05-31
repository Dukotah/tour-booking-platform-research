# Improvements Over Competitors — Build This Better

This document outlines specific areas where this platform should be built to be meaningfully better than existing tour booking platforms. Each section describes the gap and the target experience.

---

## 1. Customer Self-Service Portal

**The gap:** Customers who book online have no way to manage their own reservations without calling. There is no customer login, no "manage my booking" page, and no way to reschedule or cancel online.

**What to build:**
- Token-based booking management (no account required — link in email)
- View booking details, receipt, and waiver status
- Reschedule within policy window (e.g., within 48 hours, no same-day changes)
- Cancel within policy window with automatic refund
- Optional customer account (email + password or magic link)
- Customer dashboard showing booking history and upcoming reservations
- Membership self-service (view status, update payment, pause, cancel)

**Why it wins:** Reduces front-desk calls by 30-40%. Modern customers expect Amazon-style self-service.

---

## 2. Modern, Mobile-First Booking UX

**The gap:** The existing booking flow feels dated. It uses multi-page forms with page reloads. Mobile layout is functional but not delightful. Images are slow to load. No Apple Pay / Google Pay.

**What to build:**
- Single-page booking flow (React, no page reloads between steps)
- Bottom sheet date/time picker on mobile
- Swipeable image galleries
- Apple Pay + Google Pay via Square SDK (one-tap payment)
- Skeleton loading states (not spinners)
- Instant availability feedback (no loading delay when changing date)
- Progress indicator (Step 1 of 4)
- Auto-fill support for all fields
- Smooth animations between steps

**Why it wins:** Conversion rate improvement. Customers expect Airbnb-quality UX.

---

## 3. True Offline Mode for Register App

**The gap:** The Register app technically has some offline support but it is unreliable. At outdoor venues (docks, fields, slopes) with spotty LTE, staff routinely encounter failures.

**What to build:**
- Service Worker with full manifest caching on page load
- IndexedDB queue for all write operations (check-ins, cash bookings, merch sales)
- Visual sync status indicator (green/yellow/red)
- Deterministic offline-to-online merge (no data loss)
- Conflict resolution UI when coming back online (rare but handled gracefully)
- Works on airplane mode — manifest loads in under 1 second from cache

**Why it wins:** Operators at docks, fields, and remote venues will choose this platform specifically for reliability.

---

## 4. Dynamic and Flexible Pricing

**The gap:** Operators can only set static prices per rate type. There is no demand-based pricing, early-bird discounts, or tiered group pricing.

**What to build:**
- Dynamic pricing rules: "If slot is 80%+ full, increase price by 15%"
- Early-bird pricing: "Book 14+ days in advance, save $10"
- Last-minute pricing: "Book within 24 hours, get 20% off"
- Group volume tiers: "6+ people: $5 off per person; 10+ people: $10 off"
- Seasonal rate overrides (without cloning the whole activity)
- A/B pricing test mode
- Price calendar view (show different prices per day in booking calendar)

**Why it wins:** Operators can maximize revenue during peak times and fill gaps during slow periods. This alone can increase operator revenue 10-20%.

---

## 5. Real-Time Availability and Live Manifest

**The gap:** Availability updates require page refresh. The manifest does not update in real-time — staff have to reload to see new check-ins or bookings.

**What to build:**
- WebSocket connection (Socket.io) for manifest updates
- Live capacity counter on booking site ("Only 3 spots left!")
- Live check-in status on manifest (updates instantly when another device checks someone in)
- Optimistic UI updates (show change immediately, confirm from server)
- Display App (TV board) fed from same socket, zero-latency

**Why it wins:** Eliminates "we got double-booked" complaints and "manifest was stale" errors.

---

## 6. Open API and Webhooks

**The gap:** There is no public API and no webhook system. Operators cannot build custom integrations, connect accounting software, or distribute through channels like Viator or GetYourGuide.

**What to build:**
- REST API with full CRUD for bookings, customers, activities, orders
- API key management per operator (scoped read/write keys)
- Webhook event subscriptions: booking.created, booking.cancelled, payment.completed, checkin.completed, waiver.signed, etc.
- Webhook retry logic + delivery logs
- Zapier integration (no-code automation)
- Embeddable booking widget (script tag)
- OAuth2 for third-party app connections

**Why it wins:** Unlocks the ecosystem. Operators can connect QuickBooks, Mailchimp, custom CRMs, and eventually channel managers. This is a major recurring complaint about closed platforms.

---

## 7. Product Variants (Merchandise)

**The gap:** Merchandise items cannot have variants like size or color. An operator selling branded t-shirts has to create a separate product for each size.

**What to build:**
- Variant system: define attributes (Size, Color) and values (S/M/L/XL, Red/Blue/Black)
- Per-variant: SKU, price, stock
- Variant selection in POS (dropdown or button grid)
- Inventory tracked per variant
- Variant-aware barcode support

**Why it wins:** Removes a major operational workaround. Operators with apparel/gear retail will specifically cite this as a reason to switch.

---

## 8. Customer Loyalty & Rewards

**The gap:** There is no loyalty program. Returning customers get no recognition or reward.

**What to build:**
- Points system: earn points per dollar spent
- Rewards catalog: redeem points for discounts, free activities, merchandise
- Operator-configurable earn/burn rates
- Points balance shown in customer portal and at Register
- Birthday rewards (automated)
- "Visit streak" rewards (come 5 times, get a free gift)

**Why it wins:** Increases repeat visit rate. For operators with returning customers (ziplining, kayaking, escape rooms), this directly drives LTV.

---

## 9. Accounting Integrations

**The gap:** Operators must manually export reports and import into QuickBooks or Xero. This is time-consuming and error-prone.

**What to build:**
- Native QuickBooks Online sync (invoices, payments, refunds, tax)
- Xero sync
- Daily reconciliation export (Square settlement vs platform records)
- Chart of accounts mapping UI (operator maps revenue categories to their COA)
- Sync log with error details

**Why it wins:** Saves operators 2-5 hours per week. Accountants will specifically request this.

---

## 10. Better Reporting & Custom Reports

**The gap:** Reports are fixed-template. Operators cannot build custom reports, cannot schedule email delivery, and cannot export to formats beyond CSV.

**What to build:**
- Custom report builder (select metrics, dimensions, date ranges, filters)
- Save and name custom reports
- Schedule reports (daily/weekly/monthly email delivery)
- PDF export with operator branding
- Comparison mode (this week vs last week vs same week last year)
- Cohort analysis (customer retention over time)
- Forecasting (projected revenue based on current bookings)
- Embed in daily email digest

**Why it wins:** Operators who care about their business deeply will value this. It also reduces support requests ("can you add a report for X").

---

## 11. Channel Distribution (Future Phase)

**The gap:** Activities are only bookable through the operator's own site. Competitors like Viator, GetYourGuide, and Airbnb Experiences have massive marketing reach.

**What to build:**
- Channel manager: sync availability to Viator, GetYourGuide, Airbnb Experiences
- Bookings from channels flow into the platform as regular bookings
- Rate management per channel (mark up for commission)
- Channel performance reporting

**Why it wins:** Unlocks new customer acquisition without operator needing to do marketing. This is a significant value-add for operators trying to grow.

---

## 12. Superior Waiver Customization

**The gap:** Waiver templates have limited custom fields. Operators cannot add conditional logic, file uploads, or complex health disclosures.

**What to build:**
- Rich custom field types: text, number, checkbox, dropdown, date, signature
- Conditional logic: "If age < 18, show guardian section"
- Required/optional field control
- Section grouping
- Waiver versioning (customers who signed v1 need to re-sign v2)
- Annual expiry with auto-re-sign reminders
- Template library (starter templates for common activity types)

---

## 13. SMS Automation

**The gap:** SMS capabilities are limited. There is no automated SMS workflow.

**What to build:**
- Booking confirmation SMS (opt-in)
- Day-before reminder SMS
- "Your waiver is ready to sign" SMS
- "We're sorry you missed us" no-show follow-up
- Custom SMS campaigns (e.g., "Spring sale - 20% off this weekend")
- Two-way SMS for simple responses (e.g., reply CONFIRM to confirm)
- Unsubscribe handling (STOP keyword)
- SMS opt-in at booking

---

## 14. Staff Performance Tools

**The gap:** There is no way to see staff performance beyond "who did what in the logs."

**What to build:**
- Upsell tracking per staff member (average add-on $ per booking)
- Check-in speed metrics
- Tip amounts per guide over time
- Guide satisfaction (simple post-trip feedback SMS: "Rate your guide 1-5")
- Leaderboard (friendly competition, opt-in)

---

## Summary Priority Table

| Improvement | Impact | Effort | Priority |
|-------------|--------|--------|----------|
| Customer self-service portal | High | Medium | Phase 1 |
| Modern booking UX | High | High | Phase 1 |
| True offline mode | High | High | Phase 2 |
| Dynamic pricing | High | Medium | Phase 6 |
| Real-time availability | Medium | Medium | Phase 1 |
| Open API + webhooks | High | Medium | Phase 3 (parallel) |
| Product variants | Medium | Low | Phase 4 |
| Customer loyalty | Medium | High | Phase 8 |
| Accounting integrations | Medium | Medium | Phase 9 |
| Better reporting | Medium | Medium | Phase 9 |
| Channel distribution | High | Very High | Future |
| Waiver customization | Low | Low | Phase 3 |
| SMS automation | Medium | Low | Phase 2 |
| Staff performance tools | Low | Low | Phase 14 |
