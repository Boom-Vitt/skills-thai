---
name: handoff
description: บีบอัดบทสนทนาปัจจุบันให้เป็นเอกสาร handoff สำหรับให้ agent ตัวอื่นรับช่วงต่อ
argument-hint: "จะใช้ session ถัดไปทำอะไร?"
disable-model-invocation: true
---

เขียนเอกสาร handoff ที่สรุปบทสนทนาปัจจุบัน เพื่อให้ agent ตัวใหม่ทำงานต่อได้ บันทึกลง temporary directory ของ OS ของ user — ไม่ใช่ใน workspace ปัจจุบัน

ใส่หัวข้อ "suggested skills" ไว้ในเอกสารด้วย เพื่อแนะนำ skill ที่ agent ตัวถัดไปควรเรียกใช้

อย่าเขียนซ้ำเนื้อหาที่ถูกบันทึกไว้ใน artifact อื่นอยู่แล้ว (spec, plan, ADR, issue, commit, diff) ให้อ้างอิงด้วย path หรือ URL แทน

ปกปิด (redact) ข้อมูลอ่อนไหวทั้งหมด เช่น API key, รหัสผ่าน หรือข้อมูลส่วนบุคคลที่ระบุตัวตนได้

ถ้า user ส่ง argument มา ให้ถือว่ามันคือคำอธิบายว่า session ถัดไปจะโฟกัสเรื่องอะไร แล้วปรับเนื้อหาเอกสารให้เข้ากับมัน
