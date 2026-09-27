# 09 - Best Practices

## 📚 เนื้อหาหลักสูตร

โมดูลนี้รวบรวม**แนวทางปฏิบัติที่ดี (Best Practices)** สำหรับการสร้างและจัดการ Power BI Semantic Model รวมถึง Naming Conventions, Data Model Organization, Column Properties, Measures Best Practices, Relationships Best Practices, Error Handling, และ Documentation

> **หมายเหตุ:** สำหรับการวิเคราะห์และ Optimize Performance ให้ดูที่ **08-Performance-Optimization**

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 📋 หัวข้อการเรียนรู้

### 1. Naming Conventions (หลักการตั้งชื่อ) ⭐

Naming Conventions คือหลักการตั้งชื่อที่ใช้ร่วมกันในทีมเพื่อให้ Model อ่านง่าย เข้าใจง่าย และทำงานร่วมกับทีมได้สะดวกขึ้น

**ทำไมต้องมี Naming Conventions:**

เมื่อเราสร้าง Semantic Model ที่มี Tables, Columns, และ Measures จำนวนมาก ถ้าเราไม่มีหลักการตั้งชื่อที่ชัดเจน เราอาจจะสับสนได้ว่า Table ไหนเป็น Fact Table หรือ Dimension Table หรือ Column ไหนเป็น Foreign Key หรือ Primary Key

Naming Conventions จะช่วยให้ Model อ่านง่าย เข้าใจง่าย ทำให้ทำงานร่วมกับทีมได้สะดวกขึ้น และลดความสับสนและข้อผิดพลาดลงมาก

#### 1.1 หลักการตั้งชื่อ Tables

การตั้งชื่อ Tables มีความสำคัญมาก เพราะจะช่วยให้ผู้ใช้หรือทีมงานรู้ได้ทันทีว่า Table ไหนเป็น Fact Table หรือ Dimension Table

**หลักการตั้งชื่อ Tables:**

เราควรใช้ **Prefix** เพื่อบอกประเภทของ Table เช่น ใช้ `Fact` สำหรับ Fact Tables และใช้ `Dim` สำหรับ Dimension Tables

ตัวอย่างชื่อ Table ที่ดี เช่น `FactResellerSales`, `FactInternetSales`, `DimProduct`, `DimDate`, `DimCustomer` ซึ่งจะบอกได้ทันทีว่า `FactResellerSales` เป็น Fact Table และ `DimProduct` เป็น Dimension Table

**ตัวอย่างชื่อ Table ที่ดี:**
- `FactResellerSales` - ใช้ Prefix `Fact` เพื่อบอกว่าเป็น Fact Table
- `DimProduct` - ใช้ Prefix `Dim` เพื่อบอกว่าเป็น Dimension Table
- `DimDate` - ใช้ Prefix `Dim` เพื่อบอกว่าเป็น Dimension Table
- `DimCustomer` - ใช้ Prefix `Dim` เพื่อบอกว่าเป็น Dimension Table

**ตัวอย่างชื่อ Table ที่ไม่ดี:**
- `Sales_Table` - ใช้ Underscore และไม่มี Prefix
- `product` - ไม่มี Prefix และใช้ตัวพิมพ์เล็ก
- `Date_Dimension_Table` - ชื่อยาวเกินไปและซ้ำซ้อน
- `Customer Info` - มีช่องว่างซึ่งไม่ควรมี

**หลักการเพิ่มเติม:**
เราควรใช้ **PascalCase** (ตัวแรกของแต่ละคำเป็นตัวพิมพ์ใหญ่) เช่น `FactResellerSales` ไม่ใช่ `fact_reseller_sales` หรือ `factresellersales` และควรหลีกเลี่ยงช่องว่างและสัญลักษณ์พิเศษ เช่น Underscore (`_`) หรือ Dash (`-`) และใช้ชื่อภาษาอังกฤษที่ชัดเจนและสื่อความหมาย

#### 1.2 หลักการตั้งชื่อ Columns

การตั้งชื่อ Columns ก็สำคัญเหมือนกัน เพราะจะช่วยให้รู้ได้ทันทีว่า Column ไหนเป็น Foreign Key หรือ Column ไหนเป็น Attribute

**หลักการตั้งชื่อ Columns:**

เราควรใช้ **PascalCase** เหมือนกับการตั้งชื่อ Tables เช่น `ProductKey`, `ProductName`, `OrderDate`, `SalesAmount`

