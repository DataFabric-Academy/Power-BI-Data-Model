# แบบฝึกหัด - Case Studies

## 📚 เอกสารแบบฝึกหัดสำหรับการฝึกปฏิบัติ

ไฟล์นี้รวบรวมแบบฝึกหัดแบบ Step-by-Step สำหรับโมดูล Case Studies โดยใช้ AdventureWorksDW2025 เป็นตัวอย่าง

---

## 🎯 แบบฝึกหัดที่ 1: สร้าง Sales Analysis Model

### วัตถุประสงค์

สร้าง Sales Analysis Model ที่มี Time Intelligence Measures ครบถ้วน เพื่อวิเคราะห์ยอดขายแยกตาม Product, Customer, Date

### ข้อมูลที่ต้องการ

- **FactResellerSales** (Fact Table)
- **DimProduct** (Dimension Table)
- **DimDate** (Dimension Table)
- **DimCustomer** (Dimension Table)
- **DimReseller** (Dimension Table)

---

### ขั้นตอนที่ 1: เชื่อมต่อกับ AdventureWorksDW2025

**Step 1.1:** เปิด Power BI Desktop

**Step 1.2:** ไปที่ **Home** → **Get Data** → **SQL Server**

**Step 1.3:** กรอกข้อมูลการเชื่อมต่อ:
- **Server:** `your-server.database.windows.net` (หรือชื่อเซิร์ฟเวอร์ที่คุณมี)
- **Database:** `AdventureWorksDW2025`
- เลือก **Import Mode**

**Step 1.4:** เลือกตารางต่อไปนี้:
- ✅ FactResellerSales
- ✅ DimProduct
- ✅ DimDate
- ✅ DimCustomer
- ✅ DimReseller

**Step 1.5:** คลิก **Load** เพื่อ Import ข้อมูล

---

### ขั้นตอนที่ 2: ตรวจสอบ Data Model

**Step 2.1:** ไปที่ **Model View** (ไอคอนรูปแผนผังด้านซ้าย)

**Step 2.2:** ตรวจสอบ Tables ที่มี:
- ✅ FactResellerSales (Fact Table)
- ✅ DimProduct, DimDate, DimCustomer, DimReseller (Dimension Tables)

**Step 2.3:** ตรวจสอบ Columns ใน FactResellerSales:
- ✅ มี Measures: SalesAmount, OrderQuantity, UnitPrice
- ✅ มี Foreign Keys: ProductKey, OrderDateKey, CustomerKey, ResellerKey

---

### ขั้นตอนที่ 3: สร้าง Relationships

**Step 3.1:** สร้าง Relationship ระหว่าง FactResellerSales และ DimProduct:
- ลาก `ProductKey` จาก `FactResellerSales` ไปยัง `ProductKey` ใน `DimProduct`
- ตรวจสอบว่า Cardinality เป็น **Many-to-One** (Fact → Dim)
- ตรวจสอบว่า Cross-filter direction เป็น **Single**
- คลิก **OK**

**Step 3.2:** สร้าง Relationship ระหว่าง FactResellerSales และ DimDate:
- ลาก `OrderDateKey` จาก `FactResellerSales` ไปยัง `DateKey` ใน `DimDate`
- ตรวจสอบว่า Cardinality เป็น **Many-to-One**
- ตรวจสอบว่า Cross-filter direction เป็น **Single**
- คลิก **OK**

**Step 3.3:** สร้าง Relationship ระหว่าง FactResellerSales และ DimCustomer:
- ลาก `CustomerKey` จาก `FactResellerSales` ไปยัง `CustomerKey` ใน `DimCustomer`
- ตรวจสอบว่า Cardinality เป็น **Many-to-One**
- คลิก **OK**

**Step 3.4:** สร้าง Relationship ระหว่าง FactResellerSales และ DimReseller:
- ลาก `ResellerKey` จาก `FactResellerSales` ไปยัง `ResellerKey` ใน `DimReseller`
- ตรวจสอบว่า Cardinality เป็น **Many-to-One**
- คลิก **OK**

