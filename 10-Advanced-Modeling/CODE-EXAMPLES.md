# Code Examples - Advanced Modeling Patterns

## 📝 เอกสารตัวอย่างโค้ด DAX สำหรับ Advanced Modeling Patterns

ไฟล์นี้รวบรวมตัวอย่างโค้ด DAX สำหรับ Advanced Modeling Patterns รวมถึง Heterogeneous Granularity, Bidirectional Filters, และเทคนิคขั้นสูงอื่นๆ

> **หมายเหตุ:** ตัวอย่างโค้ดสำหรับ Calculation Groups ได้ย้ายไปอยู่ใน **08-Fact-Tables-Design/CODE-EXAMPLES.md** แล้ว

> **ไฟล์ตัวอย่าง:**
> - `Data Model Conformed Date Dimension.pbix`
> - `AdventureWorksDW2025` - ตัวอย่าง FactSalesQuota และ FactResellerSales
> **หมายเหตุ:** ไฟล์ตัวอย่างเป็นสื่อการสอนของผู้สอน (Trainer Material) — ขอไฟล์ได้จากผู้สอนระหว่างเรียน ไม่ได้แจกจ่ายผ่าน repository นี้

---

## 🎯 Heterogeneous Granularity

### ตัวอย่างที่ 1: Measures จาก Fact Tables ที่มี Granularity แตกต่างกัน

**FactSalesQuota - ระดับเดือน:**
```dax
Sales Quota = SUM(FactSalesQuota[SalesAmountQuota])
```

**FactResellerSales - ระดับวัน:**
```dax
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

**อธิบาย:**
- FactSalesQuota มี Granularity ระดับเดือน
- FactResellerSales มี Granularity ระดับวัน
- ไม่สามารถเปรียบเทียบในระดับวันได้โดยตรง

---

### ตัวอย่างที่ 2: Aggregate ข้อมูลระดับวันเป็นระดับเดือน

**Base Measure:**
```dax
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

**Measure ที่ Aggregate เป็นระดับเดือน:**
```dax
Reseller Sales Revenue (Monthly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day]),
    VALUES('Date'[MonthKey])
)
```

**อธิบาย:**
- ใช้ `ALL('Date'[Day])` เพื่อลบ Filter Context ในระดับวัน
- ใช้ `VALUES('Date'[MonthKey])` เพื่อกำหนด Filter Context ในระดับเดือน
- ผลลัพธ์คือยอดขายรวมในแต่ละเดือน

---

### ตัวอย่างที่ 3: Aggregate ข้อมูลระดับวันเป็นระดับไตรมาส

**Measure ที่ Aggregate เป็นระดับไตรมาส:**
```dax
Reseller Sales Revenue (Quarterly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day], 'Date'[MonthKey]),
    VALUES('Date'[CalendarQuarter])
)
```

**อธิบาย:**
- ใช้ `ALL()` เพื่อลบ Filter Context ในระดับวันและเดือน
- ใช้ `VALUES('Date'[CalendarQuarter])` เพื่อกำหนด Filter Context ในระดับไตรมาส
- ผลลัพธ์คือยอดขายรวมในแต่ละไตรมาส

---

### ตัวอย่างที่ 4: Aggregate ข้อมูลระดับวันเป็นระดับปี

**Measure ที่ Aggregate เป็นระดับปี:**
```dax
Reseller Sales Revenue (Yearly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day], 'Date'[MonthKey], 'Date'[CalendarQuarter]),
    VALUES('Date'[CalendarYear])
)
```

**อธิบาย:**
- ใช้ `ALL()` เพื่อลบ Filter Context ในระดับวัน, เดือน, และไตรมาส
- ใช้ `VALUES('Date'[CalendarYear])` เพื่อกำหนด Filter Context ในระดับปี
- ผลลัพธ์คือยอดขายรวมในแต่ละปี

---

