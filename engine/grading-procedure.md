# Human Grading Procedure

## Purpose
Human attribution helps the system learn which components actually drive conversions,
rather than just which full pages win. This builds the component-level intelligence.

## Telegram Grading Flow

### 1. Cycle Complete Notification
After determining a winner, send formatted results to Wesley via `tg_send_message`.

Include:
- Winner ID and metrics
- All variant metrics for comparison
- What components differed between winner and losers
- Numbered list of component tags for easy reply

### 2. Grading Prompt Format
```
Grade this cycle:

Winner changed these vs the losers:
1. headline_urgency (added deadline to headline)
2. cta_scarcity (changed CTA to "Claim Before Slots Fill")
3. image_house_aerial (switched to aerial home photo)

Which drove the win? Reply with numbers (e.g., "1,2")
Or reply:
- "all" = all changes contributed equally
- "skip" = auto-grade (equal credit to all)
- "none" = win was random/not attributable
```

### 3. Processing Responses

| Response | Action |
|----------|--------|
| "1,2" | Credit tags 1 and 2 as wins, tag 3 as neutral |
| "all" | Credit all tags as wins |
| "skip" | Credit all tags as wins (same as "all") |
| "none" | Record as tie/no-attribution, don't update component-wins |
| "1" | Credit tag 1 as win, tags 2 and 3 as neutral |
| No reply (1hr+) | Auto-grade as "all" on next cycle |

### 4. Confidence Levels

Ask Wesley for confidence after tag selection:
```
Confidence? (h)igh / (m)edium / (l)ow
```

Default to "medium" if no reply.

### 5. Recording Grades

Write to `data/human-grades.json`:
```json
{
  "cycle_id": "cycle-003",
  "winner": "solar-v47-a",
  "human_attribution": {
    "winning_components": ["headline_urgency", "cta_scarcity"],
    "neutral_components": ["image_house_aerial"],
    "confidence": "high",
    "notes": ""
  },
  "graded_at": "2026-02-02T15:30:00Z",
  "graded_by": "wesley"
}
```

### 6. Updating Component Wins

After grading:
```
For each tag in winning_components:
  component-wins[tag].wins += 1
  component-wins[tag].total_tests += 1

For each tag in neutral_components:
  component-wins[tag].ties += 1
  component-wins[tag].total_tests += 1

For each tag unique to LOSING variants:
  component-wins[tag].losses += 1
  component-wins[tag].total_tests += 1

Recalculate win_rates for all affected tags.
```

### 7. Confidence Aggregation

After 10+ tests of a tag:
- Win rate > 60%: confidence = "high"
- Win rate 40-60%: confidence = "medium"
- Win rate < 40%: confidence = "low" (consider avoiding this tag)

## Alternative: Telegram Poll (Phase 2)

Use `tg_create_poll` for structured grading:
```
TOOL: tg_create_poll
  question: "Cycle #3: What drove the win?"
  options: ["headline_urgency", "cta_scarcity", "image_aerial", "all equally", "none/random"]
```

More structured but less flexible for notes.
