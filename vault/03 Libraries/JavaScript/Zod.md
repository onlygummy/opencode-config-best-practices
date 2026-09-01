---
type: library
category: javascript
tags: [validation, schema, typescript]
created: 2026-09-01
last-reviewed: 2026-09-01
---

# Zod

## Overview

TypeScript-first schema declaration and validation library with type safety and runtime validation.

## Installation

```bash
npm install zod
```

## When to Use

- Need to validate input data
- Need type inference from schema
- Need clear error messages
- Using TypeScript project

## When NOT to Use

- Pure JavaScript projects (use Joi or Yup instead)
- Need very high performance (use JSON Schema instead)

## Common Patterns

### Basic Schema

```typescript
import { z } from 'zod';

const UserSchema = z.object({
  name: z.string(),
  email: z.string().email(),
  age: z.number().min(0).max(150),
});

type User = z.infer<typeof UserSchema>;
```

### Validation

```typescript
const result = UserSchema.safeParse(data);
if (result.success) {
  console.log(result.data);
} else {
  console.error(result.error);
}
```

## Common Mistakes

1. Not using `.safeParse()` which makes errors hard to handle
2. Creating overly complex schemas
3. Not reusing schemas

## Related

- [[TypeScript]]
- [[React]]
- [[Fastify]]
