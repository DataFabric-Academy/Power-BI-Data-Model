# 01 - Introduction & VertiPaq Engine

## เนื้อหาหลักสูตร

โมดูลนี้แนะนำแนวคิดพื้นฐานเกี่ยวกับ Power BI Semantic Model และปูพื้นฐานความเข้าใจเกี่ยวกับ VertiPaq Engine ซึ่งเป็น Storage Engine ที่ Power BI ใช้ในการจัดเก็บและประมวลผลข้อมูลใน Semantic Model

> **Data Source หลักของหลักสูตร:** หลักสูตรนี้ใช้ **AdventureWorksDW2025** เป็น Data Source หลักสำหรับตัวอย่างและแบบฝึกหัดทั้งหมด

---

## 📋 หัวข้อการเรียนรู้

### Part 1: Power BI Semantic Model Introduction

#### 1. บทนำเกี่ยวกับ Power BI Semantic Model

- ความแตกต่างระหว่าง Data Model และ Semantic Model
- ความสำคัญของ Semantic Model ที่ดี
- Semantic Model Architecture

#### 2. Power BI Process ⭐

การประมวลผล DAX (และ MDX) ใน Power BI ใช้ 2 องค์ประกอบหลักที่ทำงานร่วมกัน:

##### Formula Engine (FE)
- **หน้าที่**: วิเคราะห์และประมวลผลคำสั่ง DAX หรือ MDX
- **การทำงาน**:
  - แปลความหมายของคำสั่ง
  - ตรวจสอบไวยากรณ์
  - วางแผนการดำเนินการ
  - ส่งคำขอข้อมูลไปยัง Storage Engine
- **ผลลัพธ์**: ส่งคืนผลลัพธ์สุดท้ายให้ผู้ใช้

##### Storage Engine (SE)
- **หน้าที่**: จัดการการดึงข้อมูลจากแหล่งที่เก็บข้อมูล
- **การทำงาน**:
  - รับคำขอจาก Formula Engine
  - ดึงข้อมูลจากหน่วยความจำหรือดิสก์
  - ใช้เทคนิคการจัดเก็บแบบคอลัมน์และการบีบอัดข้อมูล
- **ผลลัพธ์**: ส่งข้อมูลที่ดึงมาให้ Formula Engine

#### 3. Relationships เป็นหัวใจของ Semantic Model ⭐

- Relationships ช่วยเชื่อมโยงข้อมูลระหว่างตาราง
- ไม่จำเป็นต้องรวมข้อมูลทั้งหมดไว้ในตารางเดียว
- ลดความซับซ้อนและเพิ่มประสิทธิภาพ
- **👉 เรียนรู้เพิ่มเติมในโมดูล 04-Relationships**

---

### Part 2: VertiPaq Engine ⭐ **ปูพื้นฐานสำคัญ**

#### 1. VertiPaq Engine คืออะไร

VertiPaq เป็น **In-Memory Storage Engine** ที่ใช้ใน:
- Power BI
- SQL Server Analysis Services (SSAS) Tabular
- Power Pivot ใน Excel

**คุณสมบัติหลัก:**
- ออกแบบมาเพื่อรองรับการประมวลผลข้อมูลขนาดใหญ่ได้อย่างรวดเร็ว
- ใช้ **Columnar Storage** และ **Data Compression** ขั้นสูง
- ทำงานในหน่วยความจำ (In-Memory) ทำให้เร็วมาก

#### 2. ทำไม VertiPaq ถึงเร็วกว่าระบบทั่วไป

##### 2.1 Columnar Storage (โครงสร้างการจัดเก็บแบบคอลัมน์)

**ความแตกต่างจาก Row-Based Database:**
- **Row-Based**: เก็บข้อมูลเป็นแถว ๆ
- **VertiPaq**: เก็บข้อมูลเป็นคอลัมน์

**ข้อดี:**
- เรียกใช้เฉพาะคอลัมน์ที่จำเป็นต่อการคำนวณ
- ลดจำนวนข้อมูลที่ต้องอ่าน
- ลดเวลาในการดึงข้อมูล
- มีประสิทธิภาพสูงสำหรับ Data Analytics

##### 2.2 Data Compression (เทคนิคบีบอัดข้อมูล)

VertiPaq ใช้เทคนิคการบีบอัดข้อมูลหลายรูปแบบ:

