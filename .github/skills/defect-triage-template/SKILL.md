---
name: defect-triage-template
description: 'Generate defect triage templates and classification frameworks. Use when setting up defect management processes, creating triage workflows, defining severity and priority models, or standardizing how teams classify and route defects.'
---

# Defect Triage Template

Generate defect triage templates and classification frameworks for consistent defect management.

## When to Use

- When setting up or improving defect management processes
- When teams need a standard classification framework
- When triage meetings need a consistent workflow

## Procedure

1. Define severity levels with clear criteria and examples
2. Define priority levels with response time expectations
3. Create a classification taxonomy (functional, performance, security, usability, etc.)
4. Define triage workflow with roles and responsibilities
5. Create a triage decision template

## Output Format

```markdown
# Defect Triage Framework

## Severity Model
| Level | Definition | Example |
|-------|-----------|---------|
| S1 - Critical | System down or data loss | Payment processing fails for all users |
| S2 - High | Major feature broken, no workaround | Cannot create new orders |
| S3 - Medium | Feature impaired, workaround exists | Export works but formatting is wrong |
| S4 - Low | Cosmetic or minor inconvenience | Tooltip text has typo |

## Priority Model
| Level | Response Time | Resolution Target |
|-------|--------------|-------------------|
| P1 | Immediate | [hours] |
| P2 | Same day | [days] |
| P3 | Next sprint | [sprint] |
| P4 | Backlog | Best effort |

## Triage Decision Template
- **Defect ID**: [ID]
- **Summary**: [Brief description]
- **Severity**: [S1-S4]
- **Priority**: [P1-P4]
- **Category**: [Functional/Performance/Security/Usability/Data]
- **Assigned to**: [Team/Person]
- **Root cause zone**: [Code/Config/Data/Infra/Dependency]
- **Retest scope**: [What to retest after fix]
```
