# Register / POS App — Feature Specification

The Register app (register.[domain].com) is the staff-facing point-of-sale and operations app. It runs on tablets or desktops at the check-in desk, dock, or entrance. It must work well on touchscreens and handle spotty internet connections.

---

## Core Design Requirements

- **PWA (Progressive Web App)**: Installable on iPad/Android tablet, works offline
- **Touch-optimized**: Large tap targets, swipe gestures, minimal typing required
- **Fast**: Page loads under 1 second, check-in under 2 taps
- **Offline-capable**: Core functions work without internet; syncs when reconnected
- **Role-aware**: Shows appropriate features based on logged-in staff role
- **Session-based**: Staff sign in with PIN or biometric after initial login

---

## Navigation Structure

Manifest (daily view) [HOME]
New Booking
POS / Sell
  - Merchandise
  - Gift Cards
  - Packages
  - Memberships
Customers
  - Search / Lookup
Settings
  - My Account
  - Printer
  - Cash Drawer
  - Sync Status

---

## Manifest View (Home)

**Purpose:** The primary daily operations view. Shows all bookings for today.

**Layout:**
- Date selector at top (swipe left/right to change date)
- Filter bar: All Activities | [Activity Name tabs]
- Time-grouped booking list

**Booking Card shows:**
- Activity name + time slot
- Customer name
- Party size (e.g., "4 guests")
- Check-in status: Not Arrived / Checked In / No-Show
- Waiver status: Complete / Pending (badge count)
- Balance due (if deposit was taken, shows remaining balance)
- Special notes indicator

**Actions:**
- Tap booking card -> Booking Detail
- Swipe right -> Quick check-in
- Tap slot header -> View/print manifest for that slot

**Manifest Summary Bar:**
- Total booked / capacity for each slot
- Checked-in count
- Revenue for the day

---

## Booking Detail View

Shown when tapping a booking card on the manifest.

### Guest Info
- Customer name, phone, email
- Party breakdown: 2 adults, 1 child
- Notes (internal + customer-provided)

### Activity Info
- Activity name, date, time slot
- Guide assigned (if applicable)
- Transportation pickup (if applicable)

### Financials
- Order total, amount paid, balance due
- Payment method (card, cash, gift card)
- Refund history

### Waiver Status
- List of participants with waiver status
- "Send waiver link" button
- "Sign waiver here" button (opens waiver on this device)

### Actions
- **Check In** (big green button, primary action)
- **Collect Balance** (if balance due)
- **Reschedule** (opens calendar picker)
- **Modify** (change participants, add notes)
- **Add Items** (sell add-on merchandise)
- **Cancel + Refund** (with confirmation dialog)
- **No Show** (marks as no-show, prompts for refund policy)
- **Print Receipt**
- **Resend Confirmation Email**

---

## Check-In Flow

**Standard check-in:**
1. Tap booking card on manifest OR search by name/confirmation #
2. Review party details
3. Tap "Check In" -> confirm
4. Optional: collect balance, collect signature, print receipt
5. Booking status updates live across all devices

**QR code check-in:**
1. Tap scan icon
2. Point camera at QR code on confirmation email
3. Booking pulls up automatically
4. Confirm check-in

---

## New Booking Flow (Walk-up / Phone)

**Step 1: Activity + Date**
- Select activity (grid of activity cards)
- Select date (calendar)
- Select time slot (list of available slots with capacity indicators)

**Step 2: Participants**
- Tap +/- for each rate type (Adult $X, Child $Y, Senior $Z)
- Running total updates in real time

**Step 3: Customer**
- Search existing customer (name/phone/email)
- Or create new customer (name, email, phone)
- Quick-create option: just phone number + first name

**Step 4: Add-ons (optional)**
- Merchandise / package upsells
- Transportation pickup selection

**Step 5: Payment**
- Tap payment method: Card / Cash / Gift Card / Membership / Split
- Card: tap Square Reader (Bluetooth), manual entry, or swipe
- Cash: enter amount tendered, shows change due
- Gift card: enter code, shows balance, applies up to total
- Membership: lookup by member card/phone, validates and applies
- Tip prompt (configurable)

**Step 6: Confirmation**
- Order summary
- Print Receipt and/or Email Receipt toggles
- Confirm + complete

---

## POS / Sell (No Booking Required)

### Merchandise Sale
- Category tiles -> product list
- Tap product -> add to cart
- Product search and barcode scan
- Quantity adjuster
- Apply discount / promo code
- Process payment

### Gift Card Sale
- Choose: physical or digital
- Enter amount (preset options: $25, $50, $100, $150, or custom)
- Digital: enter recipient email, optional personalized message
- Physical: scan/enter card code, activate
- Process payment

### Membership Sale
- Select membership plan
- Look up / create customer
- Configure start date + auto-renewal
- Process payment

---

## Customer Lookup

- Search by: name, phone, email, confirmation number
- Recent customers list (last 10 checked in)
- Customer card shows: name, upcoming bookings today, membership status, outstanding balance

---

## Offline Mode

**What works offline:**
- View today's manifest (cached on load)
- Check in bookings (queued, syncs when online)
- New cash bookings (queued)
- View customer waivers (cached)
- Sell merchandise (queued)

**What requires connectivity:**
- Card payments (Square Reader requires internet)
- Pulling bookings for future dates
- Real-time capacity updates

**Sync indicator:**
- Top bar shows: "Online" (green dot) or "Offline -- X actions pending" (yellow)
- Auto-syncs immediately when connectivity returns

---

## Waiver Collection (On-Site)

### Staff-Initiated (Tablet Handoff)
1. Open booking detail -> Tap "Sign Waiver"
2. Pass tablet to guest
3. Guest reads, signs, taps "I Agree"
4. Staff takes back tablet
5. Waiver stored, manifest updates

---

## Refunds & Cancellations

**Cancel + Refund Flow:**
1. Open booking detail
2. Tap "Cancel + Refund"
3. Select refund type: Full / Partial / No Refund
4. Enter reason from dropdown
5. Confirm with manager override PIN (configurable)
6. Refund processed via original payment method
7. Cancellation email sent to customer (optional)

---

## Cash Drawer Management

- Open drawer: tap icon (triggers connected cash drawer)
- Cash count at start/end of day
- Cash drawer report (expected vs actual)
- Petty cash in/out with reason

---

## Printer Support

- Receipts: Star Micronics, Epson TM (Bluetooth or network)
- Tickets/wristbands: Zebra label printer
- Manifest: Print to any AirPrint/network printer

---

## Staff Session

- Staff PIN login (4-6 digit PIN after initial auth)
- Session tracks: who checked in what, who processed which payments
- Per-session sales report

---

## Keyboard Shortcuts (Desktop Mode)

| Key | Action |
|-----|--------|
| F1 | New Booking |
| F2 | Search Customer |
| F3 | Sell (POS) |
| F4 | Manifest |
| / | Search |
| Enter | Confirm current action |
| Esc | Cancel / go back |