**Step 3.5:** ตรวจสอบ Relationships ทั้งหมดใน Model View ว่าเชื่อมต่อกันถูกต้อง

---

### ขั้นตอนที่ 4: Mark as Date Table

**Step 4.1:** เลือก `DimDate` Table ใน Model View

**Step 4.2:** ไปที่ **Table tools** → **Mark as Date Table**

**Step 4.3:** เลือก `Date` Column (หรือ `FullDateAlternateKey` ถ้ามี)

**Step 4.4:** คลิก **OK**

**เหตุผล:** การ Mark as Date Table จะทำให้ Power BI รองรับ Time Intelligence Functions ได้ดีขึ้น

---

### ขั้นตอนที่ 5: สร้าง Measures พื้นฐาน

**Step 5.1:** สร้าง Measure: **Total Sales**

1. เลือก `FactResellerSales` Table
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตรต่อไปนี้:

```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
```

4. กด Enter เพื่อบันทึก Measure

**Step 5.2:** สร้าง Measure: **Sales YTD** (Year-to-Date)

1. สร้าง Measure ใหม่ใน `FactResellerSales`
2. พิมพ์สูตรต่อไปนี้:

```dax
Sales YTD = 
CALCULATE(
    [Total Sales],
    DATESYTD(DimDate[Date])
)
```

3. กด Enter เพื่อบันทึก

**Step 5.3:** สร้าง Measure: **Sales QTD** (Quarter-to-Date)

```dax
Sales QTD = 
CALCULATE(
    [Total Sales],
    DATESQTD(DimDate[Date])
)
```

**Step 5.4:** สร้าง Measure: **Sales MTD** (Month-to-Date)

```dax
Sales MTD = 
CALCULATE(
    [Total Sales],
    DATESMTD(DimDate[Date])
)
```

**Step 5.5:** สร้าง Measure: **Sales LY** (Last Year)

```dax
Sales LY = 
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

**Step 5.6:** สร้าง Measure: **Sales YoY Growth** (Year-over-Year Growth)

```dax
Sales YoY Growth = 
VAR CurrentYear = [Total Sales]
VAR LastYear = [Sales LY]
RETURN
    DIVIDE(CurrentYear - LastYear, LastYear, 0)
