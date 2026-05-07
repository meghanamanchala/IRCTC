# IRCTC Problem Discovery — Part A

## Summary

- Total problems documented: 6 (3 given + 3 self-discovered)
- Platform explored: irctc.co.in and IRCTC Rail Connect mobile app
- Devices used:
  - Desktop Chrome (Windows)
  - IRCTC Rail Connect App (Android)
- Date tested: May 2026

---

# Problem 1: Tatkal Booking Crashes at 10:00 AM [Given]

## What is broken

The IRCTC server becomes extremely slow or completely unresponsive at exactly 10:00 AM when Tatkal booking opens. Users experience page freezes, HTTP 502 errors, session timeouts, delayed OTP delivery, and failed payments during the most critical stage of booking.

The system provides no queue status or live progress updates, causing users to repeatedly refresh or click buttons multiple times, which increases server load even more.

---

## Affected users

- Users booking Tatkal tickets daily
- Students traveling for exams
- Migrant workers
- Tier 2 and Tier 3 city users dependent on rail travel
- Mobile users on slower networks

Estimated affected users:
20–40 lakh users between 9:58 AM and 10:05 AM every day.

---

## Frequency

Occurs daily during Tatkal opening hours.

Observed consistently during peak booking periods and widely reported across social media and app reviews.

---

## Current flow — step by step

1. User opens IRCTC around 9:45–9:50 AM
2. User logs into account and searches for trains
3. User selects Tatkal quota and checks availability
4. User fills passenger details before 10:00 AM
5. User clicks “Book Now” exactly at 10:00 AM
6. Page freezes and loading spinner appears
7. No queue position or progress status is shown
8. User waits 15–45 seconds
9. System shows HTTP 502 error, CAPTCHA reset, or session timeout
10. User refreshes page and gets logged out
11. User logs in again and sees Tatkal quota exhausted
12. User checks bank account to confirm whether payment was deducted

---

## Where exactly it breaks

The failure occurs primarily in Steps 6–10.

The backend receives massive concurrent requests without a queue management system. The frontend provides no real-time status feedback, causing users to retry repeatedly and overload the system further.

---

# Problem 2: Search Filters Do Not Work Reliably [Given]

## What is broken

Train search filters for class, quota, availability, and departure time frequently fail to apply correctly. Some filters reset after navigation, while others display stale or incorrect train availability data.

Users often see waitlisted trains even after selecting “Available Only.”

---

## Affected users

- All users searching for trains
- Senior citizens
- First-time users
- Users booking urgent travel
- Mobile users with unstable internet

Estimated affected users:
Most of the 8 crore registered users interact with train search filters.

---

## Frequency

Occurs intermittently.

Observed approximately 30–40% of the time during repeated searches and more frequently during high traffic periods.

---

## Current flow — step by step

1. User enters source station, destination, and travel date
2. User clicks “Search Trains”
3. System displays 20–40 trains
4. User applies filters such as “Sleeper Class” and “Available”
5. Page reloads slowly
6. Some waitlisted trains still appear
7. User clicks one train expecting available seats
8. Selected train shows WL/RAC status instead
9. User clicks back button
10. Previously selected filters reset automatically
11. User reapplies filters again
12. User manually scans trains due to low trust in filters

---

## Where exactly it breaks

The failure occurs in Steps 5–10.

Filter state is not preserved correctly after page reloads, and live availability updates are inconsistent with cached filter results.

---

# Problem 3: Seat Selection Resets Randomly [Given]

## What is broken

Selected seats or berth preferences are sometimes lost while moving from seat selection to passenger details. Users selecting lower berths or specific seats often receive automatic or incorrect seat assignments.

This issue is more severe on mobile devices.

---

## Affected users

- Families traveling together
- Elderly passengers requiring lower berths
- Users with disabilities
- Women traveling alone
- Mobile users

Estimated affected users:
30–40% of booking users use berth preferences.

---

## Frequency

Occurs in approximately:
- 15–25% of desktop sessions
- 30–35% of mobile sessions

---

## Current flow — step by step

1. User selects train and travel class
2. User proceeds to seat selection page
3. Seat map loads with available berths
4. User selects preferred lower berth
5. Selected berth turns blue
6. User clicks “Proceed”
7. Passenger details page opens
8. Selected seat changes to “Auto”
9. Different berth number appears
10. User navigates back to seat map
11. Previously selected berth now shows as unavailable
12. User completes booking with unwanted berth assignment

---

## Where exactly it breaks

The failure occurs in Steps 6–9.

Seat preference state is not reliably transferred between components. On mobile devices, re-rendering clears local selection state.

---

# Problem 4: UPI Payment Status Is Unclear After Payment [Self-Discovered]

## How I found it

I tested the booking flow until the payment page using UPI payment on both desktop and mobile.

---

## Screenshot / Description

Screen: Payment gateway page after selecting UPI payment.

Observed behavior:
After approving payment in the UPI app, IRCTC displayed a long loading spinner without confirming whether payment succeeded or failed.

Suggested screenshot:
`assets/screenshots/payment-loading.png`

---

## What is broken

The payment flow does not provide real-time payment confirmation. After completing UPI payment, users are left on a loading screen with no status updates, causing confusion and duplicate booking attempts.

Many users close the tab thinking payment failed, which later results in delayed refunds or duplicate payment confusion.

