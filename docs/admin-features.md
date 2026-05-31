# Admin Dashboard — Feature Specification

The Admin Dashboard (manage.[domain].com) is the back-office control center for operators. It is used by business owners, managers, and office staff to set up the platform, manage bookings, view reports, and configure all settings.

---

## Navigation Structure

```
Dashboard (home/overview)
Bookings
  - All Bookings
  - Calendar View
  - Manifest (daily)
Activities
  - Activity List
  - Schedules
  - Resources
Customers
  - Customer List
  - Waivers
Products
  - Merchandise
  - Gift Cards
  - Packages
  - Memberships
Promotions
  - Promo Codes
  - Affiliates
Staff
  - Users & Roles
  - Guides
Transportation
Reports
  - Sales
  - Bookings
  - Customers
  - Tax
  - Gift Cards
  - Merchandise
  - Memberships
Settings
  - Business Info
  - Locations
  - Payment Settings
  - Email Templates
  - Integrations
  - Feature Flags
```

---

## Dashboard (Home)

**Purpose:** At-a-glance business health for today and recent history.

**Widgets / Metrics:**
- Today's revenue (vs yesterday, vs same day last week)
- Today's bookings (count, capacity used %)
- Upcoming trips (next 3 time slots)
- Pending waivers (bookings with unsigned waivers)
- Recent orders (last 10 transactions)
- Monthly revenue chart (30-day bar chart)
- Top activities this month (by revenue + bookings)
- Quick actions: New Booking, Check In, Sell Gift Card

---

## Bookings Module

### All Bookings View
- Filterable list: date range, activity, status (confirmed/cancelled/pending/completed)
- Sortable columns: date, activity, customer name, party size, total, status
- Search by: customer name, email, phone, confirmation number
- Bulk actions: export to CSV, mark as complete, send reminder email
- Click into booking for detail view

### Booking Detail View
- Full order summary: customer info, activity, date/time, participants, total, payment
- Booking status history (audit log)
- Waiver status per participant
- Ability to: reschedule, modify participants, add notes, cancel, process refund
- Resend confirmation email
- Link to customer profile

### Calendar View
- Month/week/day toggle
- Color-coded by activity
- Shows capacity fill per slot (e.g., 8/12 booked)
- Click slot to see manifest for that time
- Drag-to-reschedule (future)

### Manifest View
- Select date (defaults to today)
- Filter by activity / time slot
- List of all bookings for selected date/activity
- Per-booking: customer name, party size, check-in status, waiver status, guide assigned
- One-click check-in
- Print manifest

---

## Activities Module

### Activity List
- Grid or list view of all activities
- Quick toggle: active/inactive
- Sort by name, revenue, booking count
- Clone activity

### Activity Editor
**Basic Info tab:**
- Name, short description, long description
- Category / type tags
- Duration (hours + minutes)
- Minimum / maximum group size
- Hero image + gallery images
- Video embed URL (optional)
- Activity is bookable online: yes/no toggle
- Activity is visible on booking site: yes/no
- Custom confirmation message

**Rates tab:**
- Add unlimited rate types (name, price, age range, description)
- Set min/max per rate type per booking
- Set rate as "private/exclusive" (customer books whole slot)
- Tax rate assignment per rate
- Deposit configuration (% or $ amount required upfront)

**Schedule tab:**
- Create recurring schedules: daily, specific days of week, date range
- One-off schedules (specific dates/times)
- Capacity per slot (can override per individual date)
- Booking cutoff: N hours/days before slot starts
- Advance booking window: max N days ahead
- Blackout dates picker
- Copy schedule to other activities

**Waivers tab:**
- Assign one or more waiver templates to this activity
- Set waiver as required before check-in: yes/no

**Resources tab:**
- Assign resources (boats, bikes, etc.) to this activity
- Capacity auto-calculated from resource capacity

**Add-ons tab:**
- Add optional upsell items (merchandise or custom add-on)
- Show in booking flow: yes/no
- Required vs optional

**Transportation tab:**
- Enable transportation for this activity: yes/no
- Assign pickup locations

### Schedule Manager
- View all schedules across all activities in calendar
- Identify gaps or conflicts
- Bulk capacity override by date range
- Bulk blackout by date range

### Resources
- Resource list (name, type, capacity, status)
- Assignment matrix (which activities use which resources)
- Resource availability calendar
- Conflict warnings

---

## Customers Module

### Customer List
- Search by name, email, phone
- Filter by: last booking date, total spend, membership status
- Sort by: name, total bookings, lifetime value
- Export to CSV

### Customer Profile
- Contact info (name, email, phone, address)
- Booking history (all past and upcoming bookings)
- Order history with totals
- Waiver history
- Notes (internal staff notes)
- Membership status
- Gift card balance
- Communication history (emails sent)
- Edit customer info

### Waivers
- List all signed waivers across all customers
- Filter by: activity, date range, customer name
- View / download individual waiver PDFs
- Export waiver list

---

## Products Module

### Merchandise
- Product list (name, SKU, category, price, stock, status)
- Product editor: name, description, images, price, cost, SKU, category, tax rate
- Inventory management: adjust stock, set low-stock threshold
- Product variants: size, color (with per-variant pricing and stock)
- Barcode assignment
- Active/inactive toggle
- Sales history per product