Foreign Keys ควรลงท้ายด้วย `Key` เพื่อบอกว่าคือ Foreign Key เช่น `ProductKey`, `DateKey`, `CustomerKey` ซึ่งจะช่วยให้รู้ได้ทันทีว่าเป็น Foreign Key ที่ใช้สำหรับ Relationship

**ตัวอย่างชื่อ Column ที่ดี:**
- `ProductKey` - ใช้ PascalCase และลงท้ายด้วย Key เพื่อบอกว่าเป็น Foreign Key
- `ProductName` - ใช้ PascalCase และชื่อชัดเจน
- `OrderDate` - ใช้ PascalCase และชื่อชัดเจน
- `SalesAmount` - ใช้ PascalCase และชื่อชัดเจน

**ตัวอย่างชื่อ Column ที่ไม่ดี:**
- `product_key` - ใช้ Underscore และตัวพิมพ์เล็ก
- `Product Name` - มีช่องว่างซึ่งไม่ควรมี
- `order_date` - ใช้ Underscore และตัวพิมพ์เล็ก
- `sales_amount` - ใช้ Underscore และตัวพิมพ์เล็ก

**หลักการเพิ่มเติม:**
Measures ควรใช้ชื่อที่บอกความหมายชัดเจน เช่น `Total Sales`, `Average Price`, `Customer Count` และควรหลีกเลี่ยงช่องว่างในชื่อ Column (แต่ Measures สามารถมีช่องว่างได้เพื่ออ่านง่าย)

#### 1.3 หลักการตั้งชื่อ Measures

การตั้งชื่อ Measures มีความสำคัญมาก เพราะ Measures จะถูกแสดงใน Fields List และผู้ใช้จะเห็นชื่อ Measures เหล่านี้

**หลักการตั้งชื่อ Measures:**

เราควรใช้ **ช่องว่าง** ระหว่างคำเพื่ออ่านง่าย เช่น `Total Sales` ไม่ใช่ `TotalSales` และใช้ชื่อที่บอกความหมายชัดเจน เช่น `Total Sales Amount`, `Average Order Value`, `Customer Count`

สำหรับ Time Intelligence Measures ควรใช้ Prefix เพื่อบอกว่าเป็น Time Intelligence เช่น `YTD` = Year-to-Date, `QTD` = Quarter-to-Date, `MTD` = Month-to-Date, `LY` = Last Year

**ตัวอย่างชื่อ Measure ที่ดี:**
- `Total Sales` - ใช้ช่องว่างและชื่อชัดเจน
- `Sales YTD` - ใช้ Prefix YTD เพื่อบอกว่าเป็น Year-to-Date
- `Average Order Value` - ใช้ช่องว่างและชื่อชัดเจน
- `Customer Count` - ใช้ช่องว่างและชื่อชัดเจน

**ตัวอย่างชื่อ Measure ที่ไม่ดี:**
- `TotalSales` - ไม่มีช่องว่างทำให้อ่านยาก
- `sum_sales` - ใช้ Underscore และไม่ชัดเจน
- `Sales` - ชื่อคลุมเครือ ไม่บอกว่าเป็นอะไร

---

### 2. Data Model Organization (การจัดระเบียบ Data Model)

การจัดระเบียบ Data Model ให้เป็นระบบจะช่วยให้ Model อ่านง่าย เข้าใจง่าย และดูแลรักษาง่าย

#### 2.1 การจัดเรียง Tables

เมื่อเราเปิด Model View ใน Power BI Desktop เราจะเห็น Tables ทั้งหมดที่เรามี ซึ่งถ้าเราไม่จัดระเบียบให้ดี ก็อาจจะสับสนได้ว่า Table ไหนเกี่ยวข้องกับ Table ไหน

**หลักการจัดเรียง Tables:**

เราควรจัดเรียง Tables ให้ **Fact Tables อยู่ตรงกลาง** และ **Dimension Tables อยู่รอบๆ Fact Tables** เพื่อให้เห็นความสัมพันธ์ระหว่าง Tables ได้ชัดเจน

ตัวอย่างการจัดเรียงที่ดี เช่น วาง `FactResellerSales` ไว้ตรงกลาง แล้ววาง `DimProduct` ไว้ด้านบน `DimDate` ไว้ด้านซ้าย `DimCustomer` ไว้ด้านล่าง และ `DimGeography` ไว้ด้านขวา ซึ่งจะทำให้เห็นความสัมพันธ์ระหว่าง Fact Table และ Dimension Tables ได้ชัดเจน