### ตัวอย่างที่ 5: เปรียบเทียบ Sales Revenue กับ Sales Quota

**รวม Measures จากทั้งสอง Fact Tables:**

```dax
// Base Measures
Sales Quota = SUM(FactSalesQuota[SalesAmountQuota])
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])

// Aggregate Reseller Sales Revenue เป็นระดับเดือน
Reseller Sales Revenue (Monthly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day]),
    VALUES('Date'[MonthKey])
)

// Measure เปรียบเทียบ
Variance vs Quota = 
[Reseller Sales Revenue (Monthly)] - [Sales Quota]

Variance vs Quota % = 
DIVIDE(
    [Variance vs Quota],
    [Sales Quota],
    0
)
```

**อธิบาย:**
- ใช้ `Reseller Sales Revenue (Monthly)` เพื่อให้ Granularity ตรงกับ `Sales Quota`
- สร้าง Measures เปรียบเทียบ Variance และ Variance %

---

### ตัวอย่างที่ 6: ใช้ Hierarchy เพื่อควบคุม Granularity

**โครงสร้าง Date Hierarchy:**
```
Calendar Year
└── Calendar Quarter
    └── Calendar Month
        └── Day
```

**Measures:**
```dax
Sales Quota = SUM(FactSalesQuota[SalesAmountQuota])
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

**การใช้งาน:**
- เมื่อเลือก **Calendar Month** → ทั้งสอง Measures จะแสดงข้อมูลในระดับเดือน
- เมื่อเลือก **Day** → เฉพาะ `Reseller Sales Revenue` จะแสดงข้อมูลได้

**ประโยชน์:**
- ควบคุม Granularity ผ่าน Hierarchy
- ไม่ต้องเขียน DAX ซับซ้อน
- ผู้ใช้เลือกระดับความละเอียดที่ต้องการได้เอง

---

### ตัวอย่างที่ 6b: Daily Allocation ด้วย CROSSFILTER(None) + TREATAS (จากไฟล์จริง AdventureWorksDW2025)

จัดสรร quota รายไตรมาสลงเป็นรายวัน เพื่อเทียบกับยอดขายรายวันใน visual เดียว

**ข้อเท็จจริงของ FactSalesQuota บนข้อมูลจริง:**
- Grain = รายพนักงานต่อไตรมาส (แถว 10–17 แถว/ไตรมาส ตามขนาดทีม)
- วันที่ anchor ไม่ตรง boundary: ปี 2011 คือ 31 มี.ค. / 30 มิ.ย. / 29 ก.ย. / 29 ธ.ค.
- จึง map ด้วยคู่ `(CalendarYear, CalendarQuarter)` ของตารางโควตาเอง — ห้ามคำนวณจากวันที่

**Relationships ที่ใช้:**
```
FactSalesQuota[DateKey]     --> DimDate[DateKey]     (Active — ใช้กับ measure ธรรมดา)
FactSalesQuota[EmployeeKey] --> DimEmployee[EmployeeKey] (Active)
```

**Measure ธรรมดา (ใช้ระดับไตรมาสขึ้นไป):**
```dax
'Sales Quota Amount' = SUM ( FactSalesQuota[SalesAmountQuota] )
```

**Measure จัดสรรรายวัน:**
```dax
'Sales Quota Amount (Daily Alloc)' =
// โควตาต่อวัน = โควตาของไตรมาสนั้น / จำนวนวันของไตรมาส
// 1) CROSSFILTER(None) ปิด relationship วันที่ชั่วคราว ไม่ให้ filter "วัน" กลั่นแถวโควตา
// 2) TREATAS ทาบคู่ (ปี, ไตรมาส) กลับเข้าตารางโควตา (virtual relationship)
SUMX (
    VALUES ( DimDate[FullDateAlternateKey] ),
    VAR CurrentDate = DimDate[FullDateAlternateKey]
    VAR CurrentYear = YEAR ( CurrentDate )
    VAR CurrentQuarter = ROUNDUP ( DIVIDE ( MONTH ( CurrentDate ), 3 ), 0 )
    VAR QuarterQuota =
        CALCULATE (
            SUM ( FactSalesQuota[SalesAmountQuota] ),
            CROSSFILTER ( FactSalesQuota[DateKey], DimDate[DateKey], None ),
            TREATAS (
                { ( CurrentYear, CurrentQuarter ) },
                FactSalesQuota[CalendarYear],
                FactSalesQuota[CalendarQuarter]
            )
        )
    VAR QuarterStart = DATE ( CurrentYear, CurrentQuarter * 3 - 2, 1 )
    VAR QuarterEnd = EOMONTH ( QuarterStart, 2 )
    VAR DaysInQuarter = COUNTROWS ( CALENDAR ( QuarterStart, QuarterEnd ) )
    RETURN
        DIVIDE ( QuarterQuota, DaysInQuarter )
)
```

**ตรวจสอบด้วยข้อมูลจริง:**

```dax
EVALUATE
SUMMARIZECOLUMNS (
    DimDate[FullDateAlternateKey],
    TREATAS (
        { DATE ( 2011, 4, 8 ), DATE ( 2011, 6, 30 ), DATE ( 2011, 7, 1 ) },
        DimDate[FullDateAlternateKey]
    ),
    "Quota (plain)", [Sales Quota Amount],
    "Quota (Daily Alloc)", [Sales Quota Amount (Daily Alloc)]
)
```

| วันที่ | Quota (plain) | Quota (Daily Alloc) |
|---|---|---|
| 8 เม.ย. 2011 | (blank) | 52,198 |
| 30 มิ.ย. 2011 | 4,750,000 | 52,198 |
| 1 ก.ค. 2011 | (blank) | 55,087 (= Q3 5,068,000 ÷ 92 วัน) |

- รวมทั้งปี 2011: ทั้งสอง measure ให้ค่าเดียวกัน (25,982,000) — ตัวจัดสรรแค่กระจายลงวัน
- ช่วงที่ไม่มีแถวโควตา (เช่น ไตรมาส 2/2011 ก่อนแก้ต้นทาง) ตัวจัดสรรจะ blank อย่างถูกต้อง — จุดคุยเรื่อง data quality ในคลาส

---

## 🎯 Bidirectional Filters

### ตัวอย่างที่ 7: การตั้งค่า Bidirectional Filter

**Relationship Settings:**
```
DimProduct → FactResellerSales
Cross-Filter Direction: Both
```

**ผลกระทบ:**
- เมื่อกรอง Product → Filter Reseller Sales
- เมื่อกรอง Reseller Sales → Filter Product (กลับทิศทาง)

**ข้อควรระวัง:**
- อาจทำให้เกิด Circular Dependencies
- Performance อาจลดลง

---

## 🎯 Many-to-Many Relationships

### ตัวอย่างที่ 8: ใช้ Bridge Table แก้ปัญหา Many-to-Many

**โครงสร้าง:**
```
FactSales
  ↓
