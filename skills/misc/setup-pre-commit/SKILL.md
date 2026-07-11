---
name: setup-pre-commit
description: ตั้งค่า Husky pre-commit hook พร้อม lint-staged (Prettier), type checking, และ test ใน repo ปัจจุบัน ใช้เมื่อผู้ใช้อยากเพิ่ม pre-commit hook, ตั้งค่า Husky, ตั้งค่า lint-staged หรือเพิ่มการ format/typecheck/test ตอน commit
---

# Setup Pre-Commit Hooks

## สิ่งที่จะตั้งค่าให้

- **Husky** pre-commit hook
- **lint-staged** ที่รัน Prettier กับไฟล์ที่ stage ไว้ทั้งหมด
- config ของ **Prettier** (ถ้ายังไม่มี)
- script **typecheck** และ **test** ใน pre-commit hook

## ขั้นตอน

### 1. ตรวจหา package manager

เช็กว่ามี `package-lock.json` (npm), `pnpm-lock.yaml` (pnpm), `yarn.lock` (yarn), `bun.lockb` (bun) ตัวไหนอยู่ก็ใช้ตัวนั้น ถ้าไม่ชัดให้ default เป็น npm

### 2. ติดตั้ง dependency

ติดตั้งเป็น devDependencies:

```
husky lint-staged prettier
```

### 3. เริ่มต้น Husky

```bash
npx husky init
```

คำสั่งนี้สร้างโฟลเดอร์ `.husky/` และเพิ่ม `prepare: "husky"` ลง package.json

### 4. สร้าง `.husky/pre-commit`

เขียนไฟล์นี้ (Husky v9+ ไม่ต้องใส่ shebang):

```
npx lint-staged
npm run typecheck
npm run test
```

**ปรับตามจริง**: แทน `npm` ด้วย package manager ที่ตรวจเจอ ถ้า repo ไม่มี script `typecheck` หรือ `test` ใน package.json ให้ตัดบรรทัดนั้นออกแล้วบอกผู้ใช้

### 5. สร้าง `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. สร้าง `.prettierrc` (ถ้ายังไม่มี)

สร้างเฉพาะเมื่อยังไม่มี config ของ Prettier อยู่เลย ใช้ค่า default เหล่านี้:

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

### 7. ตรวจสอบ

- [ ] `.husky/pre-commit` มีอยู่และ execute ได้
- [ ] `.lintstagedrc` มีอยู่
- [ ] script `prepare` ใน package.json เป็น `"husky"`
- [ ] config ของ `prettier` มีอยู่
- [ ] รัน `npx lint-staged` เพื่อยืนยันว่าทำงานได้

### 8. Commit

Stage ไฟล์ที่แก้/สร้างทั้งหมด แล้ว commit ด้วยข้อความ: `Add pre-commit hooks (husky + lint-staged + prettier)`

การ commit นี้จะวิ่งผ่าน pre-commit hook ตัวใหม่ — เป็น smoke test ที่ดีว่าทุกอย่างทำงานจริง

## หมายเหตุ

- Husky v9+ ไม่ต้องใส่ shebang ในไฟล์ hook
- `prettier --ignore-unknown` จะข้ามไฟล์ที่ Prettier parse ไม่ได้ (รูปภาพ ฯลฯ)
- pre-commit รัน lint-staged ก่อน (เร็ว เฉพาะไฟล์ที่ stage) แล้วค่อยรัน typecheck กับ test เต็ม ๆ
