# Agent Design Standard — Hybrid Lean Core + On-Demand Skills

> มาตรฐานการออกแบบ Agent สำหรับ AI Agent Team
> เรียนรู้จาก Designer Agent — ทำงานได้ดี, ประหยัด tokens, ขยายได้

---

## หลักการ

```
Lean Core (~30-120 บรรทัด)
└── identity, principles, tools, environment
    └── On-Demand Skills (เรียกเมื่อจำเป็น)
        ├── skill-thinking    ← ปัญหา/วางแผน
        ├── skill-spec        ← ต้องการสเปกชัดเจน
        ├── skill-quality     ← review/audit
        └── skill-output      ← deliver output
```

### 1. Lean Core Agent File
มีแค่สิ่งที่ต้องใช้ **ทุกครั้ง**:
- Identity (บทบาท, personality)
- หลักการทำงาน (principles)
- รายการ core skills สั้นๆ (1-2 บรรทัดต่อ skill)
- Tools ที่ใช้
- Environment references
- กฎภาษา/การสื่อสาร

### 2. On-Demand Skills
ย้ายรายละเอียดเฉพาะออกเป็น skill files:
- **thinking** — framework การคิด/วางแผน/แก้ปัญหา
- **spec** — template/checklist สำหรับสร้างของ
- **quality** — rubric/เกณฑ์ตรวจสอบ
- **output** — format การ deliver

### 3. การตัดสินใจ Core vs Skill

| อยู่ใน Core ✅ | อยู่ใน Skill 🎯 |
|---------------|-----------------|
| Identity, personality | Deep process/framework |
| Principles (3-7 ข้อ) | Checklists ยาว >10 รายการ |
| Core skills list (1 บรรทัด) | Rubric/เกณฑ์ประเมิน |
| Tools ที่ใช้ | Templates/format |
| Environment path | Domain-specific knowledge |
| กฎภาษา/การสื่อสาร | ตัวอย่าง/anti-patterns |

---

## โครงสร้าง Agent File

```markdown
# Agent: <name> (บาท, บทบาทสั้นๆ)

## Identity
<บทบาท, personality, วิธีการทำงาน>

## Core Principles
<3-7 ข้อ กระชับ>

## Core Skills
- **<skill-name>** — <1 บรรทัด>
- **<skill-name>** — <1 บรรทัด>

## Tools
- Tool 1, Tool 2, Tool 3

## Communication
<ภาษา, รูปแบบการตอบ>

## Environment
<paths, config references>
```

### ขนาดที่เหมาะสม
| ขนาด | เหมาะกับ |
|------|---------|
| ~30 br. | Agent บทบาทกว้าง เปลี่ยนบ่อย (steward) |
| ~50-80 br. | Agent ทั่วไป (designer, debugger) |
| ~80-120 br. | Agent เฉพาะทาง (minecrafter) |

---

## โครงสร้าง Skill File

```markdown
# <skill-name> — <คำอธิบาย 1 บรรทัด>

## เมื่อไหร่ควรใช้
<trigger conditions>

## Framework/Process
<ขั้นตอน>

## Checklist/Rubric
<รายการ>

## Anti-patterns
<อะไรที่ไม่ควรทำ>
```

### ขนาดที่เหมาะสม
- 40-80 บรรทัดต่อ skill
- ถ้า <20 บรรทัด → ควรยุบรวม
- ถ้า >120 บรรทัด → ควรแยกย่อย

---

## ข้อดีของ Standard นี้

| ด้าน | ก่อน (Monolithic) | หลัง (Hybrid) |
|------|-------------------|---------------|
| **Token ต่อ session** | ~4,000-6,000 | ~1,000-2,000 |
| **เริ่มต้น agent ใหม่** | ต้องออกแบบใหม่หมด | ใช้ template |
| **maintenance** | ยุ่งยาก file ใหญ่ | แยกส่วน แก้ง่าย |
| **ขยาย skill** | ต้องแก้ agent file | เพิ่ม skill file |
| **trigger เฉพาะทาง** | ไม่มี, ใช้ทุกครั้ง | เฉพาะเมื่อจำเป็น |

