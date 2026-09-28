# Code Examples - Relationships

## 📋 ไฟล์รวบรวม Code Examples สำหรับการ Copy-Paste

ไฟล์นี้รวบรวม Code Examples ทั้งหมดสำหรับโมดูล Relationships รวมถึงการสร้าง Relationships และ DAX Functions ที่เกี่ยวข้อง

---

## Part 1: การสร้าง Relationships

### 1. การสร้าง Relationship แบบ Manual

**ขั้นตอน:**
1. เปิด Model View
2. ลากคอลัมน์จาก Fact Table ไปยัง Dimension Table
3. ตรวจสอบ Cardinality และ Cross Filter Direction

**ตัวอย่าง:**
```
ลาก: FactResellerSales[ProductKey] → DimProduct[ProductKey]
```

### 2. การตั้งชื่อ Relationship

**แนะนำ:**
- ใช้ชื่อที่ชัดเจน เช่น "Sales → Product"
- รวมชื่อตารางทั้งสอง

**ตัวอย่าง:**
```
FactResellerSales[ProductKey] → DimProduct[ProductKey]
ชื่อ Relationship: "ResellerSales → Product"
```

### 3. การสร้าง Inactive Relationship

**ขั้นตอน:**
1. สร้าง Relationship ตามปกติ
2. คลิกที่เส้น Relationship
3. คลิกขวา → Properties
4. **ยกเลิก** Active checkbox

**ตัวอย่าง:**
```
FactResellerSales[ShipDate] → DimDate[DateKey]
→ Inactive Relationship (สำหรับ USERELATIONSHIP())
```

---

## Part 2: DAX Functions สำหรับ Relationships

### 1. RELATED() และ RELATEDTABLE()

#### RELATED() - ดึงข้อมูลชื่อสินค้า

```dax
Product Name = RELATED(Product[ProductName])
```

#### RELATED() - ดึงข้อมูลหลายคอลัมน์

```dax
// คอลัมน์ 1: ชื่อสินค้า
Product Name = RELATED(Product[ProductName])

// คอลัมน์ 2: ราคาสินค้า
Product Price = RELATED(Product[Price])

// คอลัมน์ 3: หมวดหมู่สินค้า
Product Category = RELATED(Product[Category])
```

#### RELATEDTABLE() - นับจำนวนการขาย

```dax
Sales Count = COUNTROWS(RELATEDTABLE(Sales))
```

#### RELATEDTABLE() - คำนวณยอดขายรวม

```dax
Total Sales = 
SUMX(
    RELATEDTABLE(Sales),
    Sales[Quantity] * Sales[UnitPrice]
)
```

#### RELATED() ใน Iterator Function

```dax
Total Sales by Category = 
SUMX(
    Sales,
    RELATED(Product[Price]) * Sales[Quantity]
)
```

---

### 2. CALCULATE() และ Relationship Filters

#### CALCULATE() - Filter ผ่าน Relationship

```dax
Sales - Electronics = 
CALCULATE(
    SUM(Sales[Amount]),
    Product[Category] = "Electronics"
)
```

#### CALCULATE() - Filter หลายเงื่อนไข

```dax
Sales - Electronics 2024 = 
CALCULATE(
    SUM(Sales[Amount]),
    Product[Category] = "Electronics",
    'Date'[Year] = 2024
)
```

#### ALL() - ลบ Filter ทั้งหมด

```dax
Total Sales All Categories = 
CALCULATE(
    SUM(Sales[Amount]),
    ALL(Product[Category])
)
```

#### เปอร์เซ็นต์ของยอดขาย

```dax
Sales % by Category = 
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(
        SUM(Sales[Amount]),
        ALL(Product[Category])
    )
)
```

#### FILTER() - สินค้าราคาสูงกว่าเฉลี่ย

```dax
Sales - Above Avg Price = 
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        Product,
        Product[Price] > AVERAGE(Product[Price])
    )
)
```

#### FILTER() - สินค้าขายได้มากกว่า 100 หน่วย

```dax
Sales - High Volume = 
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        Product,
        SUMX(
            RELATEDTABLE(Sales),
            Sales[Quantity]
        ) > 100
    )
)
```

---

### 3. USERELATIONSHIP()

#### USERELATIONSHIP() - ใช้ Inactive Relationship

```dax
Sales by Order Date = SUM(Sales[Amount])

Sales by Ship Date = 
CALCULATE(
    SUM(Sales[Amount]),
    USERELATIONSHIP(Sales[ShipDate], Date[DateKey])
)
```

#### USERELATIONSHIP() - เปรียบเทียบ Order Date และ Ship Date

