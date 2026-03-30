---
name: phase-test-matrix-generator
description: 'Generate SDLC phase-to-test-type matrices that map which test types apply to each development phase. Use when planning test coverage across SDLC phases, visualizing which testing happens when, creating test strategy matrices, or ensuring no phase has testing gaps.'
---

# Phase-Test Matrix Generator

Generate matrices that map test types to SDLC phases for comprehensive test planning.

## When to Use

- When planning test coverage across SDLC phases
- When visualizing which testing happens in which phase
- When creating test strategy overview documents
- When ensuring no SDLC phase has testing gaps

## Procedure

1. Identify the SDLC phases for the project:
   - Requirements and planning
   - Architecture and design
   - Build and coding
   - Integration and assembly
   - System validation
   - Acceptance and release
   - Deployment and operations
   - Maintenance and change
2. Map test types to each phase:
   - Static testing (reviews, inspections)
   - Component/unit testing
   - Integration testing
   - System testing
   - Acceptance testing
   - Non-functional testing
   - Security testing
   - Change-related testing (regression, confirmation)
3. Indicate primary vs secondary coverage:
   - **Primary**: The phase where this test type is most active
   - **Secondary**: The phase where it also contributes
   - **Continuous**: Testing that spans the entire lifecycle
4. Identify gaps and recommend additions

## Output Format

```markdown
# Phase-Test Matrix: [Project/Team]

## Matrix

| Test Type | Req/Plan | Design | Build | Integration | System | Acceptance | Deploy | Maintenance |
|-----------|----------|--------|-------|-------------|--------|-----------|--------|-------------|
| Requirements review | 🔵 | 🔘 | | | | | | |
| Unit testing | | | 🔵 | 🔘 | | | | |
| Integration testing | | | 🔘 | 🔵 | 🔘 | | | |
| System testing | | | | | 🔵 | 🔘 | | |
| Performance testing | | 🔘 | | | 🔵 | | | |
| Security testing | 🔘 | 🔘 | 🔘 | 🔘 | 🔵 | 🔘 | | 🔘 |
| UAT | | | | | | 🔵 | | |
| Regression testing | | | | 🔘 | 🔘 | 🔘 | | 🔵 |

Legend: 🔵 Primary | 🔘 Secondary

## Gaps Identified
- [Phase with insufficient coverage]
```
