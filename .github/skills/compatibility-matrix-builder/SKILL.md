---
name: compatibility-matrix-builder
description: 'Build compatibility testing matrices for browsers, devices, operating systems, and platforms. Use when defining which browser/device combinations to test, prioritizing platform coverage, planning cross-browser testing, or creating device test matrices for mobile apps.'
---

# Compatibility Matrix Builder

Build compatibility testing matrices that prioritize platform coverage based on usage data and risk.

## When to Use

- When defining browser/device combinations to test
- When prioritizing platform coverage under time constraints
- When planning cross-browser or cross-device testing
- When creating mobile device test matrices

## Procedure

1. Gather usage data:
   - Browser usage statistics (from analytics or market data)
   - Device/OS distribution
   - Screen resolution distribution
   - Geographic considerations
2. Build the compatibility matrix:
   - List all supported browsers and versions
   - List all supported operating systems
   - List all supported devices (for mobile/responsive)
   - List screen resolutions to cover
3. Prioritize testing tiers:
   - **Tier 1** (Must test): Top browsers/devices covering ~80% of users
   - **Tier 2** (Should test): Next tier covering ~15% of users
   - **Tier 3** (Nice to test): Remaining platforms
4. Define what to test on each tier:
   - Tier 1: Full functional testing
   - Tier 2: Key user journeys and known problem areas
   - Tier 3: Smoke test only

## Output Format

```markdown
# Compatibility Matrix: [Application]

## Browser Matrix
| Browser | Versions | OS | Tier | Test Scope |
|---------|----------|-----|------|-----------|
| Chrome | Latest, Latest-1 | Win, Mac, Linux | 1 | Full |
| Safari | Latest | Mac, iOS | 1 | Full |

## Device Matrix (Mobile)
| Device | OS Version | Screen Size | Tier | Test Scope |
|--------|-----------|-------------|------|-----------|

## Screen Resolutions
| Resolution | Category | Tier |
|-----------|----------|------|
```
