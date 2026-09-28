# 08 - Fact Tables Design

## เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับการออกแบบ Fact Tables และการสร้าง Measures (Explicit Measures) รวมถึง Calculation Groups ใน Power BI Semantic Model

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 📋 หัวข้อการเรียนรู้

### 1. Explicit Measures vs Implicit Measures

#### Implicit Measures

**คำอธิบาย:**
- Measures ที่ Power BI สร้างอัตโนมัติจากคอลัมน์ตัวเลข
- Power BI จะ SUM, COUNT, AVERAGE อัตโนมัติเมื่อลากคอลัมน์ตัวเลขลงใน Visual

**ปัญหา:**
- อาจสรุปค่าที่ไม่ควรสรุป (เช่น ราคาเฉลี่ยต่อหน่วย)
- ไม่สามารถควบคุม Logic การคำนวณได้
- Performance ไม่ดีเท่า Explicit Measures

**ตัวอย่าง:**
- ลาก `SalesAmount` ลงใน Visual → Power BI จะ SUM อัตโนมัติ

---

#### Explicit Measures

**คำอธิบาย:**
- Measures ที่สร้างด้วย DAX อย่างชัดเจน
- ผู้พัฒนากำหนด Logic การคำนวณเอง

**ข้อดี:**
- ✅ ควบคุม Logic การคำนวณได้
- ✅ Performance ดีกว่า
- ✅ สามารถ Reuse ได้
- ✅ รองรับการใช้ใน Excel
- ✅ สามารถตั้งชื่อให้มีความหมายได้

**ตัวอย่าง:**
```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
Total Orders = COUNTROWS(FactResellerSales)
Average Sales = AVERAGE(FactResellerSales[SalesAmount])
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 2. Don't Summarize

**Best Practice:**
- ตั้งค่าทุกคอลัมน์ใน Fact Table เป็น "Don't Summarize"
- บังคับให้ผู้ใช้ใช้ Explicit Measures เท่านั้น

**เหตุผล:**

1. **ป้องกันการสรุปค่าที่ไม่ถูกต้อง**
   - โดยค่าเริ่มต้น Power BI จะใช้ Implicit Measure ซึ่งทำให้คอลัมน์ตัวเลขสามารถถูกสรุปอัตโนมัติได้
   - ถ้าคุณมีคอลัมน์ที่ไม่ได้ออกแบบมาให้ถูกสรุป (เช่น ราคาเฉลี่ยต่อหน่วย, ค่าเปอร์เซ็นต์) การปล่อยให้ Power BI สรุปผลโดยอัตโนมัติอาจทำให้ได้ค่าที่ผิดพลาด

2. **บังคับให้สร้าง Explicit Measures**
   - กระตุ้นให้นักพัฒนา BI ต้องสร้าง Explicit Measures โดยใช้ DAX
   - ช่วยให้มีการคำนวณที่ถูกต้อง และสามารถควบคุมตรรกะการรวมกันของข้อมูลได้ดีขึ้น

3. **ปรับปรุงประสิทธิภาพของ Query**
   - การใช้ Implicit Measures จะทำให้ Power BI ทำการคำนวณในแต่ละ Visual เอง
   - Explicit Measures สามารถจัดการได้อย่างมีประสิทธิภาพมากกว่า

4. **ป้องกันความเข้าใจผิดของผู้ใช้งาน**
   - หากปล่อยให้คอลัมน์ใน Fact Table สามารถถูกสรุปได้โดยอัตโนมัติ ผู้ใช้งานอาจลากคอลัมน์มาใช้ใน Visual โดยไม่รู้ว่ามีการสรุปค่าอยู่
   - การตั้ง "Don't Summarize" ช่วยให้ผู้ใช้งานต้องเลือกใช้ Measures ที่กำหนดไว้เท่านั้น

5. **รองรับการใช้งานใน Excel**
   - เมื่อเชื่อมต่อ Power BI Dataset กับ Excel ผ่าน Analyze in Excel จะพบว่า Implicit Measures ไม่สามารถใช้งานใน PivotTable ของ Excel ได้
   - การสร้าง Explicit Measures จะช่วยให้การวิเคราะห์ข้อมูลใน Excel เป็นไปอย่างราบรื่น

---

### 3. การสร้าง Explicit Measures

**ความสำคัญ:**
- การสร้าง Explicit Measures อย่างรอบคอบมีความสำคัญมาก
- Measures เป็นหัวใจของ Fact Tables Design

**ความยืดหยุ่นและความซับซ้อนในการคำนวณ:**
- Implicit Measures จำกัดอยู่ที่ฟังก์ชันการสรุปพื้นฐาน เช่น SUM, COUNT หรือ AVERAGE
- Explicit Measures ช่วยให้สามารถใช้ DAX เพื่อสร้างการคำนวณที่ซับซ้อนและปรับแต่งได้ตามต้องการ

**ความสามารถในการนำกลับมาใช้ใหม่:**
- Explicit Measures ที่สร้างขึ้นสามารถนำไปใช้ซ้ำในหลายๆ รายงานหรือ Visual โดยไม่ต้องสร้างการคำนวณใหม่ทุกครั้ง

**ความสอดคล้องและการจัดการที่ง่ายขึ้น:**
- การมี Explicit Measures ช่วยให้มั่นใจว่าการคำนวณมีความสอดคล้องกันทั่วทั้งรายงาน

**ตัวอย่าง Measures พื้นฐาน:**
```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
Total Orders = COUNTROWS(FactResellerSales)
Average Sales = AVERAGE(FactResellerSales[SalesAmount])
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 4. Calculation Groups