**ตัวอย่างการจัดเรียง:**
```
[FactResellerSales] ← ตรงกลาง
├── [DimProduct] ← ด้านบน
├── [DimDate] ← ด้านซ้าย
├── [DimCustomer] ← ด้านล่าง
└── [DimGeography] ← ด้านขวา
```

#### 2.2 การซ่อน Objects ที่ไม่ใช้

ใน Model View เราสามารถซ่อน Columns ที่ไม่ต้องการให้แสดงใน Report View ได้ ซึ่งจะช่วยให้ Fields List ดูเรียบร้อยและไม่สับสน

**สิ่งที่ควรซ่อน:**

**Foreign Keys** ใน Fact Tables ควรซ่อนเพราะใช้เฉพาะสำหรับ Relationships เท่านั้น ไม่จำเป็นต้องแสดงใน Report

**Surrogate Keys** ใน Dimension Tables ก็ควรซ่อนเช่นกัน ถ้าไม่จำเป็นต้องแสดงใน Report

**Columns ที่ถูกใช้สร้าง Hierarchies แล้ว** ควรซ่อนเพื่อไม่ให้ซ้ำซ้อน เพราะเราสามารถใช้ Hierarchy แทนได้

**Technical Columns** เช่น `RowNumber`, `IsDeleted` ที่ใช้สำหรับเทคนิคการทำงานภายใน ไม่จำเป็นต้องแสดงใน Report

**วิธีซ่อน Columns:**

เราสามารถซ่อน Columns ได้โดยไปที่ **Model View** แล้ว Right-click ที่ Column ที่ต้องการซ่อน แล้วเลือก **Hide in Report View** ซึ่งจะทำให้ Column นั้นไม่แสดงใน Fields List แต่ยังสามารถใช้กับ Relationships และ Calculations ได้ตามปกติ

---

### 3. Column Properties Best Practices

การตั้งค่า Properties ของ Columns ให้ถูกต้องจะช่วยให้ Model ทำงานได้ดีและแสดงผลได้ถูกต้อง

#### 3.1 Data Type

การเลือก Data Type ที่เหมาะสมเป็นสิ่งสำคัญมาก เพราะจะช่วยลดขนาดของ Model และเพิ่มประสิทธิภาพ

**หลักการเลือก Data Type:**

เราควรเลือก Data Type ที่เหมาะสมและเล็กที่สุดที่สามารถเก็บข้อมูลได้ เช่น ถ้าเราเก็บจำนวนเต็ม ควรใช้ Integer แทน Text หรือถ้าเราไม่จำเป็นต้องใช้เวลา ควรใช้ Date แทน DateTime

**ตัวอย่างการเลือก Data Type:**
- `ProductKey: Integer` - ใช้ Integer สำหรับ Key
- `ProductName: Text` - ใช้ Text สำหรับชื่อ
- `OrderDate: Date` - ใช้ Date สำหรับวันที่ (ถ้าไม่จำเป็นต้องใช้เวลา)
- `OrderDate: DateTime` - ใช้ DateTime เฉพาะเมื่อจำเป็นต้องใช้เวลา

การเลือก Data Type ที่เล็กที่สุดจะช่วยลดขนาดของ Model และเพิ่มประสิทธิภาพ เพราะ Integer ใช้ Space น้อยกว่า Text และ Date ใช้ Space น้อยกว่า DateTime

#### 3.2 Format

การกำหนด Format ให้กับ Columns เป็นสิ่งสำคัญมาก เพราะจะช่วยให้ข้อมูลแสดงผลได้ถูกต้องและสวยงาม

**หลักการกำหนด Format:**

เราควรกำหนด Format ที่เหมาะสมตามประเภทของข้อมูล เช่น **Currency** สำหรับเงิน เช่น `$#,##0.00` **Percentage** สำหรับเปอร์เซ็นต์ เช่น `0.00%` และ **Number** สำหรับตัวเลข เช่น `#,##0.00`

**ตัวอย่างการกำหนด Format:**
- `SalesAmount` → Format: Currency (`$#,##0.00`) - แสดงเป็นสกุลเงิน
- `DiscountPercent` → Format: Percentage (`0.00%`) - แสดงเป็นเปอร์เซ็นต์
- `OrderQuantity` → Format: Whole Number (`#,##0`) - แสดงเป็นจำนวนเต็ม

