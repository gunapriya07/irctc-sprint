# Impact vs Effort Matrix

## The Matrix

|                 | Low Effort                                                                       | High Effort                                                                                                                       |
| --------------- | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **High Impact** | Mobile Number Validation Lacks Error Message,<br>Footer Service Links Are Broken | Tatkal Booking Queue and Recovery System,<br>Persistent and Accurate Train Search Filters,<br>Reliable Seat Selection Persistence |
| **Low Impact**  | Tour Package Filter Count Mismatch                                               | —                                                                                                                                 |

## How I Scored Each Dimension

### Impact Scoring (1–5)

I scored Impact based on:

- Number of users affected (from Part A frequency analysis)
- Whether the problem is in the core booking flow
- Severity of consequence for the user

### Effort Scoring (1–5)

I scored Effort based on:

- Number of system components touched
- Whether new infrastructure is required
- Risk of breaking existing flows
- Railway API dependencies

---

## Placement Justifications

### Tatkal Booking Queue and Recovery System — High Impact / High Effort

This problem affects 20–40 lakh users daily during Tatkal hours and directly impacts the most critical booking flow on the platform. The solution requires major infrastructure changes including queue systems, payment handling improvements, session recovery, and real-time booking status management. Its placement in this quadrant means it should be treated as a strategic long-term priority because solving it would significantly improve platform reliability and public trust.

### Persistent and Accurate Train Search Filters — High Impact / High Effort

Train search is used by nearly all IRCTC users, making unreliable filters a large-scale usability problem affecting booking confidence and efficiency. Fixing this issue requires backend filtering changes, live availability synchronization, caching improvements, and persistent frontend state management. This quadrant placement indicates that the feature is highly valuable but should be carefully planned to avoid breaking existing search and booking flows.

### Reliable Seat Selection Persistence — High Impact / High Effort

Seat preference failures strongly affect families, elderly passengers, and users requiring accessibility support, making the consequence highly severe within the booking journey. The solution touches seat allocation services, session management, mobile rendering behavior, and temporary seat locking infrastructure. This belongs in the High Impact / High Effort quadrant because it improves booking trust significantly but requires careful coordination with railway inventory systems.

### Mobile Number Validation Lacks Error Message — High Impact / Low Effort

This issue affects almost every new registering user and creates immediate confusion during onboarding. The fix mainly involves frontend validation messaging and accessibility improvements without requiring major backend or infrastructure changes. Since it provides a large usability improvement with minimal engineering effort, it should be prioritized early as a quick win.

### Footer Service Links Are Broken — High Impact / Low Effort

Broken footer links affect all users attempting to access tourism-related services and damage user trust in the platform. The solution mainly involves correcting URL mappings, DNS validation, and adding fallback error pages, making implementation relatively simple. This quadrant placement means the issue should be fixed quickly because it has visible user impact with low development risk.

### Tour Package Filter Count Mismatch — Low Impact / Low Effort

This issue affects only users browsing tourism packages and does not interfere with the core train booking flow. The fix primarily requires synchronizing frontend filter counts with backend aggregation data, which is technically straightforward. Its placement suggests it can be scheduled after higher-priority booking and reliability improvements are completed.

---

## Recommended Sprint Order

1. Mobile Number Validation Lacks Error Message — Fastest high-impact usability improvement with very low engineering risk.
2. Footer Service Links Are Broken — Simple fix that immediately improves trust and restores broken navigation.
3. Persistent and Accurate Train Search Filters — Core search reliability issue affecting almost every booking journey.
4. Reliable Seat Selection Persistence — Important accessibility and family-booking improvement within the booking flow.
5. Tatkal Booking Queue and Recovery System — Largest infrastructure-heavy initiative requiring careful phased rollout.
6. Tour Package Filter Count Mismatch — Lower-priority tourism usability issue with limited impact compared to booking-related problems.

## Peer Review Matrix Reconsideration

During peer review, the placement of the “Persistent and Accurate Train Search Filters” feature was discussed.

Initially, the feature was considered High Effort because it requires backend synchronization and live availability handling. However, reviewers suggested that much of the required infrastructure already exists in the current train search system.

As a result, the effort assessment was reconsidered from:

Very High Effort → Moderate-to-High Effort

The feature remains in the High Impact / High Effort quadrant because:

- It affects nearly all train-search users
- It directly impacts booking confidence
- Incorrect filtering can lead to failed booking decisions

However, the review concluded that this feature could potentially be delivered earlier than originally planned if implemented incrementally.