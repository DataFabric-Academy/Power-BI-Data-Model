# 11 - Case Studies

## 📚 เนื้อหาหลักสูตร

โมดูลนี้เป็นกรณีศึกษาจากโครงการจริง โดยใช้ฐานข้อมูล **AdventureWorksDW2025** เป็นตัวอย่าง เพื่อให้นักเรียนได้เรียนรู้วิธีการนำความรู้ทั้งหมดที่เรียนมาไปประยุกต์ใช้ในสถานการณ์จริง พร้อมทั้งเรียนรู้วิธีแก้ปัญหาและการทำ Best Practices

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025** จาก Microsoft
> 
> **แหล่งข้อมูลอ้างอิง:**
> - [Microsoft SQL Server Samples - AdventureWorks](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)
> - [SQLBI.com - SQLBI Methodology at Work](https://www.sqlbi.com/blog/marco/2008/10/02/sqlbi-methodology-at-work/)
> - [SQLBI.com - Data Import Best Practices in Power BI](https://www.sqlbi.com/articles/data-import-best-practices-in-power-bi/)
> - [SQLBI.com - The Many-to-Many Revolution 2.0](https://www.sqlbi.com/wp-content/uploads/The_Many-to-Many_Revolution_2.0.pdf)

---

## 📋 หัวข้อการเรียนรู้

### 1. กรณีศึกษาที่ 1: Sales Analysis Report ⭐

กรณีศึกษานี้เป็นกรณีศึกษาพื้นฐานที่สุดสำหรับการวิเคราะห์ยอดขาย ซึ่งเป็น Scenario ที่พบได้บ่อยที่สุดในงานจริง

#### 1.1 สถานการณ์

บริษัท AdventureWorks ซึ่งเป็นบริษัทจำหน่ายจักรยานและอุปกรณ์จักรยาน ต้องการสร้าง Report เพื่อวิเคราะห์ยอดขาย และต้องการดูยอดขายแยกตาม Product, Customer, Date และต้องการดู Time Intelligence เช่น Year-to-Date (YTD), Quarter-to-Date (QTD), Month-to-Date (MTD), และ Last Year (LY)

**ข้อมูลที่มีใน AdventureWorksDW2025:**
- **FactResellerSales** (Fact Table) - ข้อมูลการขายผ่าน Reseller มี Measures เช่น SalesAmount, OrderQuantity และ Foreign Keys ต่างๆ
- **DimProduct** (Dimension Table) - ข้อมูลสินค้า เช่น ProductName, ProductCategoryName, ProductSubcategoryName
- **DimCustomer** (Dimension Table) - ข้อมูลลูกค้า เช่น CustomerName, GeographyKey
- **DimDate** (Dimension Table) - ข้อมูลวันที่ เช่น CalendarYear, CalendarQuarter, CalendarMonth
- **DimReseller** (Dimension Table) - ข้อมูล Reseller เช่น ResellerName, BusinessType

#### 1.2 การวิเคราะห์และออกแบบ

**ขั้นตอนที่ 1: วิเคราะห์ความต้องการ**

ก่อนเริ่มสร้าง Report เราต้องเข้าใจความต้องการให้ชัดเจนว่าต้องการดูอะไรบ้าง:

1. **ยอดขายรวม (Total Sales)** - ต้องการดูยอดขายรวมทั้งหมด
2. **ยอดขายแยกตาม Product Category** - ต้องการดูว่าสินค้าประเภทไหนขายดีที่สุด
3. **ยอดขายแยกตาม Customer** - ต้องการดูว่าลูกค้าใครซื้อมากที่สุด
4. **ยอดขายแยกตาม Date** - ต้องการดูยอดขายแยกตาม Year, Quarter, Month
5. **Time Intelligence** - ต้องการเปรียบเทียบยอดขายกับช่วงเวลาก่อนหน้า เช่น YTD, QTD, MTD, LY

**ขั้นตอนที่ 2: ตรวจสอบ Data Model**

ก่อนเริ่มสร้าง Report เราต้องตรวจสอบว่า Data Model มีอะไรบ้าง:

1. **Fact Table** - มี FactResellerSales ซึ่งมี Measures และ Foreign Keys
2. **Dimension Tables** - มี DimProduct, DimCustomer, DimDate, DimReseller ซึ่งมี Attributes ที่ต้องการ
3. **Relationships** - ต้องตรวจสอบว่า Relationships ถูกต้องหรือไม่ เช่น FactResellerSales[ProductKey] → DimProduct[ProductKey]
4. **Measures** - ตรวจสอบว่ามี Measures พื้นฐานหรือไม่ ถ้าไม่มีต้องสร้างเอง

**ขั้นตอนที่ 3: สร้าง Measures ที่จำเป็น**

เมื่อเราตรวจสอบ Data Model แล้ว เราต้องสร้าง Measures ที่จำเป็นดังนี้:

```dax
Total Sales = SUM(FactResellerSales[SalesAmount])

Sales YTD = 
CALCULATE(
    [Total Sales],
    DATESYTD(DimDate[Date])
)

Sales QTD = 
CALCULATE(
    [Total Sales],
    DATESQTD(DimDate[Date])
)

Sales MTD = 
CALCULATE(
    [Total Sales],
    DATESMTD(DimDate[Date])
)

Sales LY = 
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR(DimDate[Date])
)

Sales YoY Growth = 
VAR CurrentYear = [Total Sales]
VAR LastYear = [Sales LY]
RETURN
    DIVIDE(CurrentYear - LastYear, LastYear, 0)
```

#### 1.3 การแก้ปัญหา

ในระหว่างการสร้าง Report เราอาจพบปัญหาต่างๆ ดังนี้:

**ปัญหา: Relationships ไม่ครบ**

ถ้าเราพบว่า Relationships ไม่ครบ เช่น FactResellerSales ไม่มี Relationship กับ DimProduct หรือ DimCustomer เราต้องสร้าง Relationships ให้ครบก่อน:

- `FactResellerSales[ProductKey]` → `DimProduct[ProductKey]` (One-to-Many)
- `FactResellerSales[CustomerKey]` → `DimCustomer[CustomerKey]` (One-to-Many)
- `FactResellerSales[OrderDateKey]` → `DimDate[DateKey]` (One-to-Many)
- `FactResellerSales[ResellerKey]` → `DimReseller[ResellerKey]` (One-to-Many)

**ปัญหา: Measures ไม่มี**

ถ้าเราพบว่าไม่มี Measures เราต้องสร้าง Measures ตามที่วิเคราะห์ไว้ข้างต้น

**ปัญหา: Performance ช้า**

ถ้าเราพบว่า Report โหลดช้าหรือ Query ใช้เวลานาน เราต้องตรวจสอบ Performance ด้วย VertiPaq Analyzer:

1. ตรวจสอบ Cardinality ของแต่ละ Column ด้วย VertiPaq Analyzer
2. เรียงข้อมูลตาม Foreign Keys ก่อน Import
3. ลด Cardinality ใน Fact Table โดยใช้ Dimension Tables แทนการเก็บ Attributes ใน Fact Table

#### 1.4 Lessons Learned

จากกรณีศึกษานี้ เราได้เรียนรู้สิ่งสำคัญหลายอย่าง:

1. **การวิเคราะห์ความต้องการที่ชัดเจน** - เราต้องเข้าใจความต้องการให้ชัดเจนก่อนเริ่มงาน เพื่อไม่ให้เสียเวลาแก้ไขในภายหลัง
2. **การตรวจสอบ Data Model ก่อนเริ่มงาน** - เราต้องตรวจสอบว่ามี Fact Tables, Dimension Tables, และ Relationships ครบหรือไม่
3. **การสร้าง Relationships ที่ถูกต้อง** - Relationships เป็นหัวใจสำคัญของ Semantic Model เราต้องสร้างให้ถูกต้องและครบถ้วน
4. **การใช้ Time Intelligence Functions** - Time Intelligence Functions เช่น DATESYTD, DATESQTD, DATESMTD, SAMEPERIODLASTYEAR ช่วยให้เราสามารถเปรียบเทียบข้อมูลตามช่วงเวลาได้ง่าย
5. **การ Optimize Performance** - เราต้อง Optimize Performance ด้วย VertiPaq Analyzer เพื่อให้ Report ทำงานได้เร็วขึ้น

---

### 2. กรณีศึกษาที่ 2: Product Performance Analysis

กรณีศึกษานี้เป็นกรณีศึกษาที่ซับซ้อนกว่ากรณีแรก เพราะต้องจัดการกับ Heterogeneous Granularity และการเปรียบเทียบ Measures จาก Fact Tables ต่างกัน

#### 2.1 สถานการณ์

บริษัท AdventureWorks ต้องการวิเคราะห์ Performance ของสินค้า และต้องการดูยอดขายแยกตาม Product Category และ Subcategory รวมถึงต้องการเปรียบเทียบ Sales กับ Quota เพื่อดูว่าสินค้าชนิดไหนบรรลุเป้าหมายหรือไม่

**ข้อมูลที่มีใน AdventureWorksDW2025:**
- **FactResellerSales** (Fact Table) - ข้อมูลการขายผ่าน Reseller มี Daily Granularity (ขายแต่ละวัน)
- **FactSalesQuota** (Fact Table) - ข้อมูลโควต้าขาย มี Monthly Granularity (โควต้าต่อเดือน)
- **DimProduct** (Dimension Table) - ข้อมูลสินค้า เช่น ProductCategoryName, ProductSubcategoryName
- **DimDate** (Dimension Table) - ข้อมูลวันที่ เช่น CalendarYear, CalendarQuarter, CalendarMonth, MonthKey

#### 2.2 การวิเคราะห์และออกแบบ

**ขั้นตอนที่ 1: วิเคราะห์ความต้องการ**

1. **ยอดขายสินค้าแยกตาม Category และ Subcategory** - ต้องการดูว่าสินค้าประเภทไหนขายดีที่สุด
2. **เปรียบเทียบ Sales vs Quota** - ต้องการดูว่ายอดขายบรรลุเป้าหมายหรือไม่
3. **ดู Top Products** - ต้องการดูว่าสินค้าไหนขายดีที่สุด

**ขั้นตอนที่ 2: ตรวจสอบ Relationships**

เราต้องตรวจสอบว่า Relationships ถูกต้องหรือไม่:

- `FactResellerSales[ProductKey]` → `DimProduct[ProductKey]` (One-to-Many)
- `FactResellerSales[OrderDateKey]` → `DimDate[DateKey]` (One-to-Many)
- `FactSalesQuota[DateKey]` → `DimDate[DateKey]` (One-to-Many)

**ปัญหา: Heterogeneous Granularity**

ปัญหาสำคัญที่พบคือ FactResellerSales มี Daily Granularity (ขายแต่ละวัน) แต่ FactSalesQuota มี Monthly Granularity (โควต้าต่อเดือน) ซึ่งทำให้เราไม่สามารถเปรียบเทียบได้โดยตรง

**วิธีแก้ไข:**

เราต้อง Aggregate Daily Sales เป็น Monthly ก่อน เพื่อให้ Granularity เหมือนกัน:

```dax
Sales (Monthly) = 
CALCULATE(
    [Total Sales],
    VALUES(DimDate[MonthKey])
)

Quota Performance = 
DIVIDE(
    [Sales (Monthly)],
    SUM(FactSalesQuota[SalesAmountQuota]),
    0
)
```

#### 2.3 การแก้ปัญหา

**ปัญหา: Heterogeneous Granularity**

เราแก้ไขโดยการ Aggregate Daily Sales เป็น Monthly โดยใช้ VALUES(DimDate[MonthKey]) เพื่อให้ Granularity เหมือนกัน

**ปัญหา: ต้องใช้ Multiple Date Relationships**

ถ้าเรามี Multiple Date Relationships เช่น OrderDateKey, DueDateKey, ShipDateKey เราต้องใช้ USERELATIONSHIP() เพื่อเลือก Relationship ที่ต้องการ:

```dax
Sales by Order Date = 
CALCULATE(
    [Total Sales],
    USERELATIONSHIP(FactResellerSales[OrderDateKey], DimDate[DateKey])
)

Sales by Due Date = 
CALCULATE(
    [Total Sales],
    USERELATIONSHIP(FactResellerSales[DueDateKey], DimDate[DateKey])
)
```

#### 2.4 Lessons Learned

จากกรณีศึกษานี้ เราได้เรียนรู้สิ่งสำคัญหลายอย่าง:

1. **การจัดการ Heterogeneous Granularity** - เราต้องเข้าใจว่า Fact Tables ต่างกันอาจมี Granularity ต่างกัน และต้อง Aggregate ให้อยู่ในระดับเดียวกันก่อน
2. **การ Aggregate ข้อมูลให้อยู่ในระดับเดียวกัน** - เราใช้ VALUES() หรือ AGGREGATE Functions เพื่อให้ Granularity เหมือนกัน
3. **การใช้ Multiple Date Relationships** - เราต้องใช้ USERELATIONSHIP() เพื่อเลือก Relationship ที่ต้องการ
4. **การเปรียบเทียบ Measures จาก Fact Tables ต่างกัน** - เราต้องแน่ใจว่า Measures ทั้งสองมี Granularity เหมือนกันก่อนเปรียบเทียบ

---

### 3. กรณีศึกษาที่ 3: Customer Segmentation

กรณีศึกษานี้แสดงวิธีการสร้าง Segmentation ด้วย DAX และการใช้ Measures แทน Calculated Columns เพื่อเพิ่ม Performance

#### 3.1 สถานการณ์

บริษัท AdventureWorks ต้องการแบ่งกลุ่มลูกค้าตามยอดซื้อ (Customer Segmentation) เพื่อทำการตลาดแบบเฉพาะเจาะจง (Targeted Marketing) โดยแบ่งเป็น 3 ระดับ คือ Platinum (ยอดซื้อมาก), Gold (ยอดซื้อปานกลาง), และ Silver (ยอดซื้อน้อย)

**ข้อมูลที่มีใน AdventureWorksDW2025:**
- **FactResellerSales** (Fact Table) - ข้อมูลการขายผ่าน Reseller มี SalesAmount
- **DimCustomer** (Dimension Table) - ข้อมูลลูกค้า เช่น CustomerKey, CustomerName

#### 3.2 การวิเคราะห์และออกแบบ

**ขั้นตอนที่ 1: กำหนด Segmentation Rules**

เราต้องกำหนดกฎการแบ่งกลุ่มลูกค้าดังนี้:

- **Platinum**: Total Sales > $10,000 - ลูกค้าที่ซื้อมากที่สุด
- **Gold**: Total Sales $5,000 - $10,000 - ลูกค้าที่ซื้อปานกลาง
- **Silver**: Total Sales < $5,000 - ลูกค้าที่ซื้อน้อย

**ขั้นตอนที่ 2: เลือกวิธีสร้าง Segmentation**

เรามี 2 วิธีในการสร้าง Segmentation:

**วิธีที่ 1: Calculated Column (ไม่แนะนำ)**

เราสามารถสร้าง Calculated Column ใน DimCustomer เพื่อแบ่งกลุ่มลูกค้า:

```dax
Customer Segment = 
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

**ข้อเสียของวิธีนี้:**
- Calculated Column จะคำนวณทุกแถวทันทีที่ Import ข้อมูล
- จะทำให้ Model มีขนาดใหญ่ขึ้น
- ถ้ามีข้อมูลมาก จะใช้ Memory มากและ Import ช้า

**วิธีที่ 2: Measure (แนะนำ)**

เราสามารถสร้าง Measure เพื่อแบ่งกลุ่มลูกค้า:

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

**ข้อดีของวิธีนี้:**
- Measure จะคำนวณเฉพาะเมื่อมีการใช้งานจริง (Dynamic Calculation)
- ไม่ต้องใช้ Memory เก็บค่าล่วงหน้า
- Model มีขนาดเล็กลงและ Import เร็วกว่า

#### 3.3 การแก้ปัญหา

**ปัญหา: ใช้ Calculated Column ทำให้ Model ใหญ่**

ถ้าเราใช้ Calculated Column เราจะพบว่า Model มีขนาดใหญ่ขึ้นมาก และ Import ช้า

**วิธีแก้ไข:**

เราควรใช้ Measure แทน Calculated Column เพื่อลดขนาด Model และเพิ่ม Performance:

- ลดขนาด Model เพราะไม่ต้องเก็บค่าล่วงหน้า
- Dynamic Calculation เพราะคำนวณเฉพาะเมื่อใช้งานจริง
- เพิ่ม Performance เพราะใช้ Memory น้อยกว่า

#### 3.4 Lessons Learned

จากกรณีศึกษานี้ เราได้เรียนรู้สิ่งสำคัญหลายอย่าง:

1. **การสร้าง Segmentation ด้วย DAX** - เราสามารถใช้ SWITCH() หรือ IF() เพื่อแบ่งกลุ่มข้อมูลตามเงื่อนไข
2. **การใช้ Measures แทน Calculated Columns** - Measures ให้ Performance ที่ดีกว่า Calculated Columns เพราะคำนวณแบบ Dynamic
3. **การใช้ SWITCH() สำหรับ Multiple Conditions** - SWITCH() ใช้ได้ดีกว่าการซ้อน IF() หลายชั้น เพราะอ่านง่ายกว่า

---

### 4. กรณีศึกษาที่ 4: Parent-Child Hierarchy (Organization Structure)

กรณีศึกษานี้แสดงวิธีการสร้าง Parent-Child Hierarchy จาก Employee Table ซึ่งมี ManagerID (Parent-Child Relationship)

#### 4.1 สถานการณ์

บริษัท AdventureWorks ต้องการสร้าง Organization Hierarchy จาก Employee Table เพื่อดูโครงสร้างองค์กร และต้องการดู Sales by Organization Level เช่น Sales by CEO, VP, Manager, Employee

**ข้อมูลที่มีใน AdventureWorksDW2025:**
- **DimEmployee** (Dimension Table) - ข้อมูลพนักงาน เช่น EmployeeKey, ManagerID, FirstName, LastName, Title

**โครงสร้างข้อมูล:**
- แต่ละ Employee มี ManagerID ที่ชี้ไปยัง EmployeeKey ของ Manager
- ถ้า ManagerID เป็น NULL แสดงว่าเป็น CEO (ไม่มี Manager)

#### 4.2 การวิเคราะห์และออกแบบ

**ขั้นตอนที่ 1: สร้าง Path**

เราต้องสร้าง Path ที่แสดงเส้นทางจาก Employee ไปยัง CEO:

```dax
HierarchyPath = 
PATH(DimEmployee[EmployeeKey], DimEmployee[ManagerID])
```

**ตัวอย่าง Path:**
- Employee A → Manager B → VP C → CEO D
- Path = "D|C|B|A" (แยกด้วย Pipe |)

**ขั้นตอนที่ 2: สร้าง Levels**

เราต้องสร้าง Levels เพื่อแยกระดับใน Hierarchy:

```dax
Level 1 (CEO) = 
PATHITEM([HierarchyPath], 1, INTEGER)

Level 2 (VP) = 
PATHITEM([HierarchyPath], 2, INTEGER)

Level 3 (Manager) = 
PATHITEM([HierarchyPath], 3, INTEGER)

Level 4 (Employee) = 
PATHITEM([HierarchyPath], 4, INTEGER)
```

**ขั้นตอนที่ 3: สร้าง Hierarchy**

เราต้องสร้าง Hierarchy ใน Power BI Desktop:

1. ไปที่ DimEmployee Table
2. Right-click และเลือก "New Hierarchy"
3. เพิ่ม Levels: Level 1, Level 2, Level 3, Level 4
4. ตั้งชื่อ Hierarchy เป็น "Organization Hierarchy"

**โครงสร้าง Hierarchy:**
```
Organization Hierarchy:
├── Level 1 (CEO)
│   ├── Level 2 (VP)
│   │   ├── Level 3 (Manager)
│   │   │   └── Level 4 (Employee)
```

#### 4.3 การใช้งาน Hierarchy

หลังจากสร้าง Hierarchy แล้ว เราสามารถใช้ Hierarchy เพื่อดู Sales by Organization Level:

```dax
Sales by Organization = 
CALCULATE(
    [Total Sales],
    USERELATIONSHIP(FactResellerSales[EmployeeKey], DimEmployee[EmployeeKey])
)
```

#### 4.4 Lessons Learned

จากกรณีศึกษานี้ เราได้เรียนรู้สิ่งสำคัญหลายอย่าง:

1. **การใช้ PATH() Functions** - PATH(), PATHITEM(), PATHLENGTH() ช่วยให้เราสามารถสร้าง Parent-Child Hierarchy ได้
2. **การสร้าง Parent-Child Hierarchy** - Parent-Child Hierarchy ช่วยให้เราสามารถสร้าง Hierarchy จากข้อมูลที่ไม่ได้อยู่ในรูปแบบ Star Schema ได้
3. **การจัดการ Organization Structure** - เราสามารถใช้ Parent-Child Hierarchy เพื่อดูโครงสร้างองค์กรและวิเคราะห์ข้อมูลตาม Organization Level

---

### 5. กรณีศึกษาที่ 5: Many-to-Many Relationship (Basket Analysis)

กรณีศึกษานี้แสดงวิธีการจัดการ Many-to-Many Relationship และการทำ Basket Analysis เพื่อดูว่าสินค้าชนิดไหนที่ลูกค้ามักซื้อร่วมกัน

**อ้างอิง:** [SQLBI.com - The Many-to-Many Revolution 2.0](https://www.sqlbi.com/wp-content/uploads/The_Many-to-Many_Revolution_2.0.pdf)

#### 5.1 สถานการณ์

บริษัท AdventureWorks ต้องการวิเคราะห์ตะกร้าสินค้า (Basket Analysis) เพื่อดูว่าสินค้าชนิดไหนที่ลูกค้ามักซื้อร่วมกัน ซึ่งจะช่วยในการทำ Cross-Selling และ Product Recommendations

**ข้อมูลที่มีใน AdventureWorksDW2025:**
- **FactResellerSales** (Fact Table) - ข้อมูลการขายผ่าน Reseller
- **DimProduct** (Dimension Table) - ข้อมูลสินค้า
- **DimCustomer** (Dimension Table) - ข้อมูลลูกค้า

**ความสัมพันธ์:**
- หนึ่ง Order (Transaction) มีหลาย Products (Many-to-Many)
- หนึ่ง Product อยู่ในหลาย Orders (Many-to-Many)

#### 5.2 การวิเคราะห์และออกแบบ

**ขั้นตอนที่ 1: วิเคราะห์ความต้องการ**

1. ต้องการดูว่าสินค้า A และสินค้า B ถูกซื้อพร้อมกันบ่อยแค่ไหน
2. ต้องการดู Top Product Combinations
3. ต้องการดูว่าสินค้าไหนที่ลูกค้ามักซื้อเพิ่มเมื่อซื้อสินค้าชนิดหนึ่ง

**ขั้นตอนที่ 2: สร้าง Bridge Table Pattern**

เพื่อจัดการ Many-to-Many Relationship เราต้องสร้าง Bridge Table:

**Bridge Table: FactResellerSales_Basket**
- OrderKey (Foreign Key to FactResellerSales)
- ProductKey (Foreign Key to DimProduct)
- SalesAmount
- OrderQuantity

**Relationships:**
- `FactResellerSales[OrderKey]` → `FactResellerSales_Basket[OrderKey]` (One-to-Many)
- `DimProduct[ProductKey]` → `FactResellerSales_Basket[ProductKey]` (One-to-Many)

#### 5.3 การสร้าง Measures

**Measure: Products in Same Basket**

```dax
Products in Same Basket = 
VAR SelectedProducts = VALUES(DimProduct[ProductKey])
VAR OrdersWithSelectedProducts = 
    CALCULATETABLE(
        VALUES(FactResellerSales_Basket[OrderKey]),
        SelectedProducts
    )
RETURN
    CALCULATE(
        DISTINCTCOUNT(DimProduct[ProductKey]),
        OrdersWithSelectedProducts,
        ALL(DimProduct[ProductKey])
    )
```

#### 5.4 Lessons Learned

จากกรณีศึกษานี้ เราได้เรียนรู้สิ่งสำคัญหลายอย่าง:

1. **การจัดการ Many-to-Many Relationship** - เราต้องสร้าง Bridge Table เพื่อจัดการ Many-to-Many Relationship
2. **Basket Analysis** - เราสามารถใช้ Many-to-Many Relationship เพื่อทำ Basket Analysis
3. **การสร้าง Product Recommendations** - Basket Analysis ช่วยให้เราสามารถแนะนำสินค้าให้ลูกค้าได้

---

### 6. Best Practices จากกรณีศึกษา

จากกรณีศึกษาทั้งหมด เราได้เรียนรู้ Best Practices ที่สำคัญหลายอย่าง:

#### 6.1 วิเคราะห์ความต้องการก่อนเริ่ม

**หลักการ:**
- เข้าใจความต้องการให้ชัดเจนว่าต้องการดูอะไรบ้าง
- ระบุ Measures และ Dimensions ที่ต้องการ
- ตรวจสอบ Data Model ที่มีว่ามี Tables และ Relationships ครบหรือไม่

**ตัวอย่าง:**
- ถ้าต้องการดู Sales Analysis ต้องระบุว่าจะดู Sales แยกตามอะไรบ้าง เช่น Product, Customer, Date
- ถ้าต้องการดู Time Intelligence ต้องระบุว่าจะดู YTD, QTD, MTD, หรือ LY

#### 6.2 ตรวจสอบ Data Model

**หลักการ:**
- ตรวจสอบ Fact Tables และ Dimension Tables ว่ามีครบหรือไม่
- ตรวจสอบ Relationships ว่าถูกต้องและครบถ้วนหรือไม่
- ตรวจสอบ Cardinality ว่ามี High Cardinality ที่ควรแก้ไขหรือไม่

**ตัวอย่าง:**
- ตรวจสอบว่า FactResellerSales มี Relationships กับ DimProduct, DimCustomer, DimDate หรือไม่
- ตรวจสอบว่า Relationships เป็น One-to-Many หรือไม่
- ตรวจสอบ Cardinality ด้วย VertiPaq Analyzer

#### 6.3 Optimize Performance

**หลักการ:**
- ใช้ VertiPaq Analyzer ตรวจสอบ Cardinality ของแต่ละ Column
- เรียงข้อมูลก่อน Import ตาม Foreign Keys
- ใช้ Measures แทน Calculated Columns

**ตัวอย่าง:**
- ถ้าพบว่า ProductName ใน Fact Table มี Cardinality สูง ควรแยกไปไว้ใน DimProduct
- เรียง FactResellerSales ตาม ProductKey ก่อน Import
- ใช้ Measure แทน Calculated Column สำหรับ Segmentation

#### 6.4 Test และ Validate

**หลักการ:**
- ทดสอบ Measures ทุกตัวว่าคำนวณได้ถูกต้องหรือไม่
- Validate ผลลัพธ์ว่าตรงกับความต้องการหรือไม่
- ตรวจสอบ Performance ว่า Report โหลดเร็วหรือไม่

**ตัวอย่าง:**
- ทดสอบว่า Total Sales ตรงกับผลรวมของ SalesAmount หรือไม่
- Validate ว่า Sales YTD ตรงกับการรวม Sales จากต้นปีหรือไม่
- ตรวจสอบ Performance ด้วย Performance Analyzer

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ วิเคราะห์ความต้องการและออกแบบ Data Model ได้
- ✅ นำความรู้ทั้งหมดไปประยุกต์ใช้ในโครงการจริงได้
- ✅ แก้ปัญหาในสถานการณ์ต่างๆ ได้ เช่น Heterogeneous Granularity, Many-to-Many Relationship
- ✅ ใช้ Best Practices ในการสร้าง Semantic Model ได้
- ✅ เรียนรู้จากกรณีศึกษาและปรับปรุงการทำงานได้

---

## 📝 สรุป Case Studies

### 🎯 Key Learnings

1. **Sales Analysis:**
   - การสร้าง Time Intelligence Measures (YTD, QTD, MTD, LY)
   - การใช้ Relationships เพื่อเชื่อม Fact Tables และ Dimension Tables
   - Performance Optimization ด้วย VertiPaq Analyzer

2. **Product Performance:**
   - การจัดการ Heterogeneous Granularity (Daily vs Monthly)
   - การ Aggregate ข้อมูลให้อยู่ในระดับเดียวกัน
   - การใช้ Multiple Date Relationships ด้วย USERELATIONSHIP()

3. **Customer Segmentation:**
   - การสร้าง Segmentation ด้วย DAX (SWITCH(), IF())
   - การใช้ Measures แทน Calculated Columns เพื่อเพิ่ม Performance
   - การใช้ Variables ใน DAX เพื่อเพิ่มความชัดเจน

4. **Parent-Child Hierarchy:**
   - การใช้ PATH() Functions (PATH(), PATHITEM(), PATHLENGTH())
   - การสร้าง Organization Hierarchy จาก Parent-Child Data
   - การวิเคราะห์ข้อมูลตาม Organization Level

5. **Many-to-Many Relationship (Basket Analysis):**
   - การจัดการ Many-to-Many Relationship ด้วย Bridge Table
   - การทำ Basket Analysis เพื่อดู Product Combinations
   - การสร้าง Product Recommendations

---

## 🔗 เอกสารที่เกี่ยวข้อง

### โมดูลที่เกี่ยวข้อง
- [README.md](../README.md) - โครงสร้างหลักสูตร
- **02-Data-Sources** - การเชื่อมต่อ AdventureWorksDW2025
- **03-Data-Modeling-Basics** - Star Schema และ Dimensional Model
- **04-Relationships** - Relationships และ DAX Functions
- **05-Dimension-Table-Design** - Parent-Child Hierarchy
- **06-Date-Dimensions-Relationships** - Time Intelligence
- **07-Fact-Tables-Design** - Measures และ Calculation Groups
- **08-Performance-Optimization** - Performance Optimization
- **09-Best-Practices** - Best Practices

### แหล่งข้อมูลอ้างอิง
- [Microsoft SQL Server Samples - AdventureWorks](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) - ดาวน์โหลด AdventureWorksDW2025
- [SQLBI.com - SQLBI Methodology at Work](https://www.sqlbi.com/blog/marco/2008/10/02/sqlbi-methodology-at-work/) - SQLBI Methodology
- [SQLBI.com - Data Import Best Practices in Power BI](https://www.sqlbi.com/articles/data-import-best-practices-in-power-bi/) - Best Practices สำหรับ Import ข้อมูล
- [SQLBI.com - The Many-to-Many Revolution 2.0](https://www.sqlbi.com/wp-content/uploads/The_Many-to-Many_Revolution_2.0.pdf) - Many-to-Many Relationships และ Basket Analysis

---

## 💡 Tips สำหรับการสอบ

1. **วิเคราะห์ความต้องการ**: เข้าใจให้ชัดเจนก่อนเริ่มว่าต้องการดูอะไรบ้าง
2. **ตรวจสอบ Data Model**: ดู Fact Tables, Dimension Tables, Relationships ว่าครบหรือไม่
3. **ใช้ Best Practices**: Naming Conventions, Relationships, Measures, Performance Optimization
4. **Optimize Performance**: ลด Cardinality, เรียงข้อมูล, ใช้ Measures แทน Calculated Columns
5. **Test และ Validate**: ทดสอบทุก Measure และ Validate ผลลัพธ์