**1. Value Encoding (Bit Packing)**
- ใช้บิตแทนค่าจริงที่มีช่วงค่าจำกัด
- ลดขนาดของข้อมูลโดยไม่เสียความแม่นยำ

**2. Dictionary Encoding (เข้ารหัสแบบพจนานุกรม)**
- สร้าง Dictionary ของค่าที่ไม่ซ้ำกันในแต่ละคอลัมน์
- เก็บเพียง Reference ID ไปยังค่าใน Dictionary
- แทนที่จะเก็บค่าซ้ำ ๆ
- ลดพื้นที่จัดเก็บและเพิ่มความเร็ว

**3. Run-Length Encoding (RLE) (เข้ารหัสแบบจำนวนซ้ำ)**
- เมื่อค่าต่อเนื่องกันมีค่าซ้ำกัน RLE จะบันทึกค่าพร้อมจำนวนครั้งที่ซ้ำ
- แทนที่จะบันทึกค่าเดิมซ้ำ ๆ
- **มีประสิทธิภาพสูงเมื่อใช้กับคอลัมน์ที่ถูกจัดเรียง (Sorted Columns)**

#### 3. การจัดเรียงข้อมูลส่งผลต่อ VertiPaq อย่างไร

**การเรียงลำดับคอลัมน์ (Sorting Columns)** มีผลต่อประสิทธิภาพของการบีบอัดข้อมูลด้วยวิธี RLE:

- หากคอลัมน์มีค่าซ้ำเรียงติดกันมากขึ้น → การทำงานของ Run-Length Encoding จะมีประสิทธิภาพมากขึ้น
- ส่งผลให้การบีบอัดข้อมูลมีประสิทธิภาพมากขึ้น

**Best Practice:**
- พิจารณาให้คอลัมน์ที่มีการเปลี่ยนแปลงน้อยอยู่ในลำดับหน้า
- เพื่อให้ VertiPaq สามารถใช้ RLE ได้อย่างเต็มที่

#### 4. Low Cardinality และผลกระทบต่อ Performance

**Low Cardinality** = คอลัมน์ที่มีค่าซ้ำกันมาก (Cardinality ต่ำ)

**เหตุผลที่ VertiPaq ชอบ Low Cardinality:**
- Dictionary Encoding ทำงานได้ดีกับค่าซ้ำกันมาก
- RLE Encoding ทำงานได้ดีกับค่าซ้ำกันเรียงติดกัน
- ลดขนาดข้อมูล → ลดการใช้หน่วยความจำ → เพิ่มความเร็ว

**Cardinality คืออะไร?**
- **Cardinality = จำนวนค่าที่ไม่ซ้ำกัน (unique values) ในคอลัมน์**

**ตัวอย่างง่ายๆ:**

**ตารางนักเรียน (มี 10,000 แถว):**

| ชื่อ | เพศ | เกรด |
|------|-----|------|
| สมชาย | ชาย | A |
| สมหญิง | หญิง | B |
| วิชัย | ชาย | A |
| วิไล | หญิง | C |
| ... (อีก 9,996 แถว) | ... | ... |

**Cardinality ของแต่ละคอลัมน์:**

- **"เพศ"**: มีค่าไม่ซ้ำกัน = 2 ค่า (ชาย, หญิง)
  - **Cardinality = 2** (ต่ำมาก ✅ Low Cardinality)
  - แต่ละค่าใช้ซ้ำ ~5,000 ครั้ง (10,000 ÷ 2)
  - Dictionary Encoding เก็บแค่ 2 ค่า → ประหยัดพื้นที่มาก

- **"เกรด"**: มีค่าไม่ซ้ำกัน = 5 ค่า (A, B, C, D, F)
  - **Cardinality = 5** (ต่ำ ✅ Low Cardinality)
  - แต่ละค่าใช้ซ้ำ ~2,000 ครั้ง (10,000 ÷ 5)
  - Dictionary Encoding เก็บแค่ 5 ค่า → ประหยัดพื้นที่

- **"ชื่อ"**: แต่ละแถวมีชื่อไม่ซ้ำกันเลย
  - **Cardinality = 10,000** (สูงมาก ❌ High Cardinality)
  - เท่ากับจำนวนแถวทั้งหมด (10,000 แถว = 10,000 ชื่อ)
  - Dictionary Encoding ต้องเก็บ 10,000 ค่า → ใช้พื้นที่มาก

