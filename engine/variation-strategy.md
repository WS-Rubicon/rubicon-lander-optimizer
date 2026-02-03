# Variation Strategy

## Core Principle
Change 1-3 components per challenger. Never more. Attribution clarity requires controlled experiments.

## Component Hierarchy (What to Test First)

Priority order based on typical CVR impact:

1. **Headline** (highest impact) - First thing visitors see
2. **CTA text + color** - Direct conversion driver
3. **Hero image** - Attention and emotional hook
4. **Subheadline** - Supports/qualifies headline
5. **Trust elements** - Reduces friction
6. **Form fields** - Friction vs qualification
7. **Body copy** - Lower impact for skimmers

## Variation Types

### Type 1: Single Component Swap
Change ONE component. Clearest attribution.
```
Champion: "California Homeowners: $0 Down Solar Is Back"
Challenger: "California Homeowners: Federal Credit Drops in 2026"
Tags changed: [price_anchor -> deadline]
```

### Type 2: Paired Component Test
Change TWO related components. Tests a "theme."
```
Champion headline: "$0 Down Solar Is Back" + CTA: "Check My Savings"
Challenger headline: "Credit Drops in 2026" + CTA: "Lock In Your Rate"
Tags changed: [price_anchor->deadline, benefit_focused->urgency]
```

### Type 3: Style Test
Compare editorial vs quiz vs offer for the same angle.
Only run early (first 5 cycles) to establish best style per vertical.

## Brief Construction for Variations

When generating a challenger, construct the brief to target specific changes:

```
Base brief (from champion):
"Page targeting [geo] [audience]. Focus on [angle]. [tone] tone."

Challenger A brief (modify angle):
"Page targeting [geo] [audience]. Focus on [NEW_ANGLE]. [tone] tone.
IMPORTANT: Headline must emphasize [specific_tag]. Keep form and trust elements similar to standard."

Challenger B brief (modify CTA approach):
"Page targeting [geo] [audience]. Focus on [angle]. [tone] tone.
IMPORTANT: CTA should use [scarcity/urgency/benefit] approach. Headline can stay similar."
```

## Tag Assignment Rules

Every component change gets tagged from the vertical's tag library.
If a change doesn't fit an existing tag, create a new one and add to config.

Tag format: `{component}_{descriptor}`
Examples: `headline_urgency`, `cta_scarcity`, `image_lifestyle`, `form_minimal`

## Challenger Generation Logic

### When component-wins.json has < 20 records:
- **Explore broadly**: Test different angles, styles, components
- Each cycle should test something new
- Avoid repeating the same test

### When component-wins.json has 20+ records:
- **Exploit winners**: Combine high-win-rate tags
- **Explore edges**: Test untested combinations of proven tags
- 70% exploit / 30% explore split

### Specific Patterns to Test:
1. Urgency in headline + scarcity in CTA (deadline + limited slots)
2. Social proof in headline + testimonial trust elements
3. Location-specific headline + local imagery
4. Minimal form (1 field) vs standard form (3 fields)
5. Question headline vs statement headline
6. Number-specific claims vs vague claims ("$347/mo" vs "hundreds")
