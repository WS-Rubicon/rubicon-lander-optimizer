# Discovery Report - Rubicon Lander Optimizer

**Date**: 2026-02-02
**Phase**: 0 (Discovery)
**Status**: Complete

---

## 1. Available MCP Tools

### 1.1 Landing Page Generation (Presales MCP)

| Tool | Description | Relevance |
|------|-------------|-----------|
| `presales_generate` | Generate AI landing pages (editorial/quiz/offer) from natural language brief. Supports 7 verticals, 1-10 variants per style. | **CORE** - Primary variation engine. Can generate multiple variants per call. |
| `presales_preview_copy` | Preview AI-generated copy as JSON before rendering HTML. | **HIGH** - Use to inspect/tag components before committing to full generation. |
| `presales_list_templates` | List available template styles and supported verticals. | **MEDIUM** - Reference for what's available. |
| `presales_check_compliance` | Validate copy against vertical-specific compliance rules. Returns score 0-100. | **HIGH** - Pre-deploy validation gate. |
| `presales_ftp_upload` | Upload HTML files to FTP. Auto-generates campaign IDs. | **CORE** - Primary deployment mechanism (FTP, not GitHub/Netlify). |
| `presales_upload_asset` | Upload individual assets (author images, etc.) to FTP. | **LOW** - For supplementary assets. |
| `presales_list_deployments` | List all deployed landers from local registry. | **HIGH** - Track what's live. |
| `presales_register_landers` | Register uploaded landers with RedTrack. | **HIGH** - Bridge between deploy and tracking. |
| `presales_register_redtrack` | Register a single lander URL with RedTrack. | **HIGH** - Individual lander registration. |
| `presales_create_campaign` | Generate + deploy + register in one call. | **CORE** - All-in-one workflow for new landers. |

**Key Finding**: The presales system generates 3 styles (editorial, quiz, offer) and supports `num_variants` up to 10 per style. This is the variation engine - we don't need to build one from scratch.

### 1.2 RedTrack (Tracking & Performance)

| Tool | Description | Relevance |
|------|-------------|-----------|
| `rt_get_stats` | Pull clicks, conversions, revenue, cost, profit, ROI, EPC, CPA, CR. Supports grouping by campaign, sub1-sub3, country, etc. | **CORE** - Primary performance data source. |
| `rt_create_campaign_v2` | Create tracking campaign with offer/lander split. Supports landing_ids with weights. | **CORE** - Traffic splitting across lander variants. |
| `rt_setup_campaign` | One-call campaign setup: creates offers + campaign + tracking URL. | **HIGH** - Initial campaign setup. |
| `rt_update_campaign` | Update campaign settings (status, cost model, name). | **HIGH** - Pause/activate campaigns between cycles. |
| `rt_create_lander` | Register a landing page in RedTrack (name + URL). Returns lander ID. | **CORE** - Register each variant for tracking. |
| `rt_list_landings` | List all registered landing pages. | **MEDIUM** - Audit/reference. |
| `rt_list_sources` | List traffic sources (get source_id for campaign creation). | **SETUP** - One-time reference. |
| `rt_list_domains` | List tracking domains (get domain_id for campaign creation). | **SETUP** - One-time reference. |
| `rt_get_offers` | List all offers. | **MEDIUM** - Reference for campaign setup. |
| `rt_get_campaigns` | List all campaigns. | **MEDIUM** - Audit existing campaigns. |
| `rt_get_campaign_url` | Get click tracking URL for a campaign. | **HIGH** - Used in lander CTAs. |
| `rt_lookup_offer` | Check if offer exists (avoid duplicates). | **MEDIUM** - Dedup check. |

**Key Finding**: RedTrack supports `landing_ids` with `landing_weights` on campaign creation. This means we can do 33/33/34 traffic splits natively in RedTrack - no external A/B test tool needed.

### 1.3 Image Generation (Gemini MCP)

| Tool | Description | Relevance |
|------|-------------|-----------|
| `gemini_generate_image` | Single image from text prompt. Supports aspect ratios, styles, verticals. | **MEDIUM** - Hero image generation for variants. |
| `gemini_batch_generate` | Multiple images from prompt list. | **HIGH** - Batch hero images for all variants. |
| `gemini_generate_from_template` | Pre-built templates (mortgage_homeowner, solar_installation, etc.). | **HIGH** - Consistent, proven image styles. |
| `gemini_variations` | Create subtle/moderate/significant variations of an existing image. | **HIGH** - Test image impact without changing copy. |
| `gemini_edit_image` | Edit existing image with text prompt. | **MEDIUM** - Targeted image adjustments. |
| `gemini_get_info` | Check Gemini API status. | **LOW** - Health check. |

**Key Finding**: `gemini_variations` with strength control (subtle/moderate/significant) is perfect for isolating image impact in A/B tests.

### 1.4 Telegram (Notifications & Human Input)

