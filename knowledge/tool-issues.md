# Tool Issues & Workarounds

## Active Issues

### 1. MCP `rt_get_stats` group_by=landing doesn't split by lander
**Discovered:** 2026-02-03
**Frequency:** Every time
**Impact:** Cannot get per-lander performance breakdown via MCP tool
**Workaround:** Query RedTrack API directly via curl:
```
curl -s "https://api.redtrack.io/report?api_key=API_KEY&date_from=YYYY-MM-DD&date_to=YYYY-MM-DD&group=landing&campaigns=CAMPAIGN_ID"
```
**Status:** Open — MCP tool returns single aggregated row regardless of group_by value.

### 2. MCP `rt_update_campaign` cannot modify lander streams
**Discovered:** 2026-02-03 (Session 2)
**Frequency:** Every time
**Impact:** Cannot add/remove landers from existing RT campaigns via MCP
**Workaround:** Use RedTrack API directly via curl PUT:
```
curl -s -X PUT "https://api.redtrack.io/campaigns/CAMPAIGN_ID?api_key=API_KEY" \
  -H "Content-Type: application/json" -d @payload.json
```
**Status:** Open — MCP tool only supports status/cost/name changes.

### 3. Netlify CLI not available (no Node.js in WSL)
**Discovered:** 2026-02-03 (Session 2)
**Frequency:** Persistent
**Impact:** Cannot use `netlify deploy` CLI command
**Workaround:** Deploy via Netlify REST API:
1. SHA1 hash all files
2. POST to /api/v1/sites/{SITE_ID}/deploys with file manifest
3. PUT each required file to /api/v1/deploys/{DEPLOY_ID}/files/{path}
**Status:** Open — would need Node.js installed in WSL to resolve.

### 4. Champion page on Framer — cannot extract content or install tracking
**Discovered:** 2026-02-03 (Session 1)
**Frequency:** Persistent
**Impact:** Cannot install RT track.js on champion page. LP views = 0 for champion. Cannot fully populate champion schema (many null fields).
**Workaround:** None for tracking. Champion schema has partial data based on what could be extracted from page title/URL.
**Status:** Open — requires Framer page editor access.
