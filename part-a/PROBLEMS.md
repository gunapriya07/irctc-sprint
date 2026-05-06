## Problem 1 - Tatkal Booking Crashes at 10:00 AM

**What is broken:**
The IRCTC server crashes or becomes unresponsive at exactly 10:00 AM every morning when Tatkal quota opens. Users who successfully reach the payment page often have their sessions dropped, their selected seats re-released, and their OTPs delayed — causing the booking to fail at the final step despite completing every earlier step.

**Affected users:**
Every Indian trying to book a Tatkal ticket — roughly 20–40 lakh active users in the 9:58–10:05 AM window. This disproportionately impacts people in Tier 2 and 3 cities who depend on train travel and cannot afford to miss a booking window.

**Frequency:**
Daily. Every single morning at 10:00 AM. The pattern has existed for years and is a known, documented failure that IRCTC has attempted to patch multiple times without solving the root cause.

**Current user flow - step by step:**

1. User opens IRCTC at 9:50 AM, logs in, searches for train and selects a train
2. User selects Tatkal quota — page shows availability as "Available 12" at 9:55 AM
3. User fills passenger details (pre-filled if saved) and clicks "Book Now" at 9:59:45
4. At 10:00:00 — page freezes. Loading spinner appears. No progress feedback.
5. After 15–45 seconds: either HTTP 502 error, session timeout, or CAPTCHA reset
6. User refreshes — logged out. Logs back in. Train shows "Tatkal WL 1" — quota is gone.
7. User has no way to know if their payment was attempted or not — checks bank statement in panic

**Where it breaks:** Steps 4–6. The critical failure is that the system provides zero feedback during the freeze. Users cannot tell if their request is queued, failed, or succeeded. This causes repeat clicks, which compounds the server load, which makes the crash worse.


## Problem 2 - Search Filters Do Not Work Reliably 

**What is broken:**
The train search results page has filters for quota type, class, availability, and departure time. These filters frequently either do not apply correctly, reset when the page refreshes, or show trains that do not match the selected filter criteria.

**Affected users:**
Every user who searches for trains — all 8 crore registered users. Senior citizens and first-time users who rely on filters to find accessible coaches or specific departure times are disproportionately affected because they do not know to distrust the filter output.

**Frequency:**
Inconsistent — the filters work correctly roughly 60–70% of the time. The failure rate increases during high-traffic periods. The "quota" filter is the most unreliable.

**Current user flow - step by step:**

1. User enters source, destination, date - clicks Search Trains
2. Results appear showing 20–40 trains - overwhelming without filtering
3. User selects "Sleeper Class" and "Available" from the filter panel
4. Page reloads - some trains that are "WL" (waitlisted) still appear
5. User clicks a train - class shows "WL 34". Filter said "Available".
6. User goes back - filter has reset to "All Classes" - must reapply
7. User abandons filtering, manually scans all trains - adds 8–15 minutes to the process

**Where it breaks:** Steps 3–6. Filters are applied client-side on a cached result set that may already be stale. When the page refreshes to show "live" availability, the filter state is not preserved.

## Problem 3 - Seat Selection Resets Randomly 

**What is broken:**
During the booking flow, when a user selects a specific seat in the seat map, the selection is sometimes lost when they proceed to the next step. They arrive at the passenger details page with either a different seat assigned or the "Auto" option selected - meaning they may end up in any seat on the train.

**Affected users:**
Users booking for families (especially those with elderly or children who need lower berths), users with physical disabilities who require specific berths, and anyone who has paid extra attention to selecting a preference. Estimated 30–40% of all booking attempts involve a seat preference.

**Frequency:**
Occurs in approximately 15–25% of sessions involving seat map interaction. The rate is higher on mobile (35%) than desktop (12%).

**Current user flow - step by step:**

1. User selects a train, class, and quota - proceeds to seat selection
2. Seat map loads - shows available (white), booked (grey), selected (blue) berths
3. User clicks a lower berth for an elderly passenger - turns blue (selected)
4. User clicks "Proceed" - page loads passenger details form
5. Seat preference shows "Auto" or a different berth number - not what was selected
6. User goes back to reselect - seat map reloads, selected seat now shown as taken
7. User proceeds with auto-assignment - boards train to find they have an upper berth