**Calculation Groups** เป็นฟีเจอร์สำคัญที่ช่วยลดจำนวน Measures ที่ต้องสร้างและบำรุงรักษา

#### 4.1 ความหมายและคุณสมบัติ

**คำอธิบาย:**
- Calculation Group ช่วยให้สามารถใช้ Calculation Logic เดียวกันกับหลาย Measures
- ลดจำนวน Measures ที่ต้องสร้างแยกกัน

**ตัวอย่าง:**
- แทนที่จะสร้าง: `Sales YTD`, `Profit YTD`, `Cost YTD` (3 Measures)
- สร้าง Calculation Group "Time Intelligence" ที่มี Calculation Item "YTD"
- ใช้ได้กับทุก Measure → `[Sales] YTD`, `[Profit] YTD`, `[Cost] YTD`

**คุณสมบัติหลัก:**
- **Dynamic Calculation Application** – สามารถเปลี่ยนการคำนวณได้แบบ Dynamic
- **Reusability** – ลดจำนวน Measures ที่ต้องสร้างแยกกัน
- **Consistency** – สร้าง Business Logic ที่สอดคล้องกัน
- **Performance Optimization** – ลดปริมาณการคำนวณที่ซ้ำซ้อน

#### 4.2 องค์ประกอบ

**Calculation Group ประกอบด้วย:**
- **Calculation Items**: แต่ละ Variation (เช่น YTD, QTD, MTD, LY)
- **Display Column**: Column ที่ใช้แสดงชื่อ Calculation Items
- **Ordinal Column**: Column ที่ใช้เรียงลำดับ Calculation Items

#### 4.3 การสร้าง Calculation Group ใน Power BI Desktop ⭐ (ของใหม่ - GA)

