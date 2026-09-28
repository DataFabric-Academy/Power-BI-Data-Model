# Code Examples - Fact Tables Design

## 📝 เอกสารตัวอย่างโค้ด DAX สำหรับ Fact Tables Design

ไฟล์นี้รวบรวมตัวอย่างโค้ด DAX สำหรับ Explicit Measures และ Calculation Groups

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 🎯 Explicit Measures

### ตัวอย่างที่ 1: Measure พื้นฐาน - SUM

**สถานการณ์:** คำนวณยอดขายรวม

```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
```

**อธิบาย:**
- ใช้ `SUM()` เพื่อรวมค่าทั้งหมดในคอลัมน์
- Measure นี้จะคำนวณตาม Filter Context ที่มีอยู่

---

### ตัวอย่างที่ 2: Measure พื้นฐาน - COUNT

**สถานการณ์:** นับจำนวนรายการขาย

```dax
Total Orders = COUNTROWS(FactResellerSales)
```

**อธิบาย:**
- ใช้ `COUNTROWS()` เพื่อนับจำนวนแถวในตาราง

---

### ตัวอย่างที่ 3: Measure พื้นฐาน - AVERAGE

**สถานการณ์:** คำนวณยอดขายเฉลี่ยต่อรายการ

```dax
Average Sales = AVERAGE(FactResellerSales[SalesAmount])
```

---

### ตัวอย่างที่ 4: Measure พื้นฐาน - DISTINCTCOUNT

**สถานการณ์:** นับจำนวนสินค้าที่ไม่ซ้ำกัน

```dax
Unique Products = DISTINCTCOUNT(FactResellerSales[ProductKey])
```

---

### ตัวอย่างที่ 5: Measure ที่ใช้ CALCULATE()

**สถานการณ์:** คำนวณยอดขายเฉพาะสินค้าประเภท Bikes

```dax
Sales - Bikes = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimProduct[ProductCategoryName] = "Bikes"
)
```

**อธิบาย:**
- `CALCULATE()` ใช้เพื่อเปลี่ยน Filter Context
- Filter ผ่าน Relationship ไปยัง DimProduct

---

### ตัวอย่างที่ 6: Measure ที่ใช้ DIVIDE()

**สถานการณ์:** คำนวณเปอร์เซ็นต์

```dax
Sales % by Category = 
DIVIDE(
    SUM(FactResellerSales[SalesAmount]),
    CALCULATE(
        SUM(FactResellerSales[SalesAmount]),
        ALL(DimProduct[ProductCategoryName])
    )
)
```

**อธิบาย:**
- `DIVIDE()` ใช้แทน `/` เพื่อป้องกันการหารด้วยศูนย์
- `ALL()` ลบ Filter ออกจาก ProductCategoryName

---

### ตัวอย่างที่ 7: Measure ที่ใช้ SUMX()

**สถานการณ์:** คำนวณยอดขายรวมจากหลายตาราง

```dax
Total Sales All Tables = 
SUMX(
    FactResellerSales,
    FactResellerSales[SalesAmount]
) + 
SUMX(
    FactInternetSales,
    FactInternetSales[SalesAmount]
)
```

---

### ตัวอย่างที่ 8: Measure ที่ใช้ ALLSELECTED()

**สถานการณ์:** คำนวณยอดขายรวมใน Visual

```dax
Total Sales Selected = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    ALLSELECTED()
)
```

---

## 🎯 Calculation Groups

### ตัวอย่างที่ 9: Basic Calculation Group - Measure Selector

**โครงสร้าง:**

```
Calculation Group: "my 1st Calculation group"
├── Calculation Item: "Reseller Sales Cost"
│   └── Expression: [Reseller Sales Cost]
└── Calculation Item: "Reseller Sales Revenue"
    └── Expression: [Reseller Sales Revenue]
```

**คอลัมน์:**
- `Measure Selector` - Column ที่ใช้แสดงชื่อ Calculation Items
- `Ordinal` - Column ที่ใช้เรียงลำดับ