```dax
Sales - Order Date = SUM(Sales[Amount])

Sales - Ship Date = 
CALCULATE(
    SUM(Sales[Amount]),
    USERELATIONSHIP(Sales[ShipDate], Date[DateKey])
)
```

---

### 4. CROSSFILTER()

#### CROSSFILTER() - เปลี่ยนเป็น Both Direction

```dax
Sales - Both Direction = 
CALCULATE(
    SUM(Sales[Amount]),
    CROSSFILTER(Sales[ProductKey], Product[ProductKey], BOTH)
)
```

#### CROSSFILTER() - ปิด Filter Direction

```dax
Sales - No Filter = 
CALCULATE(
    SUM(Sales[Amount]),
    CROSSFILTER(Sales[ProductKey], Product[ProductKey], NONE)
)
```

#### CROSSFILTER() - กรณีใช้จริง: วัด Penetration ของ Reseller Network ⭐ (จากไฟล์จริง)

**โจทย์ธุรกิจ:** เลือกหมวดสินค้า/ช่วงเวลาแล้ว มี Reseller กี่รายที่ขายได้จริง? ปัญหาคือ relationship ทุกเส้นเป็น single direction (`Dim → Fact`) filter จาก DimProduct/DimDate ไหลมาสุดที่ FactResellerSales แล้ว**หยุด** ไม่ย้อนไปกรอง DimReseller — `DISTINCTCOUNT(DimReseller[ResellerKey])` เลยได้ 701 (ทุกราย) เสมอ

**แก้ด้วย CROSSFILTER(Both) แบบ "ชั่วคราวใน measure" ดีกว่าเปิด bidirectional ที่ relationship (กระทบทั้งโมเดล + ambiguity):**

```dax
// Reseller ที่ "มีการขายจริง" ตาม filter ปัจจุบัน (เช่น หมวดสินค้า/ช่วงเวลา)
'Active Reseller Count' =
CALCULATE (
    DISTINCTCOUNT ( DimReseller[ResellerKey] ),
    CROSSFILTER (
        FactResellerSales[ResellerKey],  // จากฝั่ง Fact
        DimReseller[ResellerKey],        // ย้อนกลับมากรองฝั่ง Dim
        Both
    )
)

// Reseller ที่ยังไม่มีการขาย — ใช้เป็น chase list ของทีมขาย
// (วันที่/สินค้ากรองไม่ถึง DimReseller จึงนับ "ทั้งหมด" ได้ตรงนี้)
'Inactive Reseller Count' =
COUNTROWS ( DimReseller ) - [Active Reseller Count]

// KPI penetration ของ reseller network
'Active Reseller %' =
DIVIDE ( [Active Reseller Count], COUNTROWS ( DimReseller ) )
```

**ผลตรวจจากข้อมูลจริง (AdventureWorksDW2025):**

| หมวดสินค้า | ไม่มี CROSSFILTER | `Active Reseller Count` |
|---|---|---|
| Bikes | 701 | **587** |
| Components | 701 | **580** |
| Clothing | 701 | **475** |
| Accessories | 701 | **346** |

| ปี | Active | Inactive | Active % |
|---|---|---|---|
| 2010 | 38 | 663 | 5.4% |
| 2011 | 251 | 450 | 35.8% |
| 2012 | 401 | 300 | 57.2% |
| 2013 | 489 | 212 | 69.8% |

**ไฟล์ตัวอย่าง:** `Data Model - Reseller Sales.pbix` — *Trainer Material — ขอไฟล์จากผู้สอน*

---

### 5. ALLRELATED() และ ALLSELECTED()

#### ALLRELATED()

```dax
Total Sales All Related = 
CALCULATE(
    SUM(Sales[Amount]),
    ALLRELATED(Product)
)
```

#### ALLSELECTED()

```dax
Total Sales Selected = 
CALCULATE(
    SUM(Sales[Amount]),
    ALLSELECTED(Product[Category])
)
```

#### เปรียบเทียบ ALL(), ALLRELATED(), ALLSELECTED()

```dax
// ALL - ลบ Filter ทั้งหมด
Total Sales - ALL = 
CALCULATE(
    SUM(Sales[Amount]),
    ALL(Product)
)

// ALLRELATED - ลบ Filter จาก Related Tables เท่านั้น
Total Sales - ALLRELATED = 
CALCULATE(
    SUM(Sales[Amount]),
    ALLRELATED(Product)
)

// ALLSELECTED - คง Filter จาก Visual Level
Total Sales - ALLSELECTED = 
CALCULATE(
    SUM(Sales[Amount]),
    ALLSELECTED(Product[Category])
)
```

---

### 6. SELECTEDVALUE() - Filter Context และ Measure Selector

