---
name: improve-codebase-architecture
description: สแกน codebase หาโอกาส deepening แล้วนำเสนอเป็น HTML report แบบ visual จากนั้น grill เจาะลึกตัวที่คุณเลือก
disable-model-invocation: true
---

# Improve Codebase Architecture

ขุดหาจุดฝืดเชิงสถาปัตยกรรม แล้วเสนอ **โอกาส deepening** — refactor ที่เปลี่ยน module ตื้น ๆ ให้กลายเป็น deep module เป้าหมายคือให้ test ได้ง่ายและให้ AI นำทางใน codebase ได้สะดวก

คำสั่งนี้_อิง_ domain model ของโปรเจกต์ และสร้างบนคลังคำศัพท์ด้าน design ที่ใช้ร่วมกัน:

- รัน skill `/codebase-design` เพื่อโหลดคำศัพท์ด้านสถาปัตยกรรม (**module**, **interface**, **depth**, **seam**, **adapter**, **leverage**, **locality**) และหลักการของมัน (deletion test, "the interface is the test surface", "one adapter = hypothetical seam, two = real") ใช้คำเหล่านี้ให้ตรงเป๊ะในทุกข้อเสนอ — อย่าเผลอไปใช้คำอย่าง "component," "service," "API," หรือ "boundary"
- ภาษา domain ใน `CONTEXT.md` ช่วยตั้งชื่อให้ seam ที่ดี ส่วน ADR ใน `docs/adr/` บันทึกการตัดสินใจที่คำสั่งนี้ไม่ควรหยิบมาถกใหม่

## ขั้นตอนการทำงาน

### 1. Explore

อ่าน domain glossary ของโปรเจกต์ (`CONTEXT.md`) และ ADR ในบริเวณที่กำลังจะแตะก่อน

จากนั้นใช้ Agent tool แบบ `subagent_type=Explore` เดินสำรวจ codebase อย่ายึด heuristic ตายตัว — สำรวจแบบเป็นธรรมชาติ แล้วจดจุดที่รู้สึกฝืด:

- ตรงไหนที่การทำความเข้าใจ concept เดียวต้องกระโดดไปมาระหว่าง module เล็ก ๆ หลายตัว?
- ตรงไหนที่ module **ตื้น** — interface ซับซ้อนแทบเท่าตัว implementation เอง?
- ตรงไหนที่แยก pure function ออกมาเพียงเพื่อให้ test ได้ แต่บั๊กจริง ๆ ซ่อนอยู่ในวิธีที่มันถูกเรียก (ไม่มี **locality**)?
- ตรงไหนที่ module ซึ่งผูกกันแน่นรั่วข้าม seam ของตัวเอง?
- ส่วนไหนของ codebase ที่ไม่มี test หรือ test ผ่าน interface ปัจจุบันได้ยาก?

ใช้ **deletion test** กับทุกอย่างที่สงสัยว่าตื้น: ถ้าลบมันทิ้ง ความซับซ้อนจะถูกรวมศูนย์ หรือแค่ย้ายที่? คำตอบ "ใช่ รวมศูนย์" คือสัญญาณที่เราต้องการ

### 2. นำเสนอ candidate เป็น HTML report

เขียนไฟล์ HTML แบบ self-contained ลง temp directory ของ OS เพื่อไม่ให้อะไรหลุดเข้าไปใน repo หา temp dir จาก `$TMPDIR` ถ้าไม่มีให้ fallback เป็น `/tmp` (หรือ `%TEMP%` บน Windows) แล้วเขียนไปที่ `<tmpdir>/architecture-review-<timestamp>.html` เพื่อให้แต่ละรอบได้ไฟล์ใหม่เสมอ เปิดไฟล์ให้ผู้ใช้ดู — `xdg-open <path>` บน Linux, `open <path>` บน macOS, `start <path>` บน Windows — แล้วบอก absolute path ให้เขาด้วย

report ใช้ **Tailwind ผ่าน CDN** สำหรับ layout และ styling และ **Mermaid ผ่าน CDN** สำหรับ diagram ในจุดที่ graph/flow/sequence สื่อโครงสร้างได้ชัดกว่า ผสม Mermaid กับ visual ที่วาดเองด้วย CSS/SVG — ใช้ Mermaid เมื่อความสัมพันธ์มีรูปทรงแบบ graph (call graph, dependency, sequence) และใช้ div/SVG ที่ประกอบเองเมื่ออยากได้อะไรที่ editorial กว่า (mass diagram, cross-section, animation การยุบรวม) candidate แต่ละตัวต้องมี **before/after visualisation** เน้น visual เข้าไว้

