---
type: lesson-learned
category: mistake
severity: high
tags: [over-engineering, simplicity, yagni]
created: 2026-09-01
---

# Over-engineering

## What Happened

The team built overly complex design patterns for a small project, resulting in:
- Development time increased 3x
- New developers found it hard to understand
- High maintenance cost

## Root Cause

1. Fear that the system would not scale
2. Wanting to try new patterns
3. Not assessing the actual complexity

## Impact

- Project delayed by 2 months
- Had to refactor the entire system
- Team lost confidence

## Prevention

1. Start with a simple solution first
2. Apply YAGNI principle — do not build what is not needed
3. Evaluate complexity vs benefit before implementing
4. Use [[Clean Architecture]] but adapt to project size

## Related

- [[Clean Architecture]]
- [[SOLID Principles]]
- [[KISS Principle]]
