# IRCTC Design Engineering Sprint — Part B

# Feature Spec 1: Tatkal Virtual Queue System

## Problem Statement

As documented in Part A Problem 1, IRCTC experiences severe server crashes and booking failures during Tatkal opening hours between 10:00–10:05 AM. Users face session timeouts, HTTP 502 errors, payment failures, and repeated login issues due to massive concurrent traffic spikes. This directly affects lakhs of users daily and causes revenue loss, booking failure, and reduced trust in the platform.

---

## Current State (from Part A)

In the current flow, users enter Tatkal booking around 9:45–10:00 AM and attempt to book tickets exactly at 10:00 AM. The failure occurs during Steps 6–10 where the booking page freezes, the server becomes unresponsive, CAPTCHA resets occur, and users are logged out unexpectedly. The system provides no live queue position or progress tracking, leading users to repeatedly refresh the page and overload the system further.

---

## Proposed Solution

Introduce a virtual Tatkal waiting room and queue management system. Users joining the Tatkal section before opening time receive a queue position and estimated waiting time. Instead of everyone hitting the booking API simultaneously, users are processed gradually based on server capacity.

Users see:
- Live queue position
- Estimated wait time
- Countdown timer
- Booking slot timer when their turn arrives

This reduces panic-refreshing and distributes server load evenly.

---

## Proposed User Flow — Step by Step

1. User opens Tatkal booking page before 10:00 AM
2. User joins virtual waiting room automatically
3. System assigns queue position
4. User sees live countdown until Tatkal opens
5. Queue processing begins at 10:00 AM
6. Live queue position updates every few seconds
7. When user’s turn arrives, booking form unlocks
8. User gets 90-second booking window
9. Passenger details are pre-filled
10. User completes payment
11. Confirmation page appears instantly
12. If timer expires, slot passes to next user

---

## Technical Implementation Plan

### System components affected:
- Frontend booking flow
- Backend booking gateway
- Session management service
- Redis caching layer
- Real-time queue server
- Payment gateway integration

### New data requirements:
- Queue ID
- Queue position
- Estimated wait time
- Booking slot expiration timestamp
- Active session token

### API changes:
- `POST /tatkal/queue/join`
- `GET /tatkal/queue/status/:id`
- `POST /tatkal/queue/confirm`
- WebSocket event for live queue updates

### Frontend changes:
- Queue position component
- Countdown timer
- Live progress bar
- Booking slot modal
- Auto-refresh seat availability

### Third-party services (if any):
- Redis for queue management
- Socket.io/WebSocket server

---

## Success Metrics

- Tatkal booking completion rate improves from 40% → 70%
- Server crashes during 10 AM reduce by 80%
- Queue-related complaints reduce by 75%
- Average page refresh rate reduces significantly

---

## Edge Cases and Constraints

- Redis failure should fall back to normal booking flow
- Users with unstable internet should reconnect safely
- Queue token reuse must be prevented
- Queue system should support mobile app users
- Railway reservation backend APIs cannot be modified heavily

---

## Wireframe

![Tatkal Queue Wireframe]()

---

---

# Feature Spec 2: Persistent Smart Search Filters

## Problem Statement

As documented in Part A Problem 2, IRCTC train search filters frequently fail to persist or apply correctly. Users selecting filters such as “Available Only” or “Sleeper Class” still see incorrect results or lose filters after navigation. This creates confusion and increases booking time.

---

## Current State (from Part A)

The issue occurs during Steps 5–10 where filters reload inconsistently, stale cached results appear, and previously selected filters reset after navigating back from train details.

---

## Proposed Solution

Implement persistent filter state management and real-time availability validation. Filters selected by users remain active even after navigation or refresh. Availability data is validated before displaying trains.

Users can:
- Save filters during session
- Reapply filters automatically
- View accurate availability-only results
- Restore previous search instantly

---

## Proposed User Flow — Step by Step

1. User searches trains
2. User selects filters
3. Filters apply instantly without full reload
4. Selected filters remain pinned at top
5. User opens train details
6. User navigates back
7. Filters remain active
8. Train list restores automatically
9. Live availability refreshes every few seconds
10. User books ticket confidently

---

## Technical Implementation Plan

### System components affected:
- Search frontend
- Search API
- Availability caching layer

### New data requirements:
- Session filter preferences
- Last search state
- Availability cache timestamp

### API changes:
- `GET /search/trains?filters=`
- `POST /user/search/preferences`

### Frontend changes:
- Sticky filter chips
- Local/session storage persistence
- Background availability refresh

### Third-party services:
- None

---

## Success Metrics

- Filter reset complaints reduce by 90%
- Search completion time reduces by 35%
- Bounce rate from train search page decreases

---

## Edge Cases and Constraints

- Cached availability may still delay during heavy load
- Session storage should work across app refreshes
- Mobile app and desktop should sync filter state

---

## Wireframe

![Search Filter Wireframe]()

---

---

# Feature Spec 3: Reliable Seat Preference Preservation

## Problem Statement

As documented in Part A Problem 3, selected berth preferences often reset during the booking flow, especially on mobile app devices. Users lose selected lower berths or preferred seats, causing frustration and accessibility issues.

---

## Current State (from Part A)

The failure occurs during Steps 6–9 where selected seats are lost between the seat selection page and passenger details page due to frontend re-rendering and state loss.

---

## Proposed Solution

Implement centralized seat selection persistence and real-time seat locking. Once a user selects a seat, it remains temporarily reserved while proceeding through booking steps.

---

## Proposed User Flow — Step by Step

