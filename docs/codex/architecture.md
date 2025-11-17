# Architecture

Defines how Robby's Motivator is structured so the system stays predictable and maintainable.

## 1. High-Level Overview
The app is a Flutter-based Android client that periodically synchronizes with a Google Sheet to obtain motivational messages, schedules a local notification using the requested frequency, and records delivery status back into the sheet. A thin UI surfaces connection status and next-run information, while background services handle scheduling and sheet updates.

## 2. Module Breakdown
1. **GoogleSheetClient (data/sources/)**
   - Responsibilities: authenticate with Google, fetch sheet contents, push delivery-flag updates, reset cycle.
   - Inputs: OAuth credentials, spreadsheet ID, requested ranges (messages, frequency cell).
   - Outputs: Structured DTOs containing rows, flags, and frequency values.
   - Dependencies: Google APIs, HTTP client, secure storage for tokens.
2. **SheetRepository (data/repositories/)**
   - Responsibilities: Manage local cache, diff snapshots, expose stream of sheet state to the rest of the app.
   - Inputs: GoogleSheetClient snapshots.
   - Outputs: Domain models (`MessageRow`, `DeliveryCycle`, `FrequencySetting`).
   - Dependencies: Local storage for caching and timestamping.
3. **SchedulerService (services/scheduler/)**
   - Responsibilities: Translate frequency settings into Android alarms/background tasks, trigger selection on schedule.
   - Inputs: FrequencySetting, pending cycle state.
   - Outputs: Foreground/background callbacks to show notifications.
   - Dependencies: Flutter background execution APIs, `flutter_local_notifications`.
4. **DeliveryEngine (services/delivery/)**
   - Responsibilities: Select random undelivered message, signal UI/notification payloads, request sheet updates.
   - Inputs: Current snapshot from SheetRepository.
   - Outputs: Notification payloads, update commands for GoogleSheetClient.
   - Dependencies: Random generator, repository interface.
5. **UI Layer (ui/)**
   - Responsibilities: Provide configuration status, manual sync trigger, and debug views (optional) without editing sheet content.
   - Inputs: Streams from SheetRepository and SchedulerService.
   - Outputs: Visual state, user-triggered syncs.

## 3. Data Flow
1. SchedulerService triggers according to the frequency read from the sheet.
2. DeliveryEngine requests the latest snapshot from SheetRepository (which syncs with GoogleSheetClient if stale).
3. DeliveryEngine picks an undelivered message, sends it to NotificationManager, and instructs GoogleSheetClient to mark the row delivered.
4. GoogleSheetClient updates column B and returns confirmation; SheetRepository refreshes the cache.
5. When all rows are delivered, DeliveryEngine issues a reset command that clears column B and restarts the selection process.

## 4. Component Interactions
- UI ↔ SheetRepository: UI subscribes to sheet status via streams/futures.
- SchedulerService → DeliveryEngine: passes timer callbacks; DeliveryEngine operates headlessly with minimal UI involvement.
- DeliveryEngine ↔ GoogleSheetClient: uses repository interfaces to read/write without exposing HTTP details.
- GoogleSheetClient ↔ Google APIs: REST calls to Sheets scopes for batch read/write.
- Notification subsystem: DeliveryEngine hands off payloads to `flutter_local_notifications` for display.

## 5. Repository Structure
```
docs/
  build/
  codex/
lib/
  data/
    sources/           # Google API integration
    repositories/      # Snapshot cache + domain models
  services/
    scheduler/         # Frequency to alarm mapping
    delivery/          # Random selection + reset logic
  ui/
    screens/           # Status/diagnostics screens
    widgets/
  utils/
    frequency_parser.dart
android/               # Native Android project files
test/
  data/
  services/
  utils/
```

## 6. System Lifecycle
1. **Startup**: App initializes secure storage, fetches cached snapshot, and attempts Google OAuth if not yet authorized.
2. **Initial Sync**: SheetRepository calls GoogleSheetClient to pull messages, delivery flags, and frequency; SchedulerService schedules the next notification.
3. **Operational Loop**: On each scheduled trigger, DeliveryEngine selects a message, dispatches a notification, and requests the sheet update.
4. **Reset Cycle**: When no undelivered rows remain, DeliveryEngine signals GoogleSheetClient to set all flags to false, then restarts scheduling.
5. **User Edits**: Users change the sheet directly; the next sync picks up the changes and updates caches/frequency accordingly.
6. **Shutdown**: Background tasks remain scheduled via Android alarm manager; when the app process dies, the next trigger relaunches the necessary background isolate.
