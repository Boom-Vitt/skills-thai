<p>
  <a href="https://www.aihero.dev/s/skills-newsletter">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skills-repo-dark_2x.png">
      <source media="(prefers-color-scheme: light)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png">
      <img alt="Skills" src="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png" width="369">
    </picture>
  </a>
</p>

# Skills สำหรับวิศวกรตัวจริง

> [!NOTE]
> repo นี้คือฉบับแปลภาษาไทยของ [mattpocock/skills](https://github.com/mattpocock/skills) โดย [Matt Pocock](https://github.com/mattpocock) ซึ่งเผยแพร่ภายใต้ [MIT license](./LICENSE) — แปลโดย Boom ([@Boom-Vitt](https://github.com/Boom-Vitt)) เนื้อหาต้นฉบับและเครดิตทั้งหมดเป็นของ Matt Pocock

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

นี่คือ agent skills ที่ผมใช้ทำงานวิศวกรรมซอฟต์แวร์จริง ๆ ทุกวัน — ไม่ใช่ vibe coding

การพัฒนาแอปพลิเคชันจริงเป็นเรื่องยาก แนวทางอย่าง GSD, BMAD และ Spec-Kit พยายามช่วยด้วยการเข้ามาคุมกระบวนการแทนเรา แต่ระหว่างนั้นมันก็พรากการควบคุมไปจากเรา และทำให้ bug ที่เกิดขึ้นในกระบวนการแก้ได้ยาก

skills ชุดนี้ถูกออกแบบมาให้เล็ก ปรับแต่งง่าย และประกอบเข้าด้วยกันได้ ใช้ได้กับทุกโมเดล และกลั่นมาจากประสบการณ์ด้านวิศวกรรมหลายสิบปี ลองแฮ็กเล่นดู ปรับให้เป็นของคุณเอง ขอให้สนุก

ถ้าอยากติดตามการเปลี่ยนแปลงของ skills เหล่านี้ รวมถึง skills ใหม่ ๆ ที่ Matt สร้างขึ้น เข้าร่วม newsletter กับ dev อีกราว 60,000 คนได้ที่:

[สมัครรับ Newsletter](https://www.aihero.dev/s/skills-newsletter)

## เริ่มต้นใช้งาน (setup ใน 30 วินาที)

1. รัน installer ของ skills.sh:

```bash
npx skills@latest add Boom-Vitt/skills-thai
```

(หรือติดตั้งฉบับภาษาอังกฤษต้นฉบับด้วย `npx skills@latest add mattpocock/skills`)

2. เลือก skills ที่ต้องการ และเลือกว่าจะติดตั้งลง coding agent ตัวไหนบ้าง **อย่าลืมเลือก `/setup-matt-pocock-skills` ด้วย**

3. รัน `/setup-matt-pocock-skills` ใน agent ของคุณ มันจะ:
   - ถามว่าคุณอยากใช้ issue tracker ตัวไหน (GitHub, Linear หรือไฟล์ local)
   - ถามว่าคุณติด label อะไรให้ ticket ตอน triage บ้าง (`/triage` ใช้ label เหล่านี้)
   - ถามว่าอยากเก็บเอกสารที่เราสร้างขึ้นไว้ที่ไหน

4. เท่านี้ก็พร้อมใช้งานแล้ว

## ทำไม Skills เหล่านี้ถึงเกิดขึ้น

ผมสร้าง skills เหล่านี้ขึ้นมาเพื่อแก้ failure mode ที่เจอบ่อย ๆ เวลาใช้ Claude Code, Codex และ coding agent ตัวอื่น ๆ

### #1: Agent ไม่ได้ทำสิ่งที่เราต้องการ

> "ไม่มีใครรู้แน่ชัดหรอกว่าตัวเองต้องการอะไร"
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**ปัญหา** failure mode ที่พบบ่อยที่สุดในการพัฒนาซอฟต์แวร์คือความเข้าใจที่ไม่ตรงกัน คุณคิดว่า dev รู้ว่าคุณต้องการอะไร แต่พอเห็นสิ่งที่เขาสร้างออกมา — ถึงได้รู้ว่าเขาไม่เข้าใจคุณเลย

ยุค AI ก็ไม่ต่างกัน ระหว่างคุณกับ agent มีช่องว่างในการสื่อสารอยู่ วิธีแก้คือ **grilling session** — การให้ agent ซักถามคุณอย่างละเอียดว่ากำลังจะสร้างอะไร

**วิธีแก้** คือใช้:

- [`/grill-me`](./skills/productivity/grill-me/SKILL.md) - สำหรับงานที่ไม่ใช่ code
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) - เหมือน [`/grill-me`](./skills/productivity/grill-me/SKILL.md) แต่แถมของดีมาให้อีก (ดูด้านล่าง)

สองตัวนี้คือ skills ที่ได้รับความนิยมมากที่สุด มันช่วยให้คุณกับ agent เข้าใจตรงกันก่อนเริ่มงาน และคิดให้ลึกเกี่ยวกับการเปลี่ยนแปลงที่กำลังจะทำ ใช้มัน_ทุกครั้ง_ที่จะแก้อะไรก็ตาม

### #2: Agent พูดเยอะเกินไป

> "เมื่อมี ubiquitous language บทสนทนาระหว่าง developer และสิ่งที่เขียนอยู่ใน code ล้วนมาจาก domain model เดียวกัน"
>
> Eric Evans, [Domain-Driven-Design](https://www.amazon.co.uk/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

**ปัญหา**: ช่วงเริ่มต้นของโปรเจกต์ dev กับคนที่เขาสร้างซอฟต์แวร์ให้ (domain expert) มักพูดกันคนละภาษา

ผมรู้สึกถึงแรงตึงแบบเดียวกันกับ agent ของผม ปกติ agent จะถูกโยนเข้าไปในโปรเจกต์แล้วต้องเดาศัพท์เฉพาะเอาเองระหว่างทาง ผลคือมันใช้ 20 คำในที่ที่คำเดียวก็พอ

**วิธีแก้** คือภาษากลางที่ใช้ร่วมกัน (shared language) — เอกสารที่ช่วยให้ agent ถอดรหัสศัพท์เฉพาะที่ใช้ในโปรเจกต์ได้

<details>
<summary>
ตัวอย่าง
</summary>

นี่คือตัวอย่าง [`CONTEXT.md`](https://github.com/mattpocock/course-video-manager/blob/076a5a7a182db0fe1e62971dd7a68bcadf010f1c/CONTEXT.md) จาก repo `course-video-manager` ของ Matt ลองดูว่าแบบไหนอ่านง่ายกว่ากัน

- **ก่อน**: "มีปัญหาตอนที่ lesson ใน section ของ course ถูกทำให้เป็น 'ของจริง' (คือได้ที่อยู่ใน file system)"
- **หลัง**: "มีปัญหากับ materialization cascade"

ความกระชับแบบนี้ให้ผลตอบแทนกลับมา session แล้ว session เล่า

</details>

ความสามารถนี้ถูกฝังอยู่ใน [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) แล้ว — มันคือ grilling session ที่ช่วยคุณสร้างภาษากลางร่วมกับ AI และบันทึกการตัดสินใจที่อธิบายยาก ๆ ลงใน ADR

พลังของเทคนิคนี้อธิบายเป็นคำพูดได้ยาก มันอาจเป็นเทคนิคที่เจ๋งที่สุดใน repo นี้เลยก็ได้ ลองใช้ดูแล้วจะเห็นเอง

> [!TIP]
> ภาษากลางร่วมกันมีประโยชน์มากกว่าแค่ลดความเยิ่นเย้อ:
>
> - **ตัวแปร ฟังก์ชัน และไฟล์ถูกตั้งชื่ออย่างสม่ำเสมอ** ตามภาษากลางนั้น
> - ผลคือ **codebase อ่านและนำทางได้ง่ายขึ้น** สำหรับ agent
> - agent ยัง **ใช้ token ในการคิดน้อยลง** เพราะมีภาษาที่กระชับกว่าให้ใช้

### #3: Code ใช้งานไม่ได้

> "ก้าวทีละก้าวเล็ก ๆ อย่างตั้งใจเสมอ อัตราของ feedback คือขีดจำกัดความเร็วของคุณ อย่ารับงานที่ใหญ่เกินไป"
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**ปัญหา**: สมมติว่าคุณกับ agent เข้าใจตรงกันแล้วว่าจะสร้างอะไร แล้วถ้า agent _ยัง_ ผลิตของห่วยออกมาอยู่ล่ะ?

ถึงเวลาดู feedback loop ของคุณแล้ว ถ้าไม่มี feedback ว่า code ที่มันเขียนรันได้จริงแค่ไหน agent ก็เหมือนบินโดยมองไม่เห็นอะไรเลย

**วิธีแก้**: คุณต้องมี feedback loop ชุดมาตรฐาน: static type, การเข้าถึง browser และ automated test

สำหรับ automated test ลูป red-green-refactor คือหัวใจสำคัญ — agent เขียน test ที่ fail ก่อน แล้วค่อยแก้ให้ test ผ่าน วิธีนี้ทำให้ agent ได้รับ feedback สม่ำเสมอ และได้ code ที่ดีขึ้นมาก

ผมสร้าง **skill [`/tdd`](./skills/engineering/tdd/SKILL.md)** ที่เสียบเข้ากับโปรเจกต์ไหนก็ได้ มันส่งเสริม red-green-refactor และให้แนวทาง agent อย่างละเอียดว่า test ที่ดีและไม่ดีเป็นยังไง

สำหรับการ debug ผมก็สร้าง skill **[`/diagnosing-bugs`](./skills/engineering/diagnosing-bugs/SKILL.md)** ที่ห่อแนวปฏิบัติการ debug ที่ดีที่สุดไว้ในลูปง่าย ๆ

### #4: เราสร้าง Ball Of Mud ขึ้นมา

> "ลงทุนกับการออกแบบระบบ_ทุกวัน_"
>
> Kent Beck, [Extreme Programming Explained](https://www.amazon.co.uk/Extreme-Programming-Explained-Embrace-Change/dp/0321278658)

> "module ที่ดีที่สุดคือ module ที่ลึก — เข้าถึงความสามารถจำนวนมากได้ผ่าน interface ที่เรียบง่าย"
>
> John Ousterhout, [A Philosophy Of Software Design](https://www.amazon.co.uk/Philosophy-Software-Design-2nd/dp/173210221X)

**ปัญหา**: แอปส่วนใหญ่ที่สร้างด้วย agent มักซับซ้อนและแก้ไขยาก เพราะ agent เร่งความเร็วการเขียน code ได้มหาศาล มันจึงเร่ง entropy ของซอฟต์แวร์ไปด้วย codebase ซับซ้อนขึ้นในอัตราที่ไม่เคยมีมาก่อน

**วิธีแก้** คือแนวทางใหม่แบบถอนรากถอนโคนสำหรับการพัฒนาด้วย AI: ใส่ใจกับการออกแบบ code

สิ่งนี้ถูกฝังอยู่ในทุกชั้นของ skills ชุดนี้:

- [`/to-spec`](./skills/engineering/to-spec/SKILL.md) จะซักถามคุณก่อนว่ากำลังแตะ module ไหนบ้าง แล้วค่อยสร้าง spec

และที่สำคัญที่สุด [`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) ช่วยกู้ codebase ที่กลายเป็น ball of mud ไปแล้ว แนะนำให้รันกับ codebase ของคุณทุก ๆ สองสามวัน

### สรุป

พื้นฐานวิศวกรรมซอฟต์แวร์สำคัญกว่าที่เคยเป็นมา skills ชุดนี้คือความพยายามอย่างดีที่สุดของ Matt ในการกลั่นพื้นฐานเหล่านั้นให้เป็นแนวปฏิบัติที่ทำซ้ำได้ เพื่อช่วยให้คุณ ship แอปที่ดีที่สุดในชีวิตการทำงานของคุณ ขอให้สนุก

## รายการอ้างอิง

skills แบ่งตามแกนเดียว — ใครเป็นคนเรียกใช้ได้ **User-invoked** คือ skills ที่เข้าถึงได้เฉพาะตอนคุณพิมพ์เรียกเอง (เช่น `/grill-me`) หน้าที่ของมันคือควบคุมภาพรวม (orchestrate) ส่วน **Model-invoked** คือ skills ที่คุณเรียกก็ได้ _หรือ_ agent หยิบมาใช้เองอัตโนมัติเมื่องานเข้าเงื่อนไข มันเก็บวินัยการทำงานที่นำกลับมาใช้ซ้ำได้ skill แบบ user-invoked เรียกใช้ skill แบบ model-invoked ได้ แต่ห้ามเรียก user-invoked ด้วยกันเอง

### Engineering

skills ที่ Matt ใช้ทำงาน code ทุกวัน

**User-invoked**

- **[ask-matt](./skills/engineering/ask-matt/SKILL.md)** — ถามว่า skill หรือ flow ไหนเหมาะกับสถานการณ์ของคุณ เป็น router ครอบ skills แบบ user-invoked ทั้งหมดใน repo นี้
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)** — grilling session ที่ช่วยสร้าง domain model ของโปรเจกต์ไปด้วย ลับคมศัพท์เฉพาะ และอัปเดต `CONTEXT.md` กับ ADR ไปพร้อมกัน
- **[triage](./skills/engineering/triage/SKILL.md)** — พา issue เดินผ่าน state machine ของบทบาท triage ต่าง ๆ
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)** — สแกน codebase หาโอกาสทำให้ module ลึกขึ้น นำเสนอเป็นรายงาน HTML แบบเห็นภาพ แล้ว grill เจาะลึกตัวที่คุณเลือก
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)** — ตั้งค่า repo นี้สำหรับ engineering skills (issue tracker, triage label, โครงสร้างเอกสาร domain) รันครั้งเดียวต่อ repo ก่อนใช้ engineering skills ตัวอื่น
- **[to-spec](./skills/engineering/to-spec/SKILL.md)** — เปลี่ยนบทสนทนาปัจจุบันให้เป็น spec แล้วส่งขึ้น issue tracker ไม่มีการสัมภาษณ์ — แค่สังเคราะห์จากสิ่งที่คุยกันไปแล้ว
- **[to-tickets](./skills/engineering/to-tickets/SKILL.md)** — แตกแผน spec หรือบทสนทนาใด ๆ ให้เป็นชุด ticket แบบ tracer-bullet โดยแต่ละใบประกาศว่า block อะไรอยู่บ้าง — เขียนเป็นข้อความในไฟล์ local หรือเป็น blocking link จริงบน tracker ก็ได้
- **[implement](./skills/engineering/implement/SKILL.md)** — สร้างงานตามที่ spec หรือชุด ticket อธิบายไว้ ขับเคลื่อนด้วย `/tdd` ที่ seam ซึ่งตกลงกันไว้ล่วงหน้า และปิดท้ายด้วย `/code-review` ก่อน commit
- **[wayfinder](./skills/engineering/wayfinder/SKILL.md)** — วางแผนงานก้อนใหญ่มาก ๆ ที่เกิน agent session เดียวจะรับไหว ในรูปแผนที่ร่วมของ investigation ticket บน issue tracker — ค่อย ๆ ไขทีละใบจนเส้นทางสู่จุดหมายชัดเจน

**Model-invoked**

- **[prototype](./skills/engineering/prototype/SKILL.md)** — สร้าง prototype แบบใช้แล้วทิ้งเพื่อตอบคำถามด้านการออกแบบ — เป็น terminal app ที่รันได้สำหรับคำถามเรื่อง state/logic หรือ UI หลายแบบที่ต่างกันสุดขั้ว สลับดูได้จาก route เดียว
- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.md)** — ลูปวินิจฉัยอย่างมีวินัยสำหรับ bug ยาก ๆ และ performance regression: reproduce → minimise → hypothesise → instrument → fix → regression-test
- **[research](./skills/engineering/research/SKILL.md)** — สืบค้นคำถามจากแหล่งข้อมูลปฐมภูมิที่เชื่อถือได้สูง แล้วบันทึกสิ่งที่พบเป็นไฟล์ Markdown พร้อมอ้างอิงใน repo รันเป็น background agent
- **[tdd](./skills/engineering/tdd/SKILL.md)** — test-driven development ด้วยลูป red-green-refactor สร้างฟีเจอร์หรือแก้ bug ทีละ vertical slice
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.md)** — สร้างและลับคม domain model ของโปรเจกต์อย่างจริงจัง — ท้าทายศัพท์กับ glossary ทดสอบด้วยสถานการณ์ edge case และอัปเดต `CONTEXT.md` กับ ADR ไปพร้อมกัน
- **[codebase-design](./skills/engineering/codebase-design/SKILL.md)** — วินัยและคลังคำศัพท์ร่วมสำหรับการออกแบบ deep module: พฤติกรรมจำนวนมากอยู่หลัง interface เล็ก ๆ วางไว้ที่ seam ที่สะอาด และ test ได้ผ่าน interface นั้น
- **[code-review](./skills/engineering/code-review/SKILL.md)** — review diff จากจุดอ้างอิงคงที่ในสองแกน: **Standards** (ตาม coding standard ของ repo บวก baseline ของ Fowler smell หรือเปล่า?) และ **Spec** (implement ตาม issue/PRD ต้นเรื่องอย่างซื่อตรงหรือเปล่า?) รันเป็น sub-agent คู่ขนานเพื่อไม่ให้ปนเปื้อนกัน

### Productivity

เครื่องมือ workflow ทั่วไป ไม่เจาะจงเรื่อง code

**User-invoked**

- **[grill-me](./skills/productivity/grill-me/SKILL.md)** — โดนสัมภาษณ์แบบไม่ปล่อยเกี่ยวกับแผนหรือการออกแบบ จนทุกกิ่งของ decision tree ถูกไขจนหมด
- **[handoff](./skills/productivity/handoff/SKILL.md)** — บีบอัดบทสนทนาปัจจุบันให้เป็นเอกสารส่งมอบงาน เพื่อให้ agent ตัวอื่นทำงานต่อได้
- **[teach](./skills/productivity/teach/SKILL.md)** — สอน skill หรือแนวคิดใหม่ให้ผู้ใช้ต่อเนื่องหลาย session โดยใช้ directory ปัจจุบันเป็นพื้นที่สอนที่เก็บ state ได้
- **[writing-great-skills](./skills/productivity/writing-great-skills/SKILL.md)** — เอกสารอ้างอิงสำหรับการเขียนและแก้ไข skill ให้ดี: คำศัพท์และหลักการที่ทำให้ skill คาดเดาพฤติกรรมได้

**Model-invoked**

- **[grilling](./skills/productivity/grilling/SKILL.md)** — สัมภาษณ์ผู้ใช้แบบไม่ปล่อยเกี่ยวกับแผนหรือการออกแบบ จนทุกกิ่งของ decision tree ถูกไขจนหมด เป็นลูปที่นำกลับมาใช้ซ้ำได้เบื้องหลัง `grill-me` และ `grill-with-docs`