การกำหนด Format ให้ถูกต้องจะช่วยให้ผู้ใช้เห็นข้อมูลได้ชัดเจนและเข้าใจง่ายขึ้น

#### 3.3 Sort By Column

Sort By Column เป็นคุณสมบัติที่ช่วยให้เราสามารถเรียงลำดับ Column ตามค่าของ Column อื่นได้ เช่น เรียงเดือนตามลำดับตัวเลข แทนที่จะเรียงตามตัวอักษร

**ทำไมต้องใช้ Sort By Column:**

ถ้าเราไม่ใช้ Sort By Column การเรียงลำดับจะทำตามตัวอักษร เช่น ถ้าเรามีชื่อเดือนเป็น "มกราคม", "กุมภาพันธ์", "มีนาคม" การเรียงลำดับตามตัวอักษรจะได้ "กุมภาพันธ์", "มกราคม", "มีนาคม" ซึ่งไม่ถูกต้องตามลำดับเวลา

แต่ถ้าเราใช้ Sort By Column โดยให้ `MonthName` เรียงตาม `MonthNumber` การเรียงลำดับจะได้ "มกราคม (1), กุมภาพันธ์ (2), มีนาคม (3)" ซึ่งถูกต้องตามลำดับเวลา

**ตัวอย่างการใช้ Sort By Column:**
- `MonthName` → Sort By: `MonthNumber`
- `QuarterName` → Sort By: `QuarterNumber`
- `DayName` → Sort By: `DayNumber`

---

### 4. Measures Best Practices

Measures เป็นส่วนสำคัญของ Semantic Model เพราะใช้ในการคำนวณค่าต่างๆ ที่แสดงใน Report

#### 4.1 ใช้ Explicit Measures (สร้าง Measures เอง)

Explicit Measures คือ Measures ที่เราสร้างเองโดยใช้ DAX ซึ่งจะดีกว่า Implicit Measures ที่ Power BI สร้างให้อัตโนมัติ

**ทำไมต้องใช้ Explicit Measures:**

Explicit Measures ช่วยให้เราควบคุมการคำนวณได้มากขึ้น เพราะเราสามารถกำหนดสูตรการคำนวณได้เอง ชื่อที่ชัดเจนเพราะเราตั้งชื่อเอง และใช้งานใน Excel ได้เมื่อเชื่อมต่อกับ Power BI Dataset

> **หมายเหตุ:** Performance Optimization สำหรับ Measures (เช่น ใช้ Measures แทน Calculated Columns) มีอยู่ใน **08-Performance-Optimization**

**ไม่ควรใช้ Implicit Measures (Auto Summarize):**

Implicit Measures คือ Measures ที่ Power BI สร้างให้อัตโนมัติเมื่อเราลาก Column ที่เป็นตัวเลขมาวางใน Visual ซึ่งอาจไม่ถูกต้องเพราะ Power BI จะสรุปโดยอัตโนมัติ เช่น SUM, AVERAGE ซึ่งอาจไม่ใช่สิ่งที่เราต้องการ

#### 4.2 Don't Summarize

Don't Summarize คือการตั้งค่าให้ Column ไม่สามารถสรุปได้อัตโนมัติ ซึ่งเป็น Best Practice ที่สำคัญมาก

**หลักการ:**

เราควรตั้งค่า **"Don't Summarize"** สำหรับทุก Column ใน Fact Tables เพื่อป้องกันการสรุปค่าผิดพลาด เพราะถ้าเราไม่ตั้งค่านี้ Power BI จะสรุปค่าโดยอัตโนมัติ เช่น SUM, AVERAGE ซึ่งอาจทำให้ได้ค่าที่ไม่ถูกต้อง

**วิธีตั้งค่า Don't Summarize:**

เราสามารถตั้งค่า Don't Summarize ได้โดยเลือก Column ใน Model View แล้วไปที่ Properties Panel ตั้งค่า **Summarization** เป็น **Don't Summarize** ซึ่งจะป้องกันไม่ให้ Power BI สรุปค่าโดยอัตโนมัติ และบังคับให้ผู้ใช้ใช้ Explicit Measures แทน

#### 4.3 Naming Measures

การตั้งชื่อ Measures ให้ชัดเจนเป็นสิ่งสำคัญมาก เพราะจะช่วยให้ผู้ใช้เข้าใจได้ว่า Measure นี้คำนวณอะไร

**หลักการตั้งชื่อ Measures:**

