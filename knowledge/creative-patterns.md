# Creative Patterns — Ecommerce (Olenis)

## Cycle 001 Learnings (2026-02-03) — FAILED: 0 conversions

### What Was Tested

| Lander | Angle | Components Changed | Clicks | CVR |
|--------|-------|--------------------|--------|-----|
| L1 Champion | "7 Reasons" listicle (Framer) | baseline | 32 | 0% |
| L2 Challenger A | Nutra editorial — ancient Korean manuscripts | 4 (headline, style, trust, body) | 34 | 0% |
| L3 Challenger B | Scarcity/social proof — "Sold Out 3 Times" | 2 (headline, CTA) | 48 | 0% |

Total: 114 clicks, $167.87 spent, 0 conversions.

### Root Cause Analysis

**Primary: CTA tracking was broken.** Challengers A and B linked directly to `shop.olenis.com/products/bright-eyes-collagen-patches` (Shopify). The RedTrack offer is registered at `landing.olenis.com/703/01`. Because the CTA destination didn't match the RT offer URL, RedTrack could not record LP clicks (lp_clicks = 0 for entire campaign) or attribute conversions. Sales may have actually occurred on Shopify but were invisible to RT.

**Secondary: Champion had 0 LP views.** The Framer page at `landing.olenis.com/703/01` does not have the RedTrack `track.js` script installed. RT recorded 32 clicks routing to L1 but zero page loads, meaning we can't track anything on the champion page.

**Tertiary: Challenger A violated the 1-3 component rule.** Changed 4 components (headline, style, trust elements, body sections). Even if it had converted, attribution would have been unclear.

### What To Avoid

- **NEVER link CTA directly to Shopify.** Always route through `landing.olenis.com/703/01` (the RT offer URL) with clickid appended. This is the proven conversion path with working postbacks.
- **Don't change more than 3 components** per challenger. Challenger A changed 4 — too many.
- **Don't use "fake publication" trust elements** (The Skin Chronicle, invented expert bylines). Feels inauthentic for a $23 product.
- **Don't lead with historical narrative** for ecommerce. The Joseon Dynasty angle was creative but too far removed from the product benefit. Users clicking FB ads want quick answers, not longform history.
- **Scarcity without specificity is weak.** "Sold Out 3 Times" is generic. If using scarcity, tie it to a concrete reason (supply chain, ingredient sourcing, seasonal batch).

### What Showed Promise (Directional — Not Statistically Valid)

- **L3 (scarcity) got more traffic** (48 clicks vs 32-34) suggesting RedTrack may have weighted it slightly higher, or FB's algo preferred the scarcity angle.
- **L2 and L3 both tracked LP views** (32 and 50 respectively), confirming the `track.js` script works on Netlify-hosted pages.
- **Sticky mobile CTA** (shows on scroll) is good UX — keep in all future landers.

### Patterns for Next Cycle

1. **CTA must go to `landing.olenis.com/703/01`** with `?sub2={clickid}` appended. This is the only proven conversion path.
2. **Keep lander style closer to champion** — the "7 Reasons" listicle format had 3.53% CVR baseline. Don't radically change the format.
3. **Test headline angles, not page styles** — change 1-2 components max.
4. **Promising angles to test:**
   - Ingredient science (peptide delivery mechanism)
   - Price comparison ($0.38/use vs $120 eye creams)
   - Real results / before-after transformation focus
   - First-person honest review format

---

## Cycle 002 Learnings (2026-03-05) — QUIZ FUNNEL (L) IS THE WINNER

### Result Summary

**Challenger L (AG1-style quiz funnel) won the cycle.** The quiz format outperformed the social proof wall (J) and Wirecutter-style review (K). The interstitial modals between questions — which validate the user's answer and pre-sell the product — appear to be the key differentiator.

### What Won and Why

- **Quiz funnel creates investment** — by answering 3 questions, the user has committed micro-effort. The personalized "97% match" result feels earned, not pitched.
- **Interstitial modals are doing heavy lifting** — each popup delivers answer-specific education (e.g., "Great news for puffiness — cooling hydrogel constricts blood vessels on contact"). This bridges the curiosity gap AND pre-sells before the results page.
- **Match percentage is powerful conversion theater** — 95-98% match with animated ring + personalized benefit cards makes the recommendation feel clinical/scientific, not salesy.
- **Video background hero** creates premium feel — differentiates from typical FB landing pages.

### Now Testing: Quiz Optimization (Cycle 003)

Based on the L win, we're now A/B testing quiz friction levels to find the optimal flow:

| Lander | RT ID | Variant | Hypothesis |
|--------|-------|---------|------------|
| L (champion) | `69a94cf9dd85a4846fb9bcad` | 3Q + interstitial popups | Baseline winner — popups educate but add friction |
| M (challenger) | `69aa7a783e246949aa0e3dd7` | 3Q, NO popups — straight flow | Removing popups reduces friction; faster path to results may increase completion rate |
| N (challenger) | `69aa7a7a3e246949aa0e3ddf` | 1 question only — instant results | Minimal friction; tests if the single "what's your concern?" question + personalized results is enough to convert without the full quiz investment |

**What we're learning:** Is the quiz value in the *questions themselves* (user investment / commitment bias) or in the *interstitial education* (pre-selling via answer-specific content)? M isolates the questions, N tests if even 1 question is enough.

**Traffic split:** ~33% each. Campaign `6979c0adcad13a4d8c3cbb94`.

---

### What Was Built

3 new challenger landers for Olenis Bright Eyes Peptide Patches, deployed to `firstlightrituals.com`.

| Lander | Style | Persuasion Strategy | URL |
|--------|-------|---------------------|-----|
| J | Social Proof Wall | Volume social proof — IG reels, TikTok screenshots, review walls. "47,000 women can't be wrong." | `/peptide-patches/challenger-j.html` |
| K | Wirecutter-Style Review | First-person confessional editorial. "$4,200 on eye creams in 3 years" cost comparison narrative. BeautyStandards masthead for native camouflage. | `/peptide-patches/challenger-k.html` |
| L | AG1-Style Quiz Funnel | 3-question quiz with interstitial modals. Personalized results page with match percentage + benefit cards. Video background hero. | `/peptide-patches/challenger-l.html` |

### Design Decisions & Rationale

**J — Social Proof Wall:**
- Targets the "everyone's using it" heuristic
- IG Reels screenshots + UGC imagery for authenticity
- Multiple CTAs: mid-page after proof section + bottom + sticky mobile

**K — Wirecutter Review:**
- Hero image REMOVED after multiple iterations (kept looking zoomed/cropped). Text-forward layout works better for this editorial format.
- First-person "I spent $4,200" narrative creates loss aversion + relatability
- Cost comparison table (old routine $1,180/yr vs patches $92/yr) is the key conversion element
- Sidebar product card with rating anchors the offer

**L — Quiz Funnel:**
- Shortened from 5 questions to 3 after initial build. 5 felt too long — quiz fatigue before results page.
- Final 3 questions: (1) concern type, (2) spending history, (3) time for routine
- Each question has an interstitial modal with answer-specific contextual feedback (AG1 pattern)
- Interstitials are key differentiator — they validate the user's answer and pre-sell the product before results
- Results page: personalized benefit cards based on Q1 answer, match percentage ring (95-98%), coral review stars
- Olenis brand colors (#242655 navy, #D4836B coral) — NOT gold/luxury. Matched from shop.olenis.com.

### Tracking Setup

- All CTAs point to `https://hpkpn.ttrk.io/click` (RedTrack redirect)
- `track.js` loaded at bottom before `</body>` (not in `<head>`)
- Old click ID passthrough script removed — track.js handles it natively
- This fixes the Cycle 001 tracking issue permanently

### What To Watch

1. **Quiz completion rate on L** — 3 questions should be fast, but interstitials add friction. If drop-off is high between Q2-Q3, consider removing one interstitial.
2. **K vs J CTR** — K is text-heavy (Wirecutter format). If CTR is low, the lack of hero image may be hurting above-fold engagement. Could test adding a small product image inline instead.
3. **L match percentage effect** — 95-98% random range. If CVR is high, the personalization theater is working.

### Patterns Confirmed

- **Sticky mobile CTA** — kept across all 3 landers (proven from Cycle 001)
- **Video background** works well for quiz funnels (L) — creates premium feel without page weight
- **AG1 interstitial pattern** — answer-specific modals between quiz questions increase perceived personalization
- **3 questions is the sweet spot** for quiz funnels in ecommerce. 5 was too many.

### Cloudinary Assets Used

| Asset | File | Used In |
|-------|------|---------|
| UGC bathroom mirror selfie | `scene_1_mr5gie.jpg` | K (inline article image) |
| IG Reels screenshot | `scene_teudap.jpg` | J, L (interstitials) |
| Editorial blonde woman | `video_B1_forced_perspective-end-a12262a2c7_gne52x.jpg` | L (interstitials) |
| Playful UGC patches | `02-playful-ugc-genz-COSRX_ln59yl.jpg` | L (offer card, interstitials) |
| Video bg 1 | `final_2_wrrjdp.mp4` | L (hero background) |
| Video bg 2 | `final_5_edao4y.mp4` | L (results page) |
