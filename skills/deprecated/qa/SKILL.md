---
name: qa
description: QA session แบบ interactive ที่ผู้ใช้เล่าบั๊กหรือปัญหาแบบคุยกันธรรมดา แล้ว agent เปิด GitHub issue ให้ พร้อมสำรวจ codebase เบื้องหลังเพื่อเก็บ context และภาษา domain ใช้เมื่อผู้ใช้อยากรายงานบั๊ก ทำ QA เปิด issue แบบคุยกัน หรือพูดถึง "QA session"
---

# QA Session

รัน QA session แบบ interactive ผู้ใช้เล่าปัญหาที่เจอ คุณช่วยถามให้ชัด สำรวจ codebase เพื่อเก็บ context แล้วเปิด GitHub issue ที่คงทน เน้นมุมมองผู้ใช้ และใช้ภาษา domain ของโปรเจกต์

## สำหรับแต่ละ issue ที่ผู้ใช้ยกขึ้นมา

### 1. ฟังก่อน แล้วถามให้ชัดแบบพอดี ๆ

ให้ผู้ใช้เล่าปัญหาด้วยคำพูดของเขาเอง ถามคำถามสั้น ๆ **ไม่เกิน 2-3 ข้อ** โฟกัสที่:

- เขาคาดหวังอะไร vs เกิดอะไรขึ้นจริง
- ขั้นตอน reproduce (ถ้ายังไม่ชัด)
- เกิดทุกครั้งหรือเป็น ๆ หาย ๆ

อย่าสัมภาษณ์ยืดเยื้อ ถ้าคำอธิบายชัดพอจะเปิด issue ได้แล้ว ไปต่อเลย

### 2. สำรวจ codebase เบื้องหลัง

ระหว่างคุยกับผู้ใช้ ให้ยิง Agent (subagent_type=Explore) ทำงานเบื้องหลังเพื่อทำความเข้าใจบริเวณที่เกี่ยวข้อง เป้าหมายไม่ใช่การหาวิธีแก้ — แต่เพื่อ:

- เรียนรู้ภาษา domain ที่ใช้ในบริเวณนั้น (เช็ก UBIQUITOUS_LANGUAGE.md)
- เข้าใจว่า feature นั้นควรทำอะไร
- ระบุขอบเขตพฤติกรรมฝั่งที่ผู้ใช้มองเห็น

context นี้ช่วยให้เขียน issue ได้ดีขึ้น — แต่ตัว issue เองต้องไม่อ้างถึงไฟล์ เลขบรรทัด หรือรายละเอียด implementation ภายใน

### 3. ประเมิน scope: issue เดียวหรือต้องแตกย่อย?

ก่อนเปิด issue ตัดสินใจว่านี่คือ **issue เดียว** หรือควร **แตกย่อย** เป็นหลาย issue

แตกย่อยเมื่อ:

- การแก้กินพื้นที่หลายบริเวณที่เป็นอิสระต่อกัน (เช่น "validation ของ form ผิด แล้วก็ไม่มี success message แล้วก็ redirect พัง")
- มีประเด็นที่แยกจากกันชัดเจนจนคนละคนหยิบไปทำขนานกันได้
- ผู้ใช้เล่าอะไรที่มี failure mode หรืออาการหลายแบบที่แยกจากกัน

คงเป็น issue เดียวเมื่อ:

- เป็นพฤติกรรมเดียวที่ผิดในที่เดียว
- อาการทั้งหมดมาจากพฤติกรรมต้นตอเดียวกัน

### 4. เปิด GitHub issue

สร้าง issue ด้วย `gh issue create` ไม่ต้องให้ผู้ใช้ review ก่อน — เปิดเลยแล้วแชร์ URL

issue ต้อง **คงทน** — อ่านแล้วยังเข้าใจได้แม้ผ่าน refactor ใหญ่ ๆ เขียนจากมุมมองของผู้ใช้

#### สำหรับ issue เดียว

ใช้ template นี้:

```
## What happened

[Describe the actual behavior the user experienced, in plain language]

## What I expected

[Describe the expected behavior]

## Steps to reproduce

1. [Concrete, numbered steps a developer can follow]
2. [Use domain terms from the codebase, not internal module names]
3. [Include relevant inputs, flags, or configuration]

## Additional context

[Any extra observations from the user or from codebase exploration that help frame the issue — e.g. "this only happens when using the Docker layer, not the filesystem layer" — use domain language but don't cite files]
```

#### สำหรับการแตกย่อย (หลาย issue)

สร้าง issue ตามลำดับ dependency (ตัวที่ block คนอื่นก่อน) เพื่อให้อ้างเลข issue จริงได้

ใช้ template นี้กับ sub-issue แต่ละตัว:

```
## Parent issue

#<parent-issue-number> (if you created a tracking issue) or "Reported during QA session"

## What's wrong

[Describe this specific behavior problem — just this slice, not the whole report]

## What I expected

[Expected behavior for this specific slice]

## Steps to reproduce

1. [Steps specific to THIS issue]

## Blocked by

- #<issue-number> (if this issue can't be fixed until another is resolved)

Or "None — can start immediately" if no blockers.

## Additional context

[Any extra observations relevant to this slice]
```

เวลาแตกย่อย:

- **เลือก issue บาง ๆ หลายอันดีกว่า issue หนา ๆ ไม่กี่อัน** — แต่ละอันต้องแก้และตรวจได้อย่างอิสระ
- **ระบุความสัมพันธ์แบบ block กันตามจริง** — ถ้า issue B test ไม่ได้จริง ๆ จนกว่า issue A จะถูกแก้ ก็บอกไปตรง ๆ ถ้าเป็นอิสระต่อกัน ให้ระบุทั้งคู่ว่า "None — can start immediately"
- **สร้าง issue ตามลำดับ dependency** เพื่อให้อ้างเลข issue จริงในช่อง "Blocked by" ได้
- **เน้นให้ขนานกันได้มากที่สุด** — เป้าหมายคือให้หลายคน (หรือหลาย agent) หยิบคนละ issue ไปทำพร้อมกันได้

#### กติกาสำหรับเนื้อหา issue ทุกอัน

- **ห้ามใส่ file path หรือเลขบรรทัด** — พวกนี้ล้าสมัยเร็ว
- **ใช้ภาษา domain ของโปรเจกต์** (เช็ก UBIQUITOUS_LANGUAGE.md ถ้ามี)
- **อธิบายพฤติกรรม ไม่ใช่ code** — "sync service apply patch ไม่สำเร็จ" ไม่ใช่ "applyPatch() throws on line 42"
- **ขั้นตอน reproduce เป็นสิ่งบังคับ** — ถ้าหาไม่ได้ ให้ถามผู้ใช้
- **กระชับเข้าไว้** — dev ควรอ่าน issue จบได้ใน 30 วินาที

หลังเปิด issue เสร็จ ให้พิมพ์ URL ของ issue ทั้งหมด (พร้อมสรุปความสัมพันธ์ว่าอันไหน block อันไหน) แล้วถามว่า: "issue ถัดไป หรือพอแค่นี้?"

### 5. คุยต่อจนจบ session

ทำต่อไปเรื่อย ๆ จนผู้ใช้บอกว่าพอ issue แต่ละอันเป็นอิสระต่อกัน — อย่ารวบไปทำเป็น batch
