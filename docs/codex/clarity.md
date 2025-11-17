# Clarity (disambiguation)

Ensures Codex interprets terms consistently across the Robby's Motivator system.

## 1. Glossary
- **Motivator Sheet**: The Google Sheets document that holds all messages, delivery flags, and the frequency field.
- **Message Row**: A single row in the sheet; column A = message text authored by the user.
- **Delivery Flag**: Column B of each row; `true`/check mark when the app has delivered that message this cycle, `false` otherwise.
- **Delivery Cycle**: The period from when at least one message is undelivered until every row has been delivered and the flags reset.
- **Frequency Field**: The sheet cell (visible to the user) containing `hourly`, `daily`, `weekly`, or `monthly`, which the app maps to scheduling intervals.
- **Snapshot**: The in-memory representation of the sheet (rows + frequency) captured at a specific sync time.
- **Reset**: Operation that sets every delivery flag in column B back to `false` once an entire cycle completes.

## 2. Ambiguous Terms
- **"Static notification generator"** means the app does not let users edit message content inside the UI; it only displays notifications based on the sheet.
- **"Check mark"** refers to setting the Google Sheets boolean value in column B to true—no additional formatting should be added.
- **"Row deleted"**: If a row from the snapshot is missing when writing back, treat it as intentionally removed by the user and skip it.
- **"More rows than before"**: When new rows are added after the snapshot, leave their delivery flags untouched until a fresh sync captures them.

## 3. Naming Rules
- Refer to the sheet consistently as `Motivator Sheet` in documentation and logs.
- Name Dart files using snake_case (e.g., `delivery_engine.dart`).
- Use nouns for data classes (`MessageRow`, `FrequencySetting`) and verbs for service classes (`SheetSynchronizer`, `DeliveryEngine`).
- Configuration constants should be upper snake case (e.g., `frequency_range` → `FREQUENCY_RANGE` in Dart).

## 4. Domain-Specific Notes
- Only the app may edit the second column and the frequency field; message text is immutable from the app’s perspective.
- Random selection must consider only undelivered rows; duplicates are allowed if the user adds them in the sheet.
- Frequency values are case-insensitive when parsed but must be written back exactly as the user provided.
- The app should not rely on message order for meaning—row index is simply a positional identifier for the delivery flag.

## 5. Cross-Document Consistency Rules
- Always describe the data source as a Google Sheet under the user’s Google Drive.
- When mentioning frequency options, list them in the canonical order: hourly, daily, weekly, monthly.
- Refer to the boolean column as “delivery flag” across requirements, architecture, and constraints.
- Mention resets as “clearing all delivery flags” for clarity.