### Gift Cards
- List all gift cards (code, type, balance, issued date, last used)
- Filter: physical vs digital, active vs redeemed vs expired
- Issue new gift card (physical or digital)
- View transaction history per card
- Adjust balance (manual correction)
- Bulk import gift card codes

### Packages
- Package list
- Package editor: name, description, contents (activities + rates + merchandise)
- Package pricing (total price vs sum of components)
- Availability settings (date range, quantity limit)
- Redemption tracking

### Memberships
- Membership plan list
- Plan editor: name, price, billing interval, benefits description, included bookings/discounts
- Punch pass configuration: N uses, expiry
- Season pass configuration: start/end dates, included activities
- Active memberships list (customer, plan, status, renewal date)
- Pause / cancel membership
- Bulk renewal management

---

## Promotions Module

### Promo Codes
- Code list (code, type, value, uses, limit, expiry, status)
- Create new code: % off, $ off, free merchandise
- Set: usage limit, per-customer limit, expiry date, minimum order value
- Applicable activities filter (all or specific)
- Performance report (uses, revenue generated)
- Deactivate code

### Affiliates
- Affiliate list (name, code, email, total bookings, total commission)
- Create affiliate: assign code, set commission rate (% or $)
- Commission report export
- Link generator

---

## Staff Module

### Users & Roles
- User list (name, email, role, last login, status)
- Invite new user via email
- Role assignment: Admin, Manager, Staff, Guide, Read-Only
- Permission matrix per role (can view, can edit, can refund, etc.)
- Deactivate user
- Activity log per user

**Role Permissions (example):**
| Permission | Admin | Manager | Staff | Guide |
|-----------|-------|---------|-------|-------|
| View reports | Yes | Yes | No | No |
| Process refunds | Yes | Yes | No | No |
| Create bookings | Yes | Yes | Yes | No |
| Check in | Yes | Yes | Yes | Yes |
| Edit activities | Yes | Yes | No | No |
| View manifest | Yes | Yes | Yes | Yes |

### Guides
- Guide profile: name, photo, bio, certifications, contact
- Assign to activities (which activities they're qualified for)
- Schedule view (their assigned trips)
- Tip history
- Availability calendar

---

## Transportation Module

- Pickup location list (name, address, notes)
- Pickup time windows per location per activity
- Driver list (name, vehicle, capacity)
- Transportation manifest (by date, by route)
- Assign drivers to routes
- Guest notification settings (reminder SMS/email before pickup)

---

## Reports Module

### Sales Report
- Date range selector
- Breakdown by: day, week, month
- Metrics: gross revenue, net revenue, refunds, tax collected, transaction count, average order
- Filter by: activity, payment method, location
- Chart: revenue over time
- Export CSV / PDF

### Bookings Report
- Bookings by activity (count, revenue, capacity %)
- Booking status breakdown (confirmed/cancelled/completed/no-show)
- Booking source (online, walk-up, phone)
- Lead time (how far in advance bookings are made)

### Customers Report
- New vs returning customers over time
- Customer lifetime value distribution
- Top customers by spend
- Customer acquisition source

### Tax Report
- Tax collected by rate (e.g., 8.5% state, 2% local)
- Tax by activity / product
- Formatted for accounting export

### Gift Card Report
- Issued vs redeemed vs outstanding balance
- Gift card liability total
- Redemption history

### Merchandise Report
- Sales by product, by category
- Inventory movement (sold, restocked)
- Revenue and margin per product

### Membership Report
- Active memberships count
- New sign-ups over time
- Churn rate
- MRR (monthly recurring revenue)
- Upcoming renewals

### Custom Report Builder (v2)
- Drag-and-drop metric selector
- Custom date ranges and groupings
- Save + schedule reports (email delivery)

---

## Settings Module

### Business Info
- Business name, logo, address, phone, email, website
- Tax ID / EIN
- Timezone
- Currency

### Locations
- Location list (for multi-location operators)
- Per-location: name, address, phone, hours, map coordinates
- Assign activities to locations

### Payment Settings
- Square account connection (OAuth)
- Stripe connection (fallback)
- Cash handling settings
- Refund policy text
- Booking fee configuration (service fee added to customer total)
- Tipping: enable/disable, preset amounts

### Email Templates
- Template editor for: booking confirmation, reminder, cancellation, waiver request, gift card delivery
- Custom branding (logo, colors, footer)
- Preview before save
- Test send

### Integrations
- Google Analytics (Measurement ID)
- Meta Pixel (Pixel ID)
- Mailchimp (API key + list selection)
- QuickBooks / Xero (OAuth connection)
- Zapier webhook URL
- Custom webhook endpoints (event subscriptions)

### Feature Flags
- Toggle modules on/off per client: WAIVERS, MERCHANDISE, GIFT_CARDS, MEMBERSHIPS, TRANSPORTATION, KIOSK, DISPLAY_APP, PHOTOS, RENTALS, GUIDE_SCHEDULING, AFFILIATES, TIPPING
- Useful for onboarding and pricing tiers

---

## Design Requirements

- **Responsive**: Works on desktop (1280px+) and large tablets
- **Dark mode**: Optional toggle
- **Fast**: Table views paginate at 50 rows, lazy load images
- **Keyboard navigable**: Full keyboard support for power users
- **Accessible**: WCAG 2.1 AA
- **Real-time updates**: Booking counts, manifest check-ins update live without page refresh