**ทำไม VertiPaq ชอบ Low Cardinality?**
- **Low Cardinality** = คอลัมน์ที่มีค่าซ้ำกันมาก = Cardinality ต่ำ (น้อยกว่าจำนวนแถว)
  - เก็บข้อมูลได้ง่าย = ประหยัดพื้นที่ = เร็วขึ้น ✅
  
- **High Cardinality** = คอลัมน์ที่มีค่าซ้ำกันน้อยหรือไม่มีเลย = Cardinality สูง (ใกล้เคียงหรือเท่ากับจำนวนแถว)
  - เก็บข้อมูลยาก = ใช้พื้นที่มาก = ช้าลง ❌

**ตัวอย่าง Cardinality สูง (ไม่ดี):**

**ตารางการขาย (มี 50,000 แถว):**

| รหัสใบเสร็จ | เวลาที่ซื้อ (Timestamp) |
|------------|------------------------|
| INV-000001 | 2024-01-15 10:23:45.123 |
| INV-000002 | 2024-01-15 10:23:45.456 |
| INV-000003 | 2024-01-15 10:23:45.789 |
| INV-000004 | 2024-01-15 10:23:46.012 |
| ... (อีก 49,996 แถว) | ... |

**ปัญหาของ Timestamp แบบ Milliseconds:**

- **Cardinality = 50,000** (สูงมาก ❌ High Cardinality)
- เท่ากับจำนวนแถวทั้งหมด (50,000 แถว = 50,000 Timestamp ที่ไม่ซ้ำกัน)
- Dictionary Encoding ต้องเก็บ 50,000 ค่า → ใช้พื้นที่มากมาก
- แต่ละค่าใช้แค่ 1 ครั้ง (ไม่ซ้ำกัน) → ไม่สามารถบีบอัดได้ดี

**วิธีลด Cardinality (ทำให้มีประสิทธิภาพมากขึ้น):**

##### วิธีที่ 1: แบ่งคอลัมน์ที่มี Cardinality สูงออกเป็นหลายคอลัมน์

**ตัวอย่าง: แบ่ง DateTime ออกเป็น Date และ Time**

❌ **ไม่ดี (Cardinality สูง - ไม่มีค่าซ้ำกัน):**

**ตารางการขาย (มี 50,000 แถว):**

| รหัสใบเสร็จ | เวลาที่ซื้อ (DateTime) |
|------------|----------------------|
| INV-000001 | 2024-01-15 10:23:45.123 |
| INV-000002 | 2024-01-15 10:23:45.456 |
| INV-000003 | 2024-01-15 10:23:45.789 |
| ... (อีก 49,997 แถว) | ... |

**ผลลัพธ์:**
- **Cardinality = 50,000** (สูงมาก ❌)
- เท่ากับจำนวนแถวทั้งหมด (ไม่มีค่าซ้ำกันเลย)
- Dictionary Encoding ต้องเก็บ 50,000 ค่า → ใช้พื้นที่มากมาก

✅ **ดี (Cardinality ต่ำ - มีค่าซ้ำกันมาก):**

**ตารางการขาย (มี 50,000 แถว):**

| รหัสใบเสร็จ | วันที่ซื้อ | ช่วงเวลา |
|------------|-----------|---------|
| INV-000001 | 2024-01-15 | เช้า |
| INV-000002 | 2024-01-15 | เช้า |
| INV-000003 | 2024-01-15 | เช้า |
| ... (อีก 49,997 แถว) | ... | ... |

**ผลลัพธ์:**
- **วันที่ซื้อ**: Cardinality = 365 ค่า (ประมาณ 1 ปี) → แต่ละค่าซ้ำ ~137 ครั้ง (50,000 ÷ 365) ✅
- **ช่วงเวลา**: Cardinality = 3-4 ค่า (เช้า, บ่าย, เย็น, กลางคืน) → แต่ละค่าซ้ำ ~12,500-16,667 ครั้ง ✅
- Dictionary Encoding เก็บแค่ 365 + 4 ค่า แทนที่จะเป็น 50,000 ค่า → ประหยัดพื้นที่มาก

##### วิธีที่ 2: ใช้ Dimension Tables แทนการเก็บข้อมูลใน Fact Table โดยตรง

**ตัวอย่าง: เก็บข้อมูลสินค้า**