```

**Step 5.7:** ตรวจสอบ Measures ทั้งหมดใน Fields List ว่าแสดงผลถูกต้อง

---

### ขั้นตอนที่ 6: สร้าง Visual เพื่อทดสอบ

**Step 6.1:** ไปที่ **Report View**

**Step 6.2:** สร้าง Table Visual:
1. ลาก `DimDate[CalendarYear]` ไปยัง Rows
2. ลาก `Total Sales` ไปยัง Values
3. ลาก `Sales LY` ไปยัง Values
4. ลาก `Sales YoY Growth` ไปยัง Values

**Step 6.3:** ตรวจสอบผลลัพธ์ว่าถูกต้องหรือไม่

---

### ขั้นตอนที่ 7: ทดสอบและ Validate

**Step 7.1:** ทดสอบ Measure: **Total Sales**
- ตรวจสอบว่าผลรวมตรงกับ SUM ของ SalesAmount หรือไม่

**Step 7.2:** ทดสอบ Measure: **Sales YTD**
- ตรวจสอบว่าผลรวมของปีปัจจุบัน ตรงกับการรวม Sales จากต้นปีหรือไม่

**Step 7.3:** ทดสอบ Measure: **Sales YoY Growth**
- ตรวจสอบว่าคำนวณเปอร์เซ็นต์การเติบโตได้ถูกต้องหรือไม่

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Star Schema ที่สร้างมี Fact Tables และ Dimension Tables อะไรบ้าง?**
   - **คำตอบ:** Fact Table: FactResellerSales, Dimension Tables: DimProduct, DimDate, DimCustomer, DimReseller

2. **Relationships ทั้งหมดเป็นแบบ One-to-Many หรือไม่?**
   - **คำตอบ:** ใช่ Relationships ทั้งหมดเป็นแบบ One-to-Many (Many-to-One จากมุมมองของ Fact Table)

3. **Measures ที่สร้างคำนวณได้ถูกต้องหรือไม่?**
   - **คำตอบ:** ตรวจสอบโดยเปรียบเทียบผลลัพธ์กับข้อมูลต้นฉบับ หรือทดสอบด้วยข้อมูลที่รู้ผลลัพธ์แล้ว

---

## 🎯 แบบฝึกหัดที่ 2: Product Performance Analysis

### วัตถุประสงค์

สร้าง Product Performance Analysis ที่จัดการกับ Heterogeneous Granularity และเปรียบเทียบ Sales vs Quota

### ข้อมูลที่ต้องการ

- **FactResellerSales** (Fact Table - Daily Granularity)
- **FactSalesQuota** (Fact Table - Monthly Granularity)
- **DimProduct** (Dimension Table)
- **DimDate** (Dimension Table)

---

### ขั้นตอนที่ 1: ตรวจสอบ Granularity

**Step 1.1:** ไปที่ **Data View**

**Step 1.2:** ตรวจสอบ FactResellerSales:
- ดู Columns: `OrderDateKey`, `ProductKey`, `SalesAmount`
- สังเกตว่า `OrderDateKey` เป็น Daily (แต่ละแถวเป็นแต่ละวัน)

**Step 1.3:** ตรวจสอบ FactSalesQuota:
- ดู Columns: `DateKey` (หรือ `MonthKey`), `SalesAmountQuota`
- สังเกตว่า `DateKey` เป็น Monthly (แต่ละแถวเป็นแต่ละเดือน)

**Step 1.4:** สรุปผล:
- **FactResellerSales** มี **Daily Granularity**
- **FactSalesQuota** มี **Monthly Granularity**
- **ปัญหา:** Granularity ไม่เหมือนกัน → ต้อง Aggregate Daily Sales เป็น Monthly

---

### ขั้นตอนที่ 2: ตรวจสอบ Relationships

**Step 2.1:** ไปที่ **Model View**

**Step 2.2:** ตรวจสอบ Relationship:
- ✅ `FactResellerSales[OrderDateKey]` → `DimDate[DateKey]`
- ✅ `FactSalesQuota[DateKey]` → `DimDate[DateKey]`

**Step 2.3:** ตรวจสอบ DimDate:
- ✅ มี `MonthKey` Column (หรือสร้างใหม่ถ้าไม่มี)

---

### ขั้นตอนที่ 3: สร้าง Measure เพื่อ Aggregate Daily Sales เป็น Monthly

**Step 3.1:** สร้าง Measure: **Sales (Monthly)**

1. เลือก `FactResellerSales` Table
2. สร้าง Measure ใหม่:

```dax
Sales (Monthly) = 
CALCULATE(
    [Total Sales],
    VALUES(DimDate[MonthKey])
)
```

**อธิบายสูตร:**
- `VALUES(DimDate[MonthKey])` จะให้ MonthKey ทั้งหมดที่ถูกเลือก
- `CALCULATE([Total Sales], ...)` จะคำนวณ Total Sales แต่จำกัดให้อยู่ใน MonthKey ที่ถูกเลือก
- ผลลัพธ์คือ Sales ที่ถูก Aggregate เป็น Monthly

**Step 3.2:** ทดสอบ Measure:
- สร้าง Visual ที่มี `DimDate[CalendarYear]`, `DimDate[CalendarMonth]` และ `Sales (Monthly)`
- ตรวจสอบว่าผลลัพธ์เป็น Monthly หรือไม่

---

### ขั้นตอนที่ 4: สร้าง Measure เพื่อเปรียบเทียบ Sales vs Quota

**Step 4.1:** สร้าง Measure: **Quota Performance**

```dax
Quota Performance = 
DIVIDE(
    [Sales (Monthly)],
    SUM(FactSalesQuota[SalesAmountQuota]),
    0
)
```

**อธิบายสูตร:**
- `[Sales (Monthly)]` คือ Sales ที่ Aggregate เป็น Monthly แล้ว
- `SUM(FactSalesQuota[SalesAmountQuota])` คือ Quota ที่เป็น Monthly อยู่แล้ว
- `DIVIDE(..., ..., 0)` จะคืนค่า 0 ถ้าหารด้วย 0

**Step 4.2:** สร้าง Measure: **Quota Variance**

```dax
Quota Variance = 
[Sales (Monthly)] - SUM(FactSalesQuota[SalesAmountQuota])
```

**Step 4.3:** สร้าง Measure: **Quota Variance %**

```dax
Quota Variance % = 
DIVIDE(
    [Quota Variance],
    SUM(FactSalesQuota[SalesAmountQuota]),
    0
)
```

---

### ขั้นตอนที่ 5: สร้าง Visual เพื่อแสดง Quota Performance

**Step 5.1:** สร้าง Matrix Visual:
1. ลาก `DimDate[CalendarYear]` ไปยัง Rows
2. ลาก `DimDate[CalendarMonth]` ไปยัง Rows
3. ลาก `Sales (Monthly)` ไปยัง Values
4. ลาก `SUM(FactSalesQuota[SalesAmountQuota])` ไปยัง Values
5. ลาก `Quota Performance` ไปยัง Values

**Step 5.2:** Format Visual:
- ตั้งค่า Format ของ `Quota Performance` เป็น Percentage (0.00%)

**Step 5.3:** เพิ่ม Conditional Formatting:
- เลือก `Quota Performance` Column
- ไปที่ **Conditional formatting** → **Background color**
- ตั้งค่า: ถ้า >= 100% = เขียว, ถ้า < 100% = แดง

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Granularity ของ FactResellerSales และ FactSalesQuota แตกต่างกันอย่างไร?**
   - **คำตอบ:** FactResellerSales มี Daily Granularity (แต่ละแถวเป็นแต่ละวัน) แต่ FactSalesQuota มี Monthly Granularity (แต่ละแถวเป็นแต่ละเดือน)

2. **ทำไมต้อง Aggregate Daily Sales เป็น Monthly ก่อนเปรียบเทียบ?**
   - **คำตอบ:** เพราะ Granularity ไม่เหมือนกัน ถ้าเปรียบเทียบโดยตรงจะได้ผลลัพธ์ผิดพลาด ต้อง Aggregate ให้อยู่ในระดับเดียวกันก่อน

3. **Quota Performance แสดงผลได้ถูกต้องหรือไม่?**
   - **คำตอบ:** ตรวจสอบว่าค่าที่ได้สมเหตุสมผลหรือไม่ เช่น ถ้า Sales มากกว่า Quota ควรได้ > 100%

---

## 🎯 แบบฝึกหัดที่ 3: Customer Segmentation

### วัตถุประสงค์

สร้าง Customer Segmentation เพื่อแบ่งกลุ่มลูกค้าตามยอดซื้อ และเปรียบเทียบ Performance ระหว่าง Measure vs Calculated Column

### ข้อมูลที่ต้องการ

- **FactResellerSales** (Fact Table)
- **DimCustomer** (Dimension Table)

---

### ขั้นตอนที่ 1: กำหนด Segmentation Rules

**Step 1.1:** กำหนดกฎการแบ่งกลุ่ม:
- **Platinum:** Total Sales > $10,000
- **Gold:** Total Sales $5,000 - $10,000
- **Silver:** Total Sales < $5,000

---

### ขั้นตอนที่ 2: สร้าง Measure เพื่อแบ่งกลุ่มลูกค้า (แนะนำ)

**Step 2.1:** สร้าง Measure: **Customer Segment** (ใช้ Measure)

1. เลือก `DimCustomer` Table
2. สร้าง Measure ใหม่:

```dax
Customer Segment = 
VAR TotalSales = [Total Sales]
RETURN
    SWITCH(
        TRUE(),
        TotalSales > 10000, "Platinum",
        TotalSales >= 5000, "Gold",
        "Silver"
    )