**การใช้งาน:**
- ใช้เพื่อเลือก Measure ที่ต้องการแสดง
- ผู้ใช้สามารถเลือก Measure ผ่าน Slicer ได้

---

## 🎯 Time Intelligence Calculation Group

### ตัวอย่างที่ 10: Time Intelligence Calculation Group

**โครงสร้าง:**

```
Calculation Group: "Time Intelligence"
├── Calculation Item: Current
│   └── Expression: SELECTEDMEASURE()
├── Calculation Item: LY
│   └── Expression: CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR(...))
├── Calculation Item: MTD
│   └── Expression: CALCULATE(SELECTEDMEASURE(), DATESMTD(...))
├── Calculation Item: QTD
│   └── Expression: CALCULATE(SELECTEDMEASURE(), DATESQTD(...))
└── Calculation Item: YTD
    └── Expression: CALCULATE(SELECTEDMEASURE(), DATESYTD(...))
```

### Calculation Item: Current

```dax
Current = SELECTEDMEASURE()
```

**อธิบาย:**
- คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง
- ใช้เป็น Baseline สำหรับเปรียบเทียบ

---

### Calculation Item: LY (Last Year)

```dax
LY = 
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- คำนวณ Measure สำหรับปีที่แล้ว
- ใช้ SAMEPERIODLASTYEAR() เพื่อหาช่วงเวลาที่เท่ากันในปีที่แล้ว

---

### Calculation Item: MTD (Month to Date)

```dax
MTD = 
CALCULATE(
    SELECTEDMEASURE(),
    DATESMTD(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- คำนวณ Measure จากต้นเดือนถึงปัจจุบัน
- ใช้ DATESMTD() เพื่อหา Range จากต้นเดือน

---

### Calculation Item: QTD (Quarter to Date)

```dax
QTD = 
CALCULATE(
    SELECTEDMEASURE(),
    DATESQTD(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- คำนวณ Measure จากต้นไตรมาสถึงปัจจุบัน
- ใช้ DATESQTD() เพื่อหา Range จากต้นไตรมาส

---

### Calculation Item: YTD (Year to Date)

```dax
YTD = 
CALCULATE(
    SELECTEDMEASURE(),
    DATESYTD(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- คำนวณ Measure จากต้นปีถึงปัจจุบัน
- ใช้ DATESYTD() เพื่อหา Range จากต้นปี

---

### Calculation Item: Prev Year

```dax
Prev Year = 
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- คำนวณ Measure สำหรับปีที่แล้ว
- ใช้ SAMEPERIODLASTYEAR() เพื่อหาช่วงเวลาที่เท่ากันในปีที่แล้ว

**ตัวอย่าง:** ถ้าปัจจุบันเลือกวันที่ "2024-03-15" จะคำนวณค่าสำหรับ "2023-03-15"

---

### Calculation Item: LY (Offset) — แนวทาง Modern ด้วย Offset Columns

**ขั้นที่ 1: เพิ่ม Offset Columns ใน DimDate (Calculated Column, ซ่อนไว้)**

```dax
MonthOffset =
// เดือนปัจจุบัน = 0, อดีต = ติดลบ — ค่าคำนวณตอน refresh ข้อมูล
DATEDIFF ( TODAY (), DimDate[FullDateAlternateKey], MONTH )

YearOffset =
DATEDIFF ( TODAY (), DimDate[FullDateAlternateKey], YEAR )
```

**ขั้นที่ 2: Calculation Item ที่เลื่อน offset ไป −12 เดือน**

```dax
'LY (Offset)' =
VAR SelectedOffsets =
    SELECTCOLUMNS ( VALUES ( DimDate[MonthOffset] ), "@Prev", [MonthOffset] - 12 )
RETURN
    CALCULATE (
        SELECTEDMEASURE (),
        REMOVEFILTERS ( DimDate ),
        DimDate[MonthOffset] IN SelectedOffsets
    )
```

**อธิบาย:**
- จับค่า `MonthOffset` ที่อยู่ใน filter context ปัจจุบัน แล้วลบ 12 → "เดือนเดิมปีก่อน"
- เป็น filter ธรรมดาบนคอลัมน์ ไม่ override ทาง date relationship เหมือน Classic TI
- ทำงานกับปฏิทินพิเศษ (4-4-5, Fiscal) ได้โดยไม่ต้องแก้สูตร

**ตรวจสอบจากข้อมูลจริง (AdventureWorksDW2025):** `LY (Offset)` ให้ผล**ตรงกับ** `SAMEPERIODLASTYEAR` ทุกปี — เช่น Sales 2013 = 33,574,834 ทั้งคู่ ทำให้เทียบสองแนวทางใน Calculation Group เดียว (มีทั้ง `LY` และ `LY (Offset)`) ได้ทันที

**ข้อควรระวัง:** `TODAY()` คำนวณตอน refresh — ถ้าข้อมูลเก่า ค่า offset จะเพี้ยน ต้อง refresh DimDate

---

## 🎯 เปรียบเทียบ: With vs Without Calculation Group

### ตัวอย่างที่ 11: Without Calculation Group

**ต้องสร้าง Measures หลายตัว:**

```dax
// Base Measures
Order Sales Amount = SUM(FactResellerSales[SalesAmount])
Total Cost = SUM(FactResellerSales[TotalProductCost])

// Time Intelligence Measures สำหรับ Order Sales Amount
Order Sales Amount MTD = 
CALCULATE(
    [Order Sales Amount],
    DATESMTD(DimDate[FullDateAlternateKey])
)

Order Sales Amount QTD = 
CALCULATE(
    [Order Sales Amount],
    DATESQTD(DimDate[FullDateAlternateKey])
)

Order Sales Amount YTD = 
CALCULATE(
    [Order Sales Amount],
    DATESYTD(DimDate[FullDateAlternateKey])
)

// Time Intelligence Measures สำหรับ Total Cost
Total Cost MTD = 
CALCULATE(
    [Total Cost],
    DATESMTD(DimDate[FullDateAlternateKey])
)

Total Cost QTD = 
CALCULATE(
    [Total Cost],
    DATESQTD(DimDate[FullDateAlternateKey])
)

Total Cost YTD = 
CALCULATE(
    [Total Cost],
    DATESYTD(DimDate[FullDateAlternateKey])
)
```

**ผลลัพธ์:** 8 Measures (2 Base + 6 Time Intelligence)

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW2025

---

### ตัวอย่างที่ 12: With Calculation Group

**สร้างเพียง Base Measures และ Calculation Group:**

```dax
// Base Measures (สร้างเพียง 2 ตัว)
Order Sales Amount = SUM(FactResellerSales[SalesAmount])
Total Cost = SUM(FactResellerSales[TotalProductCost])

// Calculation Group: "Time Intelligence"
calculationItem 'Current Period' = SELECTEDMEASURE()

calculationItem 'Prev Year' =
    CALCULATE(
        SELECTEDMEASURE(),
        SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
    )

calculationItem MTD =
    CALCULATE(
        SELECTEDMEASURE(),
        DATESMTD(DimDate[FullDateAlternateKey])
    )

calculationItem QTD =
    CALCULATE(
        SELECTEDMEASURE(),
        DATESQTD(DimDate[FullDateAlternateKey])
    )

calculationItem YTD =
    CALCULATE(
        SELECTEDMEASURE(),
        DATESYTD(DimDate[FullDateAlternateKey])
    )
```

**ผลลัพธ์:** 2 Measures + 1 Calculation Group (5 Calculation Items)

**ข้อดี:**
- ลดจำนวน Measures อย่างมาก
- เมื่อต้องการเปลี่ยน Logic (เช่น ปรับสูตร YTD) แก้ไขที่ Calculation Item เดียว
- Model เรียบง่ายขึ้น และดูแลรักษาง่ายขึ้น

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW2025

---

## 🎯 Currency Conversion Calculation Group

### ตัวอย่างที่ 13: Conversion Rate Calculation Group (จากไฟล์จริง)

**โครงสร้าง:**

```
Calculation Group: "Conversion Rate"
├── Calculation Item: No conversion (USD)
│   └── Expression: SELECTEDMEASURE()
├── Calculation Item: Conversion (AVG)
│   └── Expression: SELECTEDMEASURE() * AverageRate
└── Calculation Item: Conversion (EOD)
    └── Expression: SELECTEDMEASURE() * EndOfDayRate
```

### Calculation Item: No conversion (USD)

```dax
'No conversion (USD)' = SELECTEDMEASURE()
```

**อธิบาย:**
- ไม่แปลงค่า (ใช้ Local Currency - USD)
- คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง

---

### Calculation Item: Conversion (AVG)

```dax
'Conversion (AVG)' =
    VAR _rate =
        CALCULATE (
            AVERAGE ( FactCurrencyRate[AverageRate] ),
            CROSSFILTER ( DimDate[DateKey], FactCurrencyRate[DateKey], BOTH )
        )
    RETURN
        SELECTEDMEASURE () * _rate
```

**อธิบาย:**
- คำนวณ Average Exchange Rate จาก FactCurrencyRate table
- ใช้ `CROSSFILTER()` เพื่อเชื่อม Date Dimension กับ FactCurrencyRate table
- `CROSSFILTER(..., BOTH)` เปิดใช้งาน Bidirectional Filter
- คูณ `SELECTEDMEASURE()` ด้วย Average Rate

**เหตุผลใช้ CROSSFILTER():**
- FactCurrencyRate table ไม่ได้เชื่อมโดยตรงกับ Fact Table
- ต้องใช้ Date Dimension เป็นตัวเชื่อม
- CROSSFILTER() ช่วยให้สามารถ Filter FactCurrencyRate จาก Date ใน Visual ได้

---

### Calculation Item: Conversion (EOD)

```dax
'Conversion (EOD)' =
    VAR _rate =
        CALCULATE (
            AVERAGE ( FactCurrencyRate[EndOfDayRate] ),
            CROSSFILTER ( DimDate[DateKey], FactCurrencyRate[DateKey], BOTH )
        )
    RETURN
        SELECTEDMEASURE () * _rate
```

**อธิบาย:**
- คำนวณ End of Day Exchange Rate จาก FactCurrencyRate table
- ใช้ `CROSSFILTER()` เพื่อเชื่อม Date Dimension กับ FactCurrencyRate table
- คูณ `SELECTEDMEASURE()` ด้วย End of Day Rate

**ตัวอย่างการใช้งาน:**
- เมื่อเลือกวันที่ "2024-03-15" และเลือก "Conversion (AVG)"
- จะคำนวณอัตราแลกเปลี่ยนเฉลี่ยสำหรับวันที่ 2024-03-15
- แล้วคูณด้วย Measure ที่เลือก (เช่น Total Sales)

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW2025

---

### ตัวอย่างที่ 14: ข้อจำกัดของสูตรง่ายบนโมเดลหลายสกุลเงิน (วัดจากข้อมูลจริง)

สูตรในตัวอย่างที่ 13 ถูกต้องเมื่อ **ยอดขายเก็บสกุลเดียว** แต่ FactResellerSales ของ AW เก็บหลายสกุลปนกัน (USD, CAD, GBP, EUR, AUD ฯลฯ) เมื่อวัดจริงจะเจอสามอาการ:

| สถานการณ์ | อาการ | ตัวเลขจริง (ปี 2013) |
|---|---|---|
| ไม่เลือกสกุลใน slicer | `AVERAGE(rate)` เฉลี่ยข้ามทุกสกุลที่มี rate แล้วคูณยอดรวม | 33,574,834 → **16,487,164** (ไร้ความหมาย) |
| เลือก GBP | relationship `DimCurrency → FactResellerSales` ตัดยอดเหลือแถว GBP ก่อน แล้วค่อยคูณ rate | **2,720,032** (ยอด nominal แถว GBP) ไม่ใช่ยอดทั้งบริษัทเป็นปอนด์ |
| ทิศทาง rate | rate ของ AW = USD ต่อ 1 หน่วยของสกุล → `× rate` ได้แค่สกุลถิ่น→USD | เลือก THB แล้วเห็น "฿6,617" ซึ่งเป็นมูลค่า USD ของยอดบาท ไม่ใช่ยอดบาท |

**บทเรียน:** สูตร DAX สั้นหรือยาวเป็นผลของการออกแบบโมเดล ไม่ใช่ฝีมือเขียนสูตร — ตัวอย่างสั้นบน Microsoft Learn สมมติว่ายอดเก็บสกุลเดียว + currency ไม่ผูกกับตารางขาย

---

### ตัวอย่างที่ 15: Display Currency — แปลงทุกสกุลเป็นสกุลที่เลือก (cross-rate)

หัวใจ: แยกยอดเป็น **รายวัน × รายสกุลต้นทาง** แล้วแปลงผ่าน USD ด้วย rate ของวันนั้นจริง ๆ

```dax
'Conversion (EOD)' =
// แปลงยอดขาย "ทุกสกุล" เป็นสกุลที่เลือกใน slicer (display currency)
// วิธี: cross-rate รายวัน = ยอด x rate(สกุลต้นทาง) / rate(สกุลปลายทาง)
VAR TargetKey =
    SELECTEDVALUE ( DimCurrency[CurrencyKey] )          // สกุลปลายทางจาก slicer (สมมติเลือกเดียว)
RETURN
    IF (
        ISBLANK ( TargetKey ),                          // ไม่เลือกสกุล = ไม่แปลง
        SELECTEDMEASURE (),
        CALCULATE (
            SUMX (
                SUMMARIZE ( FactResellerSales, DimDate[DateKey], FactResellerSales[CurrencyKey] ),   // แยกยอดรายวัน x รายสกุลต้นทาง (fact เก็บหลายสกุลปนกัน ต้องใช้ rate ของสกุลนั้นจริง ๆ)
                VAR SourceRate =
                    CALCULATE (
                        MAX ( FactCurrencyRate[EndOfDayRate] ),
                        TREATAS ( { FactResellerSales[CurrencyKey] }, FactCurrencyRate[CurrencyKey] )   // ทาบ key ตรง เพราะ fact กับ rate ไม่มี relationship ตรงกันเอง
                    )
                VAR TargetRate =
                    CALCULATE (
                        MAX ( FactCurrencyRate[EndOfDayRate] ),
                        TREATAS ( { TargetKey }, FactCurrencyRate[CurrencyKey] )
                    )
                VAR Amount = SELECTEDMEASURE ()          // ยอดของ (วันนี้, สกุลต้นทางนี้)
                RETURN
                    DIVIDE ( Amount * SourceRate, TargetRate )   // เส้นทาง: สกุลต้นทาง -> USD -> สกุลปลายทาง
            ),
            REMOVEFILTERS ( DimCurrency )                // สำคัญ: filter จาก slicer ไหลเข้าตาราง rate ทาง relationship DimCurrency -> rate ทำให้เห็น rate อยู่สกุลเดียว ต้องล้างก่อนถึงจะหา cross-rate ได้ครบ
        )
    )
```

**Conversion (AVG):** ใช้โครงเดียวกัน เปลี่ยนคอลัมน์เป็น `FactCurrencyRate[AverageRate]` (rate เฉลี่ยของวัน แทน rate ปิดวัน)

**ผลตรวจจากข้อมูลจริง (ปี 2013):** USD 32,324,839 / GBP 21,807,497 / THB 992,991,005 — สามค่าเทียบกันด้วย cross-rate ได้ตรงกันเอง (21.81M × rate GBP ÷ rate THB = 992.99M)

---

### ตัวอย่างที่ 16: Dynamic Format String ต่อสกุลเงิน

**ขั้นที่ 1:** เพิ่มคอลัมน์ format ใน DimCurrency (หน้างานจริงมักโหลดจาก SQL) เช่น `"$"#,0.00`, `"€"#,0.00`, `"฿"#,0.00`

**ขั้นที่ 2:** ตั้ง **Format string expression** ของ Calculation Item ตาม pattern ทางการของ Microsoft Learn:

```dax
SELECTEDVALUE (
    DimCurrency[CurrencyFormatString],
    SELECTEDMEASUREFORMATSTRING ()   // fallback: ใช้ format ของ Measure ต้นทาง (เช่น $ ของ Sales Amount)
)
```

**ข้อควรรู้ (จาก Microsoft Learn):**
- Dynamic format string มีผลเฉพาะใน **visual** — DAX Query จะเห็นแต่ตัวเลขดิบเสมอ
- ถ้าแสดงผลเพี้ยน ให้เช็ค **Display units** ของ visual → เปลี่ยนจาก Auto เป็น None
- หลาย Calculation Groups พร้อมกัน → **format ของ group ที่ Precedence สูงสุดชนะ**
- `No conversion (USD)` ควรตั้ง format string เป็น `SELECTEDMEASUREFORMATSTRING()` เพื่อสืบทอด format ของ measure ต้นทาง (ไม่ hardcode)

---

### ตัวอย่างที่ 17: Inactive Relationship + USERELATIONSHIP กับสกุลเงิน

ถ้า slicer สกุลเงินมีสองความหมายพร้อมกัน (กรองยอด + เลือกสกุลแสดงผล) ผู้ใช้จะตีความผิดง่าย วิธีแก้: ทำ `DimCurrency → FactResellerSales` เป็น **inactive** ให้ slicer เหลือความหมายเดียว แล้วเปิดใช้เฉพาะ measure ที่ต้องการ

```dax
// ยอดขายแยกตามสกุลที่บันทึกในแถว (transaction currency)
// เปิด relationship ชั่วคราวด้วย USERELATIONSHIP เพราะความสัมพันธ์หลัก inactive
'Sales (Recorded Currency)' =
CALCULATE (
    [Sales Amount],
    USERELATIONSHIP ( DimCurrency[CurrencyKey], FactResellerSales[CurrencyKey] )
)
```

**ระวัง:** Calculated Column ที่อาศัย relationship นี้ต้องแก้ตาม เช่น flag กรอง slicer:

```dax
'Has Exchange Rate' =
// TRUE = สกุลนี้มีทั้ง rate และมียอดขายจริง → เลือกใน slicer แล้วมีตัวเลขเสมอ
VAR HasRate =
    CALCULATE ( COUNTROWS ( FactCurrencyRate ) ) > 0
VAR HasSales =
    CALCULATE (
        COUNTROWS ( FactResellerSales ),
        USERELATIONSHIP ( DimCurrency[CurrencyKey], FactResellerSales[CurrencyKey] )
    ) > 0
RETURN
    HasRate && HasSales
```

(บนข้อมูลจริง: FactCurrencyRate มี rate แค่ 14 จาก 105 สกุลใน DimCurrency และมียอดขายจริงแค่ 6 สกุล — flag นี้กันผู้เรียนเลือกสกุลแล้วเจอ blank เฉย ๆ)

**ผลตรวจ:** หลังทำ inactive — conversion ทุกค่าเท่าเดิมทุกตัว (GBP 2013 = 21,807,497 เหมือนเดิม) แต่ `Sales Amount` เมื่อเลือก GBP กลายเป็นยอดรวมทั้งบริษัท 39,358,118 แทนที่จะถูกตัดเหลือแถว GBP

---

## 🎯 SELECTEDMEASURE() Function

### พื้นฐาน

**Syntax:**
```dax
SELECTEDMEASURE()
```

**คำอธิบาย:**
- คืนค่า Measure ที่อยู่ใน Context ปัจจุบัน
- ไม่ต้องส่ง Parameter
- ใช้ใน Calculation Items เท่านั้น

**ตัวอย่าง:**
```dax
Current = SELECTEDMEASURE()
```

---

### ใช้กับ CALCULATE()

**ตัวอย่าง:**
```dax
LY = 
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- ใช้ SELECTEDMEASURE() ใน CALCULATE()
- เปลี่ยน Filter Context เพื่อคำนวณปีที่แล้ว

---

## 🎯 SELECTEDVALUE() Function

### พื้นฐาน

**Syntax:**
```dax
SELECTEDVALUE(<columnName>[, <alternateResult>])
```

**คำอธิบาย:**
- คืนค่าที่ถูกเลือกไว้ในคอลัมน์จาก Filter Context หรือ Slicer
- หากมีการเลือกค่ามากกว่า 1 ค่า หรือไม่มีการเลือกค่าใดๆ จะคืนค่าตาม Argument ที่สอง

**ตัวอย่าง:**
```dax
Selected Period = 
SELECTEDVALUE(
    'Time Intelligence'[Period],
    "All"
)
```

**อธิบาย:**
- ถ้ามีการเลือก Period ใน Slicer จะคืนค่า Period นั้น
- ถ้าไม่มีการเลือก จะคืนค่า "All"

---

### ใช้ใน Calculation Item

**ตัวอย่าง:**
```dax
Dynamic Format = 
VAR selectedPeriod = SELECTEDVALUE('Time Intelligence'[Period])
RETURN
    IF(
        selectedPeriod = "YTD",
        SELECTEDMEASURE(),  // Format เป็นตัวเลข
        SELECTEDMEASURE()   // Format เป็นเปอร์เซ็นต์
    )
```

---

## 🎯 Multiple Calculation Groups

### Precedence

**เมื่อมีหลาย Calculation Groups:**

```dax
// Calculation Group 1: Time Intelligence
// Calculation Group 2: Conversion Rate (precedence: 1)
```

**การทำงาน:**
- Calculation Group ที่มี Precedence สูงกว่าจะทำงานก่อน
- จากตัวอย่าง: Conversion Rate (precedence: 1) จะทำงานก่อน Time Intelligence

**ตัวอย่างการทำงาน:**

```dax
// ถ้าเลือก YTD และ AVG Rate
// จะทำงานแบบนี้:
AVG Rate: SELECTEDMEASURE() * xrate
  ↓
YTD: CALCULATE(AVG Rate, DATESYTD(...))
```

---

## 🎯 Best Practices

### 1. ใช้ SELECTEDMEASURE() แทนการระบุ Measure โดยตรง

**❌ ไม่ดี:**
```dax
calculationItem 'Sales LY' = 
CALCULATE(
    [Total Sales],  // ระบุ Measure โดยตรง
    SAMEPERIODLASTYEAR(...)
)
```

**✅ ดี:**
```dax
calculationItem LY = 
CALCULATE(
    SELECTEDMEASURE(),  // ใช้ SELECTEDMEASURE()
    SAMEPERIODLASTYEAR(...)
)
```

**เหตุผล:**
- สามารถใช้กับทุก Measure ได้
- ลดจำนวน Calculation Items ที่ต้องสร้าง

---

### 2. ตั้งชื่อ Calculation Items ให้ชัดเจน

**❌ ไม่ดี:**
```dax
calculationItem 'Item1' = SELECTEDMEASURE()
```

**✅ ดี:**
```dax
calculationItem 'YTD' = 
CALCULATE(SELECTEDMEASURE(), DATESYTD(...))
```

---

### 3. ใช้ Precedence เมื่อมีหลาย Calculation Groups

**ตัวอย่าง:**
```dax
// Time Intelligence (ไม่มี precedence)
// Conversion Rate (precedence: 1) - ทำงานก่อน
```

---

## 📚 สรุป

### ✅ ข้อดีของ Calculation Groups

1. **ลดจำนวน Measures**
   - ไม่ต้องสร้าง Measures แยกสำหรับแต่ละ Variation
   - เช่น ไม่ต้องสร้าง Sales YTD, Sales QTD, Sales MTD แยกกัน

2. **ง่ายต่อการบำรุงรักษา**
   - แก้ไข Calculation Item เดียว มีผลต่อทุก Measure
   - เช่น แก้ไข YTD Logic มีผลต่อทุก Measure

3. **Performance ดีขึ้น**
   - ลด Cardinality ของ Measures
   - Query Execution Plan ดีขึ้น

4. **ความสอดคล้อง**
   - Business Logic สอดคล้องกันทั่วทั้ง Report

---

**📖 เอกสารที่เกี่ยวข้อง:**
- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