เราควรใช้ชื่อที่บอกความหมายชัดเจน เช่น `Total Sales Amount` ไม่ใช่แค่ `Sales` และควรระบุหน่วยถ้ามี เช่น `Total Sales Amount (USD)` หรือ `Customer Count`

เราควรใช้ช่องว่างเพื่ออ่านง่าย เช่น `Total Sales Amount` ไม่ใช่ `TotalSalesAmount` ซึ่งจะช่วยให้อ่านง่ายขึ้น

**ตัวอย่างชื่อ Measure ที่ดี:**
- `Total Sales Amount` - ชื่อชัดเจนและใช้ช่องว่าง
- `Average Order Value` - ชื่อชัดเจนและใช้ช่องว่าง
- `Customer Count` - ชื่อชัดเจนและใช้ช่องว่าง
- `Sales YTD` - ใช้ Prefix YTD เพื่อบอกว่าเป็น Year-to-Date

**ตัวอย่างชื่อ Measure ที่ไม่ดี:**
- `Sales` - ชื่อไม่ชัดเจน ไม่บอกว่าเป็นอะไร
- `TotalSales` - ไม่มีช่องว่างทำให้อ่านยาก

---

### 5. Relationships Best Practices

Relationships เป็นส่วนสำคัญของ Semantic Model เพราะใช้เชื่อมระหว่าง Tables และช่วยให้เราสามารถ Query ข้อมูลจากหลาย Tables ได้

#### 5.1 Cardinality

Cardinality คือประเภทของ Relationship ที่บอกว่าความสัมพันธ์ระหว่าง Tables เป็นแบบไหน เช่น One-to-Many, Many-to-One, One-to-One, หรือ Many-to-Many

**หลักการ:**

เราควรใช้ **One-to-Many** เป็นหลัก เพราะเป็น Cardinality ที่ใช้บ่อยที่สุดและให้ Performance ที่ดีที่สุด โดยปกติ Fact Table จะอยู่ฝั่ง Many และ Dimension Table จะอยู่ฝั่ง One

**Many-to-Many** ควรใช้เท่าที่จำเป็นเท่านั้น เพราะมีผลต่อ Performance และซับซ้อนกว่า One-to-Many

> **หมายเหตุ:** Performance Impact ของ Relationships มีอยู่ใน **08-Performance-Optimization**

**ตัวอย่าง Relationship ที่ดี:**
- `DimProduct[ProductKey]` → `FactResellerSales[ProductKey]` (One-to-Many) - Dimension อยู่ฝั่ง One Fact อยู่ฝั่ง Many
- `DimDate[DateKey]` → `FactResellerSales[OrderDateKey]` (One-to-Many) - Dimension อยู่ฝั่ง One Fact อยู่ฝั่ง Many

#### 5.2 Cross Filter Direction

Cross Filter Direction คือทิศทางที่ Filter จะทำงานใน Relationship ซึ่งมี 2 แบบคือ Single Direction และ Both Direction

**หลักการ:**

เราควรใช้ **Single Direction** เป็นหลัก เพราะให้ Performance ที่ดีกว่าและไม่สับสน Single Direction หมายความว่า Filter จะทำงานจาก Dimension Table ไปยัง Fact Table เท่านั้น

**Both Direction** ควรใช้เท่าที่จำเป็นเท่านั้น เพราะมีผลต่อ Performance และอาจทำให้เกิดความสับสนหรือผลลัพธ์ที่ไม่ถูกต้อง

> **หมายเหตุ:** Performance Impact ของ Cross Filter Direction มีอยู่ใน **08-Performance-Optimization**

**ตัวอย่าง Relationship ที่ดี:**
- `DimProduct` → `FactResellerSales` (Single Direction) - Filter จาก Dimension ไป Fact เท่านั้น
- `DimProduct` ↔ `FactResellerSales` (Both Direction) - ใช้เมื่อจำเป็นเท่านั้น เพราะ Filter ทำงานทั้งสองทิศทาง

#### 5.3 Hide Foreign Keys

Foreign Keys ใน Fact Tables ควรซ่อนเพราะใช้เฉพาะสำหรับ Relationships เท่านั้น ไม่จำเป็นต้องแสดงใน Report

**หลักการ:**

เราควรซ่อน Foreign Keys ใน Fact Tables เพราะ Foreign Keys เป็นตัวเลขหรือ ID ที่ไม่มีความหมายต่อผู้ใช้ และใช้เฉพาะสำหรับ Relationships เท่านั้น การซ่อน Foreign Keys จะช่วยให้ Fields List ดูเรียบร้อยและไม่สับสน

