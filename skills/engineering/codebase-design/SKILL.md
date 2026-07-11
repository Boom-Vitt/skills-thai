---
name: codebase-design
description: คำศัพท์กลางสำหรับออกแบบ deep module ใช้เมื่อ user อยากออกแบบหรือปรับปรุง interface ของ module, หาโอกาสทำ module ให้ deep ขึ้น, ตัดสินใจว่า seam ควรอยู่ตรงไหน, ทำ code ให้ test ง่ายขึ้นหรือให้ AI นำทางได้ง่ายขึ้น หรือเมื่อ skill อื่นต้องใช้คำศัพท์เรื่อง deep module
---

# การออกแบบ Codebase

ออกแบบ **deep module**: พฤติกรรมจำนวนมากอยู่หลัง interface เล็ก ๆ วางไว้ที่ seam (จุดต่อที่เราสลับพฤติกรรมได้โดยไม่ต้องแก้ code ตรงนั้น) ที่สะอาด และ test ได้ผ่าน interface นั้น ใช้ภาษาและหลักการชุดนี้ทุกที่ที่มีการออกแบบหรือปรับโครงสร้าง code เป้าหมายคือ leverage ให้ฝั่ง caller, locality ให้คนดูแล code และความ testable ให้ทุกคน

## Glossary

ใช้คำเหล่านี้แบบเป๊ะ ๆ — อย่าเปลี่ยนไปใช้ "component," "service," "API," หรือ "boundary" แทน ภาษาที่สม่ำเสมอคือหัวใจของเรื่องนี้

**Module** — อะไรก็ตามที่มี interface กับ implementation ตั้งใจให้ไม่ผูกกับขนาด: จะเป็น function, class, package หรือ slice ที่พาดผ่านหลาย tier ก็ได้ _เลี่ยง_: unit, component, service

**Interface** — ทุกอย่างที่ caller ต้องรู้เพื่อใช้ module ได้ถูกต้อง: ไม่ใช่แค่ type signature แต่รวมถึง invariant, ข้อบังคับเรื่องลำดับการเรียก, รูปแบบ error, config ที่ต้องมี และคุณสมบัติด้าน performance ด้วย _เลี่ยง_: API, signature (แคบเกินไป — สองคำนี้พูดถึงแค่ผิวระดับ type เท่านั้น)

**Implementation** — สิ่งที่อยู่ข้างใน module ตัวเนื้อ code ของมัน ต่างจาก **Adapter**: ของชิ้นหนึ่งอาจเป็น adapter เล็กที่มี implementation ใหญ่ (Postgres repo) หรือ adapter ใหญ่ที่มี implementation เล็ก (fake ใน memory) ก็ได้ ใช้คำว่า "adapter" เมื่อประเด็นอยู่ที่ seam นอกนั้นใช้ "implementation"

**Depth** — leverage ที่ตัว interface: ปริมาณพฤติกรรมที่ caller (หรือ test) ใช้งานได้ต่อหน่วยของ interface ที่ต้องเรียนรู้ module จะ **deep** เมื่อพฤติกรรมจำนวนมากอยู่หลัง interface เล็ก ๆ และ **shallow** เมื่อ interface ซับซ้อนแทบเท่า implementation

**Seam** _(Michael Feathers)_ — จุดที่เราเปลี่ยนพฤติกรรมได้โดยไม่ต้องแก้ code ตรงจุดนั้น; คือ*ตำแหน่ง*ที่ interface ของ module อาศัยอยู่ จะวาง seam ตรงไหนเป็นการตัดสินใจออกแบบอีกเรื่องหนึ่ง แยกจากว่าอะไรจะอยู่ข้างหลังมัน _เลี่ยง_: boundary (ความหมายชนกับ bounded context ของ DDD)

**Adapter** — ของจริงชิ้นหนึ่งที่ทำตาม interface ณ seam หนึ่ง อธิบาย*บทบาท* (เสียบเข้าช่องไหน) ไม่ใช่เนื้อใน (ข้างในมีอะไร)

**Leverage** — สิ่งที่ caller ได้จาก depth: ความสามารถมากขึ้นต่อหน่วย interface ที่ต้องเรียนรู้ implementation เดียวคืนทุนกระจายไปทั่ว N จุดเรียกใช้และ M test

**Locality** — สิ่งที่คนดูแล code ได้จาก depth: การเปลี่ยนแปลง, bug, ความรู้ และการ verify กระจุกอยู่ที่เดียวแทนที่จะกระจายไปตาม caller แก้ครั้งเดียว หายทุกที่

## Deep vs shallow

**Deep module** = interface เล็ก + implementation เยอะ:

```
┌─────────────────────┐
│   Small Interface   │  ← Few methods, simple params
├─────────────────────┤
│                     │
│  Deep Implementation│  ← Complex logic hidden
│                     │
└─────────────────────┘
```

**Shallow module** = interface ใหญ่ + implementation น้อย (ควรเลี่ยง):

```
┌─────────────────────────────────┐
│       Large Interface           │  ← Many methods, complex params
├─────────────────────────────────┤
│  Thin Implementation            │  ← Just passes through
└─────────────────────────────────┘
```

เวลาออกแบบ interface ให้ถามว่า:

- ลดจำนวน method ลงได้ไหม?
- ทำ parameter ให้เรียบง่ายกว่านี้ได้ไหม?
- ซ่อนความซับซ้อนไว้ข้างในได้มากกว่านี้ไหม?

## หลักการ

- **Depth เป็นคุณสมบัติของ interface ไม่ใช่ของ implementation** deep module ข้างในอาจประกอบจากชิ้นเล็ก ๆ ที่ mock ได้ สลับได้ — แค่ชิ้นพวกนั้นไม่ได้เป็นส่วนหนึ่งของ interface และ module มี **internal seam** ได้ (เป็นของ private ภายใน implementation ใช้โดย test ของตัวมันเอง) ควบคู่กับ **external seam** ที่ interface ของมัน
- **Deletion test** ลองจินตนาการว่าลบ module ทิ้ง ถ้าความซับซ้อนหายไปด้วย แปลว่ามันเป็นแค่ pass-through ถ้าความซับซ้อนโผล่กลับมากระจายอยู่ใน caller ทั้ง N ที่ แปลว่ามันทำหน้าที่คุ้มค่าตัวอยู่จริง
- **Interface คือพื้นผิวสำหรับ test** caller กับ test ข้าม seam เดียวกัน ถ้าเราอยาก test *ทะลุ* interface เข้าไปข้างใน แปลว่า module น่าจะมีรูปทรงที่ผิด
- **Adapter เดียวคือ seam สมมุติ สอง adapter ถึงเป็น seam จริง** อย่าใส่ seam ถ้าไม่มีอะไรแปรผันข้ามมันจริง ๆ

## ออกแบบเพื่อความ testable

Interface ที่ดีทำให้การ test เป็นเรื่องธรรมชาติ:

1. **รับ dependency เข้ามา อย่าสร้างเอง**

   ```typescript
   // Testable
   function processOrder(order, paymentGateway) {}

   // Hard to test
   function processOrder(order) {
     const gateway = new StripeGateway();
   }
   ```

2. **คืนผลลัพธ์ อย่าสร้าง side effect**

   ```typescript
   // Testable
   function calculateDiscount(cart): Discount {}

   // Hard to test
   function applyDiscount(cart): void {
     cart.total -= discount;
   }
   ```

3. **พื้นผิวเล็ก** method น้อย = test ที่ต้องเขียนน้อย parameter น้อย = setup test ง่ายขึ้น

## ความสัมพันธ์

- **Module** หนึ่งตัวมี **Interface** เดียวเป๊ะ ๆ (พื้นผิวที่มันเปิดให้ caller และ test)
- **Depth** เป็นคุณสมบัติของ **Module** วัดเทียบกับ **Interface** ของมัน
- **Seam** คือตำแหน่งที่ **Interface** ของ **Module** อาศัยอยู่
- **Adapter** อยู่ที่ **Seam** และทำตาม **Interface**
- **Depth** ให้ **Leverage** แก่ caller และให้ **Locality** แก่คนดูแล code

## Framing ที่ตัดทิ้งไปแล้ว

- **Depth แบบอัตราส่วนจำนวนบรรทัด implementation ต่อบรรทัด interface** (Ousterhout): จูงใจให้ปั้น implementation ให้บวมเปล่า ๆ เราใช้ depth แบบ leverage แทน
- **"Interface" ในความหมาย keyword `interface` ของ TypeScript หรือ public method ของ class**: แคบเกินไป — interface ในที่นี้รวมทุกข้อเท็จจริงที่ caller ต้องรู้
- **"Boundary"**: ความหมายชนกับ bounded context ของ DDD ให้พูดว่า **seam** หรือ **interface**

## ไปให้ลึกกว่านี้

- **ทำ cluster ให้ deep ขึ้นตาม dependency ของมัน** — ดู [DEEPENING.md](DEEPENING.md): หมวดหมู่ของ dependency, วินัยเรื่อง seam และการ test แบบ replace-don't-layer
- **สำรวจ interface ทางเลือกอื่น** — ดู [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md): ปั่น sub-agent ขนานกันหลายตัวให้ออกแบบ interface หลายแบบที่ต่างกันสุดขั้ว แล้วเอามาเทียบกันด้วย depth, locality และตำแหน่งของ seam
