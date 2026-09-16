# 67160244_Lab_Week9
# แลป OLTP / OLAP / Pivot Table — วิธีรัน

วิชา Data Warehouse · ข้อมูลจำลองทั้งหมด (รวมถึงวันที่ในอนาคต) ไม่มีภาษี ส่วนลด หรือคืนสินค้า

## โครงสร้างไฟล์

```
student/
├── lab.py                  # สร้างฐานข้อมูลสำหรับแลป (รันครั้งเดียวตอนเริ่ม)
├── query.py                 # ใช้รัน SELECT query ทีละไฟล์
├── queries.sql               # ไฟล์ทดลอง SQL (ไม่ใช่คำตอบ)
├── oltp_demo.py              # ภารกิจ 1 — แก้สถานะออเดอร์
├── pivot_student.py           # ภารกิจ 4 — Pivot ด้วย pandas
├── requirements.txt
├── q01.sql – q12.sql          # คำตอบ SQL แต่ละข้อ (สร้างเอง)
├── Answer_Template.md         # แม่แบบรายงาน
└── data/                     # ฐานข้อมูล (สร้างจาก lab.py)
    ├── warehouse.db / .csv    # ชุดหลัก ใช้ตอบทุกข้อในแลปหลัก
    ├── extended.db / .csv     # ชุดต่อยอด
    └── oltp.db               # ใช้เฉพาะภารกิจ 1
```

## 1. เตรียมเครื่อง (ทำครั้งเดียว)

เปิด Terminal (PowerShell) แล้ว `cd` เข้าโฟลเดอร์ `student` ที่มี `lab.py` อยู่ จากนั้นรันทีละบรรทัด:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python lab.py
```

**ถ้า `Activate.ps1` ขึ้น error เรื่อง execution policy** ให้รันบรรทัดนี้ก่อนแล้วค่อย activate ใหม่ (ใช้ได้แค่ session นี้ ไม่กระทบเครื่องถาวร):

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

**ถ้า activate ไม่ได้เลยไม่ว่าวิธีไหน** ข้าม activate ไปเลย แล้วเรียก python ในตำแหน่งเต็มแทนทุกคำสั่งต่อจากนี้:

```powershell
.venv\Scripts\python.exe -m pip install -r requirements.txt
.venv\Scripts\python.exe lab.py
```
(แทนที่ `python` ด้วย `.venv\Scripts\python.exe` ในทุกคำสั่งด้านล่าง หากใช้วิธีนี้)

**ตรวจว่าพร้อมแล้ว** — ควรเห็น path ขึ้นต้นด้วย `...\.venv`:
```powershell
python -c "import sys; print(sys.executable)"
```

> `python lab.py` ถ้าขึ้นว่า "Prepared data already exists" คือฐานข้อมูลมีอยู่แล้ว ถือว่าปกติ ไม่ต้องทำอะไรต่อ
> ⚠️ ใช้ `python lab.py --reset` เฉพาะตอนต้องการสร้างฐานข้อมูลใหม่ทั้งหมดเท่านั้น เพราะจะ**ลบการแก้ไขทั้งสามฐานข้อมูลทิ้ง**

## 2. รัน query ทีละข้อ (ภารกิจ 2, 3, 5)

`query.py` รับ SELECT ได้ครั้งละ 1 คำสั่งเท่านั้น เปิดฐานแบบอ่านอย่างเดียว

1. สร้างไฟล์ `q01.sql`, `q02.sql`, ... ใส่ SQL ของแต่ละข้อ (1 ไฟล์ = 1 SELECT)
2. รันทีละไฟล์:
   ```powershell
   python query.py data/warehouse.db q01.sql
   ```
3. เก็บผลลัพธ์ลงไฟล์แทนการจดมือ (ใช้ทำรายงานได้เลย):
   ```powershell
   python query.py data/warehouse.db q01.sql > q01_out.txt
   ```
4. ทำซ้ำจนครบ `q02` – `q07`, `q12` (OLAP), `q08` (Pivot SQL), `q09` – `q11` (ตรวจความถูกต้อง)

โจทย์ต่อยอด (ก) ใช้ฐานข้อมูลคนละไฟล์ เปลี่ยนแค่ path:
```powershell
python query.py data/extended.db q13.sql
```

## 3. ภารกิจ 1 — OLTP (oltp_demo.py)

เปิดไฟล์ แก้ `pass` ตามใบงาน (UPDATE พร้อม WHERE ตรวจ order_id และ status) แล้วรัน **สองครั้งติดกัน** เก็บผลทั้งสองรอบ:

```powershell
python oltp_demo.py
python oltp_demo.py
```

รอบแรกควรแก้ 1 แถว รอบสองควรแก้ 0 แถว (เพราะสถานะเปลี่ยนไปแล้วตั้งแต่รอบแรก)

## 4. ภารกิจ 4 — Pivot (pivot_student.py)

เติมโค้ดแทน `# TODO P1` ถึง `# TODO P4` ในไฟล์ตามใบงาน แล้วรัน:

```powershell
python pivot_student.py
```

ควรเห็นตาราง P1, P2 พิมพ์ออกมา และข้อความ `assert OK: 1390` (ถ้า assert error แปลว่ายังมีจุดผิด ให้ตรวจสูตร pivot ก่อน)

ไฟล์ที่ควรถูกสร้างขึ้นในโฟลเดอร์เดียวกัน:
- `pivot_province_month.csv`
- `pivot_september.csv`

**ทดลองข้อผิดพลาด**: ลบ `aggfunc='sum'` ออกจาก P1 ชั่วคราว รันใหม่ ถ่ายภาพผลที่ได้ (Bangkok/ก.ย. จะกลายเป็น 270) แล้วใส่ `aggfunc='sum'` กลับคืน รันอีกครั้ง ถ่ายภาพผลที่ถูก (540)

## 5. Excel PivotTable (ถ้ามี Excel — ไม่บังคับ)

เปิด `data/warehouse.csv` → `Insert > PivotTable > New Worksheet` → Rows=`province`, Columns=`month`, Values=`amount` (ตั้งเป็น **Sum**) → ลาก `category` ไป Filters แล้วเลือก **Drink** → Save As `pivot.xlsx`

ถ้าไม่มี Excel ใช้ pandas กรอง `category=='Drink'` แทนได้คะแนนเท่ากัน (ดูตัวอย่างโค้ดในรายงาน)

## 6. โจทย์ต่อยอด (ไม่บังคับ)

สร้าง `challenge.py` ตามใบงาน (สำเนา warehouse.db → challenge.db, เพิ่มวันที่ 2026-10-01 และรายการ O1007/O1008) แล้วรัน:

```powershell
python challenge.py
```

## รันครบทุกอย่างตั้งแต่ต้น (สรุปคำสั่งทั้งหมด)

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python lab.py

python oltp_demo.py
python oltp_demo.py

python query.py data/warehouse.db q01.sql
python query.py data/warehouse.db q02.sql
python query.py data/warehouse.db q03.sql
python query.py data/warehouse.db q04.sql
python query.py data/warehouse.db q05.sql
python query.py data/warehouse.db q06.sql
python query.py data/warehouse.db q07.sql
python query.py data/warehouse.db q08.sql
python query.py data/warehouse.db q09.sql
python query.py data/warehouse.db q10.sql
python query.py data/warehouse.db q11.sql
python query.py data/warehouse.db q12.sql

python pivot_student.py
```

## แก้ปัญหาที่เจอบ่อย

| อาการ | สาเหตุ | วิธีแก้ |
|---|---|---|
| `source` ไม่รู้จัก | เผลอใช้คำสั่ง macOS/Linux บน PowerShell | ใช้ `.\.venv\Scripts\Activate.ps1` แทน |
| Activate.ps1 รันไม่ได้ | Execution policy ปิดกั้น | `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` หรือเรียก `.venv\Scripts\python.exe` ตรง ๆ |
| `ModuleNotFoundError: pandas` | ติดตั้ง requirements ผิด Python | เช็คด้วย `python -c "import sys; print(sys.executable)"` ต้องชี้เข้า `.venv` แล้วติดตั้งใหม่ |
| `Database not found` | รันจากคนละโฟลเดอร์ หรือยังไม่รัน lab.py | `cd` เข้าโฟลเดอร์ `student` ให้ถูก แล้วรัน `python lab.py` |
| Pivot assert error | ลืมใส่ `aggfunc='sum'` กลับหลังทดลอง หรือ P1 ยังไม่เสร็จ | ตรวจโค้ด P1 ให้มี `aggfunc='sum'` |
| อยากเริ่มข้อมูลใหม่ทั้งหมด | — | `python lab.py --reset` (⚠️ ลบการแก้ไขทิ้งทั้งหมด สำรองงานก่อน) |

## สิ่งที่ต้องส่ง

ZIP ชื่อ `student_id_olap_lab.zip` ประกอบด้วย: `q01.sql`–`q12.sql`, `oltp_demo.py`, `pivot_student.py` (ที่เติมโค้ดแล้ว), ผลรันแต่ละข้อ, `pivot_province_month.csv`, `pivot_september.csv`, `pivot.xlsx` (ถ้าทำ), และรายงานคำตอบ 2–4 หน้าจาก `Answer_Template.md` — **ไม่ต้องส่ง** `.venv/` หรือฐานข้อมูลต้นฉบับใน `data/`