Bridge Table (ProductCategory)
  ↓
DimCategory
```

**ประโยชน์:**
- แก้ปัญหา Many-to-Many Relationships
- โครงสร้าง Model ชัดเจนขึ้น

---

## 🎯 Best Practices

### ✅ ข้อแนะนำสำหรับ Heterogeneous Granularity

1. **ใช้ Conformed Date Dimension**
   - ใช้ Date Dimension เดียวกันสำหรับทุก Fact Table
   - สร้าง Relationships ที่เหมาะสมตาม Granularity

2. **สร้าง Hierarchy ให้เหมาะสม**
   - สร้าง Date Hierarchy ที่รองรับหลายระดับ
   - เช่น Year > Quarter > Month > Day

3. **ใช้ Time Intelligence Functions อย่างระมัดระวัง**
   - ใช้เมื่อจำเป็นต้อง Aggregate ข้อมูล
   - พิจารณา Performance Impact

4. **อธิบาย Granularity ให้ชัดเจน**
   - ตั้งชื่อ Measures ให้ระบุ Granularity ชัดเจน
   - เช่น `Sales Revenue (Monthly)`, `Sales Revenue (Daily)`

---

**📖 เอกสารที่เกี่ยวข้อง:**
- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

---

# Code Examples - Calculation Groups (Legacy - ย้ายไป 08-Fact-Tables-Design แล้ว)

## ⚠️ หมายเหตุ

ตัวอย่างโค้ดสำหรับ Calculation Groups ได้ย้ายไปอยู่ใน **08-Fact-Tables-Design/CODE-EXAMPLES.md** แล้ว

ไฟล์นี้เก็บไว้เพื่ออ้างอิงเท่านั้น

---

## 🎯 Calculation Groups พื้นฐาน

### ตัวอย่างที่ 1: Basic Calculation Group - Measure Selector

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

### ตัวอย่างที่ 2: Time Intelligence Calculation Group

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

## 🎯 Currency Conversion Calculation Group

### ตัวอย่างที่ 3: Conversion Rate Calculation Group

**โครงสร้าง:**

```
Calculation Group: "Conversion Rate" (precedence: 1)
├── Calculation Item: AVG Rate
│   └── Expression: SELECTEDMEASURE() * AverageRate
├── Calculation Item: EOD Rate
│   └── Expression: SELECTEDMEASURE() * EndOfDayRate
└── Calculation Item: NO Conversion
    └── Expression: SELECTEDMEASURE()
