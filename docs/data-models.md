# Data Models — Database Schema

This document defines the core database entities. Tech stack: PostgreSQL + Prisma ORM.

---

## Multi-Tenancy

Every entity belongs to a `Client` (the operator). All queries are scoped by `clientId`.

---

## Core Entities

### Client
The operator / business using the platform.

Fields: id, name, slug (subdomain), logo, primaryColor, timezone, currency, phone, email, website, address, city, state, zip, country, taxId, squareAccountId, stripeAccountId, features (JSON feature flags), settings (JSON), active, createdAt, updatedAt

Relations: Locations, Activities, Users, Customers, Orders, GiftCards, Memberships, PromoCodes, WaiverTemplates, Resources, Merchandise, Guides

---

### Location
A physical location operated by the client.

Fields: id, clientId, name, address, city, state, zip, lat, lng, phone, active, createdAt

---

### Activity
A bookable experience.

Fields: id, clientId, locationId, name, slug, description, shortDesc, heroImage, images[], videoUrl, duration (minutes), minGroupSize, maxGroupSize, isOnline, isVisible, sortOrder, category, tags[], confirmationMsg, active, createdAt, updatedAt

Relations: Rates, Schedules, Bookings, WaiverTemplates (m:m), Resources (m:m), Addons

---

### Rate
A pricing tier for an activity (Adult, Child, Senior, etc.)

Fields: id, activityId, name, price (cents), description, minAge, maxAge, minQty, maxQty, sortOrder, taxRateId, active

---

### Schedule
Defines when an activity is available.

Fields: id, activityId, type (RECURRING|ONE_OFF|BLACKOUT|CAPACITY_OVERRIDE), startDate, endDate, daysOfWeek[], startTime, capacity, bookingCutoff (hours), advanceWindow (days), active, createdAt

---

### TimeSlot
A concrete bookable instance on a specific date/time.

Fields: id, scheduleId, activityId, startTime (DateTime), endTime, capacity, bookedCount, status (OPEN|CLOSED|CANCELLED|SOLD_OUT), createdAt

---

### Customer

Fields: id, clientId, firstName, lastName, email, phone, address, city, state, zip, notes, tags[], createdAt, updatedAt

Unique constraint: clientId + email

---

### Order
A transaction (bookings + merchandise).

Fields: id, clientId, customerId, confirmNumber (unique, e.g. "TBK-12345"), status (PENDING|CONFIRMED|COMPLETED|CANCELLED|REFUNDED|PARTIAL_REFUND), subtotal, taxTotal, discountTotal, tipAmount, total, amountPaid, balance, source (ONLINE|WALK_UP|PHONE|KIOSK|IMPORT), promoCodeId, notes, staffId, createdAt, updatedAt

Relations: Bookings, OrderLineItems, Payments, Refunds, PromoCode

---

### Booking
A reservation for a specific activity slot.

Fields: id, orderId, activityId, timeSlotId, status (CONFIRMED|CHECKED_IN|COMPLETED|CANCELLED|NO_SHOW|WAITLISTED), participants (JSON: [{rateId, rateName, qty, unitPrice}]), totalParticipants, subtotal, notes, guideId, checkInAt, checkInBy (userId), noShowAt, createdAt, updatedAt

Relations: Order, Activity, TimeSlot, Guide, Waivers, TransportBooking

---

### Payment

Fields: id, orderId, amount (cents), method (CARD|CASH|GIFT_CARD|MEMBERSHIP|COMP|SPLIT), status (PENDING|COMPLETED|FAILED|VOIDED), squarePaymentId, giftCardId, membershipId, tip, processedAt, processedBy (userId)

---

### Waiver
A signed digital waiver.

Fields: id, bookingId, customerId, templateId, signedAt, signerName, signature (base64/S3 URL), isMinor, guardianName, guardianSig, customFields (JSON), pdfUrl (S3), ipAddress, createdAt

---

### WaiverTemplate

Fields: id, clientId, name, body (HTML), fields (JSON custom field definitions), version, active, createdAt

---

### Resource
A shared physical asset (boat, kayak, court, etc.)

Fields: id, clientId, name, type, capacity, description, active

---

### Merchandise

Fields: id, clientId, name, sku, description, price (cents), cost, category, images[], barcode, stock, lowStockQty, taxRateId, active, createdAt

Relations: MerchVariants (size/color)

---

### GiftCard

Fields: id, clientId, code (unique), type (PHYSICAL|DIGITAL|CERTIFICATE), initialAmount, balance, isActive, expiresAt, recipientEmail, recipientName, message, issuedAt

---

### MembershipPlan

Fields: id, clientId, name, description, type (PUNCH_PASS|SEASON_PASS|SUBSCRIPTION), price, interval (month/year), uses (punch count), validDays, benefits, active

### CustomerMembership

Fields: id, customerId, planId, clientId, status (ACTIVE|EXPIRED|CANCELLED|PAUSED|PENDING), startDate, endDate, usesRemaining, autoRenew, squareSubId, createdAt

---

### User (Staff)

Fields: id, clientId, email, name, role (OWNER|ADMIN|MANAGER|STAFF|GUIDE|READ_ONLY), pin (hashed), active, lastLoginAt, createdAt

Unique constraint: clientId + email

---

### Guide

Fields: id, clientId, userId (optional link to User), name, bio, photo, phone, email, active

---

### PromoCode

Fields: id, clientId, code, type (PERCENT_OFF|DOLLAR_OFF|FREE_MERCH|FREE_ACTIVITY), value (cents or 0-100), usageLimit, usageCount, perCustomer, minOrderValue, expiresAt, active, createdAt

Unique constraint: clientId + code

---

### TaxRate

Fields: id, clientId, name (e.g. "State Tax"), rate (float, e.g. 0.085 = 8.5%), active

---

## Entity Relationship Summary

```
Client
  -> Locations (1:many)
  -> Activities (1:many)
     -> Rates (1:many)
     -> Schedules -> TimeSlots (1:many)
     -> WaiverTemplates (many:many)
     -> Resources (many:many)
     -> Addons (1:many)
  -> Customers (1:many)
     -> Orders (1:many)
        -> Bookings (1:many)
           -> Waivers (1:many)
        -> OrderLineItems
        -> Payments (1:many)
        -> Refunds (1:many)
     -> CustomerMemberships (1:many)
  -> Users (staff) (1:many)
  -> Guides (1:many)
  -> Merchandise (1:many)
  -> GiftCards (1:many)
  -> MembershipPlans (1:many)
  -> PromoCodes (1:many)
  -> WaiverTemplates (1:many)
  -> Resources (1:many)
  -> TaxRates (1:many)
```

---

## Key Indexes

- Order: clientId + createdAt, customerId, confirmNumber
- Booking: orderId, timeSlotId, activityId + status
- TimeSlot: activityId + startTime, scheduleId
- Customer: clientId + email, clientId + phone
- GiftCard: code
- PromoCode: clientId + code