```

**อธิบายสูตร:**
- `VAR TotalSales = [Total Sales]` สร้างตัวแปรเก็บ Total Sales
- `SWITCH(TRUE(), ...)` ตรวจสอบเงื่อนไขทีละบรรทัด
- ถ้า TotalSales > 10000 → "Platinum"
- ถ้า TotalSales >= 5000 → "Gold"
- นอกนั้น → "Silver"

**Step 2.2:** ทดสอบ Measure:
- สร้าง Visual ที่มี `DimCustomer[CustomerName]` และ `Customer Segment`
- ตรวจสอบว่าแบ่งกลุ่มได้ถูกต้องหรือไม่

---

### ขั้นตอนที่ 3: สร้าง Calculated Column เพื่อเปรียบเทียบ (ไม่แนะนำ)

**Step 3.1:** สร้าง Calculated Column: **Customer Segment Column** (เพื่อเปรียบเทียบ Performance)

1. เลือก `DimCustomer` Table
2. ไปที่ **Table tools** → **New column**
3. พิมพ์สูตรต่อไปนี้:

```dax
Customer Segment Column = 
VAR TotalSales = CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    ALLEXCEPT(DimCustomer, DimCustomer[CustomerKey])
)
RETURN
    IF(
        TotalSales > 10000, "Platinum",
        IF(
            TotalSales >= 5000, "Gold",
            "Silver"
        )
    )
