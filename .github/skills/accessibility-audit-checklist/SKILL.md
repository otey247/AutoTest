---
name: accessibility-audit-checklist
description: 'Generate accessibility audit checklists based on WCAG guidelines. Use when auditing web applications for accessibility compliance, checking keyboard navigation, screen reader compatibility, color contrast, or preparing for accessibility certification.'
---

# Accessibility Audit Checklist

Generate accessibility audit checklists based on WCAG guidelines for web applications.

## When to Use

- When auditing applications for accessibility compliance
- When checking WCAG 2.1 AA conformance
- When preparing for accessibility certification
- When verifying keyboard and screen reader support

## Procedure

1. Determine the target conformance level (A, AA, or AAA)
2. Generate checklist items organized by WCAG principles:
   - **Perceivable**: Content is presentable to all users
   - **Operable**: Interface is navigable by all users
   - **Understandable**: Content and operation are clear
   - **Robust**: Content works with assistive technologies
3. For each checklist item, provide:
   - The WCAG success criterion reference
   - What to check
   - How to test it
   - Common failure patterns

## Output Format

```markdown
# Accessibility Audit Checklist — WCAG 2.1 [Level]

## Perceivable
- [ ] **1.1.1 Non-text Content**: All images have alt text
  - Test: Check all `<img>` tags for meaningful alt attributes
- [ ] **1.3.1 Info and Relationships**: Structure conveyed through markup
  - Test: Verify headings, lists, and tables use proper HTML elements
- [ ] **1.4.3 Contrast**: Text has 4.5:1 contrast ratio
  - Test: Use contrast checker tool on all text elements

## Operable
- [ ] **2.1.1 Keyboard**: All functionality available via keyboard
  - Test: Tab through entire page, verify all interactive elements are reachable
- [ ] **2.4.1 Bypass Blocks**: Skip navigation link present
  - Test: Check for skip-to-content link
- [ ] **2.4.7 Focus Visible**: Focus indicator is visible
  - Test: Tab through page, verify focus ring on each element

## Understandable
- [ ] **3.1.1 Language**: Page language is specified
  - Test: Check `<html lang="...">` attribute
- [ ] **3.3.1 Error Identification**: Errors are identified and described
  - Test: Submit forms with invalid data, verify error messages

## Robust
- [ ] **4.1.1 Parsing**: Valid HTML
  - Test: Run HTML validator
- [ ] **4.1.2 Name, Role, Value**: Custom components have ARIA labels
  - Test: Check custom UI components with screen reader
```
