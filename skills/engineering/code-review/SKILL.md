---
name: code-review
description: รีวิวการเปลี่ยนแปลงตั้งแต่จุดอ้างอิงคงที่ (commit, branch, tag หรือ merge-base) ตามสองแกน — Standards (code ทำตาม coding standards ที่ repo นี้บันทึกไว้ไหม?) และ Spec (code ตรงกับสิ่งที่ issue/PRD ต้นทางขอไหม?) รันรีวิวทั้งสองแกนใน sub-agent ที่ทำงานขนานกันแล้วรายงานเทียบเคียงกัน ใช้เมื่อ user อยากรีวิว branch, PR, งานที่ยังทำค้างอยู่ หรือพูดว่า "review since X"
---

รีวิวสองแกนบน diff ระหว่าง `HEAD` กับจุดอ้างอิงคงที่ที่ user ระบุมา:

- **Standards** — code ทำตาม coding standards ที่ repo นี้บันทึกไว้เป็นเอกสารหรือเปล่า?
- **Spec** — code implement ตาม issue / PRD / spec ต้นทางอย่างซื่อตรงหรือเปล่า?

ทั้งสองแกนรันเป็น **sub-agent ขนานกัน** เพื่อไม่ให้ context ของกันและกันปนกัน แล้ว skill นี้ค่อยรวมผลที่แต่ละฝั่งเจอ

ข้อมูล issue tracker ควรถูกส่งมาให้คุณแล้ว — ถ้าไม่มี `docs/agents/issue-tracker.md` ให้รัน `/setup-matt-pocock-skills`

## ขั้นตอน

### 1. ปักหมุดจุดอ้างอิง

จุดอ้างอิงคือสิ่งที่ user บอกมา — commit SHA, ชื่อ branch, tag, `main`, `HEAD~5` ฯลฯ ถ้าไม่ได้ระบุมา ให้ถาม

เก็บคำสั่ง diff ไว้ครั้งเดียว: `git diff <fixed-point>...HEAD` (สามจุด เพื่อให้เทียบกับ merge-base) และจดรายการ commit ผ่าน `git log <fixed-point>..HEAD --oneline` ด้วย

ก่อนไปต่อ ยืนยันว่าจุดอ้างอิง resolve ได้จริง (`git rev-parse <fixed-point>`) และ diff ไม่ว่างเปล่า ref ที่ผิดหรือ diff ว่างควร fail ตรงนี้ — ไม่ใช่ไปตายอยู่ใน sub-agent สองตัวที่รันขนานกัน

### 2. ระบุแหล่งที่มาของ spec

หา spec ต้นทางตามลำดับนี้:

1. การอ้างถึง issue ใน commit message (`#123`, `Closes #45`, GitLab `!67` ฯลฯ) — ดึงข้อมูลตาม workflow ใน `docs/agents/issue-tracker.md`
2. path ที่ user ส่งมาเป็น argument
3. ไฟล์ PRD/spec ใต้ `docs/`, `specs/` หรือ `.scratch/` ที่ชื่อตรงกับ branch หรือ feature
4. ถ้าไม่เจออะไรเลย ให้ถาม user ว่า spec อยู่ไหน ถ้าเขาบอกว่าไม่มี sub-agent ฝั่ง **Spec** จะข้ามและรายงานว่า "no spec available"

### 3. ระบุแหล่งที่มาของ standards

อะไรก็ตามใน repo ที่บันทึกไว้ว่า code ควรเขียนยังไง เช่น `CODING_STANDARDS.md` หรือ `CONTRIBUTING.md`

นอกเหนือจากสิ่งที่ repo บันทึกไว้ แกน Standards จะพก **smell baseline** ด้านล่างติดตัวเสมอ — ชุด code smell ของ Fowler ที่ตายตัว (_Refactoring_ บทที่ 3) ซึ่งใช้ได้แม้ repo ไม่ได้บันทึกอะไรไว้เลย มีกติกาสองข้อกำกับ:

- **Repo ชนะเสมอ** standard ที่ repo บันทึกไว้ชนะทุกครั้ง; จุดไหนที่ repo รับรองสิ่งที่ baseline จะ flag ให้กด smell นั้นทิ้งไป
- **เป็น judgement call เสมอ** แต่ละ smell เป็น heuristic ที่ติดป้ายกำกับ ("possible Feature Envy") ไม่ใช่การละเมิดแบบฟันธง — และเช่นเดียวกับ standard อื่นในที่นี้ ให้ข้ามอะไรก็ตามที่ tooling บังคับให้อยู่แล้ว

แต่ละ smell อ่านเป็น *มันคืออะไร* → *แก้ยังไง* เอาไปเทียบกับ diff:

- **Mysterious Name** — function, ตัวแปร หรือ type ที่ชื่อไม่บอกว่ามันทำอะไรหรือเก็บอะไร → เปลี่ยนชื่อ; ถ้าคิดชื่อที่ตรงไปตรงมาไม่ออก แปลว่า design ยังขุ่นมัวอยู่
- **Duplicated Code** — logic รูปทรงเดียวกันโผล่ในมากกว่าหนึ่ง hunk หรือหนึ่งไฟล์ของการเปลี่ยนแปลงนี้ → extract รูปทรงที่ใช้ร่วมออกมา แล้วเรียกจากทั้งสองที่
- **Feature Envy** — method ที่ล้วงข้อมูลของ object อื่นมากกว่าของตัวเอง → ย้าย method ไปอยู่กับข้อมูลที่มันอิจฉา
- **Data Clumps** — field หรือ param ไม่กี่ตัวเดิม ๆ ที่เดินทางไปด้วยกันตลอด (type ที่รอวันได้เกิด) → มัดรวมเป็น type เดียว แล้วส่งอันนั้นแทน
- **Primitive Obsession** — primitive หรือ string ที่ยืนแทน concept ของ domain ซึ่งสมควรมี type ของตัวเอง → ให้ concept นั้นมี type เล็ก ๆ ของตัวเอง
- **Repeated Switches** — `switch`/`if`-cascade แบบเดียวกันบน type เดียวกันโผล่ซ้ำหลายจุดในการเปลี่ยนแปลง → แทนด้วย polymorphism หรือ map เดียวที่ทั้งสองจุดใช้ร่วมกัน
- **Shotgun Surgery** — การเปลี่ยนแปลงเชิง logic เรื่องเดียวบังคับให้ต้องแก้กระจัดกระจายหลายไฟล์ใน diff → รวบสิ่งที่ต้องเปลี่ยนพร้อมกันมาไว้ใน module เดียว
- **Divergent Change** — ไฟล์หรือ module เดียวถูกแก้ด้วยเหตุผลหลายเรื่องที่ไม่เกี่ยวกัน → แยกออกให้แต่ละ module เปลี่ยนด้วยเหตุผลเดียว
- **Speculative Generality** — abstraction, parameter หรือ hook ที่เพิ่มมาเผื่อความต้องการที่ spec ไม่มี → ลบทิ้ง; inline กลับไปจนกว่าจะมีความต้องการจริงโผล่มา
- **Message Chains** — การไล่สายเรียกยาว ๆ แบบ `a.b().c().d()` ที่ฝั่งเรียกไม่ควรต้องผูกติดด้วย → ซ่อนการไล่สายไว้หลัง method เดียวบน object ตัวแรก
- **Middle Man** — class หรือ function ที่แทบจะเอาแต่ delegate ต่อให้คนอื่น → ตัดทิ้ง แล้วเรียกเป้าหมายจริงตรง ๆ
- **Refused Bequest** — subclass หรือ implementer ที่เมินหรือ override สิ่งที่สืบทอดมาแทบทั้งหมด → เลิกใช้ inheritance แล้วใช้ composition แทน

### 4. Spawn sub-agent ทั้งสองตัวขนานกัน

ส่งข้อความเดียวที่มี `Agent` tool call สองครั้ง ใช้ subagent แบบ `general-purpose` ทั้งคู่

**Prompt ของ Standards sub-agent** — ใส่:

- คำสั่ง diff เต็ม ๆ และรายการ commit
- รายการไฟล์แหล่ง standards ที่เจอในขั้นที่ 3 **บวก smell baseline จากขั้นที่ 3** แปะมาทั้งก้อน — sub-agent ไม่มีช่องทางอื่นในการเข้าถึงมัน
- โจทย์: "รายงาน — แยกตามไฟล์/hunk ตรงจุดที่เกี่ยว — (a) ทุกจุดที่ diff ละเมิด standard ที่บันทึกไว้: อ้างอิง standard (ไฟล์ + ตัวกติกา) และ (b) baseline smell ใดก็ตามที่เจอ: ระบุชื่อและ quote ตัว hunk แยกให้ชัดระหว่างการละเมิดแบบฟันธงกับ judgement call — การละเมิด standard ที่บันทึกไว้ฟันธงได้ แต่ baseline smell เป็น judgement call เสมอ และ standard ที่ repo บันทึกไว้ชนะ baseline ทุกครั้ง ข้ามอะไรก็ตามที่ tooling บังคับให้อยู่แล้ว ไม่เกิน 400 คำ"

**Prompt ของ Spec sub-agent** — ใส่:

- คำสั่ง diff และรายการ commit
- path หรือเนื้อหาของ spec ที่ดึงมา
- โจทย์: "รายงาน: (a) requirement ที่ spec ขอแต่ยังขาดหรือทำไม่ครบ (b) พฤติกรรมใน diff ที่ไม่มีใครขอ (scope creep) (c) requirement ที่ดูเหมือน implement แล้วแต่ implementation ดูผิด quote บรรทัดของ spec ประกอบทุกข้อที่เจอ ไม่เกิน 400 คำ"

ถ้าไม่มี spec ให้ข้าม Spec sub-agent แล้วระบุไว้ในรายงานสุดท้าย

### 5. รวมผล

นำเสนอรายงานทั้งสองใต้หัวข้อ `## Standards` และ `## Spec` แบบคำต่อคำหรือเกลาเบา ๆ **ห้าม** merge หรือจัดอันดับผลใหม่ — สองแกนนี้ตั้งใจแยกจากกัน (ดู _ทำไมต้องสองแกน_)

จบด้วยสรุปบรรทัดเดียว: จำนวนสิ่งที่เจอต่อแกน และปัญหาที่หนักสุด _ภายในแต่ละแกน_ (ถ้ามี) อย่าเลือกผู้ชนะตัวเดียวข้ามแกน — นั่นแหละคือการจัดอันดับใหม่ที่การแยกแกนมีไว้ป้องกัน

## ทำไมต้องสองแกน

การเปลี่ยนแปลงหนึ่งอาจผ่านแกนหนึ่งแต่ตกอีกแกน:

- code ที่ทำตามทุก standard แต่ implement ผิดเรื่อง → **Standards ผ่าน, Spec ตก**
- code ที่ทำตรงตามที่ issue ขอเป๊ะแต่แหก convention ของโปรเจกต์ → **Spec ผ่าน, Standards ตก**

การรายงานแยกกันทำให้แกนหนึ่งไม่บดบังอีกแกน