| Tool | Description | Relevance |
|------|-------------|-----------|
| `tg_send_message` | Send message to a chat. Supports markdown and HTML formatting. | **CORE** - Cycle notifications to Wesley. |
| `tg_reply_to_message` | Reply to a specific message. | **HIGH** - Threaded conversation for grading. |
| `tg_get_messages` | Get messages from a chat with pagination. | **HIGH** - Read Wesley's grading responses. |
| `tg_get_history` | Get complete chat history. | **MEDIUM** - Pull grading history. |
| `tg_edit_message` | Edit a sent message. | **MEDIUM** - Update cycle status messages. |
| `tg_list_chats` | List available chats. | **SETUP** - Find Wesley's chat ID. |
| `tg_search_messages` | Search messages. | **LOW** - Search past grades. |
| `tg_create_poll` | Create a poll in chat. | **HIGH** - Could use for component attribution voting. |

**Key Finding**: Telegram has full bidirectional support. We can send formatted cycle reports, create polls for grading, and read responses programmatically.

### 1.5 Compliance & Validation

| Tool | Description | Relevance |
|------|-------------|-----------|
| `presales_check_compliance` | Check headline + body copy against vertical rules. | **CORE** - Pre-deploy gate. |
| `dsa_check_compliance` | Check DSA blueprint compliance. | **LOW** - Different system. |

### 1.6 Deployment Infrastructure

| Tool | Description | Relevance |
|------|-------------|-----------|
| `presales_ftp_upload` | FTP upload with campaign ID generation. | **CORE** - Primary deploy method. |
| `trigger_render_deploy` | Trigger Render.com deployment (for RubiconBot). | **NONE** - Wrong target. |
| `get_render_deploy_status` | Check Render deploy status. | **NONE** - Wrong target. |
| `dsa_deploy_pages` | Deploy DSA pages to FTP/S3. | **LOW** - Different system. |

### 1.7 System Health & Diagnostics

| Tool | Description | Relevance |
|------|-------------|-----------|
| `system_health_check` | Full system diagnostic. | **MEDIUM** - Pre-cycle health check. |
| `check_environment` | Validate environment variables. | **SETUP** - Verify config. |
| `check_storage` | Check storage health. | **LOW** - Monitoring. |
| `check_mcp_tools` | Check MCP tool loading status. | **LOW** - Debugging. |

### 1.8 Slack (Secondary Notifications)

| Tool | Description | Relevance |
|------|-------------|-----------|
| `slack_send_message` | Post to Slack channel. | **LOW** - Backup notification channel. |
| `slack_send_dm` | Send direct message. | **LOW** - Backup notification channel. |

---

## 2. Gaps Identified

### 2.1 No GitHub/Netlify Integration
**Impact**: HIGH
**Spec says**: Deploy via GitHub branches -> Netlify auto-deploy with unique URLs per variant.
**Reality**: No GitHub MCP tools available. No Netlify tools available.
**Resolution**: **Use FTP deployment instead.** The `presales_ftp_upload` tool already generates unique URLs per lander. Each variant gets its own URL on the FTP server. This is simpler and already battle-tested by the Taboola campaign launcher.

### 2.2 No Cron/Scheduling Tool
**Impact**: HIGH
**Spec says**: 6-hour cron cycle for automated optimization.
**Reality**: No built-in scheduler in MCP tools.
**Resolution Options**:
1. **System cron** - Use OS-level crontab to trigger Claude Code sessions
2. **Manual trigger** - Wesley says "run optimization cycle" in Claude Code
3. **Telegram trigger** - Wesley sends a message, bot picks it up
4. **External scheduler** - Use Render.com cron or similar

**Recommendation**: Start with manual trigger (Phase 1 MVP), add cron automation in Phase 2.

### 2.3 No Persistent Database
**Impact**: MEDIUM
**Spec says**: Learning database after 20+ cycles.
**Reality**: No database MCP tool for this project.
**Resolution**: Use JSON files in `/data/` directory. Sufficient for MVP and first 50+ cycles. Migrate to SQLite or Postgres if/when needed.

### 2.4 No Native A/B Test Statistical Engine
**Impact**: MEDIUM
**Spec says**: Statistical significance check before declaring winner.
**Reality**: No stats tool available.
**Resolution**: Build simple significance calculator in the engine. Chi-squared test for CVR comparison with p<0.05 threshold. Can be done in pure logic (no external library needed).

### 2.5 Presales Does Not Support Ecommerce Vertical
**Impact**: HIGH
**Spec says**: Use `presales_generate` to create landing page variations.
**Reality**: Presales supports 7 verticals (medicare, solar, mortgage, auto, debt, aca, mva). No ecommerce support.
**Resolution**: Generate ecommerce landing pages as standalone HTML directly. Use the base template with manual HTML generation. Deploy via `presales_ftp_upload` (FTP upload works regardless of vertical). Champion page is on Framer (`landing.olenis.com/703/01`), challengers will be static HTML on FTP.

### 2.6 No Component-Level Attribution in RedTrack
**Impact**: MEDIUM
**Spec says**: Track which specific component (headline, CTA, image) drove the win.
**Reality**: RedTrack tracks at the lander level, not component level.
**Resolution**: Use the schema tagging system. Each variant changes 1-3 components with tags. When variant A wins over variant B, the differing components get credit. This is the core learning mechanism.