---

### 6. Error Handling (การจัดการ Errors)

การจัดการ Errors และ Warnings อย่างถูกต้องเป็นสิ่งสำคัญมาก เพราะจะช่วยให้ Model ทำงานได้ถูกต้องและไม่เกิดปัญหาในอนาคต

#### 6.1 ตรวจสอบ Errors

เราควรตรวจสอบ Errors และ Warnings อย่างสม่ำเสมอเพื่อให้แน่ใจว่า Model ทำงานได้ถูกต้อง

**สิ่งที่ต้องตรวจสอบ:**

**Warning** ใน Power Query เช่น Warning เกี่ยวกับ Data Type หรือ Null Values ซึ่งอาจส่งผลต่อการคำนวณ

**Errors** ใน Relationships เช่น Relationship ที่ไม่มีค่า Match หรือ Relationship ที่ผิดพลาด

**Warnings** ใน Measures เช่น Warning เกี่ยวกับ Circular Dependency หรือ Warning เกี่ยวกับ Performance

**วิธีตรวจสอบ:**

เราควรดู **Warnings** ใน Power Query Editor อย่างสม่ำเสมอ เพื่อดูว่ามี Warning อะไรบ้างและควรแก้ไขอย่างไร

เราควรตรวจสอบ Relationships ที่มีปัญหา เช่น Relationship ที่ไม่มีค่า Match หรือ Relationship ที่ผิดพลาด

เราควรทดสอบ Measures ทุกตัวเพื่อให้แน่ใจว่า Measures ทำงานได้ถูกต้องและไม่มี Error

#### 6.2 แก้ไข Errors

เมื่อพบ Errors หรือ Warnings เราควรแก้ไขทันทีและไม่ควรปล่อยไว้

**หลักการ:**

เราควรแก้ไข Errors ทันที (อย่าปล่อยไว้) เพราะ Errors อาจส่งผลต่อการคำนวณหรือการแสดงผล และอาจทำให้เกิดปัญหาในอนาคต

เราควร Document Errors และวิธีแก้ไขไว้ เพื่อให้ทีมงานหรือตัวเราเองสามารถอ้างอิงได้ในอนาคต

เราควรทดสอบหลังแก้ไขเพื่อให้แน่ใจว่า Errors ถูกแก้ไขแล้วและไม่เกิดปัญหาใหม่

---

### 7. Documentation (การทำเอกสาร)

การทำ Documentation ที่ดีเป็นสิ่งสำคัญมาก เพราะจะช่วยให้ทีมงานหรือตัวเราเองเข้าใจ Model ได้ง่ายขึ้น และดูแลรักษาได้ง่ายขึ้น

#### 7.1 Table Documentation

เราควร Document Tables ทุกตัวเพื่อให้รู้ว่า Table แต่ละตัวเก็บข้อมูลอะไรบ้างและมาจากไหน

**สิ่งที่ควร Document:**

**Description** ของแต่ละ Table เพื่อบอกว่า Table นั้นเก็บข้อมูลอะไรบ้าง

**Source** ของข้อมูล เพื่อบอกว่าข้อมูลมาจากไหน เช่น Database, Excel File, หรือ API

**Refresh Schedule** (ถ้ามี) เพื่อบอกว่าข้อมูล Refresh เมื่อไหร่ เช่น Daily, Weekly, หรือ Monthly

**วิธีเพิ่ม Description:**

เราสามารถเพิ่ม Description ได้โดยเลือก Table ใน Model View แล้วไปที่ Properties Panel เพิ่ม **Description** ใน Description Field

**ตัวอย่าง Documentation:**
```
Table: FactResellerSales
Description: Fact table containing reseller sales transactions. 
Source: AdventureWorksDW2025 database.
Last Updated: 2024-01-15
```

#### 7.2 Measure Documentation

เราควร Document Measures ทุกตัวเพื่อให้รู้ว่า Measure แต่ละตัวคำนวณอะไรและใช้งานอย่างไร

**สิ่งที่ควร Document:**

**Description** ของ Measure เพื่อบอกว่า Measure นั้นคำนวณอะไร

**Formula** หรือ Logic เพื่อบอกว่าคำนวณอย่างไร

**Usage** (วิธีใช้งาน) เพื่อบอกว่าใช้ใน Report ไหนหรือใช้กับ Visual อะไร

