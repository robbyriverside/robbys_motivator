# Constraints

This document defines the rules and boundaries Codex must respect when building Robby's Motivator.

## 1. Technology Constraints
- Platform: Flutter application targeting Android (minimum supported SDK aligned with Flutter’s stable channel).
- Data source: Google Sheets (via Google Drive/Sheets APIs); no alternate databases or files.
- Notifications: Use Android-compatible scheduling (e.g., `flutter_local_notifications` + background service) that honors Doze restrictions.
- State persistence: Lightweight local storage (e.g., `shared_preferences`, SQLite) allowed only for caching snapshots.

## 2. Architecture Constraints
- Keep sheet synchronization, notification scheduling, and presentation concerns in separate Dart modules.
- Only the sync module may mutate Google Sheet data; UI must treat the sheet as the source of truth.
- Delivery flags must be tracked per row index; never infer identity from message text.
- Frequency parsing logic must convert the four allowed string values into scheduler intervals via a single mapping utility.

## 3. Security Constraints
- OAuth credentials for Google APIs must be stored using secure Android storage (encrypted shared preferences/keystore helpers).
- Do not log OAuth tokens or message contents.
- Network calls must use HTTPS endpoints provided by Google APIs.
- Respect Google Drive scopes: request only read/write access to the specific spreadsheet needed.

## 4. Coding Style Constraints
- Use idiomatic Dart naming (`PascalCase` for classes, `camelCase` for members, snake_case for files).
- Keep files under `lib/` organized by feature (`data/`, `services/`, `ui/`).
- Document non-trivial methods with concise comments, especially around sync/reset logic.
- Provide unit tests for frequency parsing, selection/reset algorithms, and sheet diffing logic.

## 5. Performance Constraints
- Sync operations should batch read/write operations rather than per-row network calls.
- Do not block the main isolate during network access—use async APIs.
- Schedule notifications efficiently to avoid unnecessary wakeups between intervals.

## 6. Forbidden Approaches
- Do not copy message text into local files or databases—Google Sheets remains the single source of truth.
- Do not let the app edit the first column or any user-authored content besides the delivery flag and frequency cell.
- Do not hardcode message content or notification frequency in the app bundle.
- Avoid polling the sheet more frequently than necessary; rely on configured intervals.
