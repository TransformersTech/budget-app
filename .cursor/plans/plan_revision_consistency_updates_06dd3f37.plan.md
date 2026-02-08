---
name: "Plan Revision: Consistency Updates"
overview: "Revise the budget app build plan to ensure internal consistency on: (1) Firebase-only dashboard auth with no NextAuth/cookie references, (2) composite sync cursor throughout all sync sections, (3) home-timezone based day keys for all budget/aggregate operations, and (4) clarified aggregate correction safety with server-side delta computation."
todos:
  - id: search-verify
    content: Search for problematic terms to identify exact locations needing changes
    status: pending
  - id: update-auth
    content: Remove any NextAuth/cookie references, strengthen Firebase-only auth messaging
    status: pending
  - id: update-cursor
    content: Update Milestone 5 and related sections to use composite cursor consistently
    status: pending
  - id: verify-timezone
    content: Verify no UTC day key references for aggregates, confirm home timezone throughout
    status: pending
  - id: update-aggregate-safety
    content: Add server-side delta safety notes to aggregate correction sections
    status: pending
isProject: false
---

# Plan Revision: Consistency Updates

## Current State Analysis

The plan file [`budget_app_build_plan_e14f47f6.plan.md`](budget_app_build_plan_e14f47f6.plan.md) is mostly correct but has some inconsistencies:

### Issue 1: Dashboard Auth References

- **Status**: Generally correct, uses Firebase client-side auth
- **Issue**: Line 1081 mentions "CSRF protection on dashboard (not needed for Firebase client-side auth; no cookies)" - needs clarification
- **Action**: Verify no NextAuth references exist and ensure consistent messaging

### Issue 2: Sync Cursor Inconsistency

- **Current state**: 
- Line 84 (Key Decisions): ✅ Mentions composite cursor `(serverTimestamp, eventId)`
- Line 540-551 (Milestone 5): ❌ Uses "lastSyncTimestamp" and needs composite cursor details
- Line 1529-1551 (Sync Engine Design): ✅ Has composite cursor details
- **Action**: Update Milestone 5 to use composite cursor terminology consistently

### Issue 3: Timezone Strategy

- **Current state**: Already uses home timezone approach throughout
- Line 73-74: ✅ "User Home Timezone" as Key Architecture Decision #1
- Line 1598-2022: ✅ Detailed timezone/DST section with home timezone
- **Action**: Verify no UTC day key references remain

### Issue 4: Aggregate Correction Safety

- **Current state**: 
- Line 78-79: ✅ Mentions server-authoritative projection
- Line 1598-1600: ✅ Server reads previous state from projection
- **Action**: Add explicit note about server-side delta safety when beforePayload missing

## Specific Changes Required

### A) Dashboard Auth Consistency

**Locations to update:**

- Milestone 1 (lines 342-388): Review and strengthen Firebase-only messaging
- Milestone 9 (lines 736-775): Ensure no NextAuth references
- Milestone 17 Security (line 1081): Clarify CSRF note
- Any other mentions of "cookie", "NextAuth", "session" in dashboard context

**New language:**

- "Dashboard uses Firebase Auth (client SDK) and reads data via Firestore queries filtered by auth.uid OR via callable HTTPS functions where needed."
- "Server-side API routes must verify Firebase ID tokens (Authorization: Bearer <idToken>) when applicable."

### B) Composite Sync Cursor

**Locations to update:**

- Milestone 5 (lines 534-580): Replace "lastSyncTimestamp" with composite cursor
- Update to mention: `{ lastServerTimestamp, lastEventId }`
- Update query logic: `WHERE serverTimestamp > cursor.timestamp OR (serverTimestamp == cursor.timestamp AND eventId > cursor.eventId)`
- Update ordering: `ORDER BY (serverTimestamp ASC, eventId ASC)`

### C) Home Timezone Verification

**Locations to verify:**

- Time Zones and DST section (lines 2008-2036): ✅ Already correct
- Ensure no "UTC date" or "UTC day key" references for aggregates/budgets
- All dayKey/monthKey references should mention home timezone

### D) Aggregate Correction Safety Note

**Location to update:**

- Milestone 6 (lines 583-635): Add note about server-side delta computation
- Sync Engine Design > Aggregate Corrections (lines 1590-1609): Add explicit safety note

**New language:**
"Cloud Functions should be able to compute deltas using server state if beforePayload is missing or incomplete, to avoid corrupt aggregates. Still prefer clients sending before/after for efficiency, but server remains source of truth."

## Verification Strategy

After making changes:

1. Search for "NextAuth" → should find 0 results
2. Search for "cookie" in dashboard context → should only reference "no cookies" approach
3. Search for "lastSyncTimestamp" → should be replaced with composite cursor
4. Search for "UTC day" or "UTC date" in aggregate context → should reference home timezone instead
5. Verify aggregate correction sections mention server-side delta safety

## Constraints Respected

- ✅ No refactoring of unrelated sections
- ✅ No changes to milestone structure (keeping all 20 milestones)
- ✅ Only targeted edits to the 4 specified topics
- ✅ Preserving existing correct implementations (home timezone already implemented)