**ตัวอย่าง Documentation:**
```
Measure: Total Sales
Description: Sum of all sales amounts from FactResellerSales
Formula: SUM(FactResellerSales[SalesAmount])
Usage: Use in reports to show total sales
```

#### 7.3 Relationship Documentation

เราควร Document Relationships เพื่อให้รู้ว่า Relationships แต่ละตัวทำงานอย่างไรและทำไมถึงสร้าง

**สิ่งที่ควร Document:**

**Cardinality** และ **Direction** เพื่อบอกว่า Relationship เป็นแบบไหนและ Filter ทำงานอย่างไร

**Business Logic** (เหตุผลของ Relationship) เพื่อบอกว่าทำไมถึงสร้าง Relationship นี้ เช่น เพื่อเชื่อมข้อมูลระหว่าง Tables หรือเพื่อให้สามารถ Query ข้อมูลร่วมกันได้

---

### 9. Security Best Practices

Security เป็นส่วนสำคัญของ Semantic Model เพราะจะช่วยป้องกันข้อมูลสำคัญไม่ให้ผู้อื่นเข้าถึงได้

#### 9.1 Row-Level Security (RLS)

Row-Level Security (RLS) คือการจำกัดการเข้าถึงข้อมูลตามแถว เช่น ผู้ใช้เห็นได้แค่ข้อมูลของตัวเองหรือของแผนกตัวเอง

**หลักการ:**

เราควรสร้าง Roles ที่ชัดเจน เช่น `Sales Manager`, `Sales Rep`, `Finance` เพื่อให้รู้ว่าแต่ละ Role มีสิทธิ์อะไรบ้าง

เราควรทดสอบ Roles ก่อน Deploy เพื่อให้แน่ใจว่า Roles ทำงานได้ถูกต้องและไม่มีปัญหา

เราควร Document Security Rules เพื่อให้ทีมงานหรือตัวเราเองสามารถอ้างอิงได้ในอนาคต

#### 9.2 Object-Level Security (OLS)

Object-Level Security (OLS) คือการจำกัดการเข้าถึงข้อมูลตาม Object เช่น ซ่อน Columns หรือ Tables ที่ไม่จำเป็น

**หลักการ:**

เราควรซ่อน Columns และ Tables ที่ไม่จำเป็นเพื่อไม่ให้ผู้ใช้เห็นข้อมูลที่ไม่ได้ใช้

เราควรใช้ OLS สำหรับข้อมูลที่สำคัญ เช่น ข้อมูลที่เกี่ยวข้องกับเงินหรือข้อมูลส่วนบุคคล

---

### 10. Version Control Best Practices

Version Control ช่วยให้เราจัดการไฟล์ได้ดีขึ้นและสามารถย้อนกลับไปใช้เวอร์ชันเก่าได้

#### 10.1 การจัดการไฟล์

เราควรใช้ชื่อไฟล์ที่บอกเวอร์ชันเพื่อให้รู้ว่าเป็นเวอร์ชันไหน

**หลักการ:**

เราควรใช้ชื่อไฟล์ที่บอกเวอร์ชัน เช่น `AdventureWorksDW_Model_v1.0.pbix`, `AdventureWorksDW_Model_v1.1.pbix`, `AdventureWorksDW_Model_v2.0.pbix` ซึ่งจะช่วยให้รู้ว่าเป็นเวอร์ชันไหนและมีการเปลี่ยนแปลงอะไรบ้าง

เราควรเก็บ Backup ไฟล์ไว้เสมอเพื่อป้องกันกรณีที่ไฟล์เสียหาย

เราควร Document Changes เพื่อให้รู้ว่ามีการเปลี่ยนแปลงอะไรบ้างในแต่ละเวอร์ชัน

**ตัวอย่างชื่อไฟล์:**
- `AdventureWorksDW_Model_v1.0.pbix` - เวอร์ชันแรก
- `AdventureWorksDW_Model_v1.1.pbix` - เวอร์ชันที่แก้ไขเล็กน้อย
- `AdventureWorksDW_Model_v2.0.pbix` - เวอร์ชันที่เปลี่ยนแปลงมาก

#### 10.2 การใช้ Tabular Editor

Tabular Editor เป็น External Tool ที่ช่วยให้เราจัดการ Metadata ของ Semantic Model ได้ดีขึ้น

**หลักการ:**

