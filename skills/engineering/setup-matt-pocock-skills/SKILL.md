---
name: setup-matt-pocock-skills
description: ตั้งค่า repo นี้สำหรับ engineering skills — เซ็ตอัพ issue tracker, ชุดคำศัพท์ label สำหรับ triage และโครงสร้าง domain doc รันครั้งเดียวก่อนใช้ engineering skills ตัวอื่นเป็นครั้งแรก
disable-model-invocation: true
---

# ตั้งค่า Skills ของ Matt Pocock

สร้างโครง config ราย repo ที่ engineering skills ทั้งหลายคาดหวังไว้:

- **Issue tracker** — ที่ที่ issue อยู่ (default คือ GitHub; รองรับ local markdown แบบ out of the box ด้วย)
- **Triage labels** — string ที่ใช้แทน triage role มาตรฐานทั้งห้า
- **Domain docs** — ที่อยู่ของ `CONTEXT.md` กับ ADR (บันทึกการตัดสินใจเชิงสถาปัตยกรรม) และกติกาฝั่งผู้อ่านสำหรับการใช้งาน

skill นี้ขับเคลื่อนด้วย prompt ไม่ใช่ script แบบ deterministic — สำรวจก่อน นำเสนอสิ่งที่เจอ ยืนยันกับ user แล้วค่อยเขียนไฟล์

## ขั้นตอน

### 1. สำรวจ

ดู repo ปัจจุบันเพื่อเข้าใจสถานะตั้งต้น อ่านทุกอย่างที่มีอยู่จริง อย่าเดาเอาเอง:

- `git remote -v` และ `.git/config` — repo นี้เป็น GitHub repo หรือเปล่า? repo ไหน?
- `AGENTS.md` กับ `CLAUDE.md` ที่ root ของ repo — มีไฟล์ไหนอยู่แล้วบ้าง? มี section `## Agent skills` ในไฟล์ใดไฟล์หนึ่งแล้วหรือยัง?
- `CONTEXT.md` และ `CONTEXT-MAP.md` ที่ root ของ repo
- `docs/adr/` และ directory `src/*/docs/adr/` ต่าง ๆ
- `docs/agents/` — ผลลัพธ์จากการรัน skill นี้ครั้งก่อนมีอยู่แล้วหรือเปล่า?
- `.scratch/` — สัญญาณว่า repo นี้ใช้ convention issue tracker แบบ local markdown อยู่แล้ว
- skill `triage` ถูกติดตั้งไว้ไหม? (มีโฟลเดอร์ skill `triage` อยู่ข้าง ๆ skill นี้ หรือมี `triage` ในรายการ skill ที่คุณใช้ได้) ข้อนี้ตัดสินว่า Section B จะรันหรือไม่
- สัญญาณของ monorepo — มี `pnpm-workspace.yaml`, มี field `workspaces` ใน `package.json` หรือมี `packages/*` ที่มี `src/` ของตัวเอง สัญญาณพวกนี้จะเจอเฉพาะใน repo แบบ multi-package ขนาดใหญ่จริง ๆ เท่านั้น ถ้าไม่เจอก็คือ single-context ซึ่งเป็นเคสของ repo แทบทุกตัว

### 2. นำเสนอสิ่งที่เจอแล้วถาม

สรุปว่ามีอะไรอยู่แล้วและอะไรยังขาด จากนั้นไล่ทีละ section ตามลำดับ — หนึ่ง section หนึ่งคำตอบ แล้วค่อยไปต่อ

เปิดแต่ละ section ด้วยคำตอบที่แนะนำ เพื่อให้ user ตอบรับได้ในคำเดียว ใส่คำอธิบายบรรทัดเดียวเฉพาะตอนที่ตัวเลือกแตกแขนงจริง ๆ และข้าม section นั้นไปเลยถ้าการสำรวจตอบคำถามไปแล้ว (Section B เมื่อไม่ได้ติดตั้ง `triage`, Section C เมื่อไม่ใช่ monorepo)

**Section A — Issue tracker**

> คำอธิบาย: "issue tracker" คือที่ที่ issue ของ repo นี้อยู่ skill อย่าง `to-tickets`, `triage`, `to-spec` และ `qa` อ่านและเขียนกับมัน — พวกมันต้องรู้ว่าควรเรียก `gh issue create` เขียนไฟล์ markdown ใต้ `.scratch/` หรือทำตาม workflow อื่นที่คุณอธิบาย เลือกที่ที่คุณใช้ track งานของ repo นี้จริง ๆ

ท่า default: skill ชุดนี้ออกแบบมาสำหรับ GitHub ถ้า `git remote` ชี้ไปที่ GitHub ให้เสนออันนั้น ถ้า `git remote` ชี้ไปที่ GitLab (`gitlab.com` หรือ host แบบ self-hosted) ให้เสนอ GitLab นอกเหนือจากนั้น (หรือถ้า user อยากเลือกเอง) ให้เสนอตัวเลือก:

- **GitHub** — issue อยู่ใน GitHub Issues ของ repo (ใช้ `gh` CLI)
- **GitLab** — issue อยู่ใน GitLab Issues ของ repo (ใช้ CLI [`glab`](https://gitlab.com/gitlab-org/cli))
- **Local markdown** — issue เป็นไฟล์อยู่ใต้ `.scratch/<feature>/` ใน repo นี้ (เหมาะกับโปรเจกต์เดี่ยวหรือ repo ที่ไม่มี remote)
- **อื่น ๆ** (Jira, Linear ฯลฯ) — ให้ user อธิบาย workflow มาหนึ่งย่อหน้า แล้ว skill จะบันทึกเป็นข้อความอิสระ

บันทึกตัวเลือกลง `docs/agents/issue-tracker.md` template ของ GitHub และ GitLab มี flag "PRs as a request surface" ซึ่ง default เป็น **off** — ปล่อยไว้แบบนั้นและไม่ต้องหยิบขึ้นมาถาม user ที่อยากให้ external PR เข้าคิว triage สามารถไปเปิด flag ในไฟล์เองทีหลังได้

**Section B — ชุดคำศัพท์ label สำหรับ triage** ข้าม section นี้ไปเลยถ้าไม่ได้ติดตั้ง skill `triage` (การสำรวจบอกคุณแล้ว) — skill ที่ไม่ได้ติดตั้งไม่ต้องมี label

ถ้าติดตั้งอยู่ ให้ถามคำถามเดียวเป๊ะ ๆ:

> อยากใช้ triage label ชุด default ไหม? (แนะนำ: **ใช่**)

ค่า default คือ role มาตรฐานทั้งห้า โดย string ของ label ตรงกับชื่อ role เป๊ะ: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix` ถ้าตอบ **ใช่** ก็เขียนตามนั้นเลย เฉพาะเมื่อ user ตอบไม่ — ปกติเพราะ tracker ของเขาใช้ชื่ออื่นอยู่แล้ว (เช่น `bug:triage` แทน `needs-triage`) — ค่อยเก็บค่า override เพื่อให้ `triage` ใช้ label ที่มีอยู่แล้วแทนการสร้างซ้ำ

**Section C — Domain docs** ให้ default เป็น **single-context** — `CONTEXT.md` หนึ่งไฟล์ + `docs/adr/` ที่ root ของ repo แบบนี้เหมาะกับ repo แทบทุกตัว เขียนไปได้เลยโดยไม่ต้องถาม

เสนอ **multi-context** — `CONTEXT-MAP.md` ที่ root ชี้ไปยังไฟล์ `CONTEXT.md` ราย context — เฉพาะเมื่อการสำรวจเจอสัญญาณ monorepo แล้วค่อยยืนยันว่า user ต้องการ layout แบบไหน

### 3. ยืนยันและแก้ไข

แสดง draft ให้ user ดู:

- block `## Agent skills` ที่จะเพิ่มลงใน `CLAUDE.md` / `AGENTS.md` ไฟล์ที่กำลังจะแก้ (ดูกติกาการเลือกไฟล์ในขั้นที่ 4)
- เนื้อหาของ `docs/agents/issue-tracker.md`, `docs/agents/domain.md` และ `docs/agents/triage-labels.md` (ไฟล์สุดท้ายเฉพาะเมื่อติดตั้ง `triage`)

เปิดโอกาสให้ user แก้ก่อนเขียนจริง

### 4. เขียน

**เลือกไฟล์ที่จะแก้:**

- ถ้ามี `CLAUDE.md` ให้แก้ไฟล์นั้น
- ถ้าไม่มี แต่มี `AGENTS.md` ให้แก้ไฟล์นั้น
- ถ้าไม่มีทั้งคู่ ให้ถาม user ว่าจะสร้างไฟล์ไหน — อย่าตัดสินใจแทน

ห้ามสร้าง `AGENTS.md` เมื่อมี `CLAUDE.md` อยู่แล้ว (หรือกลับกัน) — ให้แก้ไฟล์ที่มีอยู่แล้วเสมอ

ถ้าไฟล์ที่เลือกมี block `## Agent skills` อยู่แล้ว ให้อัปเดตเนื้อหาตรงนั้นแทนการต่อท้ายเป็นอันซ้ำ และอย่าเขียนทับสิ่งที่ user แก้ไว้ใน section รอบ ๆ

ตัว block:

```markdown
## Agent skills

### Issue tracker

[one-line summary of where issues are tracked]. See `docs/agents/issue-tracker.md`.

### Triage labels

[one-line summary of the label vocabulary]. See `docs/agents/triage-labels.md`.

### Domain docs

[one-line summary of layout — "single-context" or "multi-context"]. See `docs/agents/domain.md`.
```

ใส่ sub-block `### Triage labels` และเขียน `docs/agents/triage-labels.md` เฉพาะเมื่อติดตั้ง `triage` และ Section B ได้รันไปแล้วเท่านั้น ถ้าไม่ ก็ตัดทั้งสองอย่างออก

จากนั้นเขียนไฟล์ docs โดยใช้ seed template ในโฟลเดอร์ skill นี้เป็นจุดตั้งต้น:

- [issue-tracker-github.md](./issue-tracker-github.md) — issue tracker แบบ GitHub
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md) — issue tracker แบบ GitLab
- [issue-tracker-local.md](./issue-tracker-local.md) — issue tracker แบบ local markdown
- [triage-labels.md](./triage-labels.md) — mapping ของ label (เฉพาะเมื่อติดตั้ง `triage`)
- [domain.md](./domain.md) — กติกาฝั่งผู้อ่าน domain doc + layout

สำหรับ issue tracker แบบ "อื่น ๆ" ให้เขียน `docs/agents/issue-tracker.md` ขึ้นใหม่ทั้งหมดจากคำอธิบายของ user

### 5. เสร็จ

บอก user ว่าการตั้งค่าเสร็จแล้ว และ engineering skills ตัวไหนบ้างที่จะอ่านจากไฟล์เหล่านี้ บอกด้วยว่าทีหลังสามารถแก้ `docs/agents/*.md` ตรง ๆ ได้เลย — ต้องรัน skill นี้ซ้ำก็ต่อเมื่ออยากเปลี่ยน issue tracker หรืออยากเริ่มใหม่ทั้งหมด
