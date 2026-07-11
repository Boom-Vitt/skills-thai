---
name: migrate-to-shoehorn
description: ย้าย test file จาก type assertion แบบ `as` ไปใช้ @total-typescript/shoehorn ใช้เมื่อ user พูดถึง shoehorn อยากเลิกใช้ `as` ใน test หรือต้องการส่ง test data แบบ partial
---

# ย้ายไปใช้ Shoehorn

## ทำไมต้อง shoehorn?

`shoehorn` ช่วยให้ส่ง data แบบ partial ใน test ได้โดยที่ TypeScript ยังแฮปปี้ มันมาแทน assertion แบบ `as` ด้วยทางเลือกที่ type-safe

**ใช้ใน test code เท่านั้น** ห้ามใช้ shoehorn ใน production code เด็ดขาด

ปัญหาของ `as` ใน test:

- โมเดลถูก train มาไม่ให้ใช้มัน
- ต้องระบุ type ปลายทางเองทุกครั้ง
- ต้องใช้ double-as (`as unknown as Type`) เวลาตั้งใจส่ง data ผิด type

## ติดตั้ง

```bash
npm i @total-typescript/shoehorn
```

## รูปแบบการ migrate

### object ใหญ่แต่ใช้จริงแค่ไม่กี่ property

ก่อน:

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...20 more properties
};

it("gets user by id", () => {
  // Only care about body.id but must fake entire Request
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...fake all 20 properties
  });
});
```

หลัง:

```ts
import { fromPartial } from "@total-typescript/shoehorn";

it("gets user by id", () => {
  getUser(
    fromPartial({
      body: { id: "123" },
    }),
  );
});
```

### `as Type` → `fromPartial()`

ก่อน:

```ts
getUser({ body: { id: "123" } } as Request);
```

หลัง:

```ts
import { fromPartial } from "@total-typescript/shoehorn";

getUser(fromPartial({ body: { id: "123" } }));
```

### `as unknown as Type` → `fromAny()`

ก่อน:

```ts
getUser({ body: { id: 123 } } as unknown as Request); // wrong type on purpose
```

หลัง:

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

## เลือกใช้ตัวไหนเมื่อไหร่

| ฟังก์ชัน        | กรณีใช้งาน                                          |
| --------------- | -------------------------------------------------- |
| `fromPartial()` | ส่ง data แบบ partial ที่ยังผ่าน type check          |
| `fromAny()`     | ส่ง data ที่ตั้งใจให้ผิด type (ยังได้ autocomplete) |
| `fromExact()`   | บังคับ object แบบเต็ม (ค่อยสลับเป็น fromPartial ทีหลัง) |

## ขั้นตอนการทำงาน

1. **เก็บ requirement** - ถาม user:
   - test file ไหนบ้างที่มี assertion แบบ `as` แล้วสร้างปัญหา?
   - กำลังเจอ object ใหญ่ ๆ ที่สนใจจริงแค่บาง property หรือเปล่า?
   - ต้องส่ง data ที่ตั้งใจให้ผิด type สำหรับ test เคส error ไหม?

2. **ติดตั้งแล้ว migrate**:
   - [ ] ติดตั้ง: `npm i @total-typescript/shoehorn`
   - [ ] หา test file ที่มี assertion แบบ `as`: `grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] แทนที่ `as Type` ด้วย `fromPartial()`
   - [ ] แทนที่ `as unknown as Type` ด้วย `fromAny()`
   - [ ] เพิ่ม import จาก `@total-typescript/shoehorn`
   - [ ] รัน type check เพื่อยืนยันว่าผ่าน
