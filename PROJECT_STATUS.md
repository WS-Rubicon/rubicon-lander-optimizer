# Rubicon Lander Optimizer — Project Status

**Last Updated:** 2026-03-31
**Last Session (Timeshare):** **ts-cycle-001 started.** First timeshare lander optimization cycle. No prior champion — both landers are new. Editorial V2 (VSL + refi learnings: institutional header, blue CTA, cycle scarcity, 10min timer, loss-aversion body, autoplay video) vs Quiz V1 (RF3D-AA clone: 3-question eligibility screening, answer-echo headline, 10min timer). 50/50 split on RT campaign `68e374a8246ae5e16b8347ea`. Quiz questions designed to NOT overlap with offer page (gettimesharefree.com). Deployed to clearpathbenefitstoday.com via Netlify auto-deploy from WS-Rubicon/campaigns repo.
**Last Session (Refi):** **Refi cycle-008 completed (early cut), cycle-009 started.** AA retained champion (6.93% CVR today, 8.08% baseline). AM lost: 5.02% CVR, 219 clicks, 11 conv, $1.21 EPC, -$52 loss (card_title_question_dollar + processing_personalized_steps). AN lost: 6.20% CVR, 258 clicks, 16 conv, $1.49 EPC, $32 profit (alert_positive_funding + timer_above_cta). None statistically significant (all p>0.05). AM 28% below AA = probable loser. AN 11% below AA, 23% below baseline. Cycle-009 started: AA champion (34%) vs AR (fullscreen step flow / Rocket Mortgage UX + dashboard panels, 33%) vs AS (official determination notice / document format + bordered amount box, 33%). First architecture-level tests for refi.
**Last Session (Ecom):** **Cycle-011 scrapped, cycle-012 started.** Cycle-011 was compromised: user cleared all landers from RT rotation mid-test. AA retained as champion with lifetime 2.19% CVR / $0.65 EPC over 1612 clicks. Cycle-012: AA champion (34%) vs AE (concern-specific Q3 + personalized offer subtitle, 33%) vs AF (outcome hero headline + timer-in-CTA, 33%).

---

## What We're Building

A **Claude-orchestrated A/B testing system for landing pages**. It runs optimization cycles where a "champion" lander competes against "challenger" variants. Traffic is split via RedTrack, performance is measured by CVR/EPC, and winners are promoted using chi-squared statistical testing.

**Current vertical:** Ecommerce (Olenis "Bright Eyes" Peptide Eye Patches)
**Traffic source:** Facebook via RedTrack
**Deployment:** Challengers on Netlify (firstlightrituals.com), champion on Framer (landing.olenis.com)

---

## Current Status

### What's Working

- **RedTrack campaign is live** with 6 landers (cycle-002) and ~17% traffic split each
  - Campaign ID: `6979c0adcad13a4d8c3cbb94`
  - Trackback URL: `https://y.getmorelocalhelp.com/6979c0adcad13a4d8c3cbb94?sub1={{ad.id}}&sub2={{adset.id}}&sub3={{campaign.id}}&sub4={{ad.name}}&sub5={{adset.name}}&sub6={{campaign.name}}&sub7={{placement}}&sub8={{site_source_name}}&utm_source=facebook&utm_medium=paid`
- **6 landers registered in RedTrack and attached to the campaign (cycle-002):**
  - Peptides_L4 (Challenger C - Ingredient Science): `69827f5dc697e6db7e4e88e8` → https://firstlightrituals.com/peptide-patches/challenger-c.html
  - Peptides_L5 (Challenger D - 30-Day Review): `69827f5ec697e6db7e4e88ec` → https://firstlightrituals.com/peptide-patches/challenger-d.html
  - Peptides_L6 (Challenger E - Price Comparison): `69827f5ec697e6db7e4e88ed` → https://firstlightrituals.com/peptide-patches/challenger-e.html
  - Peptides_L7 (Challenger F - Viral TikTok FOMO): `6985b882715248442a337eff` → https://firstlightrituals.com/peptide-patches/challenger-f.html
  - Peptides_L8 (Challenger G - Before/After): `6985b882715248442a337f00` → https://firstlightrituals.com/peptide-patches/challenger-g.html
  - Peptides_L9 (Challenger H - Problem-Agitate-Solve): `6985b883715248442a337f17` → https://firstlightrituals.com/peptide-patches/challenger-h.html