candidate แต่ละตัว render เป็น card ที่มี:

- **Files** — ไฟล์/module ไหนบ้างที่เกี่ยวข้อง
- **Problem** — ทำไมสถาปัตยกรรมปัจจุบันถึงสร้างความฝืด
- **Solution** — อธิบายเป็นภาษาคนว่าอะไรจะเปลี่ยนไป
- **Benefits** — อธิบายในแง่ locality กับ leverage และ test จะดีขึ้นอย่างไร
- **Before / After diagram** — วางคู่กัน วาดเอง แสดงให้เห็นความตื้นและการ deepen
- **Recommendation strength** — หนึ่งใน `Strong`, `Worth exploring`, `Speculative` แสดงเป็น badge

ปิดท้าย report ด้วยส่วน **Top recommendation**: candidate ตัวไหนที่ควรลงมือก่อน และเพราะอะไร

**ใช้คำศัพท์จาก CONTEXT.md สำหรับฝั่ง domain และคำศัพท์จาก `/codebase-design` สำหรับฝั่งสถาปัตยกรรม** ถ้า `CONTEXT.md` นิยามคำว่า "Order" ก็พูดว่า "the Order intake module" — ไม่ใช่ "the FooBarHandler" และไม่ใช่ "the Order service"

**ความขัดแย้งกับ ADR**: ถ้า candidate ขัดกับ ADR ที่มีอยู่ ให้ยกขึ้นมาเฉพาะเมื่อความฝืดนั้นจริงจังพอที่จะคุ้มกับการเปิด ADR มาทบทวนใหม่ ทำเครื่องหมายให้ชัดใน card (เช่น warning callout: _"ขัดกับ ADR-0007 — แต่ควรเปิดมาคุยใหม่เพราะ…"_) อย่าไล่ลิสต์ refactor เชิงทฤษฎีทุกตัวที่ ADR ห้ามไว้

ดู [HTML-REPORT.md](HTML-REPORT.md) สำหรับ HTML scaffold ฉบับเต็ม pattern ของ diagram และแนวทาง styling

อย่าเพิ่งเสนอ interface ในขั้นนี้ หลังเขียนไฟล์เสร็จ ถามผู้ใช้ว่า: "อยากเจาะลึกตัวไหนต่อ?"

### 3. Grilling loop

พอผู้ใช้เลือก candidate แล้ว รัน skill `/grilling` เพื่อเดิน design tree ไปกับเขา — ข้อจำกัด, dependency, รูปทรงของ module ที่ deepen แล้ว, อะไรอยู่หลัง seam, test ตัวไหนรอด

side effect เกิดขึ้นระหว่างทางทันทีที่การตัดสินใจตกผลึก — รัน skill `/domain-modeling` เพื่อให้ domain model ทันสมัยอยู่เสมอ:

- **ตั้งชื่อ module ที่ deepen แล้วตาม concept ที่ไม่มีใน `CONTEXT.md`?** เพิ่มคำนั้นลง `CONTEXT.md` ถ้าไฟล์ยังไม่มีค่อยสร้างตอนนั้นเลย
- **คำที่คลุมเครือคมขึ้นระหว่างคุยกัน?** อัปเดต `CONTEXT.md` ตรงนั้นทันที
- **ผู้ใช้ปัด candidate ทิ้งด้วยเหตุผลที่มีน้ำหนัก?** เสนอทำ ADR โดยถามว่า: _"อยากให้บันทึกเรื่องนี้เป็น ADR ไหม เพื่อให้ architecture review รอบหน้าไม่เสนอซ้ำอีก?"_ เสนอเฉพาะเมื่อเหตุผลนั้นจำเป็นจริง ๆ สำหรับคนที่มาสำรวจในอนาคตเพื่อไม่ให้เสนอเรื่องเดิมซ้ำ — ข้ามเหตุผลชั่วคราว ("ตอนนี้ยังไม่คุ้ม") และเหตุผลที่ชัดในตัวเองอยู่แล้ว
- **อยากลองสำรวจ interface ทางเลือกสำหรับ module ที่ deepen แล้ว?** รัน skill `/codebase-design` แล้วใช้ pattern design-it-twice แบบ sub-agent ขนานของมัน
