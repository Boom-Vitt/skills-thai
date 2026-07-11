---
name: git-guardrails-claude-code
description: ตั้งค่า hook ของ Claude Code เพื่อบล็อกคำสั่ง git อันตราย (push, reset --hard, clean, branch -D ฯลฯ) ก่อนที่มันจะถูกรัน ใช้เมื่อ user อยากป้องกัน git operation แบบทำลายล้าง อยากเพิ่ม git safety hook หรืออยากบล็อก git push/reset ใน Claude Code
---

# ตั้งค่า Git Guardrails

ตั้งค่า PreToolUse hook ที่คอยดักและบล็อกคำสั่ง git อันตรายก่อนที่ Claude จะรันมัน

## อะไรบ้างที่ถูกบล็อก

- `git push` (ทุก variant รวมถึง `--force`)
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

เมื่อถูกบล็อก Claude จะเห็นข้อความบอกว่ามันไม่มีสิทธิ์เข้าถึงคำสั่งเหล่านี้

## ขั้นตอน

### 1. ถาม scope

ถาม user: จะติดตั้งสำหรับ **โปรเจกต์นี้เท่านั้น** (`.claude/settings.json`) หรือ **ทุกโปรเจกต์** (`~/.claude/settings.json`)?

### 2. copy script ของ hook

script ที่แนบมากับ skill นี้อยู่ที่: [scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

copy ไปไว้ที่ตำแหน่งปลายทางตาม scope:

- **Project**: `.claude/hooks/block-dangerous-git.sh`
- **Global**: `~/.claude/hooks/block-dangerous-git.sh`

ทำให้มันรันได้ด้วย `chmod +x`

### 3. เพิ่ม hook ลงใน settings

เพิ่มลงในไฟล์ settings ที่ตรงกับ scope:

**Project** (`.claude/settings.json`):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

**Global** (`~/.claude/settings.json`):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

ถ้าไฟล์ settings มีอยู่แล้ว ให้ merge hook เข้าไปใน array `hooks.PreToolUse` เดิม — อย่าเขียนทับ setting อื่น

### 4. ถามเรื่องการปรับแต่ง

ถามว่า user อยากเพิ่มหรือลบ pattern จากรายการที่ถูกบล็อกไหม แล้วแก้ script ที่ copy มาให้ตรงตามนั้น

### 5. ตรวจสอบ

รัน test สั้น ๆ:

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

ควรจบด้วย exit code 2 และ print ข้อความ BLOCKED ออกทาง stderr