❌ **ไม่ดี (เก็บข้อมูลสินค้าในตารางการขาย - มีค่าซ้ำกันน้อย):**

**ตารางการขาย (Fact Table - มี 100,000 แถว):**

| รหัสใบเสร็จ | รหัสสินค้า | ชื่อสินค้า | ราคา |
|------------|-----------|-----------|------|
| INV-000001 | P001 | น้ำดื่ม | 20 |
| INV-000002 | P002 | ขนม | 15 |
| INV-000003 | P001 | น้ำดื่ม | 20 |
| INV-000004 | P003 | นม | 25 |
| ... (อีก 99,996 แถว) | ... | ... | ... |

**ปัญหาที่เกิดขึ้น:**
- **ชื่อสินค้า**: มีสินค้าประมาณ 1,000 ชนิด → Cardinality ≈ 1,000 (สูง ❌)
  - แต่ละชื่อซ้ำ ~100 ครั้ง (100,000 ÷ 1,000)
  - ต้องเก็บ "น้ำดื่ม" ซ้ำ 100 ครั้งใน Fact Table → ใช้พื้นที่มาก

- **ราคา**: มีราคาแตกต่างกัน ~500 ราคา → Cardinality ≈ 500 (สูง ❌)
  - ต้องเก็บราคา 20 บาท ซ้ำหลายร้อยครั้ง → ใช้พื้นที่มาก

✅ **ดี (แยกเป็น Dimension Table - มีค่าซ้ำกันมาก):**

**ตารางการขาย (Fact Table - มี 100,000 แถว):**

| รหัสใบเสร็จ | รหัสสินค้า |
|------------|-----------|
| INV-000001 | P001 |
| INV-000002 | P002 |
| INV-000003 | P001 |
| INV-000004 | P003 |
| ... (อีก 99,996 แถว) | ... |

- **รหัสสินค้า**: Cardinality = 1,000 ค่า (สินค้า 1,000 ชนิด)
  - แต่ละรหัสซ้ำ ~100 ครั้ง (100,000 ÷ 1,000) ✅
  - เก็บแค่ Integer (P001, P002, ...) → ใช้พื้นที่น้อยมาก

**ตารางสินค้า (Dimension Table - มี 1,000 แถว):**

| รหัสสินค้า | ชื่อสินค้า | ราคา |
|-----------|-----------|------|
| P001 | น้ำดื่ม | 20 |
| P002 | ขนม | 15 |
| P003 | นม | 25 |
| ... (อีก 997 แถว) | ... | ... |

**ข้อดี:**
- **Fact Table** เก็บแค่ Foreign Key (รหัสสินค้า) → Cardinality ต่ำ → ประหยัดพื้นที่
- **Dimension Table** เก็บ Attributes (ชื่อ, ราคา) แค่ครั้งเดียว → ไม่ซ้ำซ้อน
- เมื่อมีข้อมูลการขายมากขึ้น → รหัสสินค้าใน Fact Table จะซ้ำกันมากขึ้น → Cardinality ลดลง

##### วิธีที่ 3: ใช้ Calculated Columns สำหรับค่าที่คำนวณได้

**ตัวอย่าง: แทนที่จะเก็บ "ปีเกิด" (High Cardinality) ให้คำนวณ "ช่วงอายุ" (Low Cardinality)**

❌ **ไม่ดี (เก็บปีเกิดโดยตรง - High Cardinality):**

**ตารางลูกค้า (มี 50,000 แถว):**

| รหัสลูกค้า | ชื่อ | ปีเกิด |
|----------|------|--------|
| C000001 | สมชาย | 1995 |
| C000002 | สมหญิง | 1996 |
| C000003 | วิชัย | 1985 |
| ... (อีก 49,997 แถว) | ... | ... |

**ปัญหาที่เกิดขึ้น:**
- **ปีเกิด**: แต่ละคนเกิดปีต่างกัน → Cardinality ≈ 50,000 (สูงมาก ❌ High Cardinality)
  - เท่ากับจำนวนแถวทั้งหมด (แต่ละแถวมีค่าไม่ซ้ำกัน)
  - Dictionary Encoding ต้องเก็บ 50,000 ค่า → ใช้พื้นที่มากมาก
  - ไม่สามารถบีบอัดได้ดี

✅ **ดี (คำนวณช่วงอายุจากปีเกิด - Low Cardinality):**

