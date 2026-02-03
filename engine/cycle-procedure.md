# Optimization Cycle Procedure

## Trigger
Manual: "Run optimization cycle for [vertical]"

## Step-by-Step

### STEP 1: Load State
```
READ: data/champions.json -> current champion for this vertical
READ: data/cycles.json -> last cycle info
READ: config/verticals.json -> vertical config
READ: config/redtrack-campaigns.json -> active RT campaign ID
```

If no champion exists, this is a **fresh start** - skip to STEP 3a.
If champion exists, go to STEP 2.

### STEP 2: Pull Performance Data (Existing Cycle)
```
TOOL: rt_get_stats
  date_from: [cycle_start_date]
  date_to: [now]
  campaign_id: [active_rt_campaign_id]
  group_by: "sub1"  # sub1 = lander variant ID

EXTRACT per variant:
  - clicks
  - conversions
  - cvr (conversions / clicks * 100)
  - revenue
  - epc (revenue / clicks)
```

Check minimum thresholds:
- Each variant needs >= min_clicks_for_significance (default 100)
- OR >= min_hours_before_decision (default 48) have elapsed
- If neither met: "Cycle not ready. [X] clicks across [Y] hours. Need [Z] more clicks or [W] more hours."

### STEP 3: Determine Winner
```
IF all variants meet minimum clicks:
  Compare CVR using chi-squared test (see stats-analysis.md)
  IF p < 0.05: declare winner
  IF p >= 0.05: declare tie, keep running or force-pick higher CVR

Record in cycle:
  - winner_id
  - winner_cvr, winner_epc
  - loser_ids with their metrics
  - statistical_significance: true/false
  - p_value
```

### STEP 3a: Fresh Start (No Champion)
```
Pick vertical's best-performing style from config

IF presales_supported == true:
  Generate initial 3 variants using presales_generate with num_variants=3
  OR generate 1 per style (editorial, quiz, offer) for style testing

IF presales_supported == false (e.g., ecommerce):
  Use existing deployed page as champion (baseline)
  Generate 2 challenger HTML pages manually:
    1. Build HTML from base template (templates/base.html)
    2. Include RedTrack script
    3. Match brand styling (colors, fonts, layout)
    4. Change 1-3 components per challenger
    5. Deploy via presales_ftp_upload
```

### STEP 4: Notify Wesley via Telegram
```
TOOL: tg_send_message
  chat_id: [wesley_chat_id]
  text: Formatted cycle report (see template below)
  parse_mode: "html"
```

**Message Template:**
```
<b>Cycle #{cycle_number} Complete - {vertical}</b>

<b>Winner:</b> {winner_id}
CVR: {cvr}% | EPC: ${epc} | Clicks: {clicks}
Significance: {yes/no} (p={p_value})

<b>Variants Tested:</b>
1. {variant_a} - CVR: {cvr}% ({clicks} clicks)
2. {variant_b} - CVR: {cvr}% ({clicks} clicks)
3. {variant_c} - CVR: {cvr}% ({clicks} clicks)

<b>Changes in winner vs others:</b>
- Headline: {old} -> {new} [tags: urgency, deadline]
- CTA: {old} -> {new} [tags: scarcity]

<b>Grade the components:</b>
Reply with the tag(s) you think drove the win:
{numbered list of differing tags}

Or reply "skip" to auto-grade.
```

### STEP 5: Wait for Grading (or Auto-Grade)
```
TOOL: tg_get_messages (check for reply)

IF Wesley replies with tag numbers:
  Record in data/human-grades.json
IF Wesley replies "skip":
  Auto-grade: credit all differing tags equally
IF no reply after 1 hour:
  Auto-grade on next cycle trigger
```

### STEP 6: Update Learning Data
```
UPDATE: data/component-wins.json
  For each tag in winner's diff: increment wins
  For each tag in loser's diff: increment losses

UPDATE: data/champions.json
  Set winner as new champion for this vertical

UPDATE: data/cycles.json
  Append full cycle record
```

### STEP 7: Generate New Challengers
```
ANALYZE: component-wins.json for high-win-rate tags not recently tested
ANALYZE: vertical config for untested angles

GENERATE 2 challengers that each change 1-3 components:
  Challenger A: Test the highest-confidence untested pattern
  Challenger B: Test a novel combination or underexplored tag

For each challenger:
  IF presales_supported:
    1. Build brief incorporating target changes
    2. TOOL: presales_preview_copy -> review
    3. TOOL: presales_generate -> HTML
    4. Run pre-deploy-checks.md
    5. TOOL: presales_ftp_upload -> deploy
    6. TOOL: presales_register_redtrack -> get RT lander ID

  IF NOT presales_supported (ecommerce):
    1. Build brief incorporating target changes
    2. Generate HTML page matching brand style
       - Use champion schema as reference for structure
       - Match brand colors, fonts, layout
       - Change target components (1-3 max)
    3. Tag all changed components in schema
    4. Run pre-deploy-checks.md (skip compliance check)
    5. TOOL: presales_ftp_upload -> deploy
    6. TOOL: rt_create_lander -> register with RT
```

### STEP 8: Update RedTrack Campaign
```
Option A (update existing campaign):
  TOOL: rt_update_campaign to swap lander IDs

Option B (create new campaign):
  TOOL: rt_create_campaign_v2
    landing_ids: "champion_id,challenger_a_id,challenger_b_id"
    landing_weights: "34,33,33"

Save new campaign ID to config/redtrack-campaigns.json
```

### STEP 9: Log & Confirm
```
UPDATE: data/cycles.json with new cycle start

TOOL: tg_send_message
  "New cycle started for {vertical}:
   Champion: {champion_id} (34%)
   Challenger A: {id} - testing {tags} (33%)
   Challenger B: {id} - testing {tags} (33%)

   Next check: {6 hours from now}"
```

## Error Handling

| Error | Action |
|-------|--------|
| presales_generate fails | Retry once, then notify Wesley |
| FTP upload fails | Save files locally, notify Wesley with manual upload instructions |
| RT API fails | Save state, retry next cycle |
| Compliance check fails | Regenerate with adjusted brief, max 2 retries |
| All variants have 0 conversions after 48h | Pause campaign, notify Wesley, flag for creative review |