#### SELECTEDVALUE() - พื้นฐาน

**Syntax:**
```dax
SELECTEDVALUE(<columnName>[, <alternateResult>])
```

**คำอธิบาย:**
- คืนค่าที่ถูกเลือกไว้ในคอลัมน์จาก Filter Context หรือ Slicer
- หากมีการเลือกค่ามากกว่า 1 ค่า หรือไม่มีการเลือกค่าใดๆ จะคืนค่าตาม Argument ที่สอง

**ตัวอย่างพื้นฐาน:**

```dax
Debug - Selected Category = 
SELECTEDVALUE(
    Product[Category],
    "No Filter or Multiple Values"
)
```

#### SELECTEDVALUE() - Dynamic Title

```dax
Dynamic Title = 
VAR SelectedCategory = SELECTEDVALUE(DimProduct[ProductCategoryName], "All Categories")
RETURN
    "Sales Report - " & SelectedCategory
```

#### SELECTEDVALUE() - Conditional Logic

```dax
Conditional Measure = 
VAR SelectedPeriod = SELECTEDVALUE('Date'[MonthName], "All")
RETURN
    IF(
        SelectedPeriod = "All",
        [Total Sales],
        CALCULATE([Total Sales], 'Date'[MonthName] = SelectedPeriod)
    )
```

#### SELECTEDVALUE() - Error Handling

```dax
Safe Selected Value = 
VAR SelectedValue = SELECTEDVALUE(DimProduct[ProductName], BLANK())
RETURN
    IF(
        ISBLANK(SelectedValue),
        "Please select a single product",
        "Selected: " & SelectedValue
    )
```

#### SELECTEDVALUE() - Measure Selector Pattern

**การสร้างตาราง Measure Selector:**

```dax
// สร้างตาราง Measure Selector ด้วย DATATABLE()
Measure Selector = 
DATATABLE(
    "Measure Name", STRING,
    "Measure Sort", INTEGER,
    {
        {"Reseller Sales Revenue", 1},
        {"Reseller Sales Cost", 2}
    }
)
```

**การสร้าง Measure ที่ใช้ SELECTEDVALUE():**

```dax
Measure to Show = 
VAR ActiveMeasurename = SELECTEDVALUE('Measure Selector'[Measure Name])
VAR Result = 
    SWITCH(
        ActiveMeasurename,
        "Reseller Sales Revenue", [Reseller Sales Revenue],
        "Reseller Sales Cost", [Reseller Sales Cost]
    )
RETURN 
    Result
```

**การใช้งาน:**
1. สร้างตาราง Measure Selector ด้วย DATATABLE()
2. สร้าง Measure ที่ใช้ SELECTEDVALUE() และ SWITCH()
3. สร้าง Slicer จาก 'Measure Selector'[Measure Name]
4. แสดง Measure to Show ใน Visual

**👉 ดูตัวอย่างที่สมบูรณ์:** `Data Model Time Intelligence without Calculation Group.pbix`

> **หมายเหตุ:** ไฟล์ตัวอย่างเป็นสื่อการสอนของผู้สอน (Trainer Material) — ขอไฟล์ได้จากผู้สอนระหว่างเรียน ไม่ได้แจกจ่ายผ่าน repository นี้

---

### 7. Filter Context Debug Functions

#### HASONEVALUE()

```dax
Debug - Product Count = 
IF(
    HASONEVALUE(Product[ProductName]),
    "Single Product Selected",
    "Multiple Products Selected"
)
```

#### ISFILTERED()

```dax
Debug - Is Category Filtered = 
IF(
    ISFILTERED(Product[Category]),
    "Category is Filtered",
    "Category is Not Filtered"
)
```

#### Debug Info ที่สมบูรณ์

```dax
Debug Info = 
VAR ProductCount = DISTINCTCOUNT(Product[ProductKey])
VAR CategoryCount = DISTINCTCOUNT(Product[Category])
VAR SelectedCategory = SELECTEDVALUE(Product[Category], "All")
RETURN
    "Products: " & ProductCount & 
    " | Categories: " & CategoryCount & 
    " | Selected: " & SelectedCategory
```

---

### 8. Measures สำหรับรายงาน

#### Measures พื้นฐาน

```dax
// Total Sales
Total Sales = SUM(FactResellerSales[SalesAmount])

// Total Sales - Bikes
Total Sales - Bikes = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimProduct[Category] = "Bikes"
)

// Total Sales - North America
Total Sales - North America = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimGeography[Region] = "North America"
)

// Sales % - Bikes
Sales % - Bikes = 
DIVIDE(
    [Total Sales - Bikes],
    [Total Sales]
)

// Sales by Year
Sales by Year = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    VALUES('Date'[Year])
)

// Average Sales per Product
Average Sales per Product = 
DIVIDE(
    [Total Sales],
    DISTINCTCOUNT(DimProduct[ProductKey])
)
```