**วิธีที่ 1: ทำใน Power Query (แนะนำ)**

**ขั้นตอน:**
1. ใน Power Query: ใช้ปีเกิดคำนวณช่วงอายุ
2. ลบคอลัมน์ปีเกิดออก (ไม่ต้องเก็บใน Model)
3. เก็บแค่ช่วงอายุ (Low Cardinality) ใน Model

**ตัวอย่าง Power Query M:**
```m
let
    Source = ...,
    #"Added Age Range" = Table.AddColumn(
        Source, 
        "ช่วงอายุ", 
        each 
            let
                CurrentAge = Date.Year(DateTime.LocalNow()) - [ปีเกิด]
            in
                if CurrentAge < 20 then "0-20"
                else if CurrentAge < 30 then "20-30"
                else if CurrentAge < 40 then "30-40"
                else if CurrentAge < 50 then "40-50"
                else "50+"
    ),
    #"Removed Columns" = Table.RemoveColumns(#"Added Age Range", {"ปีเกิด"})
in
    #"Removed Columns"
```

**ตารางลูกค้าใน Model (มี 50,000 แถว):**

| รหัสลูกค้า | ชื่อ | ช่วงอายุ |
|----------|------|---------|
| C000001 | สมชาย | 20-30 |
| C000002 | สมหญิง | 20-30 |
| C000003 | วิชัย | 30-40 |
| ... (อีก 49,997 แถว) | ... | ... |

**ข้อดี:**
- **ช่วงอายุ**: Cardinality = 5 ค่า (0-20, 20-30, 30-40, 40-50, 50+) → ต่ำมาก ✅ Low Cardinality
  - แต่ละช่วงซ้ำ ~10,000 ครั้ง (50,000 ÷ 5)
  - Dictionary Encoding เก็บแค่ 5 ค่า → ประหยัดพื้นที่มากมาก
- **ไม่ต้องเก็บปีเกิดใน Model** → ประหยัดพื้นที่ Model
- **คำนวณครั้งเดียวใน Power Query** → ไม่ต้องคำนวณซ้ำใน Model

**วิธีที่ 2: ทำใน Calculated Column (ถ้าต้องการ Dynamic)**

**กรณีนี้:** ถ้ายังต้องเก็บปีเกิดไว้ใน Model (เช่น ต้องใช้ในการคำนวณอื่น)

**ตารางลูกค้า (มี 50,000 แถว):**

| รหัสลูกค้า | ชื่อ | ปีเกิด | ช่วงอายุ (Calculated) |
|----------|------|--------|---------------------|
| C000001 | สมชาย | 1995 | 20-30 |
| C000002 | สมหญิง | 1996 | 20-30 |
| C000003 | วิชัย | 1985 | 30-40 |
| ... (อีก 49,997 แถว) | ... | ... | ... |

**สร้าง Calculated Column:**
```dax
ช่วงอายุ = 
VAR CurrentAge = YEAR(TODAY()) - [ปีเกิด]
RETURN
    SWITCH(
        TRUE(),
        CurrentAge < 20, "0-20",
        CurrentAge < 30, "20-30",
        CurrentAge < 40, "30-40",
        CurrentAge < 50, "40-50",
        "50+"
    )
```

**ข้อดี:**
- **ช่วงอายุ** (Calculated Column): Cardinality = 5 ค่า → ต่ำมาก ✅
- **ปีเกิด** ยังคงอยู่ใน Model (ถ้ายังต้องใช้) → High Cardinality แต่จำเป็น

**ข้อเสีย:**
- ยังต้องเก็บปีเกิดใน Model (High Cardinality)
- Calculated Column ต้องคำนวณทุกครั้งที่ Refresh

**สรุปสำหรับวิธีที่ 3:**
- ✅ **วิธีที่ 1 (Power Query - แนะนำ)**: คำนวณช่วงอายุใน Power Query แล้วลบปีเกิดออก → ประหยัดพื้นที่ Model มากที่สุด ✅
- ⚠️ **วิธีที่ 2 (Calculated Column)**: ใช้เมื่อจำเป็นต้องเก็บปีเกิดไว้ → ยังต้องเก็บปีเกิด (High Cardinality)

**หลักการลด Cardinality:**
- ✅ **Low Cardinality = ดี** 
  - = คอลัมน์ที่มีค่าซ้ำกันมาก = Cardinality ต่ำ
  - → เร็ว, ประหยัดพื้นที่
  
