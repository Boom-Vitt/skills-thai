---
name: to-tickets
description: แตก plan, spec หรือบทสนทนาปัจจุบันออกเป็นชุด ticket แบบ tracer bullet โดยแต่ละใบประกาศ blocking edges ของตัวเอง แล้ว publish ไปยัง tracker ที่ config ไว้ — edges เป็นข้อความในไฟล์แยกหนึ่งไฟล์ต่อ ticket ถ้าเก็บ local หรือเป็น blocking link แบบ native บน tracker จริง
disable-model-invocation: true
---

# To Tickets

แตก plan, spec หรือบทสนทนาออกเป็นชุด **ticket** — vertical slice แบบ tracer bullet (กระสุนส่องวิถี: เส้นทางแคบ ๆ ที่ทะลุครบทุก layer) โดยแต่ละใบประกาศว่ามี ticket ไหน **block** มันอยู่บ้าง

issue tracker และชุดคำศัพท์ label สำหรับ triage ควรถูกส่งมาให้แล้ว — ถ้ายังไม่มี ให้รัน `/setup-matt-pocock-skills`

## ขั้นตอน

### 1. รวบรวม context

ทำงานจากสิ่งที่อยู่ใน context ของบทสนทนาอยู่แล้ว ถ้า user ส่ง reference มาเป็น argument (path ของ spec, เลข issue หรือ URL) ให้ fetch มาแล้วอ่านทั้ง body และ comment ให้ครบ

### 2. สำรวจ codebase (optional)

ถ้ายังไม่ได้สำรวจ codebase ให้สำรวจก่อนเพื่อเข้าใจสถานะปัจจุบันของ code ชื่อและคำอธิบายของ ticket ควรใช้คำศัพท์จาก domain glossary ของโปรเจกต์ และเคารพ ADR ในบริเวณที่กำลังจะไปแตะ

มองหาโอกาส prefactor code เพื่อให้การ implement ง่ายขึ้น "ทำให้การเปลี่ยนแปลงเป็นเรื่องง่ายก่อน แล้วค่อยลงมือเปลี่ยนแบบง่าย ๆ"

### 3. ร่าง vertical slice

แตกงานออกเป็น ticket แบบ **tracer bullet**

<vertical-slice-rules>

- แต่ละ slice ตัดเส้นทางที่แคบแต่ครบทุก layer (schema, API, UI, tests) — เป็นแนวตั้ง ไม่ใช่ slice แนวนอนของ layer เดียว
- slice ที่เสร็จแล้วต้อง demo หรือ verify ได้ด้วยตัวมันเอง
- แต่ละ slice มีขนาดพอดีกับ context window ใหม่หนึ่งอัน
- ถ้ามี prefactoring อะไร ให้ทำก่อนเป็นอย่างแรก

</vertical-slice-rules>

ให้แต่ละ ticket มี **blocking edges** ของมัน — ticket อื่นที่ต้องเสร็จก่อนใบนี้ถึงจะเริ่มได้ ticket ที่ไม่มีตัว block เริ่มได้ทันที

**Wide refactor คือข้อยกเว้นของ vertical slicing** **wide refactor** คือการเปลี่ยนแบบ mechanical หนึ่งอย่าง — rename column, เปลี่ยน type ของ symbol ที่ใช้ร่วมกัน — ที่ **blast radius** (รัศมีความเสียหาย) กวาดไปทั่วทั้ง codebase จนการแก้จุดเดียวพังจุดเรียกใช้เป็นพัน ๆ ที่พร้อมกัน และไม่มี vertical slice ไหน land แบบเขียวได้ อย่าฝืนยัดมันให้เป็น tracer bullet ให้เรียงลำดับเป็น **expand–contract** แทน ขั้นแรก expand: เพิ่มรูปแบบใหม่ไว้ข้าง ๆ ของเก่าเพื่อไม่ให้อะไรพัง จากนั้น migrate จุดเรียกใช้ทีละ batch โดยกำหนดขนาด batch ตาม blast radius (ต่อ package, ต่อ directory) แต่ละ batch เป็น ticket ของตัวเองที่ถูก block โดย ticket expand และ CI จะยังเขียวจาก batch หนึ่งไปอีก batch เพราะรูปแบบเก่ายังอยู่ สุดท้าย contract: ลบรูปแบบเก่าทิ้งเมื่อไม่เหลือ caller แล้ว ใน ticket ที่ถูก block โดย migrate batch ทุกอัน ถ้าแม้แต่ batch เดี่ยว ๆ ก็ยังเขียวไม่ได้ ให้คงลำดับเดิมไว้แต่ให้พวกมันแชร์ integration branch เดียวกัน โดยทุกอัน block ticket integrate-and-verify ตัวสุดท้าย — ความเขียวถูกสัญญาไว้ที่ ticket นั้นที่เดียว

### 4. ซักถาม user

เสนอ breakdown ที่ร่างไว้เป็น numbered list สำหรับแต่ละ ticket ให้โชว์:

- **Title**: ชื่อสั้น ๆ ที่สื่อความ
- **Blocked by**: มี ticket ไหนบ้าง (ถ้ามี) ที่ต้องเสร็จก่อน
- **What it delivers**: พฤติกรรม end-to-end ที่ ticket นี้ทำให้ใช้งานได้จริง

ถาม user ว่า:

- granularity กำลังดีไหม? (หยาบไป / ละเอียดไป)
- blocking edges ถูกต้องหรือเปล่า — แต่ละ ticket depend เฉพาะ ticket ที่ gate มันจริง ๆ ใช่ไหม?
- มี ticket ไหนควรถูก merge รวมกัน หรือควร split ให้ย่อยกว่านี้ไหม?

วนปรับไปเรื่อย ๆ จนกว่า user จะ approve breakdown

### 5. Publish ticket ไปยัง tracker ที่ config ไว้

publish ticket ที่ approve แล้ว **วิธีการ**ขึ้นอยู่กับ tracker ที่ `/setup-matt-pocock-skills` config ไว้ — ตัว ticket เหมือนกันทั้งสองแบบ ต่างกันแค่รูปร่างของ blocking edges:

- **ไฟล์ local** → เขียนหนึ่งไฟล์ต่อหนึ่ง ticket ไว้ที่ `.scratch/<feature-slug>/issues/<NN>-<slug>.md` รันเลขเริ่มจาก `01` ตามลำดับ dependency (ตัว block มาก่อน) ช่อง "Blocked by" ของแต่ละไฟล์ list เลข/ชื่อของ ticket ที่มัน depend ใช้ template ต่อ ticket ด้านล่าง — หนึ่ง ticket ต่อหนึ่งไฟล์ ห้ามรวมทุกอย่างเป็นไฟล์เดียว
- **Issue tracker จริง (GitHub, Linear, …)** → publish หนึ่ง issue ต่อหนึ่ง ticket ตามลำดับ dependency (ตัว block มาก่อน) เพื่อให้ blocking edges ของแต่ละ ticket อ้างถึง identifier จริงได้ ใช้ความสัมพันธ์ blocking / sub-issue แบบ native ของ platform ถ้ามี ไม่งั้นตั้ง "Blocked by" ของแต่ละ ticket เป็น issue ที่ block มัน ติด label triage `ready-for-agent` เว้นแต่จะถูกสั่งเป็นอย่างอื่น — ticket พวกนี้ถูกออกแบบมาให้ agent หยิบไปทำได้อยู่แล้วโดย construction

ทำงานที่ **frontier**: ticket ใดก็ตามที่ตัว block ของมันเสร็จครบแล้ว ถ้าเป็น chain แบบเส้นตรงล้วน ๆ ก็คือไล่จากบนลงล่าง

ห้ามปิดหรือแก้ parent issue ใด ๆ

<local-ticket-template>

# <NN> — <Ticket title>

**What to build:** พฤติกรรม end-to-end ที่ ticket นี้ทำให้ใช้งานได้จริง จากมุมมองของ user — ไม่ใช่ list ขั้นตอน implement ทีละ layer

**Blocked by:** เลข/ชื่อของ ticket ที่ gate ใบนี้ หรือ "None — can start immediately"

**Status:** ready-for-agent

- [ ] เกณฑ์ acceptance ข้อ 1
- [ ] เกณฑ์ acceptance ข้อ 2

</local-ticket-template>

<issue-template>

## Parent

reference ไปยัง parent issue บน tracker (ใส่เมื่อ source เป็น issue ที่มีอยู่แล้ว ไม่งั้นตัด section นี้ทิ้ง)

## What to build

พฤติกรรม end-to-end ที่ ticket นี้ทำให้ใช้งานได้จริง จากมุมมองของ user — ไม่ใช่การ implement ทีละ layer

## Acceptance criteria

- [ ] เกณฑ์ข้อ 1
- [ ] เกณฑ์ข้อ 2

## Blocked by

- reference ไปยัง ticket แต่ละใบที่ block ใบนี้ หรือ "None — can start immediately"

</issue-template>

ไม่ว่าจะรูปแบบไหน ให้เลี่ยง file path หรือ code snippet แบบเจาะจง — ของพวกนี้เก่าเร็วมาก ข้อยกเว้น: ถ้า prototype ผลิต snippet ที่ encode การตัดสินใจได้แม่นยำกว่าที่ prose ทำได้ (state machine, reducer, schema, รูปทรงของ type) ให้แปะ inline แล้ว note สั้น ๆ ว่ามันมาจาก prototype ตัดให้เหลือเฉพาะส่วนที่อัดแน่นด้วยการตัดสินใจ — ไม่ใช่ demo ที่รันได้ เอาแค่ส่วนที่สำคัญ

ทำงานที่ frontier ทีละ ticket ด้วย `/implement` โดยเคลียร์ context ระหว่าง ticket