---

## 3. Architecture Recommendation

### Revised Deployment Flow (FTP-Based)

```
[Variation Engine]
    -> presales_preview_copy (inspect copy, tag components)
    -> presales_generate (generate HTML variants)
    -> Inject RedTrack script validation
    -> presales_ftp_upload (deploy to unique URLs)
    -> presales_register_redtrack (register with RT)

[RedTrack Campaign Setup]
    -> rt_create_campaign_v2 with landing_ids + landing_weights
    -> 33/33/34 split across 3 variant URLs

[6-Hour Cycle (Manual Trigger for MVP)]
    -> rt_get_stats with group_by to get per-lander metrics
    -> Statistical significance check
    -> Identify winner
    -> tg_send_message to Wesley with results + grading prompt
    -> tg_get_messages to read grading response
    -> Archive losers, promote winner to champion
    -> Generate 2 new challengers
    -> Deploy + register + update RT campaign
    -> Loop
```

### Why FTP > GitHub/Netlify for This Use Case

1. **Already integrated** - presales_ftp_upload works today
2. **Unique URLs per variant** - Built-in campaign ID generation
3. **Faster deployment** - Direct upload vs git push -> build -> deploy
4. **RedTrack registration** - Seamless with presales_register_redtrack
5. **No branch management** - Simpler operational model

### Data Storage Strategy

```
/data/
  cycles.json          # All cycle records with results
  champions.json       # Current champion per vertical
  component-wins.json  # Win/loss record per component tag
  human-grades.json    # Wesley's attribution grades
  patterns.json        # Learned patterns (after 20+ cycles)
```

### Minimum Viable Cycle

1. **Input**: Vertical + current champion schema (or fresh brief)
2. **Generate**: 3 variants via `presales_generate` (champion + 2 challengers)
3. **Validate**: RedTrack script check + `presales_check_compliance`
4. **Deploy**: `presales_ftp_upload` -> unique URLs
5. **Track**: `rt_create_campaign_v2` with 33/33/34 split
6. **Wait**: 6 hours or 100+ clicks per variant
7. **Measure**: `rt_get_stats` grouped by lander
8. **Grade**: Telegram notification + human attribution
9. **Learn**: Update component-wins.json + patterns.json
10. **Iterate**: Winner becomes champion, generate 2 new challengers

---

## 4. Tool Inventory Summary

| Category | Tools Available | Tools Needed | Gap? |
|----------|----------------|--------------|------|
| Lander Generation | 10 (presales_*) | Covered | No |
| Performance Tracking | 12 (rt_*) | Covered | No |
| Image Generation | 6 (gemini_*) | Covered | No |
| Notifications | 8+ (tg_*) | Covered | No |
| Compliance | 2 | Covered | No |
| Deployment | 1 (FTP) | Covered | No (revised from GitHub/Netlify) |
| Scheduling | 0 | 1 | Yes - use manual trigger for MVP |
| Database | 0 | 1 | Yes - use JSON files |
| Statistics | 0 | 1 | Yes - build simple chi-squared |
| A/B Split | 0 native | RT landing_weights | No (RedTrack handles it) |

**Total MCP tools relevant to this project: 39**
**Gaps requiring custom code: 2** (statistical engine, JSON data layer)
**Gaps deferred to Phase 2: 1** (automated scheduling)

---

## 5. Recommended Phase 1 Scope

1. Pick highest-traffic vertical (likely **mortgage** or **solar**)
2. Create champion schema from existing best performer
3. Build variation engine that uses `presales_generate` with tagged component changes
4. Pre-deploy validator (RedTrack script + compliance check)
5. FTP deploy + RedTrack registration + campaign split
6. Manual "run cycle" command that pulls stats, identifies winner, notifies via Telegram
7. JSON-based grading storage
8. Simple win/loss tracking per component tag

**Not in Phase 1**: Automated cron, pattern learning, autonomous variation suggestions, image A/B testing.

---

## 6. Key IDs for Setup

| Resource | ID | Source |
|----------|-----|--------|
| Taboola Source (RedTrack) | `6532d0d363d1da0001eb7642` | CLAUDE.md |
| Tracking Domain | `65c3f42c7260ea000164821c` | CLAUDE.md |
| Default Offer URL | `https://hpkpn.ttrk.io/click` | CLAUDE.md |
| RedTrack Script | `//hpkpn.ttrk.io/track.js` | Project spec |

**Resolved during setup**:
- Wesley's Telegram chat_id: `1291782615`
- RT Campaign ID (Peptide Eye Patches): `6979c0adcad13a4d8c3cbb94`
- RT Offer ID (Peptide Eye Patches): `6913159915212444f6558b9e`
- Offer URL: `https://landing.olenis.com/703/01`
- Traffic Source: "Wesley - Ecomm - Facebook"
- Current champion CVR: 3.53% (baseline from 1,953 clicks)