```

### Calculation Item: AVG Rate

```dax
AVG Rate = 
VAR xrate = CALCULATE(
    AVERAGE(FactFactCurrencyRate[AverageRate]),
    CROSSFILTER(DimDate[DateKey], FactFactCurrencyRate[DateKey], Both)
)
RETURN 
    SELECTEDMEASURE() * xrate
```

**อธิบาย:**
- คำนวณ Average Exchange Rate
- ใช้ CROSSFILTER() เพื่อ Filter ผ่าน Relationship
- คูณ SELECTEDMEASURE() ด้วย Exchange Rate

---

### Calculation Item: EOD Rate

```dax
EOD Rate = 
VAR xrate = CALCULATE(
    AVERAGE(FactFactCurrencyRate[EndOfDayRate]),
    CROSSFILTER(DimDate[DateKey], FactFactCurrencyRate[DateKey], Both)
)
RETURN 
    SELECTEDMEASURE() * xrate
```

**อธิบาย:**
- คำนวณ End of Day Exchange Rate
- คูณ SELECTEDMEASURE() ด้วย Exchange Rate

---

### Calculation Item: NO Conversion

```dax
NO Conversion = SELECTEDMEASURE()
```

**อธิบาย:**
- ไม่แปลงค่า (ใช้ Local Currency)
- คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง

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

## 📚 ตัวอย่างการใช้งาน

### ตัวอย่างที่ 4: สร้าง Calculation Group สำหรับ Format

**โครงสร้าง:**

```
Calculation Group: "Format"
├── Calculation Item: Number
│   └── Expression: SELECTEDMEASURE()
├── Calculation Item: Percentage
│   └── Expression: SELECTEDMEASURE() / 100
└── Calculation Item: Currency
    └── Expression: SELECTEDMEASURE()
```

**Format String Expression:**

```dax
// สำหรับ Calculation Item: Percentage
// Format String: "0.00%"
```

---

### ตัวอย่างที่ 5: Dynamic Format String

**Calculation Item:**

```dax
Percentage Format = SELECTEDMEASURE()
```

**Format String Expression:**

```dax
IF(
    SELECTEDVALUE('Format'[Format Type]) = "Percentage",
    "0.00%",
    "#,##0.00"
)
```

---

## 🎯 สรุป

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