1. User selects train
2. Seat map opens
3. User selects preferred berth
4. Seat becomes temporarily reserved
5. Reservation timer begins
6. User proceeds to passenger details
7. Selected seat remains visible
8. Payment completes
9. Seat assignment confirmed

---

## Technical Implementation Plan

### System components affected:
- Seat map UI
- Booking backend
- Reservation session management

### New data requirements:
- Temporary seat reservation token
- Seat lock expiration timestamp

### API changes:
- `POST /seat/lock`
- `POST /seat/release`
- `GET /seat/status`

### Frontend changes:
- Persistent seat state
- Reservation countdown
- Seat lock indicator

### Third-party services:
- None

---

## Success Metrics

- Seat reset issues reduce by 85%
- Mobile booking completion improves by 30%
- Lower berth assignment satisfaction improves

---

## Edge Cases and Constraints

- Locked seats must auto-release after timeout
- Multiple users selecting same seat simultaneously
- Seat locking should not overload backend

---

## Wireframe

![Seat Selection Wireframe]()

---

---

# Feature Spec 4: Real-Time Payment Confirmation Tracking

## Problem Statement

As documented in Part A Problem 4, users completing UPI payments often remain stuck on a loading spinner without confirmation. This creates payment anxiety and duplicate booking attempts.

---

## Current State (from Part A)

The failure occurs during Steps 8–11 where the payment gateway callback is delayed and no transaction tracking or recovery messaging exists.

---

## Proposed Solution

Add live payment status tracking with transaction recovery flow. Users receive real-time payment progress updates and recovery options if payment confirmation is delayed.

---

## Proposed User Flow — Step by Step

1. User selects UPI payment
2. User approves payment
3. Payment tracking screen appears
4. System shows:
   - Payment received
   - Verifying booking
   - Generating ticket
5. Auto-retry occurs if callback delays
6. Ticket appears instantly after confirmation
7. Recovery page appears if payment uncertain

---

## Technical Implementation Plan

### System components affected:
- Payment gateway
- Booking confirmation service
- Notification service

### New data requirements:
- Transaction state
- Retry status
- Callback timestamps

### API changes:
- `GET /payment/status/:txnId`
- `POST /payment/recover`

### Frontend changes:
- Payment tracker UI
- Recovery screen
- Transaction timeline

### Third-party services:
- UPI payment providers

---

## Success Metrics

- Payment-related support complaints reduce by 70%
- Duplicate payments reduce significantly
- Booking trust score improves

---

## Edge Cases and Constraints

- Delayed bank callbacks
- Failed transactions after deduction
- Network interruptions during payment

---

## Wireframe

![Payment Tracking Wireframe]()

---

---

# Feature Spec 5: Intelligent PNR Guidance System

## Problem Statement

As documented in Part A Problem 5, the PNR page uses technical railway terminology without explanation. Users do not understand terms like WL, RAC, or Chart Prepared.

---

## Current State (from Part A)

The issue occurs during Steps 4–7 where users receive technical ticket statuses without guidance or contextual help.

---

## Proposed Solution

Add simplified explanations, prediction insights, and next-step guidance to the PNR page. Users receive human-readable explanations for their ticket status.

---

## Proposed User Flow — Step by Step

1. User enters PNR number
2. Status page loads
3. System explains ticket status in simple language
4. User sees confirmation probability
5. Suggested next steps appear
6. User receives alerts for updates

---

## Technical Implementation Plan

### System components affected:
- PNR status service
- Notification service
- AI prediction engine

### New data requirements:
- Historical confirmation data
- User notification preferences

### API changes:
- `GET /pnr/explained-status`
- `GET /pnr/prediction`

### Frontend changes:
- Status explanation cards
- Tooltip guidance
- Progress indicator

### Third-party services:
- ML prediction API

---

## Success Metrics

- PNR confusion complaints reduce by 80%
- Users spend less time leaving IRCTC for explanations
- Notification engagement improves

---

## Edge Cases and Constraints

- Prediction confidence may vary by route
- Real-time chart preparation updates
- Railway backend delays

---

## Wireframe

![PNR Guidance Wireframe]()

---

---

# Feature Spec 6: Optimized Mobile Passenger Form

## Problem Statement

As documented in Part A Problem 6, the mobile app passenger form lags heavily during entry. Keyboard interactions trigger layout shifts and field resets, making booking difficult on lower-end Android devices.

---

## Current State (from Part A)

The failure occurs during Steps 5–10 where the page re-renders during keyboard open/close events, causing field resets and focus loss.

---

## Proposed Solution

Redesign the passenger form for mobile-first performance. Introduce lightweight components, autosave, keyboard-safe layouts, and step-based passenger entry.

---

## Proposed User Flow — Step by Step

1. User opens passenger form
2. Form loads instantly
3. User enters passenger details step-by-step
4. Keyboard-safe scrolling activates
5. Data autosaves locally
6. User resumes if interrupted
7. Form validates progressively
8. Submission completes smoothly

---

## Technical Implementation Plan

### System components affected:
- Mobile app frontend
- Form validation system
- Local storage handling

### New data requirements:
- Autosaved passenger draft
- Device performance analytics

### API changes:
- `POST /booking/draft/save`
- `GET /booking/draft`

### Frontend changes:
- Step-based mobile form
- Optimized rendering
- Keyboard-safe layout
- Draft restore modal

### Third-party services:
- None

---

## Success Metrics

- Mobile booking completion improves by 40%
- Form abandonment reduces by 50%
- Average form completion time reduces

---

## Edge Cases and Constraints

- Draft recovery after app crash
- Offline interruptions
- Older Android compatibility

---

## Wireframe

![Mobile Form Wireframe]()