# Impact vs Effort Matrix

## The Matrix

|                   | Low Effort | High Effort |
|-------------------|-------------|--------------|
| **High Impact** | Search Filter Persistence, Payment Confirmation Tracking, Mobile Passenger Form | Tatkal Virtual Queue |
| **Low Impact** | PNR Guidance System | Seat Preference Preservation, AI Waitlist Predictor |

---

# How I Scored Each Dimension

## Impact Scoring (1–5)

Impact was scored based on:
- Number of users affected
- Whether the issue blocks ticket booking
- Financial or usability consequences
- Frequency of occurrence

---

## Effort Scoring (1–5)

Effort was scored based on:
- Backend infrastructure changes
- Frontend redesign complexity
- API dependencies
- Real-time systems required
- Risk of breaking existing booking flow

---

---

# Placement Justifications

## Tatkal Virtual Queue — Major Project (High Impact, High Effort)

The Tatkal booking crash affects 20–40 lakh users daily and directly impacts booking success and revenue. The solution requires Redis infrastructure, WebSocket updates, queue orchestration, and backend scaling changes. Because of the extremely high impact, this feature should be prioritized as a dedicated engineering sprint despite the complexity.

---

## Search Filter Persistence — Quick Win (High Impact, Low Effort)

This issue affects almost every user searching for trains and creates repeated frustration throughout the booking journey. The solution mainly requires frontend state persistence and minor API improvements without major backend architecture changes. It should be implemented immediately because it provides large UX improvements with relatively low engineering effort.

---

## Payment Confirmation Tracking — Quick Win (High Impact, Low Effort)

Payment uncertainty directly impacts user trust and creates financial anxiety during booking. Most of the solution involves transaction tracking UI and improved payment callback handling rather than full infrastructure redesign. This makes it a high-priority quick win for improving booking confidence.

---

## Mobile Passenger Form Optimization — Quick Win (High Impact, Low Effort)

A large percentage of IRCTC traffic comes from mobile app users, especially Android devices. The improvements mainly involve frontend rendering optimization and draft-saving logic without requiring deep backend changes. Since mobile usability directly affects booking completion rates, this should be prioritized early.

---

## Seat Preference Preservation — Fill-In (Low Impact, High Effort)

Although berth preference issues frustrate users, they do not always block ticket booking entirely. Implementing temporary seat locking and synchronized seat state management increases backend complexity considerably. This feature is valuable but should be scheduled after higher-priority booking stability improvements.

---

## PNR Guidance System — Fill-In (Low Impact, Low Effort)

The issue mainly affects understanding and clarity rather than booking completion itself. The solution involves explanatory UI text, guidance cards, and lightweight prediction integrations with minimal infrastructure work. It is useful for improving accessibility and trust but is not as critical as booking or payment stability.

---

## AI Waitlist Predictor — Time Sink (Low Impact, High Effort)

The AI predictor improves user confidence but does not directly solve booking system failures. Building and maintaining ML pipelines, historical training datasets, and prediction APIs adds significant engineering and operational effort. This feature should only be developed after core booking reliability issues are fixed.

---

# Recommended Sprint Order

1. Search Filter Persistence — Large UX improvement with minimal effort
2. Payment Confirmation Tracking — Reduces financial anxiety quickly
3. Mobile Passenger Form Optimization — Improves mobile booking completion
4. Tatkal Virtual Queue — Critical infrastructure investment
5. PNR Guidance System — Improves clarity and accessibility
6. Seat Preference Preservation — Medium priority booking enhancement
7. AI Waitlist Predictor — Long-term enhancement after core stability improvements

---

# Peer Review Updates

## Feedback Received

### Feedback 1
Concern:
"What happens if the Tatkal queue server fails during peak hours?"

Update made:
Added graceful fallback to direct booking mode in Feature Spec 1.

---

### Feedback 2
Concern:
"Users on slow internet may disconnect from the queue."

Update made:
Added automatic queue reconnect handling and polling fallback.

---

### Feedback 3
Concern:
"Prediction systems may mislead users if confidence is low."

Update made:
Added confidence threshold rules and fallback messaging in AI feature spec.

---

### Feedback 4
Concern:
"Mobile users may lose passenger data during interruptions."

Update made:
Added draft autosave and recovery support in Mobile Passenger Form spec.