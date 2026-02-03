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
