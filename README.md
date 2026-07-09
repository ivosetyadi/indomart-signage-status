# Indomart Signage — Kill Switch (Layer 3)

External kill-switch mirror. Used **only when primary API server is down**.

## How it works

1. Signage APK polls `https://raw.githubusercontent.com/ivosetyadi/indomart-signage-status/main/status.json` every few minutes
2. If primary API (`https://indomart.us/signage/api/schedule.php`) is unreachable, APK checks this fallback
3. If device_id or tenant_id in blocklist → wipe playlist cache

## Payload

`status.json`:
```json
{
  "version": 1,
  "updated_at": "2026-07-09T00:00:00Z",
  "blocked_devices": [42, 87],
  "blocked_tenants": [3]
}
```

**Note:** IDs are opaque integers — no personally identifiable info is exposed.

## Update flow

To takedown a device when primary server is down:

```bash
# Edit status.json: add device ID to blocked_devices
# Then:
git commit -am "block device 42"
git push
# CDN propagation: ~30 seconds via GitHub raw
```

## Recovery

Remove ID from blocklist + commit + push. Device will resume on next successful primary API fetch (which restores its manifest from server).

## Security

- Payload is public — do not embed sensitive data
- Only opaque numeric IDs
- No secrets in this repo
