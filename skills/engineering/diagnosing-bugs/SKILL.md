---
name: diagnosing-bugs
description: ลูปวินิจฉัยสำหรับ bug ยากๆ และ performance regression ใช้เมื่อ user พูดว่า "diagnose"/"debug this" หรือรายงานว่ามีอะไรพัง / throw / fail / ช้า
---

# การวินิจฉัย Bug

วินัยสำหรับ bug ยากๆ ข้ามเฟสได้เฉพาะเมื่อมีเหตุผลชัดเจนเท่านั้น

ตอนสำรวจ codebase ให้อ่าน `CONTEXT.md` (ถ้ามี) เพื่อสร้าง mental model ที่ชัดของ module ที่เกี่ยวข้อง และเช็ค ADR (บันทึกการตัดสินใจเชิงสถาปัตยกรรม) ในบริเวณที่คุณกำลังแตะ

## Phase 1 — สร้าง feedback loop

**นี่แหละคือหัวใจของ skill นี้** ที่เหลือเป็นงานกลไกล้วนๆ ถ้าคุณมีสัญญาณ pass/fail ที่ **tight** สำหรับ bug นี้ — สัญญาณที่ red กับ bug _ตัวนี้_ โดยเฉพาะ — คุณจะหาสาเหตุเจอแน่ ทั้ง bisection การทดสอบ hypothesis และการ instrument ล้วนแค่หยิบ loop นี้ไปใช้ต่อ แต่ถ้าไม่มี ต่อให้จ้อง code นานแค่ไหนก็ไม่ช่วยอะไร

ทุ่มแรงตรงนี้ให้มากแบบไม่ต้องสมส่วน **ดุดันเข้าไว้ สร้างสรรค์เข้าไว้ อย่ายอมแพ้เด็ดขาด**

### วิธีสร้าง loop — ลองตามลำดับนี้คร่าวๆ

1. **Failing test** ที่ seam (จุดต่อที่เราใช้ test พฤติกรรม) ไหนก็ได้ที่ไปถึง bug — unit, integration, e2e
2. **Curl / HTTP script** ยิงใส่ dev server ที่รันอยู่
3. **เรียกผ่าน CLI** ด้วย fixture input แล้ว diff stdout เทียบกับ snapshot ที่รู้ว่าถูกต้อง
4. **Headless browser script** (Playwright / Puppeteer) — ขับ UI แล้ว assert บน DOM/console/network
5. **Replay trace ที่จับไว้** เซฟ network request / payload / event log ของจริงลง disk แล้ว replay ผ่าน code path นั้นแบบแยกเดี่ยว
6. **Throwaway harness** ปั่นระบบเวอร์ชันจิ๋วขึ้นมา (service เดียว, mock dependency) ที่เรียก code path ของ bug ได้ด้วย function call เดียว
7. **Property / fuzz loop** ถ้า bug คือ "output ผิดเป็นบางครั้ง" ให้รัน random input 1000 ชุดแล้วมองหา failure mode
8. **Bisection harness** ถ้า bug โผล่มาระหว่าง state ที่รู้จักสองจุด (commit, dataset, version) ให้ automate ขั้นตอน "boot ที่ state X, เช็ค, ทำซ้ำ" เพื่อให้ `git bisect run` ได้
9. **Differential loop** รัน input เดียวกันผ่านเวอร์ชันเก่า vs เวอร์ชันใหม่ (หรือสอง config) แล้ว diff output
10. **HITL bash script** ทางเลือกสุดท้าย ถ้าจำเป็นต้องมีคนคลิก ให้ขับ_คนนั้น_ด้วย `scripts/hitl-loop.template.sh` เพื่อให้ loop ยังมีโครงสร้าง output ที่จับได้จะป้อนกลับมาหาคุณ

สร้าง feedback loop ที่ถูกต้องได้เมื่อไหร่ bug ก็ถูกแก้ไปแล้ว 90%

### บีบ loop ให้ tight

ปฏิบัติกับ loop เหมือนเป็น product เมื่อมี loop _สักตัว_ แล้ว ให้ **tighten** มันต่อ:

- ทำให้เร็วขึ้นได้ไหม? (cache ขั้น setup, ข้าม init ที่ไม่เกี่ยว, บีบ scope ของ test ให้แคบลง)
- ทำให้สัญญาณคมขึ้นได้ไหม? (assert อาการที่เฉพาะเจาะจง ไม่ใช่แค่ "ไม่ crash")
- ทำให้ deterministic ขึ้นได้ไหม? (ตรึงเวลา, seed RNG, แยก filesystem, freeze network)

loop 30 วินาทีที่ flaky แทบไม่ดีไปกว่าไม่มี loop ส่วน loop 2 วินาทีที่ deterministic คือ tight — พลังวิเศษของการ debug

### Bug ที่ non-deterministic

เป้าหมายไม่ใช่ repro ที่สะอาด แต่คือ **อัตราการ reproduce ที่สูงขึ้น** วน trigger 100 รอบ, รันขนาน, เพิ่ม stress, บีบ timing window ให้แคบ, ฉีด sleep เข้าไป bug ที่ flake 50% ยัง debug ได้ แต่ 1% ไม่ได้ — ดันอัตราขึ้นไปเรื่อยๆ จนกว่าจะ debug ได้

