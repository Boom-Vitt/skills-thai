---
name: design-an-interface
description: สร้าง interface design หลายแบบที่ต่างกันสุดขั้วสำหรับ module หนึ่ง โดยใช้ sub-agent แบบ parallel ใช้เมื่อ user อยากออกแบบ API, สำรวจตัวเลือกของ interface, เปรียบเทียบรูปทรงของ module หรือพูดถึง "design it twice"
---

# Design an Interface

อิงจากแนวคิด "Design It Twice" ในหนังสือ "A Philosophy of Software Design": ไอเดียแรกของคุณแทบไม่เคยเป็นไอเดียที่ดีที่สุด สร้าง design หลายแบบที่ต่างกันสุดขั้ว แล้วค่อยเปรียบเทียบ

## Workflow

### 1. เก็บ Requirements

ก่อนออกแบบ ต้องเข้าใจก่อนว่า:

- [ ] module นี้แก้ปัญหาอะไร?
- [ ] ใครคือ caller? (module อื่น, ผู้ใช้ภายนอก, test)
- [ ] operation หลักคืออะไรบ้าง?
- [ ] มี constraint อะไรไหม? (performance, compatibility, pattern ที่มีอยู่แล้ว)
- [ ] อะไรควรซ่อนไว้ข้างใน อะไรควร expose ออกมา?

ถามว่า: "module นี้ต้องทำอะไรได้บ้าง? ใครจะเป็นคนใช้?"

### 2. สร้าง Design (Sub-Agent แบบ Parallel)

spawn sub-agent 3 ตัวขึ้นไปพร้อมกันด้วย Task tool แต่ละตัวต้อง produce แนวทางที่**ต่างกันสุดขั้ว**

```
Prompt template for each sub-agent:

Design an interface for: [module description]

Requirements: [gathered requirements]

Constraints for this design: [assign a different constraint to each agent]
- Agent 1: "Minimize method count - aim for 1-3 methods max"
- Agent 2: "Maximize flexibility - support many use cases"
- Agent 3: "Optimize for the most common case"
- Agent 4: "Take inspiration from [specific paradigm/library]"

Output format:
1. Interface signature (types/methods)
2. Usage example (how caller uses it)
3. What this design hides internally
4. Trade-offs of this approach
```

### 3. นำเสนอ Design

โชว์แต่ละ design พร้อม:

1. **Interface signature** - types, methods, params
2. **ตัวอย่างการใช้งาน** - caller ใช้มันจริง ๆ ยังไงในทางปฏิบัติ
3. **มันซ่อนอะไรไว้** - ความซับซ้อนที่เก็บไว้ข้างใน

นำเสนอทีละ design เพื่อให้ user ซึมซับแต่ละแนวทางได้ก่อนถึงขั้นเปรียบเทียบ

### 4. เปรียบเทียบ Design

หลังโชว์ครบทุก design แล้ว เปรียบเทียบกันในแง่:

- **ความเรียบง่ายของ interface**: method น้อยกว่า, params เรียบง่ายกว่า
- **General-purpose vs specialized**: ความยืดหยุ่น vs ความโฟกัส
- **ประสิทธิภาพของ implementation**: รูปทรงนี้เปิดทางให้ internals ทำงานได้มีประสิทธิภาพไหม?
- **ความลึก (depth)**: interface เล็กที่ซ่อนความซับซ้อนไว้เยอะ (ดี) vs interface ใหญ่แต่ implementation บาง (แย่)
- **ใช้ให้ถูกได้ง่าย** vs **ใช้ผิดได้ง่าย**

อภิปราย trade-off เป็นร้อยแก้ว ไม่ใช่ตาราง เน้นจุดที่ design แตกต่างกันมากที่สุด

### 5. สังเคราะห์

บ่อยครั้ง design ที่ดีที่สุดเกิดจากการรวม insight จากหลายตัวเลือกเข้าด้วยกัน ถามว่า:

- "design ไหนเข้ากับ use case หลักของคุณที่สุด?"
- "มีชิ้นส่วนจาก design อื่นที่ควรหยิบมาผสมไหม?"

## เกณฑ์การประเมิน

จากหนังสือ "A Philosophy of Software Design":

**ความเรียบง่ายของ interface**: method น้อยกว่า params เรียบง่ายกว่า = เรียนรู้ง่ายและใช้ถูกต้องได้ง่ายกว่า

**General-purpose**: รองรับ use case ในอนาคตได้โดยไม่ต้องแก้ แต่ระวังการ generalize เกินจำเป็น

**ประสิทธิภาพของ implementation**: รูปทรงของ interface เปิดทางให้ implement ได้มีประสิทธิภาพไหม? หรือบังคับให้ internals ต้องเขียนแบบฝืน ๆ?

**ความลึก (depth)**: interface เล็กที่ซ่อนความซับซ้อนไว้เยอะ = deep module (ดี) interface ใหญ่แต่ implementation บาง = shallow module (เลี่ยง)

## Anti-Patterns

- อย่าปล่อยให้ sub-agent produce design ที่คล้ายกัน - ต้องบังคับให้ต่างกันสุดขั้ว
- อย่าข้ามขั้นเปรียบเทียบ - คุณค่าอยู่ที่ contrast
- อย่าลงมือ implement - นี่เป็นเรื่องรูปทรงของ interface ล้วน ๆ
- อย่าประเมินจากความยาก-ง่ายของการ implement
