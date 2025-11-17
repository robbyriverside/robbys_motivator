# Requirements

This document describes what Robby's Motivator must do so Codex can implement the Android experience reliably.

## 1. Purpose
- Deliver motivational notifications on Android devices at user-controlled intervals (hourly, daily, weekly, or monthly).
- Source all messages, delivery state, and scheduling frequency from a Google Sheets document the user maintains.
- Keep the first column (message text) untouched while the app owns and updates the second column (delivery flag).

## 2. User Stories
- As a user, I manage my motivational messages and preferred frequency directly inside the Google Sheet without reopening the app.
- As the motivator app, I fetch the sheet, pick an undelivered message at random, show it as a notification, and mark it delivered.
- As the motivator app, I reset all delivery flags when every message has been delivered so the cycle starts over automatically.
- As the motivator app, I gracefully handle added or removed rows so user edits never cause crashes or stale state.
- As a user, I expect the app to authenticate with my Google Drive once and then operate on my sheet securely.

## 3. Functional Requirements
1. **Google Sheets Integration**
   - Authenticate with Google Drive/Sheets using the user’s account and target spreadsheet.
   - Pull the full table: column A = message text, column B = boolean delivered flag, frequency cell (visible to user) storing one of [hourly, daily, weekly, monthly].
   - Treat the sheet read as a snapshot with row numbers as identifiers.
2. **Notification Selection**
   - Build an in-memory list of rows marked as not delivered.
   - Randomly select one row per scheduled notification. If all rows are delivered, trigger a reset before selecting.
3. **Delivery Tracking**
   - After showing a notification, update the corresponding row’s second column to true (check mark) and push the change back to the sheet while preserving message text.
   - When every row is delivered, set all delivery flags back to false and start a new delivery cycle.
4. **Frequency Handling**
   - Parse the frequency field and map it to an Android alarm/scheduler interval (hourly, daily, weekly, monthly). Invalid values fall back to a safe default and are logged.
   - Expose the effective frequency inside the sheet so the user can confirm it.
5. **Row Drift Management**
   - If new rows exist when writing back, only update rows that existed in the fetched snapshot; leave new rows’ second column untouched until the next sync.
   - If rows were deleted, skip updates for missing indices (treat them as removed messages).
6. **Offline/Sync Behavior**
   - Cache the last successful snapshot locally so notifications can continue if the sheet is temporarily unavailable, then reconcile on the next sync.

## 4. Acceptance Criteria
- A notification displays text sourced from column A and reflects the most recent sheet data.
- The second column for the delivered message toggles to true in Google Sheets within the same cycle.
- Changing the frequency cell in the sheet updates the next scheduled notification interval without requiring a code change.
- Adding or removing messages in the sheet is reflected on the next sync without data corruption or crashes.
- After all messages are marked delivered, every row resets to false and notifications continue cycling.
- The application never overwrites message text or other columns beyond the delivery flag and frequency field.

## 5. Edge Cases
- Sheet contains zero active messages: app should log and wait until at least one row appears.
- Frequency cell missing or invalid: fall back to daily notifications and flag the issue (toast/log).
- Network/auth failures: retries with exponential backoff and user-visible error state while preserving local cache.
- Concurrent edits while pushing updates: rely on row numbers and last snapshot to avoid clobbering user changes.
- Messages added or removed between fetch and update: only touch rows that still exist.

## 6. Non-Functional Requirements
- Must run on modern Android devices via Flutter with smooth startup (<2s perceived load).
- Keep network usage low by syncing only when needed (on schedule change or delivery cycle events).
- Respect user privacy—store OAuth tokens securely and avoid logging message content.
- Provide maintainable, well-tested Dart code with clear separation between data sync, scheduling, and UI layers.