### เมื่อสร้าง loop ไม่ได้จริงๆ

หยุดแล้วบอกออกมาตรงๆ ไล่รายการสิ่งที่ลองไปแล้ว แล้วขอจาก user: (a) สิทธิ์เข้าถึง environment ที่ reproduce ได้ (b) artifact ที่จับไว้ (ไฟล์ HAR, log dump, core dump, คลิปอัดหน้าจอพร้อม timestamp) หรือ (c) อนุญาตให้เพิ่ม instrumentation ชั่วคราวบน production **ห้าม**เดินหน้าตั้ง hypothesis โดยไม่มี loop

### Completion criterion — loop ที่ tight และ red ได้

Phase 1 เสร็จเมื่อ loop นั้น **tight** และ **red-capable**: คุณระบุ **คำสั่งเดียว** ได้ — path ของ script, การรัน test, คำสั่ง curl — ที่คุณ **รันไปแล้วอย่างน้อยหนึ่งครั้ง** (แปะทั้งคำสั่งและ output ของมัน) และคำสั่งนั้นต้อง:

- [ ] **Red-capable** — ขับ code path ของ bug จริงและ assert **อาการเป๊ะๆ ตามที่ user เจอ** จึง red ได้กับ bug นี้และ green เมื่อแก้เสร็จ ไม่ใช่แค่ "รันแล้วไม่ error" — มันต้อง_จับ bug ตัวนี้โดยเฉพาะ_ให้ได้
- [ ] **Deterministic** — ให้คำตัดสินเดิมทุกรอบที่รัน (bug ที่ flaky: อัตรา reproduce สูงและตรึงไว้ได้ ตามหัวข้อข้างบน)
- [ ] **เร็ว** — หลักวินาที ไม่ใช่หลักนาที
- [ ] **Agent รันเองได้** — รันได้โดยไม่ต้องมีคนเฝ้า จะมีคนอยู่ใน loop ได้เฉพาะผ่าน `scripts/hitl-loop.template.sh` เท่านั้น

ถ้าจับได้ว่าตัวเองกำลังอ่าน code เพื่อปั้นทฤษฎีก่อนที่คำสั่งนี้จะมีจริง **หยุด — การกระโดดไปตั้ง hypothesis เลยคือความล้มเหลวแบบที่ skill นี้มีไว้ป้องกันโดยตรง** ไม่มีคำสั่งที่ red ได้ ก็ไม่มี Phase 2

## Phase 2 — Reproduce + minimise

รัน loop ดูมัน red — bug โผล่มา

ยืนยันว่า:

- [ ] loop สร้าง failure mode แบบเดียวกับที่ **user** เล่าไว้ — ไม่ใช่ failure อื่นที่บังเอิญอยู่ใกล้ๆ ผิด bug = ผิด fix
- [ ] failure เกิดซ้ำได้เมื่อรันหลายรอบ (หรือสำหรับ bug ที่ non-deterministic เกิดซ้ำในอัตราสูงพอที่จะใช้ debug ได้)
- [ ] จับอาการเป๊ะๆ เก็บไว้แล้ว (error message, output ที่ผิด, เวลาที่ช้า) เพื่อให้เฟสถัดๆ ไปตรวจได้ว่า fix จัดการอาการนั้นจริง

### Minimise

พอ red แล้ว ให้หด repro ลงเหลือ **scenario เล็กที่สุดที่ยัง red** ตัด input, caller, config, data และขั้นตอน **ทีละอย่าง** โดยรัน loop ซ้ำหลังตัดทุกครั้ง — เก็บไว้เฉพาะส่วนที่ failure ต้องพึ่งจริงๆ

ทำไมต้องเหนื่อยขนาดนี้: repro ที่ minimal ย่อ hypothesis space ใน Phase 3 (ชิ้นส่วนที่เหลือให้ต้องสงสัยน้อยลง) และกลายเป็น regression test ที่สะอาดใน Phase 5

เสร็จเมื่อ **ทุกส่วนที่เหลือล้วนจำเป็นต่อ failure** — เอาส่วนไหนออก loop ก็กลายเป็น green

ห้ามไปต่อจนกว่าจะ reproduce ได้ **และ** minimise เสร็จ

## Phase 3 — ตั้ง hypothesis

สร้าง **hypothesis 3–5 ข้อพร้อมจัดอันดับ** ก่อนจะเริ่มทดสอบข้อไหนก็ตาม การตั้งข้อเดียวจะทำให้ยึดติดกับ idea แรกที่ฟังดูเป็นไปได้

แต่ละ hypothesis ต้อง **falsifiable** (พิสูจน์ว่าผิดได้): ระบุ prediction ที่มันทำนาย

> รูปแบบ: "ถ้า <X> คือสาเหตุ การ<เปลี่ยน Y> จะทำให้ bug หายไป / การ<เปลี่ยน Z> จะทำให้มันแย่ลง"