---

## ขั้นตอนสร้าง Agent ใหม่

1. **วิเคราะห์** — agent นี้ใช้บ่อยแค่ไหน? ต้องรู้อะไรบ้าง?
2. **เขียน Core** — identity + principles + skills list + tools (~50-80 br.)
3. **แยก Skills** — อะไรที่ใช้เป็นครั้งคราว? แยกออกมา (~40-80 br. ต่อ skill)
4. **ตรวจสอบ** — core มีแค่ของที่ต้องใช้ทุกครั้งจริงๆ?
5. **วัดผล** — token ที่ประหยัดได้เทียบกับ monolithic เดิม
6. **Commit** — ขึ้น GitHub `ai-agent-team`

---

## 🚧 Guardrails — ป้องกัน Agent ติด loop + พลาด

### 1. Step Budget (จำกัดขั้นตอน)

ทุก agent ต้องมีขีดจำกัดก่อน escalate:

```markdown
## Step Budget
- Max steps ก่อนหยุด: <N> (เริ่มที่ 5-10)
- Max tool calls ต่อ task: <N>
- เมื่อถึงขีดจำกัด → สรุป output ที่มีอยู่ + บอก user ว่าติด limit
```

### 2. Stop Conditions (เมื่อไหร่ควรหยุด)

ทุก agent ต้องมีใน core ว่า:
- **"หยุดและส่ง output ทันทีเมื่อ:"** — list of conditions (e.g., กรณี debugger: เจอ root cause, กรณี designer: deliver HTML)
- **"ไม่ต้องทำต่อถ้า:"** — กรณีที่ไม่ควร waste tokens (e.g., ข้อมูลไม่พอ, task ambiguous)

### 3. Error Protocol (เมื่อ tool ล้ม)

กฎตายตัว ทุก agent:
```
1. Tool return error → REPORT ทันที (ห้ามแก้เงียบ)
2. รายงาน: error อะไร, tool ไหน, ลอง retry แล้วหรือยัง
3. 3 ทางเลือกให้ user:
   a) ลองใหม่ (retry)
   b) ข้าม tool นี้ไป
   c) ยกเลิก task
```

### 4. Forbidden Actions (ห้ามทำ)

เพิ่มใน agent core — ตัวอย่าง:
```markdown
## Forbidden
- ห้ามแก้ไข config/ไฟล์ระบบโดยไม่ถาม
- ห้ามใช้ destructive command (rm -rf, docker rm -f) โดยไม่ confirm
- ห้ามเขียนทับไฟล์โดยไม่ backup
- ห้าม deploy/push โดยไม่ review
```

---

## ✅ Self-Review Protocol — ตรวจงานตัวเองก่อนส่ง

ทุก agent ต้องมี self-review ก่อน deliver output:

```markdown
## Self-Review (ทำก่อนส่งทุกครั้ง)
1. ตรง requirements ไหม? → ถ้าไม่ → แก้
2. มีข้อมูลที่ขาด / ambiguous / hallucinate ไหม? → ถ้ามี → บอก user
3. output นี้ safe ไหม? (ไม่ลบ/แก้ของสำคัญ) → ถ้าไม่ → ขอ confirm
4. output กระชับพอไหม? ไม่มีส่วนที่ไม่จำเป็น? → ถ้าไม่ → trim
```

---

## 📏 Eval Rubric — วัดว่า Agent ทำงานดีแค่ไหน

Rubric สำหรับประเมิน output agent:

| เกณฑ์ | 1-2 (ไม่ผ่าน) | 3 (พอใช้) | 4-5 (ดี) |
|-------|-------------|-----------|---------|
| **ตรง requirement** | พลาดประเด็นหลัก | ตรงบางส่วน | ตรงครบทุกข้อ |
| **ไม่ hallucinate** | มีข้อมูลเท็จ | มีข้อมูลไม่ตรงเล็กน้อย | อ้างอิง verified |
| **error handling** | ปิด error เงียบ | รายงานแต่ไม่แก้ | รายงาน + เสนอทางเลือก |
| **efficiency** | ทำเกิน scope เยอะ | มีบ้างเล็กน้อย | ทำเท่าที่จำเป็น |
| **security aware** | สั่ง destructive โดยไม่ถาม | ถามบ้าง | ถามทุกครั้ง |

ใช้หลังจบ session เพื่อให้ feedback ตัว agent ได้

---

## 🧠 Industry Lessons — Anti-patterns ที่เจอในสนาม

| Anti-pattern | อาการ | ทางแก้ใน standard เรา |
|-------------|------|---------------------|
| **Blurring agent/tool** | เอาธุรกิจ logic ไว้ใน prompt → พังตอนเปลี่ยน | Tool boundaries: tool = logic, agent = judgment |
| **No Context Budget** | prompt ยาวล้น context | Step budget + stop conditions |
| **Swallowing Errors** | tool fail → agent พล่ามต่อ | Error protocol |
| **Prompt and Pray** | ไม่มี verification | Self-review protocol |
| **No Eval** | แก้สุ่มๆ ไม่รู้ effect | Eval rubric |
| **Infinite Loop** | เรียก tool ซ้ำๆ ไม่รู้จบ | Step budget + circuit breaker |

---

## 📋 รายการตรวจสอบ (Checklist) ก่อน commit Agent

เมื่อสร้าง/แก้ agent:

- [ ] Core ถูกว่า 30-120 บรรทัด?
- [ ] มี Identity + Principles?
- [ ] มี Step Budget?
- [ ] มี Stop Conditions?
- [ ] มี Error Protocol?
- [ ] มี Self-Review?
- [ ] มี Forbidden Actions?
- [ ] Skill files 40-80 br. ต่อ skill?
- [ ] Eval rubric สำหรับวัด output?
- [ ] push ขึ้น GitHub?

---

## ✅ Decision: Execution Patterns

> ตัดสินใจแล้ว — 29 มิ.ย. 69

| Agent | Pattern | Decision |
|-------|---------|----------|
| **designer** 🎨 | Reflection + ReAct | ✅ Designer standard (thinking + self-review) |
| **backend** ⚙️ | Plan-and-Execute + Decision Tree | ✅ เริ่มจาก Analyze → Choose Architecture → Plan → Execute → Review |
| **debugger** 🐛 | — | ⏳ รอตัดสินใจ |
| **scribe** 📝 | — | ⏳ รอตัดสินใจ |
| **steward** 🏆 | — | ⏳ รอตัดสินใจ |
| **minecrafter** ⛏️ | — | ⏳ รอตัดสินใจ |

### รายละเอียดที่ตัดสินใจแล้ว

**Backend — Plan-and-Execute + Decision Tree:**
```
1. Analyze Requirements
2. Choose Architecture ตาม scale (Layered / Hexagonal / Clean+DDD)
3. Plan (API → DB → Service → Test)
4. Execute ทีละขั้นตาม SoC
5. Self-Review + Deliver
```
[ดู full pattern ได้ที่ `agents/backend.md`]

---

## บทเรียนจาก Designer Agent

| รอบ | ขนาด | ปัญหา |
|-----|------|-------|
| v1 (GPT 5.5) | 300+ br. | กิน tokens อ่วม, maintenance ยาก |
| v2 (เขียนเอง) | 81 br. | บางไป, ขาด process |
| **v3 Final** | **74 br. + 4 skills** | **พอดี, ยืดหยุ่น, ประหยัด** |

### Token ที่ประหยัด
- ก่อน: 300+ br. ~4,500 tokens **ทุกครั้ง**
- หลัง: 74 br. ~1,000 tokens (หรือ ~2,000 ถ้าใช้ 1 skill)
- **ประหยัด ~55-85%** ต่อ session

---

*Standard version 2.0 — 29 Jun 2026*