- ❌ **High Cardinality = ไม่ดี**
  - = คอลัมน์ที่มีค่าซ้ำกันน้อยหรือไม่มีเลย = Cardinality สูง
  - → ช้า, ใช้พื้นที่มาก
  
- 🎯 **วิธีลด Cardinality**: 
  1. แบ่งคอลัมน์ (เช่น แยก DateTime เป็น Date และ Time)
  2. ใช้ Dimension Tables (แทนการเก็บ Attributes ใน Fact Table)
  3. คำนวณค่าที่สามารถคำนวณได้ใน Power Query แล้วลบคอลัมน์ High Cardinality ออก

#### 5. Sorted Data และ Encoding

- **RLE (Run-Length Encoding) ทำงานได้ดีกับข้อมูลที่เรียงลำดับ**
- เมื่อข้อมูลเรียงลำดับ RLE สามารถบีบอัดข้อมูลได้ดีมาก
- ตัวอย่าง: ข้อมูล Sales ที่เรียงตาม Date, Product จะทำให้ RLE Encoding มีประสิทธิภาพสูง
- การจัดเรียงข้อมูลควรทำที่ Fact Table โดยใช้คอลัมน์ที่มีความสำคัญสูงสุด
- Partitioning สามารถช่วยให้แต่ละ Partition มีข้อมูลเรียงลำดับได้

#### 6. Best Practices ในการใช้ VertiPaq ให้มีประสิทธิภาพสูงสุด

##### 6.1 ควบคุมจำนวนคอลัมน์
- คอลัมน์ที่ไม่จำเป็น → ลบทิ้ง เพื่อลดขนาดของโมเดล

##### 6.2 ลดจำนวนค่าที่แตกต่างกันมากเกินไปในคอลัมน์ (Cardinality Reduction)
- ค่าที่แตกต่างกันมากเกินไป → ทำให้ Dictionary Encoding ทำงานได้ไม่ดี
- **ตัวอย่าง**: วันที่แบบเต็มรูปแบบ (2025-02-16 14:23:45) อาจลดลงเป็น แค่วันที่ (2025-02-16)

##### 6.3 จัดเรียงลำดับข้อมูลให้เหมาะสม
- นำคอลัมน์ที่มีค่าซ้ำกันมากไว้ลำดับต้น ๆ
- เพื่อให้ RLE บีบอัดข้อมูลได้ดีที่สุด

##### 6.4 ใช้ Measures มากกว่า Calculated Columns และคำนวณใน Power Query เมื่อเป็นไปได้
- **Measures (DAX)**: คำนวณแบบ Dynamic โดยใช้หน่วยความจำได้มีประสิทธิภาพกว่า
- **Calculated Columns**: คำนวณและเก็บค่าลงในโมเดล → ทำให้ขนาดไฟล์ใหญ่ขึ้น
- **Power Query**: คำนวณค่าที่คำนวณได้จาก High Cardinality columns แล้วลบต้นฉบับออก → ประหยัดพื้นที่ Model มากที่สุด
  - ตัวอย่าง: คำนวณช่วงอายุจากปีเกิด แล้วลบปีเกิดออก → เก็บแค่ช่วงอายุ (Low Cardinality) ใน Model

##### 6.5 Best Practices สรุป
1. **ลด Cardinality**:
   - หลีกเลี่ยงคอลัมน์ที่มีค่าซ้ำกันน้อยหรือไม่มีเลย (High Cardinality)
   - ใช้ Dimension Tables แทนการเก็บข้อมูลโดยตรงใน Fact Table
   - แบ่งคอลัมน์ที่มี Cardinality สูงออกเป็นหลายคอลัมน์
   - **คำนวณค่าที่สามารถคำนวณได้ใน Power Query แล้วลบคอลัมน์ High Cardinality ออก** (เช่น คำนวณช่วงอายุจากปีเกิด แล้วลบปีเกิดออก)
   - เลือกคอลัมน์ที่มีค่าซ้ำกันมาก (Low Cardinality) แทน

2. **เรียงข้อมูล (Sorting)**:
   - เรียงข้อมูลใน Fact Table ตามคอลัมน์ที่ใช้บ่อยที่สุด
   - ใช้ Date, Product Key, Customer Key ในการเรียงลำดับ
   - เรียงข้อมูลก่อน Import ขึ้น Semantic Model

