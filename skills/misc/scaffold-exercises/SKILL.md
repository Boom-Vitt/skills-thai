---
name: scaffold-exercises
description: สร้างโครงสร้าง directory ของ exercise พร้อม section, problem, solution และ explainer ให้ผ่าน linting ใช้เมื่อ user อยาก scaffold exercises, สร้าง exercise stub หรือตั้ง section ใหม่ของคอร์ส
---

# Scaffold Exercises

สร้างโครงสร้าง directory ของ exercise ให้ผ่าน `pnpm ai-hero-cli internal lint` แล้ว commit ด้วย `git commit`

## การตั้งชื่อ directory

- **Section**: `XX-section-name/` อยู่ใน `exercises/` (เช่น `01-retrieval-skill-building`)
- **Exercise**: `XX.YY-exercise-name/` อยู่ในแต่ละ section (เช่น `01.03-retrieval-with-bm25`)
- เลข section = `XX`, เลข exercise = `XX.YY`
- ชื่อเป็น dash-case (ตัวพิมพ์เล็ก คั่นด้วย hyphen)

## Variant ของ exercise

แต่ละ exercise ต้องมี subfolder อย่างน้อยหนึ่งแบบจากรายการนี้:

- `problem/` - พื้นที่ทำงานของผู้เรียน มี TODO ให้ทำ
- `solution/` - implementation อ้างอิง
- `explainer/` - เนื้อหาเชิงแนวคิด ไม่มี TODO

ตอน stub ให้ default เป็น `explainer/` ยกเว้นแผนระบุไว้เป็นอย่างอื่น

## ไฟล์ที่ต้องมี

แต่ละ subfolder (`problem/`, `solution/`, `explainer/`) ต้องมี `readme.md` ที่:

- **ไม่ว่างเปล่า** (ต้องมีเนื้อหาจริง แค่บรรทัด title บรรทัดเดียวก็ใช้ได้)
- ไม่มีลิงก์เสีย

ตอน stub ให้สร้าง readme แบบ minimal ที่มี title กับคำอธิบาย:

```md
# Exercise Title

Description here
```

ถ้า subfolder มี code ก็ต้องมี `main.ts` ด้วย (>1 บรรทัด) แต่สำหรับ stub มีแค่ readme อย่างเดียวก็พอ

## Workflow

1. **Parse แผน** - ดึงชื่อ section, ชื่อ exercise และประเภท variant ออกมา
2. **สร้าง directory** - `mkdir -p` สำหรับแต่ละ path
3. **สร้าง readme stub** - `readme.md` หนึ่งไฟล์ต่อหนึ่ง variant folder พร้อม title
4. **รัน lint** - `pnpm ai-hero-cli internal lint` เพื่อ validate
5. **แก้ error ที่เจอ** - วนแก้จนกว่า lint จะผ่าน

## สรุปกฎของ lint

ตัว linter (`pnpm ai-hero-cli internal lint`) เช็คว่า:

- แต่ละ exercise มี subfolder (`problem/`, `solution/`, `explainer/`)
- มี `problem/`, `explainer/` หรือ `explainer.1/` อย่างน้อยหนึ่งอัน
- `readme.md` มีอยู่และไม่ว่างเปล่าใน subfolder หลัก
- ไม่มีไฟล์ `.gitkeep`
- ไม่มีไฟล์ `speaker-notes.md`
- ไม่มีลิงก์เสียใน readme
- ไม่มีคำสั่ง `pnpm run exercise` ใน readme
- ต้องมี `main.ts` ในแต่ละ subfolder ยกเว้นเป็นแบบ readme อย่างเดียว

## การย้าย/เปลี่ยนชื่อ exercise

เวลาเปลี่ยนเลขหรือย้าย exercise:

1. ใช้ `git mv` (ไม่ใช่ `mv`) เพื่อ rename directory - เก็บ history ของ git ไว้
2. อัปเดต prefix ตัวเลขให้ลำดับยังถูกต้อง
3. รัน lint ใหม่หลังย้ายเสร็จ

ตัวอย่าง:

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## ตัวอย่าง: stub จากแผน

สมมติได้แผนแบบนี้มา:

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

ให้สร้าง:

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

แล้วสร้าง readme stub:

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
