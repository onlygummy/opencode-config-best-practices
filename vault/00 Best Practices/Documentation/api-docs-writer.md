---
type: best-practice
category: documentation
tags: []
created: 2025-07-27
last-reviewed: 2026-09-01
---

# API Docs Writer

## Summary

Template สำหรับเขียน API endpoint documentation ที่ครบถ้วนและสม่ำเสมอ — consistent, developer-facing, OpenAPI-adjacent

## Problem

API docs หลายที่ไม่ครบ parameter, error code, หรือ code example — ทำให้ developer ต้องเดาหรือทดลองเอง, และไม่มี standard structure ที่สม่ำเสมอ

## When to Use

- เขียน API endpoint documentation สำหรับ developer
- Review API docs ที่มีอยู่เทียบมาตรฐาน
- จัดทำ developer portal หรือ README สำหรับ API

## Solution

### Required Inputs

Before documenting, gather:
- **Endpoint details** (spec, Postman export, or description)
- **Auth method** (API key / Bearer token / OAuth 2.0 / None)
- **Base URL**
- **API version** (v1, v2.3, or unversioned)
- **Rate limits** (requests per second/minute per token/IP, or "unknown")
- **Audience** (internal / external partners / public)
- **Output format** — Markdown (developer portal, README) or prose (Confluence, Notion)

### Endpoint Doc Template

```
## `[METHOD] /path/to/endpoint`

**Summary:** [One line — what this endpoint does]

**Description:** [2–4 sentences. When to use. What it returns. Important behavior: pagination, rate limits, async, etc.]

**Authentication:** [Required / Optional — method]
```

#### Request Section

**Headers:**

| Header | Required | Description |
|---|---|---|
| `Authorization` | Yes | `Bearer <token>` |
| `Content-Type` | Yes | `application/json` |

**Path Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string | Yes | Unique identifier for the resource |

**Query Parameters:**

| Parameter | Type | Required | Default | Description |
|---|---|---|---|---|
| `limit` | integer | No | 20 | Max results per page (1–100) |
| `cursor` | string | No | — | Pagination cursor from previous response |

**Request Body:**

```json
{
  "field_name": "value"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `field_name` | string | Yes | What this field does, not just what it is |
| `another_field` | integer | No | Valid range or enum values if applicable |

#### Response Section

**Success Response: `200 OK`**

```json
{
  "id": "abc123",
  "status": "active"
}
```

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique identifier |
| `status` | string | Enum: `active`, `inactive`, `pending` |

#### Error Codes Section

| Code | Error Code | Description | Resolution |
|------|------------|-------------|-----------|
| `400` | `INVALID_REQUEST` | Malformed body or missing fields | Check request body against schema |
| `401` | `UNAUTHORIZED` | Missing/invalid auth token | Verify API key or refresh token |
| `404` | `NOT_FOUND` | Resource does not exist | Check path parameter ID |
| `429` | `RATE_LIMITED` | Too many requests | Back off; retry after `Retry-After` header |
| `500` | `INTERNAL_ERROR` | Unexpected server error | Retry with exponential backoff; contact support |

#### Code Examples

Minimum 2 languages (default: cURL + Python):

**cURL:**
```bash
curl -X POST https://api.example.com/v1/endpoint \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"field_name": "value"}'
```

**Python:**
```python
import requests

response = requests.post(
    "https://api.example.com/v1/endpoint",
    headers={"Authorization": "Bearer YOUR_TOKEN"},
    json={"field_name": "value"}
)
data = response.json()
```

## Example

### GET /users/{id}

**Summary:** Retrieve a user by ID

**Description:** Returns the user object for the specified ID. Use this endpoint to fetch individual user details for profile display or editing.

**Authentication:** Required — Bearer token

**Path Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string | Yes | Unique identifier for the user |

**Success Response: `200 OK`**

```json
{
  "id": "usr_123",
  "name": "John Doe",
  "email": "john@example.com",
  "status": "active"
}
```

**Error Codes:**

| Code | Error Code | Description | Resolution |
|------|------------|-------------|-----------|
| `401` | `UNAUTHORIZED` | Missing/invalid auth token | Verify API key or refresh token |
| `404` | `NOT_FOUND` | User does not exist | Check user ID |

## Common Mistakes

- **Happy-path only docs** — every endpoint must have error codes (at least 400, 401/403, 404, 429, 500)
- **Placeholder tokens** — use realistic-looking placeholders anchored to actual base URL, not `YOUR_ENDPOINT` or `INSERT_TOKEN`
- **Missing enums** — undocumented enum values cause integration bugs
- **Missing pagination on lists** — developers will silently miss data
- **Describing what, not what it does** — "the ID" is not docs; "the unique identifier used to retrieve or update this resource" is

## References

- OpenAPI Specification: https://swagger.io/specification/
- Google API Design Guide: https://cloud.google.com/apis/design

## Related

- [[00 Best Practices/Documentation/usage-focused-docs|Usage-Focused Docs]]
- [[00 Best Practices/Documentation/user-manual-writing|User Manual Writing]]