ถ้าระบุ prediction ไม่ได้ hypothesis นั้นเป็นแค่ความรู้สึก — ทิ้งไปหรือลับให้คมขึ้น

**โชว์รายการที่จัดอันดับแล้วให้ user ดูก่อนเริ่มทดสอบ** เขามักมี domain knowledge ที่จัดอันดับใหม่ได้ทันที ("เราเพิ่ง deploy การเปลี่ยนแปลงที่เกี่ยวกับข้อ #3") หรือรู้ว่า hypothesis ไหนถูกตัดทิ้งไปแล้ว เป็น checkpoint ราคาถูกที่ประหยัดเวลามหาศาล แต่อย่ารอจนงานค้าง — เดินหน้าตามอันดับของคุณเองถ้า user ไม่อยู่

## Phase 4 — Instrument

ทุก probe ต้อง map กับ prediction เฉพาะข้อจาก Phase 3 **เปลี่ยนทีละตัวแปรเท่านั้น**

ลำดับเครื่องมือที่ควรเลือก:

1. **Debugger / REPL inspection** ถ้า environment รองรับ breakpoint เดียวชนะ log สิบบรรทัด
2. **Log แบบเจาะจงเป้า** ที่ boundary ซึ่งช่วยแยก hypothesis ออกจากกัน
3. ห้ามใช้วิธี "log ทุกอย่างแล้วค่อย grep" เด็ดขาด

**ติด tag ให้ทุก debug log** ด้วย prefix ที่ไม่ซ้ำใคร เช่น `[DEBUG-a4f2]` การ cleanup ตอนจบจะเหลือแค่ grep ครั้งเดียว log ที่ไม่ติด tag จะหลงเหลือรอดอยู่ในระบบ ส่วน log ที่ติด tag จะถูกกวาดทิ้งได้หมด

**สาย perf** สำหรับ performance regression นั้น log มักเป็นเครื่องมือที่ผิด ให้ทำแบบนี้แทน: สร้าง baseline measurement (timing harness, `performance.now()`, profiler, query plan) แล้วค่อย bisect วัดก่อน แก้ทีหลัง

## Phase 5 — Fix + regression test

เขียน regression test **ก่อนลง fix** — แต่เฉพาะเมื่อมี **seam ที่ถูกต้อง** สำหรับมันเท่านั้น

seam ที่ถูกต้องคือจุดที่ test ได้ exercise **รูปแบบ bug ของจริง** อย่างที่มันเกิดขึ้นตรง call site ถ้า seam เดียวที่มีอยู่ตื้นเกินไป (test แบบ caller เดียวทั้งที่ bug ต้องอาศัยหลาย caller, unit test ที่จำลอง chain ที่ trigger bug ไม่ได้) regression test ตรงนั้นให้ได้แค่ความมั่นใจปลอมๆ

**ถ้าไม่มี seam ที่ถูกต้อง นั่นเองก็คือสิ่งที่ค้นพบ** จดบันทึกไว้ สถาปัตยกรรมของ codebase กำลังขัดขวางไม่ให้ล็อก bug นี้เอาไว้ได้ ปักธงเรื่องนี้ไว้สำหรับเฟสถัดไป

ถ้ามี seam ที่ถูกต้อง:

1. แปลง repro ที่ minimise แล้วให้เป็น failing test ที่ seam นั้น
2. ดูมัน fail
3. ลง fix
4. ดูมัน pass
5. รัน feedback loop จาก Phase 1 ซ้ำกับ scenario ดั้งเดิม (ที่ยังไม่ถูก minimise)

## Phase 6 — Cleanup + post-mortem

ต้องครบทุกข้อก่อนประกาศว่าเสร็จ:

- [ ] repro ดั้งเดิมไม่ reproduce อีกแล้ว (รัน loop จาก Phase 1 ซ้ำ)
- [ ] regression test ผ่าน (หรือบันทึกไว้แล้วว่าไม่มี seam)
- [ ] instrumentation `[DEBUG-...]` ถูกลบออกหมด (`grep` หา prefix)
- [ ] prototype แบบใช้แล้วทิ้งถูกลบ (หรือย้ายไปที่ที่ mark ชัดเจนว่าเป็นของ debug)
- [ ] hypothesis ข้อที่ถูกต้องถูกเขียนไว้ใน commit / PR message — เพื่อให้คน debug คนถัดไปได้เรียนรู้

**จากนั้นถามตัวเอง: อะไรจะป้องกัน bug นี้ได้ตั้งแต่แรก?** ถ้าคำตอบเกี่ยวข้องกับการเปลี่ยนสถาปัตยกรรม (ไม่มี test seam ที่ดี, caller พันกันยุ่งเหยิง, coupling ที่ซ่อนอยู่) ให้ส่งต่อไปยัง skill `/improve-codebase-architecture` พร้อมรายละเอียด ให้คำแนะนำนี้ **หลังจาก** fix เข้าไปแล้ว ไม่ใช่ก่อนหน้า — ตอนนี้คุณมีข้อมูลมากกว่าตอนเริ่มเยอะ
