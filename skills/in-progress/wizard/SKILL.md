---
name: wizard
description: สร้าง bash wizard แบบ interactive ที่พาคนทำ manual procedure ทีละขั้น — setup third-party, migration แบบ one-off, เปลี่ยน state จาก A→B — เปิด URL ให้ เก็บค่า confirm ทุกขั้น แล้วเขียนไฟล์ .env และ GitHub Actions secrets ให้
disable-model-invocation: true
---

# Wizard

**wizard** คือ bash script ที่พาคนทำ manual procedure ทีละขั้นตอน สำหรับงานที่น่าเบื่อถ้าต้องทำเอง และน่าเบื่อพอกันถ้าต้องอธิบายให้ AI ฟังใหม่ทุกครั้ง มันเปิด URL ให้ทีละอัน บอกชัด ๆ ว่าต้องคลิกอะไร copy อะไร เก็บค่าที่ได้ แล้วเขียนลงที่ที่มันควรอยู่ (`.env`, GitHub secrets) confirm ทุกขั้น และโชว์ว่าเหลืออีกเท่าไหร่ ตัว wizard อาจใช้ config service ของ third-party, รัน migration แบบครั้งเดียว หรือย้าย project จาก state หนึ่งไปอีก state หนึ่งก็ได้

UX ที่ใช้งานลื่น ๆ ถูกแก้ไว้ให้แล้วใน [template.sh](template.sh) — progress พร้อมเวลาที่เหลือ, confirmation gate, การเปิด URL แบบ cross-platform (รวม WSL), การกรอก secret แบบซ่อน, upsert ค่าใน `.env` แบบ idempotent, การเขียน `gh secret`/`gh variable` และสรุปปิดท้าย **หน้าที่ของคุณมีแค่ scope ตัว procedure แล้วเขียน stage ของมันเท่านั้น** library ที่อยู่เหนือ marker `STAGES` เหมือนกันทุก wizard — ความสม่ำเสมอนี่แหละคือประเด็น ห้ามแก้มันด้วยมือเด็ดขาด

โดย default wizard เป็นของชั่วคราว — สร้างมาเพื่อรันครั้งเดียว เซฟไว้ใน scratch หรือ path `scripts/` แล้วลบทิ้งเมื่องานเสร็จ commit เข้า repo ก็ต่อเมื่อ user อยากได้ setup path ที่ทำซ้ำได้และควรอยู่ใน repo จริง ๆ

## ขั้นตอนการทำงาน

### 1. Scope ตัว procedure

ไล่ให้ครบว่าคนต้องทำ manual step อะไรบ้าง และมีค่าอะไรถูกเก็บระหว่างทาง อ่าน repo ก่อน — อย่าถามทั้งที่ยังไม่มีข้อมูล:

- สำหรับ setup: ดู `.env`, `.env.example`, `.env.*`, `README`, `docker-compose*`, config ของ framework และ `.github/workflows/*` (ทุกจุดที่อ้างถึง `secrets.*` / `vars.*` คือค่าที่ wizard ต้อง produce ให้ได้)
- สำหรับ migration หรือ transition: state ปัจจุบัน, state ปลายทาง และ action ที่ย้อนกลับไม่ได้ระหว่างทาง

จากนั้นโชว์รายการ stage ตามลำดับพร้อมค่าที่แต่ละ stage produce ให้ user ดูแล้วขอ confirm — เขาอาจเพิ่ม ตัด หรือสลับลำดับก็ได้

**เสร็จเมื่อ:** ทุก stage มีชื่อเรียงตามลำดับ และสำหรับทุกค่าที่เก็บ คุณรู้ว่า (a) คนไปเอาค่านั้นมาจากไหน (b) มันถูกเขียนลงที่ไหน (`.env`, GitHub secret, ทั้งคู่ หรือไม่เขียนเลย — บาง stage เป็น action ล้วน ๆ) และ (c) มันเป็น secret (กรอกแบบซ่อน) หรือ public

### 2. Map เส้นทางของแต่ละ stage

สำหรับแต่ละ stage เขียนเส้นทางที่คนต้องเดินให้เป๊ะ: เปิด URL ไหน ไปทำอะไรตรงนั้น ค่าโผล่ตรงไหน เอาไปเติม variable ตัวไหน — เช่น "Dashboard → Developers → API keys → Reveal test key → copy" จุดไหนที่คุณไม่รู้ UI ปัจจุบันหรือคำสั่งที่แน่นอนจริง ๆ ให้บอกตรง ๆ แล้วถาม user หรือไปเช็ค docs — ห้ามแต่งขั้นตอนที่อาจไม่มีอยู่จริงขึ้นมาเอง

**เสร็จเมื่อ:** ทุก stage ไล่ลงมาเป็นคำแนะนำที่เป็นรูปธรรมพอให้คนแปลกหน้าทำตามได้

### 3. เขียน wizard

copy `template.sh` ไปที่ target path แทนที่ stage ตัวอย่างด้วย `stage` หนึ่งอันต่อหนึ่ง step เรียงตามลำดับ dependency ใช้ helper ของ library — `stage`, `say`/`step`, `open_url`, `ask`/`ask_secret`, `write_env`, `set_secret`/`set_var`, `pause`/`confirm` — และตั้ง `TOTAL_STAGES` กับ `TOTAL_MINUTES` เป็นค่าประเมินที่ตรงกับความจริง (ตัวนี้ขับการแสดงเวลาที่เหลือ)

รักษามาตรฐานที่ template วางไว้: เปิด URL ก่อนค่อยถามค่าของมัน, ใช้ `ask_secret` กับทุกอย่างที่เป็น secret, `write_env` ทุกค่าที่ต้อง persist, `set_secret` เฉพาะค่าที่ CI ต้องใช้จริง และ `confirm` ก่อนทุก action ที่ย้อนกลับไม่ได้ แต่ละ `stage` จะเคลียร์หน้าจอให้เห็นเฉพาะ step ปัจจุบัน — จำกัดหนึ่ง stage ให้เหลืองานโฟกัสเดียว จะได้ไม่มีอะไรที่คนต้องใช้เลื่อนหลุดจอไป อย่าแตะ library ที่อยู่เหนือ marker

### 4. Verify แล้วส่งมอบ

- `bash -n <script>`; รัน `shellcheck` ถ้ามี
- `chmod +x <script>`
- อย่ารัน wizard เองแบบ end-to-end — มันจะเปิด browser และ block รอ input จากคน ให้ trace แบบ static แทน: ทุกค่าจาก step 1 ถูกเก็บและไปลงตรงที่ step 1 บอกไว้ และทุกชื่อใน `set_secret` ตรงเป๊ะกับ `secrets.*` ที่ CI อ้างถึง
- บอก user ว่ารันยังไง ถ้ามันเป็น setup path ที่ทำซ้ำได้ ให้ commit แล้วลิงก์จาก README เพื่อให้คนถัดไปรัน script แทนที่จะไปนั่งถาม AI