> **ของใหม่ 2024–2025:** ก่อนหน้านี้ต้องสร้าง Calculation Group ผ่าน External Tool (Tabular Editor) เท่านั้น ปัจจุบันสร้างได้โดยตรงใน **Model view** ของ Power BI Desktop แล้ว (GA) — ดู [Create calculation groups - Microsoft Learn](https://learn.microsoft.com/power-bi/transform-model/calculation-groups)

**ขั้นตอนใน Model view:**
1. ไปที่ **Model view** แล้วเลือกปุ่ม **Calculation group** ใน ribbon
2. Power BI สร้าง Calculation Item แรกให้อัตโนมัติ — ตั้งชื่อและเขียน Expression
3. เพิ่ม/เรียงลำดับ Calculation Items ได้จากโหนด **Calculation items** ใน **Properties pane**

**ข้อควรรู้:**
- การสร้าง Calculation Group จะเปิด **Discourage implicit measures** ให้อัตโนมัติ (Calculation Item ทำงานกับ Explicit Measures เท่านั้น — เชื่อมโยงกับหัวข้อ Don't Summarize ข้างบน)
- ทางเลือกอื่น: สร้างผ่าน **TMDL View** ใน Desktop (เหมาะกับการ reuse script) หรือ Tabular Editor (เหมาะกับ batch/automation)
- ทดสอบผลลัพธ์ได้เร็วด้วย **DAX Query View** ใน Desktop

---

### 5. SELECTEDMEASURE() Function

**SELECTEDMEASURE()** เป็น Function ที่คืนค่า Measure ที่อยู่ใน Context ปัจจุบัน

**Syntax:**
```dax
SELECTEDMEASURE()
```

**คำอธิบาย:**
- ไม่ต้องส่ง Parameter
- คืนค่าเป็น Measure ใน Context ปัจจุบัน เมื่อมีการประเมิน Calculation Item

**ตัวอย่าง:**
```dax
calculationItem Current = SELECTEDMEASURE()

calculationItem YTD = 
CALCULATE(
    SELECTEDMEASURE(),
    DATESYTD(DimDate[FullDateAlternateKey])
)
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 6. SELECTEDVALUE() Function

**SELECTEDVALUE()** คืนค่าที่ถูกเลือกไว้ในคอลัมน์จาก Filter Context

**Syntax:**
```dax
SELECTEDVALUE(<columnName>[, <alternateResult>])
```

**คำอธิบาย:**
- คืนค่าที่ถูกเลือกไว้ในคอลัมน์จาก Filter Context หรือ Slicer
- หากมีการเลือกค่ามากกว่า 1 ค่า หรือไม่มีการเลือกค่าใดๆ จะคืนค่าตามที่กำหนดไว้ใน Argument ที่สอง

**ตัวอย่าง:**
```dax
Selected Period = 
SELECTEDVALUE(
    'Time Intelligence'[Period],
    "All"
)
```

**การใช้กับ Measure Selector:**
```dax
Measure Selector = DATATABLE(
    "Measure Name", STRING,
    "Measure Sort", INTEGER,
    {
        {"Reseller Sales Revenue", 1},
        {"Reseller Sales Cost", 2}
    }
)

Measure to Show = 
    VAR ActiveMeasurename = SELECTEDVALUE('Measure Selector'[Measure Name])
    VAR Result =
        SWITCH(
            ActiveMeasurename,
            "Reseller Sales Revenue", [Reseller Sales Revenue],
            "Reseller Sales Cost", [Reseller Sales Cost]
        )
    RETURN Result
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 7. Time Intelligence Calculation Groups

**ตัวอย่าง Calculation Group สำหรับ Time Intelligence:**

#### 7.1 ปัญหาเมื่อไม่มี Calculation Group

**เมื่อไม่ใช้ Calculation Group** ต้องสร้าง Measures หลายตัวสำหรับแต่ละ Time Intelligence Variation:

**ตัวอย่าง:** มี 2 Metrics (`Order Sales Amount`, `Total Cost`) และต้องการ 4 Time Intelligence Variations (Current, MTD, QTD, YTD)

**ผลลัพธ์:** ต้องสร้าง Measures 8 ตัว:
- `Order Sales Amount`
- `Order Sales Amount MTD`
- `Order Sales Amount QTD`
- `Order Sales Amount YTD`
- `Total Cost`
- `Total Cost MTD`
- `Total Cost QTD`
- `Total Cost YTD`

**ปัญหา:**
- ต้องสร้าง Measures ซ้ำๆ หลายตัว
- เมื่อต้องการเปลี่ยน Logic (เช่น ปรับสูตร YTD) ต้องแก้ไขทุก Measure
- Model มี Measures จำนวนมาก ทำให้ดูแลรักษายาก

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW2025

---

#### 7.2 แนวทางแก้ไขด้วย Calculation Group

**เมื่อใช้ Calculation Group** สร้างเพียง Base Measures และ Calculation Group:

**Base Measures:**
- `Order Sales Amount = SUM(FactResellerSales[SalesAmount])`
- `Total Cost = SUM(FactResellerSales[TotalProductCost])`

**Calculation Group: "Time Intelligence"**
- **Current Period**: `SELECTEDMEASURE()`
- **Prev Year**: `CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR(...))`
- **MTD**: `CALCULATE(SELECTEDMEASURE(), DATESMTD(...))`
- **QTD**: `CALCULATE(SELECTEDMEASURE(), DATESQTD(...))`
- **YTD**: `CALCULATE(SELECTEDMEASURE(), DATESYTD(...))`

**ผลลัพธ์:**
- ลดจำนวน Measures จาก **8 ตัว → 2 ตัว + 1 Calculation Group**
- เมื่อต้องการเปลี่ยน Logic (เช่น ปรับสูตร YTD) แก้ไขที่ Calculation Item เดียว
- Model เรียบง่ายขึ้น และดูแลรักษาง่ายขึ้น

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW2025

---

#### 7.3 Calculation Items

**Calculation Items:**
- **Current Period**: คืนค่า Measure ปัจจุบัน
- **Prev Year**: คำนวณปีที่แล้ว
- **MTD** (Month to Date): คำนวณจากต้นเดือน
- **QTD** (Quarter to Date): คำนวณจากต้นไตรมาส
- **YTD** (Year to Date): คำนวณจากต้นปี

**ผลลัพธ์:**
- ลดจำนวน Measures จาก (จำนวน Metrics) x (จำนวน Time Intelligence Variations) 
- ให้เหลือเพียงจำนวน Metrics เดิม + 1 Calculation Group

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

#### 7.4 แนวทาง Modern: Offset Columns กับ LY (Offset) ⭐ (ของใหม่)

นอกจากการใช้ Time Intelligence Functions แบบ Classic (`SAMEPERIODLASTYEAR`, `DATESYTD`) แล้ว ยังใช้แนวทาง **Offset Columns** ได้ — เพิ่มคอลัมน์ระยะห่างจากปัจจุบันใน DimDate (Calculated Column) เช่น

```dax
MonthOffset = DATEDIFF ( TODAY (), DimDate[FullDateAlternateKey], MONTH )
YearOffset  = DATEDIFF ( TODAY (), DimDate[FullDateAlternateKey], YEAR )
```

(เดือนปัจจุบัน = 0, อดีต = ติดลบ — ค่าถูกคำนวณตอน refresh ข้อมูล)

แล้วเขียน Calculation Item "LY (Offset)" โดยเลื่อน offset ไป −12 แทนการ override filter วันที่:

```dax
VAR SelectedOffsets =
    SELECTCOLUMNS ( VALUES ( DimDate[MonthOffset] ), "@Prev", [MonthOffset] - 12 )
RETURN
    CALCULATE (
        SELECTEDMEASURE (),
        REMOVEFILTERS ( DimDate ),
        DimDate[MonthOffset] IN SelectedOffsets
    )
```

**จุดเด่นของ Offset:**
- ทำงานกับปฏิทินพิเศษ (4-4-5, Fiscal) ได้โดยไม่ต้องปรับสูตร
- เป็น filter ธรรมดา ไม่ไป override ทาง date relationship
- บนข้อมูลจริงของ AdventureWorksDW2025 ผลของ `LY (Offset)` **ตรงกับ `SAMEPERIODLASTYEAR` ทุกปี** — ตอนสอนจึงเทียบสองแนวทางใน Calculation Group เดียวได้ (ดู [CODE-EXAMPLES.md](./CODE-EXAMPLES.md))

**หมายเหตุ:** `TODAY()` จะคำนวณใหม่ตอน refresh ข้อมูล จึงต้อง refresh ตาราง DimDate เพื่อให้ค่าตรงกับวันปัจจุบัน

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 8. Multiple Calculation Groups

**เมื่อมีหลาย Calculation Groups:**
- ใช้ **Precedence** เพื่อกำหนดลำดับการทำงาน
- Calculation Group ที่มี Precedence สูงกว่าจะทำงานก่อน

**ตัวอย่าง:**
- **Conversion Rate** (precedence: 1) - แปลงค่าเงิน
- **Time Intelligence** (ไม่มี precedence) - คำนวณ Time Intelligence

**ลำดับการทำงาน:**
1. Conversion Rate ทำงานก่อน (แปลงค่าเงิน)
2. Time Intelligence ทำงานทีหลัง (คำนวณ YTD ของค่าที่แปลงแล้ว)

**ผลลัพธ์:**
- สามารถใช้ Calculation Groups หลายตัวพร้อมกันได้
- แต่ละ Calculation Group จะทำงานตามลำดับ Precedence

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 9. Currency Conversion Calculation Groups

**ตัวอย่าง Calculation Group สำหรับ Currency Conversion:**

#### 9.1 Calculation Items

**Calculation Items:**
- **No conversion (USD)**: ไม่แปลงค่า (Local Currency)
- **Conversion (AVG)**: แปลงด้วย Average Exchange Rate
- **Conversion (EOD)**: แปลงด้วย End of Day Exchange Rate

#### 9.2 การใช้ CROSSFILTER()

**เมื่อต้องเชื่อมกับ FactCurrencyRate table:**

```dax
calculationItem 'Conversion (AVG)' =
    VAR _rate =
        CALCULATE (
            AVERAGE ( FactCurrencyRate[AverageRate] ),
            CROSSFILTER ( DimDate[DateKey], FactCurrencyRate[DateKey], BOTH )
        )
    RETURN
        SELECTEDMEASURE () * _rate

calculationItem 'Conversion (EOD)' =
    VAR _rate =
        CALCULATE (
            AVERAGE ( FactCurrencyRate[EndOfDayRate] ),
            CROSSFILTER ( DimDate[DateKey], FactCurrencyRate[DateKey], BOTH )
        )
    RETURN
        SELECTEDMEASURE () * _rate
```

**อธิบาย:**
- ใช้ `CROSSFILTER()` เพื่อเชื่อม Date Dimension กับ FactCurrencyRate table
- `AVERAGE()` เพื่อหาอัตราแลกเปลี่ยนเฉลี่ย
- คูณ `SELECTEDMEASURE()` ด้วยอัตราแลกเปลี่ยน

**ผลลัพธ์:**
- ลดความยุ่งยากในการคำนวณสกุลเงิน
- สามารถนำ Calculation Group ไปใช้กับทุก Measure ที่ต้องแปลงค่าได้โดยอัตโนมัติ
- รองรับหลายอัตราแลกเปลี่ยน (Average, End of Day)

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW2025
**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

#### 9.3 ข้อจำกัดเชิง Semantic ของสูตรง่าย ⭐ (สำคัญ)

สูตร `SELECTEDMEASURE() * AVERAGE(rate)` ข้างบน**ถูกต้องเมื่อโมเดลมีเงื่อนไขครบ** แต่ใน AdventureWorksDW2025 จริง ๆ จะเจอปัญหาสามข้อ (ผมวัดตัวเลขจริงมาแล้ว):

| สถานการณ์ | อาการ | ตัวเลขจริงที่วัดได้ (ปี 2013) |
|---|---|---|
| **ไม่เลือกสกุลใน slicer** | `AVERAGE(rate)` เฉลี่ยข้ามทุกสกุลที่มี rate (14 สกุล) แล้วคูณยอดรวม → ตัวเลขไร้ความหมาย | 33.57M กลายเป็น 16.49M |
| **เลือกสกุล เช่น GBP** | relationship `DimCurrency → FactResellerSales` ตัดยอดให้เหลือเฉพาะแถวที่บันทึกด้วย GBP ก่อน แล้วค่อยคูณ rate | ได้แค่ 2.72M (ยอด nominal แถว GBP) ไม่ใช่ยอดทั้งบริษัทเป็นปอนด์ (21.81M) |
| **ทิศทางของ rate** | rate ของ AW = "จำนวน USD ต่อ 1 หน่วยของสกุลนั้น" → `× rate` ให้ผลสกุลถิ่น→USD เท่านั้น | อยากแสดง "ยอดทั้งบริษัทเป็นสกุล X" ต้องแปลงสวนทาง |

**ข้อสรุปสำหรับการสอน:** สูตรสั้นหรือยาวเป็นผลมาจาก **การออกแบบโมเดล** ไม่ใช่ฝีมือเขียน DAX —

- โมเดลที่ยอดเก็บสกุลเดียว (pivot currency) + currency ไม่ผูกกับตารางขาย → สูตร 6 บรรทัดแบบ §9.2 จบ (แบบนี้คือสมมติฐานของตัวอย่างทางการบน Microsoft Learn)
- โมเดลที่ fact เก็บ**หลายสกุลปนกัน** (AW จริงเก็บ 6 สกุล: USD, CAD, GBP, EUR, AUD ฯลฯ) และอยากให้ slicer เป็น "ตัวเลือกสกุลแสดงผล" → ต้องใช้ cross-rate (§9.4)

#### 9.4 Display Currency: แปลงทุกสกุลเป็นสกุลที่เลือก (cross-rate)

หัวใจคือแยกยอดเป็น **รายวัน × รายสกุลต้นทาง** แล้วแปลงผ่าน USD เป็นตัวกลางด้วย rate ของวันนั้นจริง ๆ:

```
ยอดแสดงผล = ยอด(แถว) × rate(สกุลต้นทาง, วันนั้น) ÷ rate(สกุลปลายทาง, วันนั้น)
```

พร้อม `REMOVEFILTERS(DimCurrency)` เพื่อไม่ให้ filter สกุลที่เลือกไหลเข้าไปบังตาราง rate แบบเดียวกับที่ตัวอย่างทางการใช้ (ดู [Calculation groups - currency conversion](https://learn.microsoft.com/analysis-services/tabular-models/calculation-groups)) — โค้ดเต็มอยู่ใน [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) ตัวอย่างที่ 15

ผลตรวจจากข้อมูลจริง (ปี 2013): USD 32.32M / GBP 21.81M / THB 992.99M — สามค่าเทียบกันด้วย cross-rate ได้ตรงกันเอง

#### 9.5 Dynamic Format String ต่อสกุลเงิน

ให้สัญลักษณ์สกุลเงินเปลี่ยนตาม slicer (เช่น € / £ / ฿) โดยเพิ่มคอลัมน์ format string ใน DimCurrency แล้วตั้ง **Format string expression** ของ Calculation Item ตาม pattern ทางการ:

```dax
SELECTEDVALUE (
    DimCurrency[CurrencyFormatString],
    SELECTEDMEASUREFORMATSTRING ()   // fallback: ใช้ format ของ Measure ต้นทาง
)
```

**ข้อควรรู้ (จาก Microsoft Learn):**
- Dynamic format string มีผลเฉพาะใน **visual** — ผล DAX Query จะเห็นแต่ตัวเลขดิบ
- ถ้า visual แสดงผลเพี้ยน ให้เช็ค **Display units** ของ visual แล้วเปลี่ยนจาก Auto เป็น None
- เมื่อใช้หลาย Calculation Groups ร่วมกัน **format ของ group ที่ Precedence สูงสุดจะชนะ** — กับโมเดลนี้ `YoY %` จึงต้องพิจารณาลำดับด้วย
- ถ้าความสัมพันธ์ `DimCurrency → ตารางขาย` ทำให้ slicer มีสองความหมาย (กรองยอด + เลือกสกุลแสดงผล) ให้ทำ relationship เป็น **inactive** แล้วเปิดเฉพาะจุดด้วย `USERELATIONSHIP` (ตัวอย่างใน [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) ตัวอย่างที่ 17)

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ เข้าใจความแตกต่างระหว่าง Implicit และ Explicit Measures
- ✅ สร้าง Explicit Measures ได้
- ✅ ตั้งค่า "Don't Summarize" ให้ Fact Tables ได้
- ✅ สร้าง Calculation Groups ได้
- ✅ ใช้ SELECTEDMEASURE() และ SELECTEDVALUE() ได้
- ✅ สร้าง Time Intelligence Calculation Groups ได้
- ✅ จัดการ Multiple Calculation Groups ด้วย Precedence ได้
- ✅ สร้าง Currency Conversion Calculation Groups ได้

---

## 📚 เอกสารที่เกี่ยวข้อง

- **04-Relationships**: พื้นฐาน Relationships สำหรับใช้ใน Measures
- **06-Date-Dimensions-Relationships**: Date Dimensions สำหรับ Time Intelligence
- **05-Dimension-Table-Design**: Dimension Tables ที่ใช้ใน Measures
- **[CODE-EXAMPLES.md](./CODE-EXAMPLES.md)** - ตัวอย่างโค้ด DAX
- **[EXERCISES.md](./EXERCISES.md)** - แบบฝึกหัด

---

## 📝 สรุป

### Best Practices

1. **ใช้ Explicit Measures เสมอ**
   - หลีกเลี่ยง Implicit Measures
   - ตั้งค่า "Don't Summarize" ให้ทุกคอลัมน์ใน Fact Table

2. **ใช้ Calculation Groups เมื่อ:**
   - มี Logic ที่ใช้ซ้ำกับหลาย Measures
   - ต้องการลดจำนวน Measures
   - ต้องการสร้าง Time Intelligence หรือ Currency Conversion

3. **ใช้ Precedence เมื่อ:**
   - มีหลาย Calculation Groups
   - ต้องการควบคุมลำดับการทำงาน

---

### ไฟล์ตัวอย่างที่แนะนำ

- `Data Model Time Intelligence with/without Calculation Group.pbix` — เทียบการทำ Time Intelligence แบบเขียน Measures เอง กับแบบใช้ Calculation Group (หัวข้อ 7)
- `Data Model Exchange Rate With Calculation Group.pbix` — Calculation Group สำหรับแปลงสกุลเงิน (หัวข้อ 9)

*Trainer Material — ขอไฟล์จากผู้สอน*

**หมายเหตุ:** ตัวอย่างในโมดูลนี้ใช้ AdventureWorksDW2025 เป็น Data Source

---

**🎉 ขอแสดงความยินดี! คุณได้เรียนจบโมดูล Fact Tables Design แล้ว!**

**ขั้นตอนต่อไป:**
- ฝึกปฏิบัติตาม [EXERCISES.md](./EXERCISES.md)
- ดูตัวอย่างโค้ดใน [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)
- เรียนโมดูล 08-Performance-Optimization ✅

