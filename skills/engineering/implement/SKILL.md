---
name: implement
description: "ลงมือ implement งานชิ้นหนึ่งตาม spec หรือชุดของ ticket"
disable-model-invocation: true
---

implement งานที่ user อธิบายไว้ใน spec หรือ ticket

ใช้ /tdd ทุกจุดที่เป็นไปได้ ที่ seam (จุดต่อที่เราใช้ test พฤติกรรม) ซึ่งตกลงกันไว้ล่วงหน้า

รัน typecheck เป็นระยะ รัน test ทีละไฟล์เป็นระยะ และรัน test suite เต็มชุดหนึ่งครั้งตอนท้าย

พอเสร็จแล้ว ใช้ /code-review เพื่อ review งานที่ทำ

commit งานลง branch ปัจจุบัน