- **All 6 landers deployed on Netlify** (site: `olenis-landers`, domain: `firstlightrituals.com`)
- **CTA tracking fixed** — all CTAs route through `landing.olenis.com/703/01` (RT offer URL) instead of directly to Shopify
- **All landers have RT track.js** + clickid passthrough JS
- **Each challenger changes only 2 components** (headline + body_sections) for clean attribution
- **Cycle-001 retired** — 0 conversions across all 3 landers after 114 clicks ($168 spent). Root causes logged in `knowledge/creative-patterns.md`

### What's Not Working / Not Built Yet

- **No automated cycle runner** — the engine docs describe the procedure but there's no script that auto-pulls stats, determines winners, or triggers new cycles. It's all manual via Claude sessions.
- **No Telegram grading integration** — the grading procedure is documented but not wired up
- **Mortgage and Solar verticals** — directory structure exists but no champion/challenger content
- **No automated stats pulling** — need to manually run `rt_get_stats` to check performance
- **Pattern detection** — needs 20+ completed cycles before it activates (currently 0 completed)
- **Component-level attribution** — `data/component-wins.json` and `data/human-grades.json` are empty

---

## All Project Files

### Config Files

| File | Description |
|------|-------------|
| `config/redtrack-campaigns.json` | Central config: RT campaign ID, offer ID, lander IDs, baseline metrics, source info |
| `config/verticals.json` | Vertical definitions (ecommerce, solar, mortgage). Defines significance thresholds, CVR targets, product info |

### Data Files (State)

| File | Description |
|------|-------------|
| `data/cycles.json` | Tracks all optimization cycles. Cycle-001 is "running" with champion + 2 challengers |
| `data/champions.json` | Current champion per vertical. Ecommerce champion: CVR 3.53%, EPC $0.81 |
| `data/component-wins.json` | Empty — will track which page components (headline, CTA, etc.) drive wins |
| `data/patterns.json` | Empty — will detect patterns after 20+ cycles |
| `data/human-grades.json` | Empty — will store Telegram-sourced human grades for component attribution |

### Engine Documentation

| File | Description |
|------|-------------|
| `engine/README.md` | Overview of the optimization engine architecture |
| `engine/cycle-procedure.md` | 9-step cycle procedure: Load State → Pull Stats → Determine Winner → Fresh Start → Telegram Notify → Wait for Grading → Update Learning → Generate Challengers → Update RT Campaign |
| `engine/stats-analysis.md` | Chi-squared test methodology. X² > 3.841 = p<0.05. Min 50 clicks, preferred 100, ideal 200+ per variant |
| `engine/variation-strategy.md` | How to generate challenger variants: change 1-2 components at a time, tag everything |
| `engine/grading-procedure.md` | How human grading works via Telegram for component attribution |
| `engine/pre-deploy-checks.md` | Checklist before deploying challengers (RT script, clickid passthrough, mobile responsive, etc.) |

### Lander Files — Ecommerce

| File | Description |
|------|-------------|
| `landers/ecommerce/champion/schema.json` | Champion schema: "7 Reasons Why..." listicle on Framer. Tags: listicle_number, comparison, social_proof |
| `landers/ecommerce/challengers/challenger-a-schema.json` | Challenger A schema: nutra-style editorial about ancient Korean manuscripts. Tags: discovery_narrative, ancient_origin, authority, nutra_editorial. **4 components changed vs champion** |
| `landers/ecommerce/challengers/challenger-b-schema.json` | Challenger B schema: scarcity/social proof angle. Tags: scarcity, purchase_count, urgency. **2 components changed vs champion** |

### Deployed Lander HTML (Netlify — firstlightrituals.com)