3. **เลือก Data Types ที่เหมาะสม**:
   - ใช้ Integer แทน Text เมื่อเป็นไปได้
   - ใช้ Date แทน DateTime ถ้าไม่จำเป็นต้องใช้เวลา
   - หลีกเลี่ยงการใช้ Large Text Fields

#### 7. สรุปจุดเด่นของ VertiPaq Engine

1. **Columnar Storage** → อ่านเฉพาะข้อมูลที่จำเป็น ทำให้ดึงข้อมูลเร็ว
2. **Data Compression** → บีบอัดข้อมูล ลดการใช้หน่วยความจำ
3. **การเรียงลำดับข้อมูล** ช่วยเพิ่มประสิทธิภาพการบีบอัด
4. **เหมาะสำหรับงานด้าน Data Analytics และ OLAP**
5. **ทำงานบน In-Memory** ช่วยให้คำนวณและวิเคราะห์ข้อมูลได้รวดเร็ว

---

### Part 3: External Tools สำหรับจัดการ Semantic Model ⭐

#### 1. Power BI Desktop Views ⭐ (ของใหม่ - GA ทั้งหมด)

> **ของใหม่ 2024–2025:** งานจำนวนมากที่เคยต้องใช้ External Tool ตอนนี้ทำได้ใน Power BI Desktop โดยตรง — โฟกัสการสอนคอร์สนี้ที่ Power BI Desktop เป็นหลัก และใช้ External Tools เป็นเครื่องมือเสริม

##### Model View
- จัดการ Semantic Model
- ดูและแก้ไข Relationships
- ตั้งค่า Properties ของ Tables และ Columns
- **สร้าง Calculation Group ได้โดยตรง** จากปุ่ม **Calculation group** ใน ribbon (GA) — ไม่ต้องใช้ Tabular Editor

