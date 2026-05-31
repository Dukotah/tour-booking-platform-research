# Customer Booking Flow — Feature Specification

The customer-facing booking site (book.[domain].com or embedded widget) is where guests discover activities and complete their reservations. This is the revenue engine and must be fast, beautiful, and conversion-optimized.

---

## Design Principles

- **Mobile-first**: 60%+ of bookings happen on phones. Design for phone first.
- **Minimal friction**: Fewer steps, fewer required fields, fewer decisions
- **Transparent pricing**: Show total including tax/fees before payment
- **Trust signals**: Reviews, photos, cancellation policy, secure payment badge
- **Speed**: First contentful paint under 1.5s, interaction under 100ms
- **White-label**: Each operator has their own branding (logo, colors, domain)

---

## Site Structure

```
/ (landing / activity listing)
/[activity-slug] (activity detail + booking start)
/book/[activity-slug]/date (date selection)
/book/[activity-slug]/time (time selection)
/book/[activity-slug]/participants (rate selection)
/book/[activity-slug]/details (customer info + add-ons)
/book/[activity-slug]/checkout (review + payment)
/confirmation/[order-id] (post-booking confirmation)
/manage/[booking-token] (customer booking management)
```

---

## Activity Listing Page

### Layout
- Hero banner with operator branding
- Activity grid/list toggle
- Category filter tabs (if multiple categories)
- Sort: Popularity | Price | Date availability

### Activity Card
- Hero image (lazy loaded)
- Activity name
- Short description (2 lines max)
- Duration badge (e.g., "2 hours")
- Price from (lowest rate, e.g., "From $45/person")
- Next available date
- Star rating + review count (if reviews enabled)
- "Book Now" CTA button

### Filtering
- Date filter (show only activities available on a specific date)
- Participant count (show only activities with capacity)
- Category filter

---

## Activity Detail Page

### Content Sections
1. **Hero image carousel** (swipeable on mobile)
2. **Activity title** + short description
3. **Key info bar**: Duration | Group size | Age requirement | Difficulty
4. **Booking panel** (sticky on desktop, fixed bottom CTA on mobile)
5. **Full description** (rich text, expandable on mobile)
6. **What's included** (bulleted list)
7. **What to bring** (bulleted list)
8. **Photos/gallery** (lightbox)
9. **Reviews** (star ratings + comments, if enabled)
10. **FAQ** (accordion)
11. **Location map** (Google Maps embed)
12. **Cancellation policy**
13. **Related activities**

### Booking Panel
- Date picker (inline calendar)
- Time slot selector (shows availability)
- Participant counter (+/-)
- Price summary (updates live)
- "Book Now" button
- "Check Availability" link (if no date selected)

---

## Booking Flow (Step-by-Step)

### Step 1: Date Selection
- Full-month calendar view
- Unavailable dates grayed out
- Available dates have a subtle indicator
- Selected date highlighted
- "Next available date" shortcut link
- Mobile: bottom sheet calendar picker

### Step 2: Time Slot Selection
- List of available time slots for selected date
- Each slot shows: time, availability indicator (available / limited / sold out)
- Spots remaining shown when < 20% capacity left (e.g., "Only 3 spots left!")
- Select slot -> highlight + proceed

### Step 3: Participant Selection
- Rate type selector (Adult / Child / Senior / etc.)
- +/- buttons for each rate type
- Min/max constraints enforced with helpful error messages
- Running price total shown at bottom (updates live)
- Private/exclusive option if activity supports it
- Age range labels for each rate type

### Step 4: Customer Details
- **Required fields**: First name, Last name, Email, Phone
- **Optional fields**: Special requests / notes
- Add-ons panel: checkbox items with price (merchandise, upgrades, etc.)
- Transportation pickup selector (if enabled for activity)
- Waiver acknowledgment: "You will be required to sign a waiver on arrival"
- Terms of service acknowledgment checkbox

### Step 5: Review + Checkout