| File | Description |
|------|-------------|
| `olenis-landers/index.html` | Root index page |
| `olenis-landers/peptide-patches/challenger-a.html` | **Nutra editorial lander.** "The Skin Chronicle" publication format. Story: historian discovers Joseon Dynasty manuscripts describing peptide eye restoration. Serif fonts, pull quotes, expert bylines, historical timeline comparison. Hero image: AI-generated Korean woman applying gold eye patches. CTA: "Shop Olenis Bright Eyes — $23" |
| `olenis-landers/peptide-patches/challenger-b.html` | **Scarcity/social proof lander.** "Sold Out 3 Times" headline. Urgency banner, social proof counters (47K+ customers, 4.8 stars, 3x sold out), reason blocks. Coral/red CTA: "Get Yours Before They Sell Out Again" |
| `olenis-landers/peptide-patches/images/korean-hero.png` | AI-generated hero image: Korean woman in 30s, applying gold eye patches, luxurious setting, warm editorial tones (638KB) |
| `olenis-landers/.netlify/state.json` | Netlify site ID: `3e45fc10-b4a3-4921-b9cd-4373c02f12b4` |
| `olenis-landers/.netlify/netlify.toml` | Netlify build config |

### Solar Vertical (Placeholder)

| File | Description |
|------|-------------|
| `landers/solar/champion/schema.json` | Solar champion schema placeholder — no active campaign |

### Templates

| File | Description |
|------|-------------|
| `templates/base.html` | Base HTML template for generating new landers |

### Documentation

| File | Description |
|------|-------------|
| `docs/discovery-report.md` | Initial discovery/research report for the project |

---

## Key IDs Reference

### RedTrack

| Resource | ID |
|----------|-----|
| RT Campaign (Peptides A/B test) | `6979c0adcad13a4d8c3cbb94` |
| RT Offer (Peptide Eye Patches) | `6913159915212444f6558b9e` |
| RT Source (Wesley - Ecomm - Facebook) | `69130f6cc135783644ffee46` |
| RT Domain | `65c3f42c7260ea000164821c` |
| Peptides_L4 (Challenger C - Ingredient Science) | `69827f5dc697e6db7e4e88e8` |
| Peptides_L5 (Challenger D - 30-Day Review) | `69827f5ec697e6db7e4e88ec` |
| Peptides_L6 (Challenger E - Price Comparison) | `69827f5ec697e6db7e4e88ed` |
| Peptides_L7 (Challenger F - Viral TikTok FOMO) | `6985b882715248442a337eff` |
| Peptides_L8 (Challenger G - Before/After) | `6985b882715248442a337f00` |
| Peptides_L9 (Challenger H - Problem-Agitate-Solve) | `6985b883715248442a337f17` |
| **Peptides_L10 (Challenger M - Quiz No Popups)** | `69aa7a783e246949aa0e3dd7` |
| **Peptides_L11 (Challenger N - Quiz 1 Question)** | `69aa7a7a3e246949aa0e3ddf` |
| **Peptides_L12 (Challenger O - Urgency Offer)** | `69ab805f516b7da137d01bf5` |
| **Peptides_L13 (Challenger P - Pain Quiz)** | `69ab8065263ab1fa61ba6ae0` |
| **Peptides_L14 (Challenger Q - Dual Scarcity)** | `69ad41de7e0c33894d182a2e` (retired cycle-005) |
| **Peptides_L15 (Challenger R - Pain Quiz + Urgency)** | `69ad4202bed5e3cc060e38e4` (retired cycle-005) |
| **Peptides_L16 (Challenger S - Video Swap)** | `69ae8c2d516b7da1377bada3` |
| **Peptides_L17 (Challenger T - Science Headline + Batch)** | `69ae8c3b263ab1fa616336d6` |
| **Peptides_L18 (Challenger U - Readable Analyzer + Transactional CTA)** | `69afb9ec...` (retired cycle-007) |
| **Peptides_L19 (Challenger V - Competitive Ranking + Guarantee Badge)** | `69afb9ee...` (retired cycle-007) |
| **Peptides_L20 (Challenger W - Featured Product Card + Coral CTA)** | `69b0f5cccd8173f43000f234` |
| **Peptides_L21 (Challenger X - Review Snippet + Daily Count)** | `69b0f5c4cd8173f43000f05e` (retired cycle-008) |
| **Peptides_L22 (Challenger Y - Personalized Headline + Review Snippet)** | `69b26b75cd8173f43059e433` |
| **Peptides_L23 (Challenger Z - Recency Count + Clinical Proof)** | `69b26b77cd8173f43059e81c` (retired cycle-009) |
| **Peptides_L24 (Challenger AA - Personalized Analyzer + CTA)** | `69b3b1c94d550bb2a59f1b7e` |
| **Peptides_L25 (Challenger AB - Matched Review + Live Toasts)** | `69b3b1cbe5032d87d2e035b2` |
| **Peptides_L28 (Challenger AE - Concern Q3 + Personalized Subtitle)** | `69c20d072dbecf7657196e6c` |
| **Peptides_L29 (Challenger AF - Outcome Hero + Timer CTA)** | `69c20d09eadb3df5558f2ea1` |
| ~~Peptides_L26 (Challenger AC - scrapped cycle-011)~~ | `69b7813bb9db85550292f813` |
| ~~Peptides_L27 (Challenger AD - scrapped cycle-011)~~ | `69b7813eb9db85550292f819` |
| ~~Peptides_L1 (champion, retired)~~ | `6981b203f161a8800ef4ab5a` |
| ~~Peptides_L2 (challenger A, retired)~~ | `6981b204f161a8800ef4ab5b` |
| ~~Peptides_L3 (challenger B, retired)~~ | `6981b205f161a8800ef4ab5c` |

