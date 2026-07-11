---
name: setup-ts-deep-modules
description: ติดตั้ง dependency-cruiser เข้า repo TypeScript เพื่อให้แต่ละ package เป็น deep module — implementation ถูกซ่อนไว้ใน subfolder เข้าถึงได้ผ่านไฟล์ entry point เท่านั้น สำหรับให้ user เรียกใช้เอง
disable-model-invocation: true
---

# Setup TS Deep Modules

ทำให้ทุก package ใน repo นี้เป็น **deep module** (module ที่มีพฤติกรรมเยอะแต่ interface เล็ก): พฤติกรรมจำนวนมากอยู่หลัง interface ที่เล็ก พื้นผิว public ของ package คือ **entry points** ของมัน — ไฟล์ที่อยู่ที่ root ของ package — และทุกอย่างใน subfolder ถูกซ่อนไว้ skill นี้ติดตั้ง [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) พร้อมกฎที่ทำให้ entry points เป็นทางเข้าเดียว แล้วพิสูจน์ว่ากฎกัดจริง

สำหรับ vocabulary (deep module, interface, seam, depth) ให้รัน skill `/codebase-design` — และใช้ภาษาของมันตลอดทั้งงาน

## รูปทรงที่กฎนี้บังคับ

```
src/packages/
  <name>/
    index.ts        ← an entry point (public). Import this from outside.
    client.ts       ← another entry point. Packages may expose SEVERAL.
    lib/            ← implementation: hidden from outside, free to import each other.
    tests/          ← co-located tests + fixtures (a subfolder, so private).
```

พื้นผิว public คือ**ไฟล์ที่ root** ของ package — ไม่ใช่ `index.ts` ตัวใดตัวหนึ่งที่ถูกกำหนดไว้ ตามธรรมเนียมแล้ว implementation อยู่ใน `lib/` และ test อยู่ใน `tests/` ทำให้ทุก package มีรูปทรงสองโฟลเดอร์เหมือนกันหมด แต่ตัวกฎเองเป็นกฎทั่วไป: *อะไรก็ตาม*ใน subfolder *ไหนก็ตาม*ถือเป็น private ดังนั้นคุณไม่ต้องไปขยาย config เพื่อเพิ่มโฟลเดอร์เลย

กฎสี่ข้อ ทั้งหมดเป็นระดับ `error`:

1. **Entry-point boundary** — code ที่อยู่นอก package (app code หรือ package อื่น) import ได้เฉพาะ entry points ของ package นั้น (ไฟล์ที่ root) ห้ามแตะอะไรใน subfolder เด็ดขาด
2. **Intra-package freedom** — ไฟล์ภายใน package เดียวกัน import กันเองได้อย่างอิสระ
3. **Tests through the entry points** — ไฟล์ใต้ `<pkg>/tests/` import ได้เฉพาะ entry points ของ package ใด ๆ กับ fixture ใน `tests/` ของตัวเอง แต่ห้าม import internals ใน subfolder ของ package ไหนทั้งนั้น (แม้แต่ของตัวเอง) integration test ข้าม package ทำได้ แต่ deep import ทำไม่ได้
4. **No cycles** — ห้ามมี dependency cycle

**Entry points ไม่ใช่ barrel** เพราะพื้นผิว public คือไฟล์ root *ทุกไฟล์* package หนึ่งจึงเปิด entry point เล็ก ๆ ได้หลายตัว (`index.ts`, `client.ts`, `server.ts`) แทนที่จะบีบทุกอย่างผ่าน `index.ts` ยักษ์ตัวเดียว barrel file ที่ re-export ทั้ง subtree เป็นสิ่งที่ไม่แนะนำ — ทำ entry point ให้เล็กและซ่อน implementation ไว้ใน subfolder

ส่วนเรื่อง layering (package ไหนพึ่ง package ไหนได้บ้าง) เป็น*คนละ*ประเด็น และถูกทิ้งไว้เป็น stub แบบ comment ใน config ให้ repo นี้เติมเอง

## ขั้นตอน

