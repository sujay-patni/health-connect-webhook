# Changelog

## [1.8.5] - 2026-05-08

### Added

- Webhook payload now includes `sync.data_window_start`, the earliest UTC instant covered by the read. Receivers (e.g. Routine Manager) use it to skip writes for past logical days that fall only partially inside the window — preventing a partial sync (say, at 1 AM local time with a 48 h lookback) from overwriting a previously-complete past day with fragment-only data.

## [1.8.4] - 2026-05-08

### Changed

- **Send full 48-hour window** is now the default for interval syncs. Late-arriving Samsung Health writes (e.g. when watch data lands in Health Connect hours after the activity) are no longer dropped by the per-record `lastSync` filter. Existing installs that previously toggled this setting keep their stored value; new installs and untouched settings now default to on.
- Removed the in-app filter that dropped Health Connect step records longer than 60 minutes. Receivers (e.g. Routine Manager) now decide what to drop based on duration, which avoids needing an APK rebuild to tune the threshold.

## [1.8.3] - 2026-05-07

### Added

- Interval sync can now resend the complete rolling 48-hour Health Connect window with the new **Send full 48-hour window** option. This is intended for idempotent receivers that recalculate and overwrite daily totals, such as Routine Manager.
- Webhook payloads now include sync metadata:

```json
{
  "sync": {
    "trigger": "interval",
    "explicit_range": false,
    "interval_full_lookback": true,
    "used_last_sync_filter": false
  }
}
```

- Webhook payloads include enough sync metadata for receivers to trace interval/manual/scheduled behavior without relying on raw response-body logging.

### Changed

- Webhook delivery now attempts every configured webhook URL for a sync payload. A successful post to one URL no longer prevents later URLs from receiving the same payload.
- Steps are sent as raw Health Connect step records with `count`, `start_time`, and `end_time`, allowing receivers to bucket steps using their own timezone and day-start rules.
- Public repository links now point to `sujay-patni/health-connect-webhook`.

### Latest Webhook API

Only arrays with records are included. Additional health arrays may be present depending on enabled data types.

```json
{
  "timestamp": "2026-05-07T18:30:00Z",
  "app_version": "1.8.3",
  "sync": {
    "trigger": "interval",
    "explicit_range": false,
    "interval_full_lookback": true,
    "used_last_sync_filter": false
  },
  "steps": [
    { "count": 312, "start_time": "2026-05-07T10:00:00Z", "end_time": "2026-05-07T10:05:00Z" }
  ],
  "sleep": [
    { "session_end_time": "2026-05-07T01:30:00Z", "duration_seconds": 28140, "stages": [] }
  ],
  "distance": [
    { "meters": 520.4, "start_time": "2026-05-07T10:00:00Z", "end_time": "2026-05-07T10:15:00Z" }
  ],
  "active_calories": [
    { "calories": 42.5, "start_time": "2026-05-07T10:00:00Z", "end_time": "2026-05-07T10:15:00Z" }
  ]
}
```
