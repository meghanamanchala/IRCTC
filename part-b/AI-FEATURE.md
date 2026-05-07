# AI Feature Specification: Waitlist Confirmation Probability Predictor

## Problem It Solves

This feature addresses Part A Problem 5: PNR Status Page Lacks Clear Guidance.

Users with WL (Waitlist) tickets do not know whether their ticket is likely to confirm before departure. This uncertainty causes anxiety, duplicate bookings, and unnecessary cancellations.

---

## Proposed Feature — User Perspective

After checking PNR status, users see a confirmation prediction card.

Example:

"Your WL 12 ticket has a 78% chance of confirmation based on historical booking patterns for this route and class."

The system also explains:
- Whether confirmation chances are improving
- Similar historical patterns
- Suggested actions

Users receive updated predictions automatically before departure.

---

## Model or API Choice

### Selected Model:
XGBoost Classification Model

### Why XGBoost?
- Works well on structured railway booking data
- Fast prediction time
- Easier to explain than deep learning
- High accuracy for tabular historical datasets

### Serving Layer:
FastAPI backend serving prediction requests.

---

## Training or Input Data

### Required Data:
- Historical WL booking records
- Train route
- Train number
- Travel month/season
- Ticket class
- Initial WL position
- Final confirmation result
- Cancellation trends

### Data Sources:
- IRCTC historical booking database
- Railway reservation logs
- PNR confirmation history

### Data Collection Need:
IRCTC already stores booking and PNR data internally, so no new user data collection is required.

---

## How Output Is Shown to the User

### UI Component

PNR STATUS RESULT
────────────────────────
WL 12
78% chance of confirmation

Trend:
⬆ Improving over last 24 hrs

Recommendation:
Avoid duplicate booking for now.
Next update in 6 hours.
────────────────────────

Users can also enable:
- SMS alerts
- Push notifications
- Confirmation tracking reminders

---

## Confidence Threshold and Fallback

### Confidence Rule:
- Show prediction only if confidence ≥ 60%
- Below 60%, show:

"Insufficient historical data available for accurate prediction."

### Fallback:
- Show standard PNR status only
- Continue manual notification tracking

---

## Success Metrics

- Reduction in PNR-related support queries
- Increased user trust in PNR system
- Reduced duplicate waitlist bookings
- Higher notification engagement rate

---

## Limitations and Risks

- Predictions are probabilistic, not guaranteed
- Sudden bulk cancellations can change outcomes
- Seasonal routes may behave unpredictably
- Users may over-rely on prediction scores

To reduce misuse:
- Predictions are labelled clearly as estimates
- Confidence score shown transparently
- Manual status always remains visible