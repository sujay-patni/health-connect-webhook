# Changelog

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