**Order Summary section:**
- Activity name, date, time
- Participants breakdown with subtotals
- Add-ons with prices
- Subtotal
- Taxes (line item, rate shown)
- Service fee (if charged)
- **Total**

**Payment section:**
- Square Web Payments SDK card form (card number, expiry, CVV, zip)
- Gift card code field (expandable)
- Promo code field (expandable with "Apply" button)
- "Complete Booking" CTA button

**Trust signals:**
- Lock icon + "Secure checkout"
- Cancellation policy summary
- "Need help? Call [phone]"

### Step 6: Confirmation Page

**Confirmation number**: Shown large and prominently

**Booking summary:**
- Activity, date/time, participants
- Amount charged
- Order #

**Next steps:**
- "Add to Calendar" button (Google / Apple / Outlook)
- "Share" button (share booking with group members)
- "Manage Your Booking" link (to customer portal)

**Waiver prompt** (if waiver required):
- "Sign your waiver now to save time" button
- Shows estimated time (e.g., "Takes 2 minutes")

**Email confirmation**: Sent automatically, includes all details + waiver link

---

## Customer Management Portal

Accessible via link in confirmation email (token-based, no account required).

**Features:**
- View booking details
- Download/view receipt
- Sign waiver (if not yet signed)
- View cancellation policy
- Reschedule booking (if policy allows, within window)
- Cancel booking (if policy allows, within window)
- Contact operator (pre-filled email form)

---

## Waiver Flow (Pre-Booking, Online)

Triggered from confirmation email link or from customer portal.

1. Waiver intro screen (activity name, operator logo)
2. Waiver text (scrollable, required to scroll to bottom)
3. Custom fields (if configured): emergency contact, health disclosures, etc.
4. Signature field (draw or type)
5. Full name + date confirmation
6. "I Accept" button
7. Confirmation screen + PDF download option

**Minor waiver:**
- Parent/guardian flow: enter guardian name, relationship
- Separate signature for each minor
- Or group minor waiver: "I am the guardian of X minors in my party"

---

## Group Bookings

For large parties (configurable threshold, e.g., 10+ people):

- Show "Planning a group?" banner with link to group inquiry form
- Group inquiry form: name, email, phone, activity of interest, date flexibility, group size, notes
- Submitted to admin as a lead (not a booking)
- Admin can manually create and confirm group bookings

---

## Mobile-Specific Optimizations

- Sticky "Book Now" bar at bottom of activity detail page
- Swipeable image carousel
- Bottom sheet modals instead of full-page dialogs
- Large tap targets (min 44x44px)
- Native date picker consideration (use custom for consistency)
- Auto-fill: supports browser autofill for name/email/phone/card
- Apple Pay / Google Pay via Square SDK (tap to pay)
- Reduced motion support (respects prefers-reduced-motion)

---

## Embedded Widget Mode

The booking flow can be embedded on an operator's own website as an iframe or web component.

- Operator installs a script tag on their site
- Widget renders inline or as a popup
- Inherits operator branding
- Full booking flow happens without leaving the operator's site
- Post-booking: redirects to operator's own confirmation page (configurable)

---

## SEO & Performance

- Activity pages are server-rendered (Next.js SSR) for SEO
- Structured data: Activity schema, Review schema
- Open Graph tags for social sharing
- Image optimization (WebP, lazy loading, CDN)
- Core Web Vitals targets: LCP < 2.5s, FID < 100ms, CLS < 0.1
- Sitemap auto-generated per operator

---

## Error Handling & Edge Cases

| Scenario | UX Response |
|----------|-------------|
| Slot fills up while user is booking | "Oops! This time sold out. Please choose another." |
| Payment declined | Inline error, keep form, offer retry |
| Customer already has same booking | Warning: "You already have a booking for this time. Continue?" |
| Activity suspended / cancelled | Notice on activity page, alternative suggestions |
| Date outside booking window | "This date is outside our booking window" with next available |
| Network error during checkout | "Something went wrong. Your card was not charged. Please try again." |
| Duplicate submission (double click) | Button disabled after first click, idempotent API |