### Netlify

| Resource | Value |
|----------|-------|
| Site ID | `3e45fc10-b4a3-4921-b9cd-4373c02f12b4` |
| Site name | `olenis-landers` |
| Custom domain | `firstlightrituals.com` |
| User ID | `6876d93f00e902d5bfe87292` |

### Other

| Resource | Value |
|----------|-------|
| Telegram chat ID | `1291782615` |
| RT tracking script | `//hpkpn.ttrk.io/track.js` |

---

## Credentials to Save Separately

**DO NOT commit these. Store in a password manager or secure notes.**

| Credential | Value | Used For |
|------------|-------|----------|
| RedTrack API Key | `[REDACTED — see password manager]` | Direct RT API calls (PUT /campaigns/{id} to update streams/landers) |
| Netlify Auth Token | `[REDACTED — see password manager]` | Netlify API deploys (POST /api/v1/sites/{id}/deploys) |

### How we used the RedTrack API key

The MCP `rt_update_campaign` tool only supports status/cost/name changes — it cannot add landers to an existing campaign. We used the API key directly via curl:

```bash
# GET campaign structure
curl -s "https://api.redtrack.io/campaigns?api_key=API_KEY&ids=CAMPAIGN_ID"

# PUT to update campaign (add landers to streams[0].stream.landings)
curl -s -X PUT "https://api.redtrack.io/campaigns/CAMPAIGN_ID?api_key=API_KEY" \
  -H "Content-Type: application/json" \
  -d @payload.json
```

### How we deployed to Netlify

**IMPORTANT: Git push does NOT deploy to Netlify.** The Netlify site is NOT connected to GitHub for auto-deploys. There are no webhooks. Pushing to GitHub only stores source code — you MUST deploy via the Netlify API separately.

Auth token location: `~/.config/netlify/config.json` → `users.{userId}.auth.token`

```bash
# 1. cd into olenis-landers/ (the publish directory)
# 2. Calculate SHA1 hashes for all files (excluding .netlify/)
# 3. POST to /api/v1/sites/{SITE_ID}/deploys with {"files": {"/path": "sha1hash"}}
# 4. Upload each "required" file via PUT /api/v1/deploys/{DEPLOY_ID}/files/{path}
# 5. Deploy auto-publishes when all required files are uploaded
```

---

## Blockers

1. **No Node.js in WSL** — Netlify CLI fails. Workaround: deploy via Netlify REST API with curl (documented above). **Git push does NOT trigger Netlify deploys — no auto-deploy is configured.**
2. **MCP `rt_update_campaign` is limited** — Cannot modify campaign streams/landers. Workaround: use RedTrack API directly with the API key (documented above).
3. **Champion page is on Framer** — Cannot extract full content via WebFetch (JS-rendered). Champion schema has partial/null component values because of this.
4. **`presales_generate` doesn't support ecommerce** — Challengers must be built as manual HTML. Can't use the presales MCP tool for this vertical.
5. **No automated stats collection** — Must manually trigger `rt_get_stats` each time.