**Where it breaks:** Steps 3–5. The seat selection state is not passed correctly between the seat map component and the passenger form. On mobile, this also triggers a re-render that clears local state.


## Problem 4 - Mobile Number Validation Lacks Error Message 

**What is broken:**
During the user registration flow, when entering a mobile number, the input field shows a red error indicator (red box) without displaying any corresponding error message. This creates confusion as users do not understand what is wrong with their input or how to fix it.

**Affected users:**
All users attempting to register on the platform, especially:

- First-time users unfamiliar with validation rules
- Users entering partial or incorrectly formatted mobile numbers
- Mobile users who rely heavily on inline validation feedback

This impacts nearly 100% of users interacting with the mobile number field during sign-up.

**Frequency:**
Occurs consistently whenever the mobile number input does not meet validation criteria.

- Approx. 100% reproducible when entering incomplete numbers (e.g., fewer than required digits)
- Observed across both desktop and mobile, though more confusing on mobile due to limited screen space

**Current user flow - step by step:**

1. User navigates to the registration page
2. User fills in email and other required details
3. User selects country code and starts entering mobile number
4. User enters an incomplete or invalid number (e.g., “871”)
5. Input field turns red indicating an error
6. No error message or helper text is displayed explaining the issue
7. User is left confused and unsure how to proceed

**Where it breaks:** Steps 4–6. The validation logic correctly detects an invalid input and triggers the error state (UI turns red), but the system fails to display an associated error message. This indicates a disconnect between validation handling and user feedback (UI messaging layer).

## Problem 5 - Footer Service Links Are Broken 

**What is broken:**
In the footer section under “Services”, specific links — Retiring Rooms, Hotels, and Tour Packages — redirect users to an unreachable or invalid external website. Instead of loading the intended service pages, users encounter a browser error page (“This site can’t be reached”), indicating a broken or misconfigured link.

**Affected users:**

- Users exploring additional services from the footer
- Users looking for accommodation or travel packages after booking
- First-time users who rely on footer navigation for discovery

This impacts all users who click on these specific footer links.

**Frequency:**

Occurs 100% of the time when clicking:
- Retiring Rooms
- Hotels
- Tour Packages
Reproducible across all browsers and devices

**Current user flow - step by step:**

1. User logs into their account or lands on the homepage
2. User scrolls down to the footer section
3. User views “Services” category
4. User clicks on one of the links (e.g., “Hotels”)
5. System redirects to an external URL (rr.irctctourism.com)
6. Browser displays error: “This site can’t be reached (DNS_PROBE_FINISHED_NXDOMAIN)”
7. User is unable to proceed and returns back or exits

**Where it breaks:** Steps 4–6. The hyperlink associated with these footer items points to an invalid, non-resolvable, or misconfigured domain. This indicates either:

- Incorrect URL mapping in the frontend
- Expired or removed domain
- Missing DNS configuration for the target service

## Problem 6 - Tour Package Filter Count Mismatch 

**What is broken:**
On the tour package search page, when applying the “Land Package” filter, the filter count incorrectly shows 0, but relevant packages are still displayed in the results. This creates confusion, as the UI suggests no packages exist under that filter while still rendering results.

**Affected users:**
All users browsing tour packages using filters, especially those relying on filter counts to decide selections. This includes first-time users, users comparing package types, and users trying to refine search results efficiently.

**Frequency:**
Occurs consistently whenever the “Land Package” filter is applied on this page (appears to be 100% reproducible based on current behavior).

**Current user flow – step by step:**

1. User navigates to the IRCTC tourism package search page
2. User views available filters under “Filters By Package Type”
3. “Land Package” shows count as (0)
4. User selects the “Land Package” filter checkbox
5. Search results still display one or more packages matching the filter
6. User notices mismatch between filter count (0) and visible results
7. User becomes uncertain whether filters are working correctly

**Where it breaks:** Steps 3–5. The filter count metadata is not synchronized with the actual dataset used to render results. Either:

- The count is not being updated correctly from the backend, or
- The frontend is using stale/incorrect aggregation data for filter counts

This leads to inconsistent UI behavior where the filter suggests no results, but results are still shown.