### 1. ตรวจสภาพแวดล้อม

- **Package manager** — `pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, `bun.lockb` → bun, นอกนั้นคือ npm ใช้ตัวที่ตรวจพบกับทุกคำสั่งด้านล่าง (`pnpm`/`yarn`/`npm run`/`bunx`)
- **Packages root** — ถ้ามี `src/` ให้ใช้ `src/packages` ไม่งั้นใช้ `packages` ถ้า repo มีธรรมเนียมอื่นที่ชัดเจนอยู่แล้ว ให้ยืนยันตัวเลือกกับ user ก่อน
- **Config เดิม** — เช็คว่ามีไฟล์ `.dependency-cruiser.*` อยู่ไหม ถ้ามี **ห้าม**เขียนทับ: ให้ merge กฎสี่ข้อกับ options เข้าไป แล้วบอก user ว่าเพิ่มอะไรไปบ้าง

**เสร็จเมื่อ:** รู้ครบทั้ง package manager, packages root และสถานะของ config เดิม

### 2. ติดตั้ง dependency-cruiser

ติดตั้ง `dependency-cruiser` เป็น devDependency ด้วย package manager ที่ตรวจพบ

**เสร็จเมื่อ:** `dependency-cruiser` อยู่ใน `devDependencies`

### 3. เขียน config

คัดลอก [`dependency-cruiser.config.cjs`](./dependency-cruiser.config.cjs) ไปไว้ที่ root ของ repo ในชื่อ `.dependency-cruiser.cjs` ตั้ง `PACKAGES_ROOT` เป็น root ที่ตรวจพบในขั้น 1 ตัวกฎอิงจากความลึกของ path และไม่ผูกกับนามสกุลไฟล์ จึงไม่ต้องปรับอะไรอย่างอื่นอีก

**เสร็จเมื่อ:** `.dependency-cruiser.cjs` มีอยู่จริงพร้อม `PACKAGES_ROOT` ที่ถูกต้อง และมีกฎ forbidden ครบทั้งสี่ข้อ

### 4. ต่อเข้ากับชุด check

- เพิ่ม script `lint:boundaries`: `depcruise <packages-root>` (หรือ `depcruise src`)
- พับมันเข้าไปในคำสั่ง check รวมของ repo — ตัวที่รัน typecheck อยู่แล้ว (เช่น script `check` / `ci` / `validate`) **ห้าม**แตะ `tsconfig` หรือเพิ่ม path alias
- ถ้าไม่มี script รวม ให้เพิ่ม `lint:boundaries` แล้วบอก user ให้เอาไปใส่ใน CI

**เสร็จเมื่อ:** `lint:boundaries` มีอยู่และรันเป็นส่วนหนึ่งของคำสั่งเดียวกับ typecheck

### 5. Scaffold package ตัวอย่าง

สร้าง `<packages-root>/example/` แบบ commit เข้า repo เป็น template ให้คัดลอกไปใช้:

- `index.ts` — entry point ตัวหนึ่ง export ฟังก์ชันหนึ่งตัวที่ delegate ไปยังไฟล์ภายใน (เพื่อให้เห็นชัดว่า package นี้ *deep* จริง ไม่ใช่แค่ pass-through)
- `lib/impl.ts` — ไฟล์ภายในที่อยู่ใน **subfolder** ถูก import โดย `index.ts` และเข้าถึงจากภายนอกไม่ได้
- `tests/example.test.ts` — import **เฉพาะ** `../index` (entry point) และ assert กับฟังก์ชัน public

บอก user ว่านี่คือ template ตั้งต้นไว้คัดลอกหรือลบทิ้งก็ได้

**เสร็จเมื่อ:** package ตัวอย่างมีอยู่จริง เปิดพฤติกรรมผ่าน entry point ที่ root และซ่อน `impl` ไว้ใน subfolder

### 6. พิสูจน์ว่ากฎกัดจริง

นี่คือเกณฑ์ความสำเร็จของ skill ทั้งตัว — config ที่ไม่ fail ตอนมีการละเมิดคือของไร้ค่า

1. รัน `lint:boundaries` มันต้อง**ผ่าน**บน example ที่สะอาด
2. เพิ่ม deep import ชั่วคราวเข้าไปใน `tests/example.test.ts` (เช่น `import { thing } from "../lib/impl"`) แล้วรัน `lint:boundaries` อีกครั้ง — มันต้อง **fail** ด้วยกฎ `tests-through-entrypoints`
3. เอา deep import ออก รันอีกรอบ — มันต้อง**ผ่าน**

**เสร็จเมื่อ:** คุณเห็นด้วยตาว่าผ่าน แล้ว fail บน deep import แล้วกลับมาผ่านอีกครั้ง ถ้าขั้น 2 ไม่ fail แปลว่ากฎยังต่อไม่ถูก — แก้ให้เสร็จก่อนจบงาน

### 7. บันทึกธรรมเนียมเป็นเอกสาร

เขียน `README.md` **ในโฟลเดอร์ packages** (`<packages-root>/README.md`) — วางไว้ข้าง ๆ packages ที่มันกำกับ — ครอบคลุม: layout แบบ `src/packages/<name>/` (entry points ที่ root, `lib/` สำหรับ implementation, `tests/` สำหรับ test), กติกา "import ผ่าน entry points ของ package (ไฟล์ที่ root) เท่านั้น" และวิธีรัน `lint:boundaries` **ระบุชัด ๆ ว่าไม่แนะนำ barrel file** — ให้เปิด entry point เล็ก ๆ หลายตัวแทนการ re-export ทั้ง subtree ผ่าน index ตัวเดียว เขียนให้กระชับ: snippet ให้คัดลอก บวกกฎสี่ข้อ ข้อละหนึ่งย่อหน้า

จากนั้นเพิ่ม **context pointer** ชี้ไปหามันจากไฟล์ agent-instructions ของ repo — `CLAUDE.md` ถ้ามี ไม่งั้นใช้ `AGENTS.md` (สร้าง `AGENTS.md` ถ้าไม่มีทั้งคู่) บรรทัดเดียวก็พอ เช่น `Packages are deep modules — see [src/packages/README.md](./src/packages/README.md) before adding or importing one.` นี่แหละคือสิ่งที่ทำให้ agent ค้นพบกฎ boundary เอง แทนที่จะเดินสะดุดมัน

**เสร็จเมื่อ:** `<packages-root>/README.md` มีอยู่จริงและไม่แนะนำ barrel และ `CLAUDE.md`/`AGENTS.md` ของ repo มีลิงก์ไปหามัน

## หมายเหตุ

- back-reference `$1` ใน config (การ match แบบ group ของ dependency-cruiser) คือสิ่งที่ทำให้ package เข้าถึง internals ของตัวเองได้ในขณะที่คนนอกทำไม่ได้ — อย่าคลี่มันออกเป็นกฎแยกรายต่อ package
- public กับ private ตัดสินด้วย**ความลึก**: ไฟล์ที่ root ของ package คือ entry points ส่วนอะไรก็ตามใน subfolder คือ private โฟลเดอร์ตามธรรมเนียมคือ `lib/` (implementation) กับ `tests/` แต่กฎไม่ได้ hardcode ชื่อพวกนี้ — subfolder ไหนก็ private หมด ดังนั้นเพิ่มโฟลเดอร์ใหม่ก็ไม่ต้องแก้ config เลย ส่วนการเพิ่ม entry point ก็แค่เพิ่มไฟล์ที่ root — ไม่ต้องมี barrel
- Packages เป็นแบบ**แบน**: มีลูกชั้นเดียวใต้ root โดยตรง internals ของ package จะซ้อนลึกแค่ไหนก็ได้ แต่ package ห้ามมี package อื่นซ้อนอยู่ข้างใน
- ใช้ `.cjs` (ไม่ใช่ `.js`) เพื่อให้ `module.exports` ของ config ทำงานได้แม้ใน repo ที่เป็น `"type": "module"`
