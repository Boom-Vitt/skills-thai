---
name: claude-handoff
description: ส่งต่อบทสนทนาปัจจุบันให้ background agent ตัวใหม่ที่หยิบงานไปทำต่อได้ทันที
argument-hint: "session ถัดไปจะเอาไว้ทำอะไร?"
disable-model-invocation: true
---

เขียน handoff summary ของบทสนทนาปัจจุบัน เพื่อให้ agent ตัวใหม่ทำงานต่อได้ แต่แทนที่จะเซฟเก็บไว้ ให้ launch background agent ที่ seed ด้วย summary นั้นเป็น prompt: `claude --bg --name "<descriptive name>" "<handoff summary>"` มันจะเริ่มทำงานที่ working directory ปัจจุบันและ return กลับมาทันที ส่วน user จัดการมันต่อได้ด้วย `claude agents`

ใส่ `-n`/`--name` พร้อมชื่อที่สื่อความเสมอ (เช่น `--name "Fix login bug"`) — มันเป็นตัวกำหนด display name ที่โชว์ใน job list, session picker และ title ของ terminal

ใส่ section "suggested skills" ไว้ใน summary ด้วย เพื่อแนะนำว่า agent ควร invoke skill ไหนบ้าง

อย่า duplicate เนื้อหาที่ถูกเก็บไว้ใน artifact อื่นอยู่แล้ว (PRD, plan, ADR, issue, commit, diff) ให้อ้างถึงด้วย path หรือ URL แทน

redact ข้อมูล sensitive ทั้งหมด เช่น API key, password หรือข้อมูลที่ระบุตัวบุคคลได้ — เพราะ summary นี้จะกลายเป็น prompt ของ agent

ถ้า user ส่ง argument มา ให้ถือว่ามันคือคำอธิบายว่า session ถัดไปจะโฟกัสเรื่องอะไร แล้วปรับ summary ให้เข้ากับเรื่องนั้น
