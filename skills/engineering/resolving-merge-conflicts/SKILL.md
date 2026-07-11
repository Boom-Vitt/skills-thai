---
name: resolving-merge-conflicts
description: "ใช้เมื่อต้อง resolve merge/rebase conflict ของ git ที่ค้างอยู่ระหว่างทาง"
---

1. **ดูสถานะปัจจุบัน** ของ merge/rebase เช็ค git history และไฟล์ที่ conflict

2. **หาแหล่งข้อมูลต้นทาง** ของแต่ละ conflict ทำความเข้าใจให้ลึกว่าแต่ละการเปลี่ยนแปลงถูกทำไปทำไม และเจตนาดั้งเดิมคืออะไร อ่าน commit message เช็ค PR เช็ค issue/ticket ต้นทาง

3. **Resolve ทีละ hunk** รักษาเจตนาของทั้งสองฝั่งไว้ถ้าเป็นไปได้ ถ้าเข้ากันไม่ได้ ให้เลือกฝั่งที่ตรงกับเป้าหมายที่ประกาศไว้ของ merge แล้วจดบันทึก trade-off ไว้ **ห้าม** สร้างพฤติกรรมใหม่ขึ้นมาเอง ต้อง resolve เสมอ; ห้าม `--abort` เด็ดขาด

4. หา **automated checks** ของโปรเจกต์แล้วรัน — ปกติคือ typecheck ตามด้วย test แล้วค่อย format แก้ทุกอย่างที่ merge ทำพัง

5. **จบ merge/rebase ให้เรียบร้อย** stage ทุกอย่างแล้ว commit ถ้าเป็นการ rebase ให้ rebase ต่อไปจนครบทุก commit
