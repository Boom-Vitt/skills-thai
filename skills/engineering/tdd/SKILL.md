---
name: tdd
description: Test-driven development ใช้เมื่อ user อยากสร้าง feature หรือแก้ bug แบบ test-first, พูดถึง "red-green-refactor" หรืออยากได้ integration test
---

# Test-Driven Development

TDD คือ loop red → green skill นี้คือ reference ที่ทำให้ loop นั้น produce test ที่ควรค่าแก่การเก็บไว้: test ที่ดีคืออะไร, test ควรอยู่ตรงไหน, anti-pattern มีอะไรบ้าง และกฎของ loop ทุก section ใช้กับทุก cycle — เปิดดูก่อนและระหว่างทำ loop ไม่ใช่หลังจบ

ตอนสำรวจ codebase ให้อ่าน `CONTEXT.md` (ถ้ามี) เพื่อให้ชื่อ test กับคำศัพท์ของ interface ตรงกับภาษา domain ของ project และเคารพ ADR ในบริเวณที่คุณกำลังแตะ

## Test ที่ดีคืออะไร

test ตรวจสอบพฤติกรรมผ่าน public interface ไม่ใช่รายละเอียด implementation ตัว code เปลี่ยนทั้งหมดได้ แต่ test ไม่ควรต้องเปลี่ยนตาม test ที่ดีอ่านแล้วเหมือน spec — "user can checkout with valid cart" บอกชัดเจนว่าระบบทำอะไรได้ — และรอด refactor ได้เพราะมันไม่สนใจโครงสร้างภายใน

ดูตัวอย่างใน [tests.md](tests.md) และแนวทางการ mock ใน [mocking.md](mocking.md)

## Seam — จุดที่ test อยู่

**seam** (จุดต่อสาธารณะที่เราใช้ test พฤติกรรม) คือขอบเขต public ที่คุณ test: interface ที่คุณสังเกตพฤติกรรมได้โดยไม่ต้องล้วงเข้าไปข้างใน test อยู่ที่ seam เสมอ ไม่มีวัน test ตัว internals

**Test เฉพาะที่ seam ที่ตกลงกันไว้ล่วงหน้าเท่านั้น** ก่อนเขียน test ใด ๆ ให้เขียนรายการ seam ที่จะ test แล้ว confirm กับ user ห้ามเขียน test ที่ seam ที่ยังไม่ได้ confirm คุณ test ทุกอย่างไม่ได้อยู่แล้ว — การตกลง seam กันตั้งแต่ต้นคือวิธีทำให้แรงที่ลงกับการ test ไปตกอยู่บน critical path และ logic ที่ซับซ้อน แทนที่จะกระจายไปทุก edge case

ถามว่า: "public interface คืออะไร และเราควร test ที่ seam ไหนบ้าง?"

## Anti-patterns

- **ผูกกับ implementation** — mock ตัว collaborator ภายใน, test method ที่เป็น private หรือตรวจสอบผ่านช่องทางอ้อม (query database ตรง ๆ แทนที่จะใช้ interface) จุดสังเกต: test พังตอน refactor ทั้งที่พฤติกรรมไม่ได้เปลี่ยน
- **Tautological** — assertion คำนวณค่าที่คาดหวังซ้ำด้วยวิธีเดียวกับที่ code ทำ (`expect(add(a, b)).toBe(a + b)`, snapshot ที่ derive ด้วยมือแบบเดียวกัน, ค่าคงที่ที่ assert ว่าเท่ากับตัวเอง) มันจึงผ่านโดยโครงสร้างของมันเองและไม่มีวันขัดแย้งกับ code ได้ ค่าที่คาดหวังต้องมาจากแหล่งความจริงที่เป็นอิสระ — literal ที่รู้ว่าถูก, ตัวอย่างที่คำนวณมือไว้, ตัว spec
- **Horizontal slicing** — เขียน test ทั้งหมดก่อน แล้วค่อย implement ทั้งหมด test ที่เขียนเป็นก้อนใหญ่ตรวจสอบพฤติกรรมที่_จินตนาการเอา_: คุณจะ test _รูปทรง_ ของสิ่งต่าง ๆ แทนพฤติกรรมที่ user เจอจริง, test จะด้านชาต่อการเปลี่ยนแปลงจริง และคุณ commit กับโครงสร้าง test ก่อนจะเข้าใจ implementation ให้ทำงานเป็น **vertical slice** แทน — หนึ่ง test → หนึ่ง implementation → วนซ้ำ โดยแต่ละ test เป็น **tracer bullet** ที่ตอบสนองต่อสิ่งที่ cycle ก่อนหน้าสอนคุณ

## กฎของ loop

- **Red ก่อน green** เขียน test ที่ fail ก่อน แล้วค่อยเขียน code แค่พอให้ test ผ่าน อย่าเผื่อ test ในอนาคตหรือเพิ่ม feature แบบเผื่อ ๆ
- **ทีละ slice** หนึ่ง seam, หนึ่ง test, หนึ่ง implementation ที่ minimal ต่อหนึ่ง cycle
- **Refactoring ไม่ใช่ส่วนหนึ่งของ loop** มันเป็นของขั้น review (ดู skill `code-review`) ไม่ใช่ของ cycle implement แบบ red → green
