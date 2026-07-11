---
name: ubiquitous-language
description: สกัด glossary ภาษากลางสไตล์ DDD จากบทสนทนาปัจจุบัน พร้อมชี้จุดกำกวมและเสนอ term มาตรฐาน เซฟลง UBIQUITOUS_LANGUAGE.md ใช้เมื่อผู้ใช้อยากนิยามศัพท์ domain สร้าง glossary ทำ terminology ให้แน่น สร้าง ubiquitous language หรือพูดถึง "domain model" หรือ "DDD"
disable-model-invocation: true
---

# Ubiquitous Language

สกัดและจัดระเบียบศัพท์ domain จากบทสนทนาปัจจุบันให้เป็น glossary ที่สอดคล้องกัน แล้วเซฟลงไฟล์ในเครื่อง

## ขั้นตอนการทำงาน

1. **สแกนบทสนทนา** หาคำนาม คำกริยา และ concept ที่เกี่ยวกับ domain
2. **ระบุปัญหา**:
   - คำเดียวกันถูกใช้กับคนละ concept (ความกำกวม)
   - คนละคำถูกใช้กับ concept เดียวกัน (คำพ้อง)
   - term ที่คลุมเครือหรือแบกความหมายหลายอย่าง
3. **เสนอ glossary มาตรฐาน** โดยฟันธงเลือก term ให้ชัด
4. **เขียนลง `UBIQUITOUS_LANGUAGE.md`** ใน working directory ตาม format ด้านล่าง
5. **สรุปผล** inline ในบทสนทนา

## รูปแบบ output

เขียนไฟล์ `UBIQUITOUS_LANGUAGE.md` ด้วยโครงสร้างนี้:

```md
# Ubiquitous Language

## Order lifecycle

| Term        | Definition                                              | Aliases to avoid      |
| ----------- | ------------------------------------------------------- | --------------------- |
| **Order**   | A customer's request to purchase one or more items      | Purchase, transaction |
| **Invoice** | A request for payment sent to a customer after delivery | Bill, payment request |

## People

| Term         | Definition                                  | Aliases to avoid       |
| ------------ | ------------------------------------------- | ---------------------- |
| **Customer** | A person or organization that places orders | Client, buyer, account |
| **User**     | An authentication identity in the system    | Login, account         |

## Relationships

- An **Invoice** belongs to exactly one **Customer**
- An **Order** produces one or more **Invoices**

## Example dialogue

> **Dev:** "When a **Customer** places an **Order**, do we create the **Invoice** immediately?"
> **Domain expert:** "No — an **Invoice** is only generated once a **Fulfillment** is confirmed. A single **Order** can produce multiple **Invoices** if items ship in separate **Shipments**."
> **Dev:** "So if a **Shipment** is cancelled before dispatch, no **Invoice** exists for it?"
> **Domain expert:** "Exactly. The **Invoice** lifecycle is tied to the **Fulfillment**, not the **Order**."

## Flagged ambiguities

- "account" was used to mean both **Customer** and **User** — these are distinct concepts: a **Customer** places orders, while a **User** is an authentication identity that may or may not represent a **Customer**.
```

## กติกา

- **ฟันธง** เมื่อมีหลายคำสำหรับ concept เดียวกัน เลือกคำที่ดีที่สุดแล้วลิสต์คำอื่นเป็น alias ที่ควรเลี่ยง
- **ชี้จุดขัดแย้งให้ชัด** ถ้า term ไหนถูกใช้อย่างกำกวมในบทสนทนา ให้เรียกออกมาในหัวข้อ "Flagged ambiguities" พร้อมคำแนะนำที่ชัดเจน
- **ใส่เฉพาะ term ที่มีความหมายสำหรับ domain expert** ข้ามชื่อ module หรือ class เว้นแต่มันมีความหมายในภาษา domain
- **นิยามให้กระชับ** ไม่เกินหนึ่งประโยค นิยามว่ามันคืออะไร ไม่ใช่มันทำอะไร
- **แสดงความสัมพันธ์** ใช้ชื่อ term ตัวหนา และระบุ cardinality เมื่อเห็นได้ชัด
- **ใส่เฉพาะ term ของ domain** ข้าม concept การเขียนโปรแกรมทั่วไป (array, function, endpoint) เว้นแต่มันมีความหมายเฉพาะใน domain นั้น
- **จัดกลุ่ม term เป็นหลายตาราง** เมื่อเห็นกลุ่มก้อนตามธรรมชาติ (เช่น ตาม subdomain, lifecycle, หรือ actor) แต่ละกลุ่มมี heading และตารางของตัวเอง ถ้า term ทั้งหมดอยู่ใน domain เดียวที่กลมกลืนกัน ตารางเดียวก็พอ — อย่าฝืนจัดกลุ่ม
- **เขียน example dialogue** บทสนทนาสั้น ๆ (โต้ตอบกัน 3-5 รอบ) ระหว่าง dev กับ domain expert ที่แสดงให้เห็นว่า term เหล่านี้ทำงานร่วมกันอย่างเป็นธรรมชาติอย่างไร บทสนทนาควรทำให้เส้นแบ่งระหว่าง concept ที่ใกล้กันชัดขึ้น และโชว์การใช้ term อย่างแม่นยำ

<example>

## Example dialogue

> **Dev:** "จะ test **sync service** โดยไม่ใช้ Docker ได้ยังไง?"

> **Domain expert:** "ส่ง **filesystem layer** เข้าไปแทน **Docker layer** มัน implement interface **Sandbox service** ตัวเดียวกัน แต่ใช้ local directory เป็น **sandbox**"

> **Dev:** "แสดงว่า **sync-in** ยังสร้าง **bundle** แล้วแตกมันเหมือนเดิม?"

> **Domain expert:** "ใช่เลย **sync service** ไม่รู้ว่ากำลังคุยกับ layer ไหน มันเรียก `exec` กับ `copyIn` — **filesystem layer** ก็แค่รันคำสั่งพวกนั้นเป็น shell command ในเครื่อง"

</example>

## การรันซ้ำ

เมื่อถูกเรียกอีกครั้งในบทสนทนาเดิม:

1. อ่าน `UBIQUITOUS_LANGUAGE.md` ที่มีอยู่
2. เพิ่ม term ใหม่จากการคุยกันหลังจากนั้น
3. อัปเดตนิยามถ้าความเข้าใจเปลี่ยนไป
4. ชี้จุดกำกวมใหม่ ๆ ที่เจอ
5. เขียน example dialogue ใหม่ให้ครอบคลุม term ที่เพิ่มเข้ามา