##### DAX Query View
- เขียนและทดสอบ DAX Queries (EVALUATE) ใน Desktop ได้เลย (GA)
- Quick Queries: สร้าง query จาก measure/table ที่เลือกอัตโนมัติ
- ใช้ CodeLens "update model" เพื่อเพิ่ม measure จาก query กลับเข้าโมเดลได้
- Debugging DAX Code ก่อนนำไปใช้จริง
- ดูเพิ่มเติม: [DAX query view - Microsoft Learn](https://learn.microsoft.com/power-bi/transform-model/dax-query-view)

##### TMDL View
- จัดการ Model ผ่าน Tabular Model Definition Language (GA ใน Power BI Desktop ตั้งแต่กันยายน 2025)
- Script → แก้ไข → กด Apply เพื่ออัปเดตโมเดล เหมาะกับ bulk edit และ reuse script
- แก้ไข Properties ที่ไม่มีใน UI เช่น `IsAvailableInMDX` ได้โดยตรง
- ดูเพิ่มเติม: [Work with TMDL view - Microsoft Learn](https://learn.microsoft.com/power-bi/transform-model/desktop-tmdl-view)

#### 2. DAX Studio ⭐ **แนะนำตั้งแต่ต้น**

**DAX Studio** เป็นเครื่องมือสำคัญสำหรับ:
- วิเคราะห์และปรับปรุงประสิทธิภาพ DAX Queries
- ใช้ VertiPaq Analyzer เพื่อวิเคราะห์ Cardinality และ Compression
- Query Profiling เพื่อดู Query Execution Plan
- ดู Storage Engine และ Formula Engine Information

**วิธีใช้งาน DAX Studio:**
1. เปิด Power BI Desktop
2. ไปที่ **External Tools** > **DAX Studio**
3. หรือเปิด DAX Studio แยก แล้วเชื่อมต่อกับ Power BI Desktop

**VertiPaq Analyzer ใน DAX Studio:**
- วิเคราะห์ Cardinality ของแต่ละคอลัมน์
- ดูขนาดข้อมูลและ Compression Ratio
- ช่วยระบุคอลัมน์ที่มี High Cardinality
- แนะนำแนวทางปรับปรุงประสิทธิภาพ

**👉 แนะนำให้ใช้ VertiPaq Analyzer ตั้งแต่ต้น** เพื่อให้เห็นภาพชัดเจนว่า VertiPaq ทำงานอย่างไรกับข้อมูลจริง

#### 3. Tabular Editor (เครื่องมือเสริม)

> **อัปเดต:** งานหลักอย่าง Calculation Groups และ IsAvailableInMDX ทำได้ใน Power BI Desktop แล้ว (Model view / TMDL View) Tabular Editor จึงเป็น**เครื่องมือเสริม**สำหรับงานระดับสูง

**Tabular Editor** เป็นเครื่องมือที่ช่วยให้การจัดการ Semantic Model เร็วขึ้นมาก:

**ใช้เมื่อไหร่:**
- สร้าง/แก้ไข Measures หรือ Columns จำนวนมากพร้อมกัน (batch edit)
- ตั้งค่า Properties ที่ซับซ้อน
- Export/Import Metadata
- รองรับ Scripting (C#) สำหรับ Automation
- ใช้ Best Practice Analyzer (BPA) ร่วมกับ ALM Toolkit

**ประเภท:**
- **Tabular Editor 2 (Open Source)**: ฟรี, มีข้อจำกัดบางอย่าง
- **Tabular Editor 3 (Commercial)**: ใช้ง่ายกว่า, เสียค่าใช้จ่ายรายเดือน

**วิธีใช้งาน Tabular Editor:**
1. ใน Power BI Desktop ไปที่ **External Tools** > **Tabular Editor**
2. หรือเปิด Tabular Editor แยก แล้วเชื่อมต่อกับ Power BI Desktop
3. จะเห็นโครงสร้าง Semantic Model ทั้งหมดใน TOM Explorer

#### 4. เครื่องมืออื่นๆ

##### ALM Toolkit
- Version Control สำหรับ Semantic Model
- Compare Models เพื่อดูความแตกต่าง
- Deploy Models จาก Development ไป Production

##### Best Practice Analyzer (BPA)
- ตรวจสอบ Best Practices ใน Semantic Model
- แนะนำแนวทางปรับปรุง
- รองรับทั้ง Tabular Editor และ DAX Studio

---

### Part 4: ภาพรวมของหลักสูตร

หลักสูตรนี้แบ่งออกเป็น 13 โมดูลหลัก:

1. **Introduction & VertiPaq Engine** (โมดูลนี้) - ปูพื้นฐานสำคัญ
2. **Data Sources** - การเชื่อมต่อและเตรียมข้อมูล
3. **Data Modeling Basics** - Star Schema, Fact/Dimension Tables
4. **Relationships** ⭐ - หัวใจของหลักสูตร
5. **Dimension Table Design** - Surrogate Keys, Hierarchies, SCD
6. **Date Dimensions & Relationships** - Conformed Date Dimension
7. **Fact Tables Design** - Explicit Measures, Calculation Groups
8. **Performance Optimization** - VertiPaq Optimization
9. **Best Practices** - แนวทางปฏิบัติที่ดี
10. **Advanced Modeling** - Bidirectional Filters, Many-to-Many
11. **Case Studies** - โครงการตัวอย่างจริง
12. **Security (RLS)** - Row-Level Security
13. **Incremental Refresh & Partitioning** - การจัดการ Large Models

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- เข้าใจ Power BI Process และการทำงานของ Formula Engine และ Storage Engine
- เข้าใจ VertiPaq Engine อย่างลึกซึ้ง
- เข้าใจความสำคัญของ Low Cardinality และ Sorted Data ต่อ Performance
- วิเคราะห์ Cardinality ด้วย VertiPaq Analyzer
- ใช้ Tabular Editor จัดการ Semantic Model ได้อย่างมีประสิทธิภาพ
- เข้าใจความสำคัญของ Semantic Model และ Relationships
- เข้าใจภาพรวมของหลักสูตร

---

## 🔗 การเชื่อมโยงกับโมดูลอื่น

- **02-Data-Sources**: เข้าใจ VertiPaq จะช่วยในการเตรียมข้อมูลเพื่อลด Cardinality
- **03-Data-Modeling-Basics**: เข้าใจ VertiPaq จะช่วยในการออกแบบ Schema
- **04-Relationships**: Low Cardinality จำเป็นสำหรับการสร้าง Relationships ที่มีประสิทธิภาพ (Relationships เป็นหัวใจของหลักสูตร)
- **08-Performance-Optimization**: ความเข้าใจ VertiPaq เป็นพื้นฐานสำคัญ