---

### 9. TREATAS() — Virtual Relationship

**TREATAS()** ทาบค่าจากตารางหนึ่งไปเป็น filter ของอีกคอลัมน์หนึ่ง โดย**ไม่ต้องมี relationship จริงในโมเดล** — เหมาะกับกรณีที่ relationship ไม่มีอยู่ (ไม่ควรสร้าง) หรือคำนวณ list ของ key เองก่อน

#### TREATAS() - Virtual Relationship ข้ามมิติ (DimDate → DimEmployee)

```dax
// นับพนักงานที่ถูกจ้าง "ในช่วงเวลาที่เลือกใน visual" (เช่น ปีจาก DimDate)
// แม้ DimEmployee[HireDate] ไม่มี relationship กับ DimDate เลย
// TREATAS ทาบค่าวันที่จาก DimDate ไปกรอง HireDate แบบ virtual
'Employees Hired' =
CALCULATE (
    COUNT ( DimEmployee[EmployeeKey] ),
    TREATAS ( VALUES ( DimDate[FullDateAlternateKey] ), DimEmployee[HireDate] )
)
```

**อธิบาย:**
- `VALUES(DimDate[FullDateAlternateKey])` = วันที่ที่อยู่ใน filter context ปัจจุบัน
- `TREATAS(..., DimEmployee[HireDate])` = เอาค่าเหล่านั้นไปกรอง HireDate ราวกับมี relationship
- จากไฟล์จริง `Data Model - Reseller Sales.pbix` — *Trainer Material — ขอไฟล์จากผู้สอน*

#### TREATAS() - ทาบ Key List ที่คำนวณเอง (New Product Sales)

```dax
// ยอดขายที่มาจาก "สินค้าใหม่" = สินค้าที่ไม่เคยขายมาก่อนช่วงเวลาที่ดู
// TREATAS รูปแบบที่สอง: คำนวณ list ของ ProductKey เองด้วย EXCEPT
// (สินค้าใน context ปัจจุบัน - สินค้าที่เคยขายก่อนหน้านี้ทั้งหมด)
// แล้วทาบกลับไปกรอง DimProduct[ProductKey] — ไม่ต้องแตะ relationship จริง
'New Product Sales' =
VAR CurrentProducts =
    VALUES ( DimProduct[ProductKey] )
VAR ProductsSoldBefore =
    CALCULATETABLE (
        VALUES ( FactResellerSales[ProductKey] ),
        FILTER (
            ALL ( DimDate ),
            DimDate[FullDateAlternateKey] < MIN ( DimDate[FullDateAlternateKey] )
        )
    )
VAR NewProducts =
    EXCEPT ( CurrentProducts, ProductsSoldBefore )
RETURN
    CALCULATE ( [Sales Amount], TREATAS ( NewProducts, DimProduct[ProductKey] ) )
```

**ผลตรวจจากข้อมูลจริง:**

| ปี | New Product Sales | อ่านค่า |
|---|---|---|
| 2010 | 489,329 | = ยอดขายทั้งหมด (ปีแรก ทุกสินค้า "ใหม่") |
| 2011 | 3,830,126 | สินค้าที่เพิ่งเริ่มขายปีนี้ |
| 2013 | 1,257 | แทบไม่มีของใหม่ — จับคู่กับ KPI portfolio ได้ |

**เมื่อไหร่ใช้ TREATAS แทน relationship จริง:**
- Budget/Target จากไฟล์ภายนอกที่ไม่ควรเพิ่ม relationship ในโมเดล
- Filter แบบคำนวณเอง (เช่น EXCEPT, TOPN, ผลของ measure)
- อยากเก็บโมเดลให้ relationship น้อยและชัด (ดีต่อ VertiPaq/การบำรุงรักษา)

---

---

## 📝 วิธีใช้งาน

1. **Copy Code** ที่ต้องการจากไฟล์นี้
2. **Paste** ลงใน Power BI Desktop
3. **ปรับแต่ง** ชื่อตารางและคอลัมน์ให้ตรงกับ Model ของคุณ
4. **ทดสอบ** และ Debug ถ้าจำเป็น

---

## ⚠️ หมายเหตุ

- ตัวอย่างทั้งหมดใช้ชื่อตารางและคอลัมน์แบบ Generic
- คุณต้องปรับแต่งให้ตรงกับ Model ของคุณ
- ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025** เป็น Data Source

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักพร้อมคำอธิบาย
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด
