---
name: obsidian-vault
description: ค้นหา สร้าง และจัดการ note ใน Obsidian vault ด้วย wikilinks และ index note ใช้เมื่อ user อยากหา สร้าง หรือจัดระเบียบ note ใน Obsidian
---

# Obsidian Vault

## ตำแหน่งของ vault

`/mnt/d/Obsidian Vault/AI Research/`

ส่วนใหญ่เป็นไฟล์วางแบน ๆ อยู่ที่ระดับ root

## ธรรมเนียมการตั้งชื่อ

- **Index note**: รวมหัวข้อที่เกี่ยวข้องกันไว้ด้วยกัน (เช่น `Ralph Wiggum Index.md`, `Skills Index.md`, `RAG Index.md`)
- ใช้ **Title Case** กับชื่อ note ทุกอัน
- ไม่ใช้ folder ในการจัดระเบียบ - ใช้ link กับ index note แทน

## การ link

- ใช้ syntax `[[wikilinks]]` ของ Obsidian: `[[Note Title]]`
- แต่ละ note จะ link ไปหา dependency/note ที่เกี่ยวข้องไว้ที่ท้ายไฟล์
- index note ก็คือ list ของ `[[wikilinks]]` เฉย ๆ

## Workflow

### ค้นหา note

```bash
# Search by filename
find "/mnt/d/Obsidian Vault/AI Research/" -name "*.md" | grep -i "keyword"

# Search by content
grep -rl "keyword" "/mnt/d/Obsidian Vault/AI Research/" --include="*.md"
```

หรือจะใช้ Grep/Glob tool ตรง ๆ บน path ของ vault ก็ได้

### สร้าง note ใหม่

1. ตั้งชื่อไฟล์แบบ **Title Case**
2. เขียนเนื้อหาให้เป็นหนึ่งหน่วยของการเรียนรู้ (ตามกฎของ vault)
3. เพิ่ม `[[wikilinks]]` ไปยัง note ที่เกี่ยวข้องไว้ที่ท้ายไฟล์
4. ถ้าเป็นส่วนหนึ่งของลำดับที่มีเลขกำกับ ให้ใช้ระบบเลขแบบลำดับชั้น

### หา note ที่เกี่ยวข้อง

ค้นหา `[[Note Title]]` ทั่วทั้ง vault เพื่อหา backlink:

```bash
grep -rl "\\[\\[Note Title\\]\\]" "/mnt/d/Obsidian Vault/AI Research/"
```

### หา index note

```bash
find "/mnt/d/Obsidian Vault/AI Research/" -name "*Index*"
```
