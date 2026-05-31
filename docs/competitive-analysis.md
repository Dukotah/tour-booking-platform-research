# Competitive Analysis — Tour Booking Platform

This document provides a feature-by-feature breakdown of the existing booking platform category, identifying what exists today and opportunities to build better.

---

## Platform Overview

The target market is small-to-mid-size experience-based operators: marinas, ziplines, tubing companies, farm tours, kayak rentals, axe throwing, escape rooms, paintball, ski/snowboard schools, winery tours, ghost tours, and similar businesses.

Key platform modules observed in the category:

1. **Admin Dashboard** (manage.domain.com) - back-office management
2. **Booking Site** (book.domain.com) - public-facing customer booking
3. **Register / POS App** (register.domain.com) - staff tablet/desktop app
4. **Kiosk App** - self-service guest terminal
5. **Display App** - TV/monitor showing upcoming trips

---

## Feature Matrix

### Core Booking

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Activity/experience listings | Yes | Basic title + description + images |
| Date picker | Yes | Calendar-based, date-specific availability |
| Time slot selection | Yes | Shows available times per date |
| Participant count selection | Yes | Per-rate-type (adult/child/senior) |
| Group size limits (min/max) | Yes | Per activity |
| Capacity tracking | Yes | Prevents overbooking |
| Waitlist | Partial | Basic waitlist exists, no automated promotion |
| Booking confirmation email | Yes | Plain-text with order details |
| Booking confirmation SMS | Partial | Limited SMS support |
| Rescheduling by customer | No | Staff must do it |
| Cancellation by customer | No | Staff must do it |
| Customer portal (manage bookings) | No | Major gap |
| Multi-language support | No | English only |
| Accessibility (WCAG) | Partial | Needs improvement |

### Pricing & Rates

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Per-person rates (adult/child/senior) | Yes | Up to ~5 rate types |
| Flat-rate (per booking, not per person) | Yes | |
| Private/exclusive booking rate | Yes | Group buys the whole slot |
| Dynamic pricing (demand-based) | No | Major gap |
| Seasonal pricing | Partial | Manual rate override per date |
| Early-bird pricing | No | |
| Last-minute discounts | No | |
| Deposit + balance due | Partial | Limited configuration |
| Tiered pricing (price decreases as group grows) | No | |
| Add-ons (upsell items at booking) | Partial | Basic, not flexible |

### Schedule Management

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Recurring schedules (daily, weekly) | Yes | |
| One-off schedule overrides | Yes | |
| Blackout dates | Yes | |
| Seasonal date ranges | Yes | |
| Capacity override per slot | Yes | |
| Buffer time between bookings | No | Can't set cleanup/prep time |
| Multi-day / overnight bookings | No | |
| Booking cutoff time | Yes | e.g., no bookings within 2 hrs of start |
| Advance booking window | Yes | e.g., max 90 days ahead |
| Resource-based availability | Partial | Tied to activity, not asset-aware |

### Payments

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Online card payment | Yes | Square primary |
| POS card payment (Square Reader) | Yes | |
| Cash payment (POS) | Yes | |
| Partial payment / deposit | Partial | Limited |
| Refunds (full) | Yes | |
| Refunds (partial) | Yes | |
| Gratuity / tip collection | Yes | At POS |
| Tax configuration | Yes | Per rate/product |
| Tax exemption (per customer) | No | |
| Invoice / net-terms billing | No | |
| Split payment | No | |
| Multi-currency | No | USD only |

### Waivers

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Digital waiver templates | Yes | Rich text editor |
| Waiver linked to activity | Yes | |
| Pre-booking waiver (email) | Yes | Sent after booking |
| On-site waiver (device) | Yes | Tablet/kiosk signing |
| Waiver storage + PDF | Yes | Stored in S3 |
| Minor waiver (parent signs) | Yes | |
| Group waiver (one signer) | Yes | |
| Waiver required before check-in | Yes | Status on manifest |
| Custom waiver fields | Partial | Limited custom fields |
| Waiver expiry (re-sign annually) | No | |
| Waiver API / webhook | No | |

### Merchandise / Retail

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Product catalog | Yes | |
| Inventory tracking | Yes | Basic stock count |
| POS sale (standalone) | Yes | |
| Add-on at booking checkout | Yes | |
| Barcode scanning | Yes | |
| Product categories | Yes | |
| Product variants (size/color) | No | Major gap for apparel |
| Bundle products | No | |
| Low-stock alerts | Partial | |
| Purchase orders / receiving | No | |

### Gift Cards & Certificates

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Physical gift card (code entry) | Yes | |
| Digital gift card (email delivery) | Yes | |
| Gift certificate (activity-specific) | Yes | |
| Partial redemption | Yes | |
| Balance inquiry | Yes | |
| Expiry date | Yes | |
| Custom design/branding | Partial | Limited templates |
| Bulk gift card generation | No | |
| API for third-party sales | No | |

