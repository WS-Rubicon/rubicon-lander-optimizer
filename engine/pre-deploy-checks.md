# Pre-Deploy Validation Checks

Every lander MUST pass ALL blocker checks before deployment. Warning checks are logged but don't block.

## BLOCKER Checks

### 1. RedTrack Script Present
```
CHECK: HTML contains '//hpkpn.ttrk.io/track.js'
FAIL: "DEPLOY BLOCKED: RedTrack script missing from [lander_id]"
```

### 2. Valid Schema
```
CHECK: Lander schema has all required fields:
  - lander_id (non-empty string)
  - vertical (valid vertical from config)
  - components.headline.text (non-empty)
  - components.cta.text OR components.form.cta_text (non-empty)
FAIL: "DEPLOY BLOCKED: Invalid lander schema - missing [field]"
```

### 3. Form Action Set
```
CHECK: Offer URL / form action is set to valid tracking URL
FAIL: "DEPLOY BLOCKED: Form has no action URL"
```

### 4. Compliance Pass
```
CHECK: presales_check_compliance returns passed=true
NOTE: For ecommerce vertical (presales not supported), skip this check.
      Instead, manually review for: false health claims, misleading before/after,
      unsubstantiated product claims, missing disclaimers.
FAIL: "DEPLOY BLOCKED: Compliance check failed - [issues]"
```

## WARNING Checks

### 5. Components Tagged
```
CHECK: Every modified component has variant_tags
WARN: "Untagged components - cannot attribute results for [component]"
```

### 6. Variant Diff Count
```
CHECK: Challenger changes 1-3 components vs champion (not more)
WARN: "Challenger changes [N] components - attribution will be unclear"
```

## Execution

Before calling `presales_ftp_upload`, run checks in this order:
1. Read generated HTML file
2. Check for RedTrack script
3. Validate schema JSON
4. Run `presales_check_compliance` on headline + body
5. Verify offer URL
6. Check component tagging
7. Count diffs vs champion

If any BLOCKER fails: stop, log error, notify via Telegram, do not deploy.
If only WARNINGs: deploy but log warnings in cycle record.