---

## Next Steps (When You Return)

### Immediate (Check A/B Test Performance)

1. **Pull stats for the A/B test** — The test has been running since ~2026-02-03 07:00 UTC. Run:
   ```
   Use rt_get_stats with date_from="2026-02-03", date_to="CURRENT_DATE",
   campaign_id for 6979c0adcad13a4d8c3cbb94, group_by="landing"
   ```
   This will show clicks, conversions, CVR per lander.

2. **Check significance** — Per `engine/stats-analysis.md`:
   - Need minimum 100 clicks per variant (300 total)
   - Chi-squared test: X² > 3.841 means p < 0.05 (significant)
   - If a challenger beats champion by a statistically significant margin, it becomes the new champion

3. **If a winner emerges:**
   - Update `data/cycles.json` — set `winner` and `completed_at`
   - Update `data/champions.json` — promote winner
   - Update RedTrack campaign to send 100% traffic to winner (or start new cycle)
   - Update `data/component-wins.json` with which tags/components won

### Short Term

4. **Build the cycle runner** — Automate the 9-step procedure from `engine/cycle-procedure.md`. Could be a Claude skill or a script that runs periodically.

5. **Wire up Telegram grading** — Use `tg_send_message` to the chat ID `1291782615` to ask for human component attribution after each cycle completes.

6. **Generate more challenger variants** — Based on what wins in cycle-001, generate cycle-002 challengers testing different components. The variation strategy in `engine/variation-strategy.md` describes the approach.

### Medium Term

7. **Expand to Solar vertical** — `landers/solar/champion/schema.json` exists but no active campaign. Need to set up RT campaign, build challengers, deploy.

8. **Build pattern detection** — After 20+ cycles, `data/patterns.json` should start identifying which tags consistently win.

9. **Automate Netlify deploys** — Either fix Node.js in WSL or build a small deploy script using the API approach documented above.

---

## Architecture Summary

```
Facebook Ads
    ↓ (trackback URL)
RedTrack Campaign (6979c0adcad13a4d8c3cbb94)
    ↓ (~17% traffic split each — cycle-002)
    ├── Peptides_L4 → firstlightrituals.com/challenger-c (Ingredient Science)
    ├── Peptides_L5 → firstlightrituals.com/challenger-d (30-Day Review)
    ├── Peptides_L6 → firstlightrituals.com/challenger-e (Price Comparison)
    ├── Peptides_L7 → firstlightrituals.com/challenger-f (Viral TikTok FOMO)
    ├── Peptides_L8 → firstlightrituals.com/challenger-g (Before/After)
    └── Peptides_L9 → firstlightrituals.com/challenger-h (Problem-Agitate-Solve)
    ↓ (CTA click with clickid passthrough → landing.olenis.com/703/01)
RT Offer URL → Shopify Store (shop.olenis.com)
    ↓ (conversion postback)
RedTrack (tracks CVR, EPC, revenue per lander)
    ↓
Claude (pulls stats, runs chi-squared test, picks winner)
    ↓
Winner becomes champion + generate new challengers for cycle-003
```

---

## Session History

### Session 1 (previous — ran out of context)
- Explored the project, read all files
- Discovered RedTrack IDs in config were fabricated (not registered in RT)
- Registered 3 real landers in RedTrack (Peptides_L1, L2, L3)
- Started looking for the RT source ID

### Session 2 (this session — 2026-02-03)
- Found RT source "Wesley - Ecomm - Facebook" on page 3 (`69130f6cc135783644ffee46`)
- Created new RT campaign via `rt_create_campaign_v2` with landers
- User requested adding landers to the **original** campaign instead
- Used RedTrack API key directly via `PUT /campaigns/{id}` to add landers to original campaign
- Deleted the duplicate campaign
- Updated all config files with real IDs
- User requested nutra-style challenger A replacement (ancient Korean manuscript discovery angle)
- Wrote full nutra editorial HTML lander (challenger-a.html)
- Generated AI hero image (Korean woman applying gold eye patches) via Gemini
- Deployed to Netlify via API (CLI doesn't work in WSL)
- Verified everything live at firstlightrituals.com
