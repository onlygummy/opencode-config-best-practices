---
type: best-practice
category: documentation
tags: []
created: 2026-08-11
last-reviewed: 2026-09-01
---

# Usage-Focused Docs & README

## Summary

Pattern สำหรับเขียน user-facing docs ที่เน้นการใช้งานจริง ไม่ใช่คู่มือ dev — แยก audience, usage-first structure, กัน docs drift

## Problem

docs หลายไฟล์ปน dev กับ usage ทำให้ user อ่านไม่รู้เรื่อง, hardcode รายการที่ server มีอยู่แล้วทำให้ล้าเร็ว, และ connect section อยู่ล่างสุดทำให้ user หาวิธีเริ่มต้นไม่เจอ

## When to Use

- เขียน/รีไรท์ README หรือ docs ที่เป็น **user-facing**
- User ต้องการ docs "วิธีใช้งาน" — ไม่ใช่คู่มือ dev
- สัญญาณจาก user: "เอาแค่วิธีใช้งานก็พอ ไม่ต้องเอาวิธีการ development", "เอา Development ออก", "Connect ขึ้นมาก่อน"

## Solution

### 1. แยก audience ก่อนเขียน

| Audience | ไฟล์ | เนื้อหา |
|---|---|---|
| User (วิธีใช้) | README, หน้า docs | connect + tools รายชื่อ |
| Developer | `docs/DEVELOPMENT.md` | run / config / auth / structure |
| Ops | `docs/DEPLOY.md` | deploy / infra / troubleshooting |

Docs หนึ่งไฟล์ = หนึ่ง audience — ไม่ปนกัน (ย้ายออกเมื่อ user บอก "เอา Development ออก")

### 2. โครงสร้าง usage-first (template)

```
1. Header        — คืออะไร (1-2 บรรทัด) + URL หลัก
2. Connect       — วิธีต่อ client/แพลตฟอร์มแต่ละตัว
                   (ขั้นตอนสั้น: เปิดที่ไหน → วาง URL → login)
                   *สำคัญที่สุด — วางก่อน Tools*
3. Tools Available — รายชื่อเท่านั้น (ไม่มีคำอธิบาย/ตัวอย่าง)
4. Development   — ลิงก์ไปไฟล์แยก (ไม่ใช่เนื้อหา)
```

### 3. กัน docs drift (สำคัญที่สุด)

- **Server-rendered dynamic** — รายการที่ server มีอยู่จริง (เช่น `server.list_tools()`) render ตอน request — อย่า hardcode ใน template → มี tool ใหม่ = docs อัปเดตเอง
- **Test markers เป็น guard** — assert เนื้อหาสำคัญของ docs ใน test (ชื่อ tool / ชื่อ client / URL / CDN links) → docs ล้า = CI fail (drift ถูกจับก่อน deploy)
- **ค่าคงที่ร่วม** — URL/ค่าที่ template กับ test ใช้ร่วมกัน ต้องเป็นตัวแปรเดียว (แก้ที่เดียว ไม่เพี้ยน)
- **ตรวจข้อเท็จจริงจาก source ทางการ** — ขั้นตอนของ product ภายนอก (เช่น MCP ของ Gemini Spark) ต้อง verify กับ official docs — ห้ามเขียนจากความจำ (Gemini ≠ CLI — เป็น Connected Apps ใน web app)
- **Rename ไล่ให้ครบทุกจุด** — เปลี่ยนชื่อ product ต้อง grep template/README/test/code comment (เช่น Gemini → Gemini Spark)

### 4. Gotchas

- `str.replace(placeholder)` แทนที่ **ทุก occurrence** — placeholder ต้องไม่ปรากฏใน HTML comment (comment กลายเป็น content แทน) — ใช้ token ไม่ซ้ำหรือเลี่ยงคำใน comment
- **Connect steps ≠ tool usage steps** — อย่าใส่ "ให้เรียก tool X" ต่อท้ายขั้นตอน connect (user ตัดออก — อยากได้ connect flow บริสุทธิ์; tool มีรายชื่อแยกอยู่แล้ว)
- **External assets (CDN/icon) = dependency** — บันทึกผลถ้า offline (หน้าไม่สวย แต่ฟอร์ม/เนื้อหายังใช้ได้)
- **Windows console + Unicode ตอน verify หน้าเว็บ** — ใช้ Invoke-WebRequest/curl + เช็ค string ใน HTML แทนการ print ตรง

## Example

### README Structure

```markdown
# Product Name

1-2 sentences describing what this is.

## Connect

### Platform A
1. Open Platform A
2. Paste URL: `https://example.com`
3. Login

### Platform B
1. Open Platform B
2. Add integration
3. Enter credentials

## Tools Available

- Tool 1
- Tool 2
- Tool 3

## Development

See [DEVELOPMENT.md](docs/DEVELOPMENT.md) for run/config/auth details.
```

## Common Mistakes

- ปน dev กับ usage ในไฟล์เดียว — ยาวเกิน, user อ่านไม่รู้เรื่อง
- Hardcode รายการที่ server มีอยู่แล้ว — ล้าทันทีที่มีของใหม่
- เขียนขั้นตอน product ภายนอกจากความจำ — ต้อง check official source
- Connect section อยู่ล่างสุด — user ต้องการสิ่งนี้ที่สุด ต้องอยู่บน
- เนื้อหาไม่ตรงกันข้ามไฟล์ (README vs docs page ใช้ client/URL ไม่เหมือนกัน)

## References

- Google Developer Documentation Style Guide: https://developers.google.com/style
- TechSmith User Documentation: https://techsmith.com/blog/user-documentation

## Related

- [[00 Best Practices/Documentation/api-docs-writer|API Docs (dev-facing — คนละเรื่อง)]]
- [[00 Best Practices/Documentation/user-manual-writing|User Manual Writing]]