### Packages

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Package builder (bundle activities) | Yes | |
| Package + merchandise bundles | Yes | |
| Package pricing (bundled discount) | Yes | |
| Package booking flow | Yes | |
| Package redemption at POS | Yes | |
| Time-limited packages | Partial | |
| Configurable package contents | Partial | Limited flexibility |

### Discounts & Promotions

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Promo codes (% off) | Yes | |
| Promo codes ($ off) | Yes | |
| Promo codes (free item) | Partial | |
| One-time-use codes | Yes | |
| Usage limit per code | Yes | |
| Expiry date per code | Yes | |
| Affiliate codes with tracking | Yes | |
| Volume discounts (group size) | No | |
| Loyalty points / rewards | No | |
| Flash sale pricing | No | |

### Memberships

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Membership plan builder | Yes | |
| Season pass (date-bound) | Yes | |
| Punch pass (N uses) | Yes | |
| Auto-renewal billing | Yes | |
| Membership check-in at POS | Yes | |
| Customer membership portal | Partial | Limited self-service |
| Pause/freeze membership | No | |
| Family/group membership | No | |
| Membership transfer | No | |
| Membership discounts on bookings | Yes | |

### Register / POS App

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Daily manifest view | Yes | |
| Check-in (tap to check in) | Yes | |
| Walk-up booking creation | Yes | |
| Booking search | Yes | |
| Booking modification | Yes | |
| Cancellation + refund | Yes | |
| Offline mode | Partial | Limited, not robust |
| Receipt printing | Yes | |
| Cash drawer support | Yes | |
| Multi-staff / shift management | No | |
| Tablet-optimized UI | Partial | Functional but dated |
| QR code check-in | Partial | |

### Reporting

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Sales summary (daily/weekly/monthly) | Yes | |
| Sales by activity | Yes | |
| Tax report | Yes | |
| Booking list export (CSV) | Yes | |
| Customer list export | Yes | |
| Capacity utilization | Partial | |
| Refund report | Yes | |
| Gift card liability | Yes | |
| Merchandise sales | Yes | |
| Guide performance | Partial | |
| Custom report builder | No | Major gap |
| Scheduled/automated reports | No | |
| API access to data | No | |
| Accounting export (QuickBooks) | No | |

### Transportation

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Pickup locations | Yes | |
| Pickup time windows | Yes | |
| Driver manifest | Yes | |
| Guest pickup assignment | Yes | |
| Shuttle capacity management | Yes | |
| Guest pickup notifications | Partial | |
| Multi-vehicle routing | No | |
| GPS integration | No | |

### Guide Management

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Guide profiles | Yes | |
| Guide assignment to trips | Yes | |
| Guide schedule view | Yes | |
| Guide tip tracking | Yes | |
| Guide availability | Partial | |
| Guide payroll export | Partial | |
| Guide mobile app | No | Staff use the Register app |
| Guide certification tracking | No | |

### Integrations & API

| Feature | Exists in Market | Notes / Gaps |
|---------|-----------------|--------------|
| Google Analytics | Partial | Basic pixel only |
| Meta / Facebook Pixel | Partial | |
| Email marketing (Mailchimp) | No | Major gap |
| Accounting (QuickBooks/Xero) | No | Major gap |
| CRM integration | No | |
| Channel manager (Airbnb, Viator) | No | Major gap |
| Public REST API | No | Major gap |
| Webhooks | No | Major gap |
| Zapier integration | No | |

---

## Key Weaknesses to Exploit

1. **No customer self-service portal** - customers can't manage their own bookings online
2. **Weak offline mode** - Register app fails with poor connectivity
3. **No dynamic/demand pricing** - can't automatically adjust rates by availability
4. **No product variants** - can't sell apparel with size/color options
5. **No accounting integrations** - operators manually export and import
6. **No public API or webhooks** - can't build custom integrations
7. **No channel manager** - can't distribute to Viator, GetYourGuide, Airbnb Experiences
8. **Dated UI** - mobile experience on booking site is functional but not modern
9. **No customer loyalty/rewards** - no way to reward repeat visitors
10. **No automated reports** - operators can't schedule weekly revenue emails
11. **Limited waiver customization** - can't create complex custom-field waivers
12. **No multi-currency** - international operators are excluded

---

## Opportunity Summary

The market has solid foundational functionality but significant gaps in:
- **Modern UX** (the booking experience feels dated)
- **Customer self-service** (no portal, no rescheduling online)
- **API/integrations** (completely closed ecosystem)
- **Pricing flexibility** (dynamic, tiered, demand-based)
- **Reporting depth** (no custom builder, no accounting export)

These gaps represent the clearest opportunities to differentiate and win.

See [improvements-over-competitor.md](./improvements-over-competitor.md) for the specific build plan.