เราสามารถ Export Metadata เป็น JSON เพื่อเก็บไว้ใน Version Control System เช่น Git ซึ่งจะช่วยให้เราจัดการ Metadata ได้ดีขึ้นและสามารถย้อนกลับไปใช้เวอร์ชันเก่าได้

เราสามารถใช้ Version Control (Git) สำหรับ Metadata เพื่อจัดการ Metadata ได้ดีขึ้น

เราสามารถใช้ Tabular Editor สำหรับ Bulk Changes เพื่อแก้ไข Metadata หลายตัวพร้อมกันได้อย่างรวดเร็ว

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ ใช้ Naming Conventions ที่ถูกต้อง
- ✅ จัดระเบียบ Data Model ได้อย่างมีประสิทธิภาพ
- ✅ จัดการ Errors และ Warnings ได้
- ✅ ทำ Documentation ที่ดีได้
- ✅ นำ Best Practices ไปใช้ในโครงการจริงได้

---

## 📝 สรุป Best Practices

### ✅ Do (ควรทำ)

**ใช้ Naming Conventions ที่ชัดเจน** - ใช้ Prefix เช่น `Fact` สำหรับ Fact Tables และ `Dim` สำหรับ Dimension Tables ใช้ PascalCase และหลีกเลี่ยงช่องว่างและสัญลักษณ์พิเศษ

**ซ่อน Objects ที่ไม่ใช้** - ซ่อน Foreign Keys, Surrogate Keys, Columns ที่ถูกใช้สร้าง Hierarchies แล้ว, และ Technical Columns เพื่อให้ Fields List ดูเรียบร้อย

**ใช้ Explicit Measures** - สร้าง Measures เองโดยใช้ DAX เพื่อควบคุมการคำนวณได้มากขึ้น

**ตั้งค่า "Don't Summarize" สำหรับ Fact Tables** - เพื่อป้องกันการสรุปค่าผิดพลาดและบังคับให้ผู้ใช้ใช้ Explicit Measures

**Document Tables และ Measures** - เพิ่ม Description, Source, และ Usage เพื่อให้ทีมงานเข้าใจ Model ได้ง่ายขึ้น

> **หมายเหตุ:** Performance Optimization Techniques (เช่น ลด Cardinality, เรียงข้อมูล, ใช้ Measures แทน Calculated Columns) มีอยู่ใน **08-Performance-Optimization**

### ❌ Don't (ไม่ควรทำ)

**ใช้ Implicit Measures** - อย่าใช้ Auto Summarize เพราะอาจทำให้ได้ค่าที่ไม่ถูกต้อง

**ปล่อย Foreign Keys แสดงใน Report** - ควรซ่อน Foreign Keys เพราะใช้เฉพาะสำหรับ Relationships

**ใช้ Both Direction Relationships โดยไม่จำเป็น** - ควรใช้ Single Direction เป็นหลักและใช้ Both Direction เท่าที่จำเป็น

**ใช้ Calculated Columns โดยไม่จำเป็น** - ควรใช้ Measures แทนเพราะให้ Performance ที่ดีกว่า (ดู Performance Impact ใน 08-Performance-Optimization)

**ปล่อย Errors ไว้ไม่แก้ไข** - ควรแก้ไข Errors ทันทีเพื่อป้องกันปัญหาในอนาคต

**ไม่ทำ Documentation** - ควรทำ Documentation เพื่อให้ทีมงานเข้าใจ Model ได้ง่ายขึ้น

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](../README.md) - โครงสร้างหลักสูตร
- **08-Performance-Optimization** - Performance Optimization Techniques (ลด Cardinality, เรียงข้อมูล, ใช้ Measures)
- [Microsoft Learn: Power BI Best Practices](https://learn.microsoft.com/power-bi/)
- [sqlbi.com: Best Practices](https://www.sqlbi.com/)

---

## 💡 Tips สำหรับการสอบ

1. **จำ Naming Conventions**: Fact → `Fact`, Dimension → `Dim` - จำ Prefix ที่ใช้สำหรับแต่ละประเภท Table
2. **จำ Best Practices**: Don't Summarize, Explicit Measures, Single Direction - จำ Best Practices ที่สำคัญที่สุด
3. **เข้าใจ Best Practices**: Naming, Organization, Documentation, Error Handling - เข้าใจว่าทำไมถึงต้องทำแบบนี้
4. **อ้างอิง Performance Optimization**: เทคนิค Performance มีอยู่ใน 08-Performance-Optimization - รู้ว่าการ Optimize Performance อยู่ที่ไหน