---

## Affected users

- UPI users
- Mobile users
- Users booking during peak traffic hours
- Users with slower internet connections

Estimated affected users:
UPI is one of the most used payment methods on IRCTC, affecting lakhs of daily users.

---

## Frequency

Observed 2 out of 5 payment attempts during testing.

The issue becomes more frequent during peak booking hours and Tatkal booking windows.

---

## Current flow — step by step

1. User selects train and enters passenger details
2. User proceeds to payment page
3. User selects UPI as payment option
4. User enters UPI ID
5. User clicks “Pay”
6. UPI app opens for payment approval
7. User approves payment successfully
8. IRCTC shows loading spinner for 20–40 seconds
9. No success or failure message appears
10. User refreshes page or closes tab
11. Ticket temporarily disappears from booking history
12. User checks bank statement to verify payment deduction

---

## Where exactly it breaks

The failure occurs in Steps 8–11.

The payment gateway callback is delayed, and the frontend lacks real-time transaction state tracking or recovery messaging.

---

# Problem 5: PNR Status Page Lacks Clear Guidance [Self-Discovered]

## How I found it

I explored the PNR status section and tested different PNR numbers to understand the passenger journey after booking.

---

## Screenshot / Description

Screen: PNR Status Result Page

Observed behavior:
The page displays technical terms like WL, RAC, CNF, Chart Prepared, etc., without explaining what they mean or what action the user should take next.

Suggested screenshot:
`assets/screenshots/pnr-status.png`

---

## What is broken

The PNR page assumes users already understand railway terminology. There is no contextual explanation, journey guidance, or predictive information about confirmation chances.

Users often leave IRCTC and search Google or YouTube for explanations.

---

## Affected users

- First-time train travelers
- Elderly users
- Regional language users
- Users unfamiliar with railway abbreviations

Estimated affected users:
Millions of users check PNR status daily.

---

## Frequency

Occurs every time users access the PNR status page.

---

## Current flow — step by step

1. User opens PNR Status page
2. User enters 10-digit PNR number
3. System displays ticket status
4. User sees terms like WL 23 / RAC / CNF
5. No explanation or help tooltip is shown
6. User becomes unsure whether travel is allowed
7. User searches Google or YouTube for clarification
8. User checks multiple unofficial apps for prediction accuracy
9. User returns to IRCTC still uncertain about final ticket status

---

## Where exactly it breaks

The failure occurs in Steps 4–7.

The information architecture is too technical and does not support first-time or non-technical users.

---

# Problem 6: Mobile App Freezes During Passenger Form Entry [Self-Discovered]

## How I found it

I tested the booking flow using the IRCTC Rail Connect Android app and observed multiple UI freezes while entering passenger details.

---

## Screenshot / Description

Screen: Passenger Details Form in IRCTC Rail Connect App

Observed behavior:
The app lagged while scrolling, and form fields reset unexpectedly when the keyboard opened or closed.

Suggested screenshot:
`assets/screenshots/mobile-app-form-lag.png`

---

## What is broken

The IRCTC mobile app booking form is poorly optimized for smaller screens and lower-performance devices. Keyboard interactions trigger layout shifts and field resets, making passenger form completion frustrating.

---

## Affected users

- Android app users
- Users with low-end or mid-range phones
- Users on slower mobile networks
- Elderly users entering passenger details slowly

Estimated affected users:
A significant percentage of IRCTC bookings are performed through the mobile app.

---

## Frequency

Observed frequently during testing on the Android app.

The issue becomes more severe on slower devices and unstable internet connections.

---

## Current flow — step by step

1. User opens the IRCTC Rail Connect app
2. User searches and selects a train
3. User proceeds to passenger details page
4. Passenger form loads slowly
5. User taps the passenger name field and keyboard opens
6. Screen auto-scrolls unexpectedly
7. User enters passenger information
8. Keyboard closes and app screen refreshes
9. Some fields reset or lose focus
10. User re-enters passenger information
11. Form submission becomes slow and frustrating
12. User risks session timeout due to delays

---

## Where exactly it breaks

The failure occurs in Steps 5–10.

The mobile app frontend is poorly optimized for keyboard interactions and state persistence, causing re-renders and field resets during form entry.

# Final Observations

## Common UX Patterns Across Problems

1. Lack of real-time feedback
2. Poor mobile optimization
3. Inconsistent frontend state management
4. Weak error recovery systems
5. Technical terminology without user guidance
6. Backend overload during peak traffic periods

---

## Most Critical Problems by Impact

| Priority | Problem | Impact |
|----------|----------|--------|
| High | Tatkal booking crash | Massive daily revenue + trust loss |
| High | Payment status confusion | Financial anxiety and refund issues |
| High | Mobile form lag | Affects large mobile user base |
| Medium | Seat selection reset | Accessibility and comfort issue |
| Medium | Search filter inconsistency | Increased booking time |
| Medium | PNR guidance issue | Information clarity problem |

---

## Conclusion

IRCTC suffers from a combination of legacy system limitations, high traffic load, inconsistent frontend state handling, and poor UX communication patterns.

The problems documented above directly affect user trust, booking success rate, accessibility, and platform reliability. These findings will be used in Part B to design technical and UX solutions that improve scalability, transparency, and user confidence.