```

**อธิบายสูตร:**
- `ALLEXCEPT(DimCustomer, DimCustomer[CustomerKey])` ลบ Filters ทั้งหมดยกเว้น CustomerKey
- `CALCULATE(SUM(...), ...)` คำนวณ Total Sales สำหรับแต่ละ Customer

**Step 3.2:** ตรวจสอบผลลัพธ์:
- สร้าง Visual ที่มี `Customer Segment Column` เพื่อดูผลลัพธ์

---

### ขั้นตอนที่ 4: เปรียบเทียบ Performance ระหว่าง Measure vs Calculated Column

**Step 4.1:** ตรวจสอบ Model Size:
1. เปิด **DAX Studio** (External Tools)
2. ไปที่แท็บ **VertiPaq Analyzer**
3. ตรวจสอบขนาดของ `DimCustomer` Table
4. สังเกตว่าหลังจากสร้าง Calculated Column ขนาดเพิ่มขึ้นหรือไม่

**Step 4.2:** เปรียบเทียบ Query Performance:
1. ใช้ **Performance Analyzer** ใน Power BI Desktop
2. สร้าง Visual ที่ใช้ `Customer Segment` (Measure)
3. สร้าง Visual ที่ใช้ `Customer Segment Column` (Calculated Column)
4. เปรียบเทียบเวลาที่ใช้

**Step 4.3:** สรุปผล:
- **Measure:** Dynamic Calculation, ไม่ใช้ Memory เก็บค่าล่วงหน้า, Model Size เล็ก
- **Calculated Column:** Static Calculation, ใช้ Memory เก็บค่าทุกแถว, Model Size ใหญ่

---

### ขั้นตอนที่ 5: สร้าง Visual เพื่อแสดง Segmentation

**Step 5.1:** สร้าง Pie Chart:
1. ลาก `Customer Segment` ไปยัง Legend
2. ลาก `DISTINCTCOUNT(DimCustomer[CustomerKey])` ไปยัง Values

**Step 5.2:** สร้าง Table:
1. ลาก `Customer Segment` ไปยัง Rows
2. ลาก `Total Sales` ไปยัง Values
3. ลาก `DISTINCTCOUNT(DimCustomer[CustomerKey])` ไปยัง Values

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมควรใช้ Measure แทน Calculated Column?**
   - **คำตอบ:** Measure ให้ Performance ที่ดีกว่าเพราะ Dynamic Calculation ไม่ใช้ Memory เก็บค่าล่วงหน้า และ Model Size เล็กกว่า

2. **Segmentation ที่สร้างแบ่งกลุ่มลูกค้าได้ถูกต้องหรือไม่?**
   - **คำตอบ:** ตรวจสอบโดยดูว่าแต่ละลูกค้าถูกแบ่งกลุ่มตามยอดซื้อที่ถูกต้องหรือไม่

3. **Performance แตกต่างกันอย่างไรระหว่าง Measure vs Calculated Column?**
   - **คำตอบ:** Measure เร็วกว่าและใช้ Memory น้อยกว่า Calculated Column เพราะคำนวณเฉพาะเมื่อใช้งานจริง

---

## 🎯 แบบฝึกหัดที่ 4: Parent-Child Hierarchy

### วัตถุประสงค์

สร้าง Parent-Child Hierarchy จาก Employee Table เพื่อดูโครงสร้างองค์กรและวิเคราะห์ Sales by Organization Level

### ข้อมูลที่ต้องการ

- **DimEmployee** (Dimension Table)
- **FactResellerSales** (Fact Table - ถ้ามี EmployeeKey)

---

### ขั้นตอนที่ 1: ตรวจสอบ DimEmployee Table

**Step 1.1:** ไปที่ **Data View**

**Step 1.2:** ตรวจสอบ Columns ใน DimEmployee:
- ✅ `EmployeeKey` (Primary Key)
- ✅ `ManagerID` (Parent Key - ชี้ไปยัง EmployeeKey ของ Manager)
- ✅ `FirstName`, `LastName`, `Title`

**Step 1.3:** ตรวจสอบข้อมูล:
- สังเกตว่า `ManagerID` ของ CEO เป็น NULL (ไม่มี Manager)

---

### ขั้นตอนที่ 2: สร้าง Calculated Columns ด้วย PATH Functions

**Step 2.1:** สร้าง Calculated Column: **HierarchyPath**

1. เลือก `DimEmployee` Table
2. ไปที่ **Table tools** → **New column**
3. พิมพ์สูตรต่อไปนี้:

```dax
HierarchyPath = 
PATH(DimEmployee[EmployeeKey], DimEmployee[ManagerID])
```

**อธิบายสูตร:**
- `PATH(EmployeeKey, ManagerID)` สร้างเส้นทางจาก Employee ไปยัง CEO
- ผลลัพธ์: "1|2|3|4" (แยกด้วย Pipe |) โดยเริ่มจาก CEO

**Step 2.2:** สร้าง Calculated Column: **Level 1 (CEO)**

```dax
Level 1 (CEO) = 
PATHITEM([HierarchyPath], 1, INTEGER)
```

**อธิบายสูตร:**
- `PATHITEM(Path, 1, INTEGER)` ดึงค่าในตำแหน่งที่ 1 จาก Path (CEO)

**Step 2.3:** สร้าง Calculated Column: **Level 2 (VP)**

```dax
Level 2 (VP) = 
PATHITEM([HierarchyPath], 2, INTEGER)
```

**Step 2.4:** สร้าง Calculated Column: **Level 3 (Manager)**

```dax
Level 3 (Manager) = 
PATHITEM([HierarchyPath], 3, INTEGER)
```

**Step 2.5:** สร้าง Calculated Column: **Level 4 (Employee)**

```dax
Level 4 (Employee) = 
PATHITEM([HierarchyPath], 4, INTEGER)
```

**Step 2.6:** สร้าง Calculated Column: **Path Length**

```dax
Path Length = 
PATHLENGTH([HierarchyPath])
```

**อธิบายสูตร:**
- `PATHLENGTH(Path)` หาความยาวของ Path (จำนวน Levels)

---

### ขั้นตอนที่ 3: สร้าง Hierarchy ใน Power BI Desktop

**Step 3.1:** ไปที่ **Model View**

**Step 3.2:** สร้าง Hierarchy:
1. Right-click ที่ `DimEmployee` Table
2. เลือก **New hierarchy**
3. ตั้งชื่อเป็น **Organization Hierarchy**

**Step 3.3:** เพิ่ม Levels:
1. ลาก `Level 1 (CEO)` ไปยัง Hierarchy
2. ลาก `Level 2 (VP)` ไปยัง Hierarchy
3. ลาก `Level 3 (Manager)` ไปยัง Hierarchy
4. ลาก `Level 4 (Employee)` ไปยัง Hierarchy

**Step 3.4:** ตรวจสอบ Hierarchy:
- ควรเห็น Hierarchy แบบ: Level 1 → Level 2 → Level 3 → Level 4

---

### ขั้นตอนที่ 4: สร้าง Visual เพื่อแสดง Organization Structure

**Step 4.1:** สร้าง Matrix Visual:
1. ลาก `Organization Hierarchy` ไปยัง Rows
2. ลาก `FirstName` และ `LastName` ไปยัง Rows
3. ลาก `Title` ไปยัง Rows

**Step 4.2:** Expand Hierarchy:
- คลิกที่ลูกศรเพื่อ Expand แต่ละ Level
- ตรวจสอบว่าโครงสร้างถูกต้องหรือไม่

---

### ขั้นตอนที่ 5: สร้าง Measure เพื่อวิเคราะห์ Sales by Organization

**Step 5.1:** ตรวจสอบว่า FactResellerSales มี EmployeeKey หรือไม่:
- ถ้ามี → ใช้ EmployeeKey
- ถ้าไม่มี → สร้างตัวอย่าง Measure

**Step 5.2:** สร้าง Measure: **Sales by Organization**

```dax
Sales by Organization = 
IF(
    HASONEVALUE(DimEmployee[EmployeeKey]),
    CALCULATE(
        [Total Sales],
        USERELATIONSHIP(FactResellerSales[EmployeeKey], DimEmployee[EmployeeKey])
    ),
    BLANK()
)
```

**อธิบายสูตร:**
- `HASONEVALUE(...)` ตรวจสอบว่ามี EmployeeKey เพียงตัวเดียวหรือไม่
- `USERELATIONSHIP(...)` ใช้ Relationship ที่ระบุ

**Step 5.3:** สร้าง Visual:
1. ลาก `Organization Hierarchy` ไปยัง Rows
2. ลาก `Sales by Organization` ไปยัง Values

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **PATH Functions ทำงานอย่างไร?**
   - **คำตอบ:** PATH สร้างเส้นทางจาก Employee ไปยัง CEO, PATHITEM ดึงค่าในตำแหน่งที่กำหนด, PATHLENGTH หาความยาวของ Path

2. **Hierarchy ที่สร้างมีโครงสร้างอย่างไร?**
   - **คำตอบ:** Level 1 (CEO) → Level 2 (VP) → Level 3 (Manager) → Level 4 (Employee)

3. **Sales by Organization Level แสดงผลได้ถูกต้องหรือไม่?**
   - **คำตอบ:** ตรวจสอบว่าผลลัพธ์ถูกต้องและแสดงตาม Organization Level หรือไม่

---

## 📝 สรุป

### ✅ สิ่งที่ควรจำ

1. **วิเคราะห์ความต้องการก่อนเริ่ม** - เข้าใจให้ชัดเจนว่าต้องการดูอะไรบ้าง
2. **ตรวจสอบ Data Model** - ดู Fact Tables, Dimension Tables, Relationships ว่าครบหรือไม่
3. **ใช้ Best Practices** - Naming, Relationships, Measures, Performance Optimization
4. **Optimize Performance** - ลด Cardinality, เรียงข้อมูล, ใช้ Measures แทน Calculated Columns
5. **Test และ Validate** - ทดสอบทุก Measure และ Validate ผลลัพธ์

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) - Code Examples เพิ่มเติม
