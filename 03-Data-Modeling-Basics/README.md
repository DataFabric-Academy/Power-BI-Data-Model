# 03 - Data Modeling Basics (Dimensional Model)

## 📚 เนื้อหาหลักสูตร

โมดูลนี้แนะนำแนวคิดพื้นฐานของ Dimensional Model ซึ่งเป็นพื้นฐานสำคัญสำหรับการสร้าง Power BI Semantic Model โดยเรียนรู้การแยกแยะระหว่าง Measures และ Dimension Attributes, Star Schema และ Snowflake Schema, และแนวคิด Conformed Dimensions

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 📋 หัวข้อการเรียนรู้

### 1. อะไรคือ Measures อะไรคือ Dimension Attributes ⭐

#### 1.1 Measures (มาตรวัด)

**Measures** = ค่าที่ต้องคำนวณ (Calculated Values)

**ลักษณะ:**
- ต้องใช้ฟังก์ชัน Aggregate (เช่น SUM, COUNT, AVERAGE)
- ไม่สามารถแสดงเป็น Raw Data ได้
- ต้องคำนวณจาก Fact Tables

**ตัวอย่าง Measures:**
- ยอดขาย (Total Sales)
- จำนวนสินค้า (Quantity)
- จำนวนลูกค้า (Customer Count)
- ราคาเฉลี่ย (Average Price)

#### 1.2 Dimension Attributes (แอตทริบิวต์มิติ)

**Dimension Attributes** = คุณลักษณะที่ใช้ในการกรองหรือจัดกลุ่ม

**ลักษณะ:**
- ไม่ต้องคำนวณ (มีค่าเป็น Raw Data)
- ใช้ในการกรอง (Filter)
- ใช้ในการจัดกลุ่ม (Group By)
- เก็บใน Dimension Tables

**ตัวอย่าง Dimension Attributes:**
- หมวดหมู่สินค้า (Product Category)
- ชื่อลูกค้า (Customer Name)
- วันที่ (Date)
- ภูมิภาค (Region)

#### 1.3 ตัวอย่างการแยกแยะ

**ตัวอย่างที่ 1:**
```
"ยอดขาย รายไตรมาส"
→ **Measure** = ยอดขาย (Total Sales)
→ **Dimension Attribute** = รายไตรมาส (Quarter)
```

**ตัวอย่างที่ 2:**
```
"จำนวนสินค้าคงคลัง แยกตามคลังสินค้า"
→ **Measure** = จำนวนสินค้าคงคลัง (Inventory Quantity)
→ **Dimension Attribute** = รายการคลังสินค้า (Warehouse)
```

**ตัวอย่างที่ 3:**
```
"จำนวนของเสียจากการผลิต แยกตามเครื่องจักร"
→ **Measure** = จำนวนของเสียจากการผลิต (Defect Count)
→ **Dimension Attribute** = รายการเครื่องจักร (Machine)
```

#### 1.4 วิธีจำง่ายๆ

**Measures:**
- คำถาม: "เท่าไหร่?" (How much? How many?)
- เช่น: ยอดขายเท่าไหร่? จำนวนเท่าไหร่?

**Dimension Attributes:**
- คำถาม: "แยกตามอะไร?" (By what? Group by?)
- เช่น: แยกตามหมวดหมู่ แยกตามวันที่

---

### 2. ข้อมูลแบบ Flat (ไม่ดี ❌)

#### 2.1 ลักษณะของ Flat Data

**Flat Data:**
- Measures อยู่รวมกับ Attributes
- Attributes ทั้งหมดอยู่ในตารางเดียว
- ไม่มี Relationships

**ตัวอย่าง Flat Data:**
```
ตาราง Sales (Flat)
├── OrderDate
├── CustomerName
├── ProductName
├── Category
├── Region
├── SalesAmount
├── Quantity
└── ...
```

#### 2.2 ปัญหาของ Flat Data

**1. ประสิทธิภาพต่ำมาก:**
- ❌ ต้อง Copy ข้อมูลซ้ำๆ
- ❌ ขนาดใหญ่
- ❌ Query ช้า

**2. ภาระงานส่วนใหญ่หมดไปกับการ Copy ข้อมูล:**
- ❌ แต่ละ Row มีข้อมูล Attributes ซ้ำกัน
- ❌ ใช้ Storage มาก

**3. ขนาดของตารางขยายตัวอย่างรวดเร็ว:**
- ❌ เมื่อเพิ่มเงื่อนไข (Attributes) ขนาดเพิ่มขึ้นมาก
- ❌ Model ใหญ่ขึ้น

**ตัวอย่างปัญหา:**
```
ตาราง Sales (Flat)
- มี 1,000,000 Orders
- แต่ละ Order มี Customer Name, Product Name, Category, Region ซ้ำกัน
- ขนาด: ~100 MB

ถ้าเพิ่ม Attribute (เช่น Brand, Color):
- ขนาดเพิ่มขึ้นมาก: ~150 MB
```

---

### 3. Star Schema ⭐ (แนะนำ)

#### 3.1 แนวคิดการออกแบบ Data Warehouse

**Star Schema** = แนวคิดการออกแบบ Data Warehouse จาก **Ralph Kimball**

**โครงสร้าง:**
- **ตาราง Dimension** (รอบๆ) = ประกอบด้วย Dimension Attributes
- **ตาราง Fact** (ตรงกลาง) = ประกอบด้วย Measures และ Dimension Keys

**ทำไมเรียกว่า "Star"?**
- เพราะ Fact Table อยู่ตรงกลาง (เหมือนดาว)
- Dimension Tables อยู่รอบๆ (เหมือนรังสี)

#### 3.2 ตัวอย่างจาก AdventureWorksDW2025

**Fact Table: FactResellerSales** (ตรงกลาง)
```
Measures:
├── SalesAmount (ยอดขาย)
└── OrderQuantity (จำนวนสินค้า)

Dimension Keys:
├── ProductKey → DimProduct
├── OrderDateKey → DimDate
├── ResellerKey → DimReseller
├── GeographyKey → DimGeography
├── EmployeeKey → DimEmployee
└── PromotionKey → DimPromotion
```

**Dimension Tables** (รอบๆ)
```
├── DimProduct (Product Attributes)
│   ├── ProductKey (Surrogate Key)
│   ├── ProductName
│   ├── Category
│   ├── Subcategory
│   └── Brand
│
├── DimDate (Date Attributes)
│   ├── DateKey (Surrogate Key)
│   ├── Date
│   ├── Year
│   ├── Quarter
│   ├── Month
│   └── Day
│
├── DimReseller (Reseller Attributes)
│   ├── ResellerKey (Surrogate Key)
│   ├── ResellerName
│   └── BusinessType
│
├── DimGeography (Geography Attributes)
│   ├── GeographyKey (Surrogate Key)
│   ├── Country
│   ├── State
│   └── City
│
├── DimEmployee (Employee Attributes)
│   └── ...
│
└── DimPromotion (Promotion Attributes)
    └── ...
```

#### 3.3 ข้อดีของ Star Schema

**1. ประสิทธิภาพสูง:**
- ✅ Fact Table มีขนาดเล็ก (เก็บแค่ Keys และ Measures)
- ✅ Dimension Tables แยกต่างหาก
- ✅ Query เร็ว

**2. ขนาดเล็ก:**
- ✅ ไม่ Copy ข้อมูลซ้ำ
- ✅ ใช้ Storage น้อย

**3. เข้าใจง่าย:**
- ✅ โครงสร้างชัดเจน
- ✅ ง่ายต่อการดูแลรักษา

**4. เหมาะกับ VertiPaq Engine:**
- ✅ Low Cardinality (Foreign Keys)
- ✅ Sorted Data
- ✅ Dictionary Encoding และ RLE Encoding ดี

#### 3.4 เปรียบเทียบ Flat Data vs Star Schema

**Flat Data:**
```
ตาราง Sales (1,000,000 rows)
├── OrderDate (ซ้ำ 1,000 ครั้ง)
├── CustomerName (ซ้ำ 500 ครั้ง)
├── ProductName (ซ้ำ 200 ครั้ง)
├── Category (ซ้ำ 10 ครั้ง)
└── SalesAmount
→ ขนาด: ~100 MB
```

**Star Schema:**
```
FactResellerSales (1,000,000 rows)
├── DateKey (ซ้ำ 1,000 ครั้ง) ✅ Cardinality ต่ำ
├── CustomerKey (ซ้ำ 500 ครั้ง) ✅ Cardinality ต่ำ
├── ProductKey (ซ้ำ 200 ครั้ง) ✅ Cardinality ต่ำ
└── SalesAmount
→ ขนาด: ~20 MB ✅ เล็กกว่า

DimDate (1,000 rows)
DimCustomer (500 rows)
DimProduct (200 rows)
→ ขนาด: ~5 MB ✅ เล็กมาก

รวม: ~25 MB (vs 100 MB) ✅ ประหยัด 75%
```

---

### 4. Snowflake Schema

#### 4.1 ลักษณะของ Snowflake Schema

**Snowflake Schema:**
- Dimension Tables เชื่อมต่อกันเป็นลำดับชั้น
- มี Normalization มากกว่า Star Schema

**ตัวอย่าง:**
```
DimProduct
├── ProductKey
├── ProductName
└── SubcategoryKey → DimProductSubcategory
                    ├── SubcategoryKey
                    ├── SubcategoryName
                    └── CategoryKey → DimProductCategory
                                     ├── CategoryKey
                                     └── CategoryName
```

**ทำไมเรียกว่า "Snowflake"?**
- เพราะมีหลายชั้น (เหมือนเกล็ดหิมะ)

#### 4.2 ปัญหาของ Snowflake Schema บน Power BI

**ปัญหาหลัก:**
- ❌ **ไม่สามารถสร้าง Hierarchy โดย Hierarchy Members มาจากหลายตารางได้**
- ❌ Query ซับซ้อนขึ้น
- ❌ Performance อาจลดลง

**ตัวอย่างปัญหา:**
```
ต้องการสร้าง Hierarchy:
Category → Subcategory → Product

แต่ Category อยู่ใน DimProductCategory
Subcategory อยู่ใน DimProductSubcategory
Product อยู่ใน DimProduct

→ Power BI ไม่สามารถสร้าง Hierarchy ข้ามตารางได้โดยตรง
```

#### 4.3 แนวทางแก้ไข

**วิธีที่ 1: ลดรูปจาก Snowflake ไปเป็น Star Schema โดยใช้ Power Query**
```
1. เปิด Power Query Editor
2. Merge DimProduct, DimProductSubcategory, DimProductCategory
3. Flatten เป็นตารางเดียว (Star Schema)
```

**วิธีที่ 2: ใช้ DAX สร้าง Calculated Column**
```
Product Category = RELATED(DimProductCategory[CategoryName])
Product Subcategory = RELATED(DimProductSubcategory[SubcategoryName])
```

**คำแนะนำ:**
- ✅ **แนะนำให้ใช้ Star Schema** สำหรับ Power BI
- ✅ ลดรูป Snowflake เป็น Star Schema ใน Power Query

---

### 5. Conformed Dimensions

#### 5.1 ความหมาย

**Conformed Dimensions** = Dimension Table ที่สามารถใช้งานกับ Fact Table หลายตัวได้

**หลักการ:**
- Dimension Table เดียวกัน
- ใช้ร่วมกันได้หลาย Fact Tables

#### 5.2 ตัวอย่าง Conformed Dimensions

**Date Dimension (Conformed Dimension):**

**ใช้ร่วมกับ Fact Tables หลายตัว:**
```
DimDate (Conformed Dimension)
│
├── → FactResellerSales (ผ่าน OrderDateKey)
├── → FactInternetSales (ผ่าน OrderDateKey)
└── → FactSalesQuota (ผ่าน MonthKey)
```

**ตัวอย่างจาก AdventureWorksDW2025:**
- **Date Dimension Table** จะถูกเชื่อมโยงกับทั้ง:
  - `FactResellerSales` Fact Table → ผ่านคอลัมน์ `OrderDateKey`
  - `FactInternetSales` Fact Table → ผ่านคอลัมน์ `OrderDateKey`
  - `FactSalesQuota` Fact Table → ผ่านคอลัมน์ `MonthKey`
  
  *(ดูรายละเอียดเรื่อง Granularity ในส่วน Bus Matrix)*

#### 5.3 ประโยชน์ของ Conformed Dimensions

**1. ความสอดคล้องของข้อมูล:**
- ✅ ใช้ Dimension เดียวกัน → ข้อมูลสอดคล้องกัน
- ✅ ไม่มีความขัดแย้ง

**2. ลดความซ้ำซ้อน:**
- ✅ ไม่ต้องสร้าง Dimension หลายตัว
- ✅ ประหยัด Storage

**3. ง่ายต่อการบำรุงรักษา:**
- ✅ แก้ไขที่เดียว มีผลทุกที่
- ✅ ไม่ต้องแก้หลายที่

**4. ความยืดหยุ่น:**
- ✅ ใช้ Dimension เดียวกันกับ Fact Tables หลายตัว
- ✅ สามารถเปรียบเทียบข้อมูลข้าม Fact Tables ได้

---

### 6. Bus Matrix และ Granularity

#### 6.1 ความหมาย

**Bus Matrix** = เครื่องมือที่แสดงความสัมพันธ์ระหว่าง:
- **Business Processes** (Fact Tables)
- **Dimensions** (Dimension Tables)
- **Granularity** (ระดับความละเอียดของข้อมูล)


**Bus Matrix:**

| Business Process | Date | Product | Customer | Geography | Employee | Granularity |
|------------------|------|---------|----------|-----------|----------|-------------|
| Reseller Sales | ✓ | ✓ | ✓ | ✓ | ✓ | Daily |
| Internet Sales | ✓ | ✓ | ✓ | ✓ | | Daily |
| Sales Quota | ✓ | | | | ✓ | Monthly |

#### 6.2 Granularity ⭐

**Granularity** = ระดับความละเอียดของข้อมูล (Level of Detail)

**ประเภท Granularity:**
- **Daily** (รายวัน) = ข้อมูลในระดับวัน
- **Monthly** (รายเดือน) = ข้อมูลในระดับเดือน
- **Yearly** (รายปี) = ข้อมูลในระดับปี
- **Transaction** (รายการ) = ข้อมูลในระดับรายการ

**ตัวอย่าง Granularity ใน AdventureWorksDW2025:**
```
FactResellerSales:
- Granularity: Daily (แต่ละ Row = 1 วัน, 1 Product, 1 Reseller)
- มีหลาย Rows ต่อ 1 วัน (ถ้ามีหลาย Product หรือ Reseller)

FactSalesQuota:
- Granularity: Monthly (แต่ละ Row = 1 เดือน, 1 Product, 1 Employee)
- มี Rows น้อยกว่า FactResellerSales (เพราะเป็นรายเดือน)
```

**ความสำคัญของ Granularity:**
- ✅ ระบุระดับความละเอียดของแต่ละ Fact Table
- ✅ ช่วยในการออกแบบ Relationships
- ✅ ช่วยในการ Aggregate ข้อมูล

**ความหมาย:**
- ✓ = Fact Table ใช้ Dimension นี้
- (ว่าง) = Fact Table ไม่ใช้ Dimension นี้
- **Granularity** = ระดับความละเอียดของข้อมูล

**คำอธิบาย Granularity ใน Bus Matrix:**
- **Daily** = แต่ละ Row แทนรายละเอียดในระดับวัน (มี Rows มาก)
- **Monthly** = แต่ละ Row แทนรายละเอียดในระดับเดือน (มี Rows น้อยกว่า)

#### 6.3 Heterogeneous Granularity

**ปัญหาที่เกิด:**
- FactResellerSales: Daily Granularity
- FactSalesQuota: Monthly Granularity
- ต้องการเปรียบเทียบ Sales กับ Quota แต่ Granularity ไม่เท่ากัน

**วิธีแก้ไข:**
- ✅ Aggregate Daily Sales เป็น Monthly ก่อนเปรียบเทียบ
- ❌ ไม่ควรแบ่ง Monthly Quota เป็น Daily

#### 6.4 ประโยชน์ของ Bus Matrix

**1. ช่วยในการออกแบบ:**
- ✅ วางแผนโครงสร้าง Data Warehouse
- ✅ ระบุ Fact Tables และ Dimension Tables
- ✅ ระบุ Granularity ของแต่ละ Fact Table

**2. สื่อสารโครงสร้าง:**
- ✅ แสดงความสัมพันธ์ระหว่าง Fact Tables และ Dimensions
- ✅ แสดง Granularity ของแต่ละ Fact Table
- ✅ เข้าใจง่าย

**3. วางแผนโครงการ:**
- ✅ จัดลำดับความสำคัญของ Fact Tables
- ✅ จัดลำดับความสำคัญของ Dimensions
- ✅ วางแผนการ Aggregate ข้อมูล

**4. ส่งเสริมการใช้ Conformed Dimensions:**
- ✅ เห็น Dimension ที่ใช้ร่วมกันได้
- ✅ วางแผน Conformed Dimensions
- ✅ เข้าใจ Heterogeneous Granularity

---

### 7. Low Cardinality Columns สำหรับ Foreign Keys ⭐

#### 7.1 ความสำคัญ

**ทำไม Foreign Keys ต้องมี Low Cardinality?**
- Low Cardinality = Dictionary Encoding ดี = ประหยัด Memory = เร็ว

**ตัวอย่าง:**
```
FactResellerSales
├── ProductKey: 1, 1, 1, 2, 2, 3, 3, 3, ... (Cardinality = 100)
├── DateKey: 20240101, 20240101, 20240102, ... (Cardinality = 365)
└── CustomerKey: 1, 2, 3, 1, 4, 2, ... (Cardinality = 500)

→ Low Cardinality ✅
→ Dictionary Encoding ดี ✅
→ ประหยัด Memory ✅
```

#### 7.2 วิธีทำให้ Foreign Keys มี Low Cardinality

**1. ใช้ Surrogate Keys (Integer):**
```
✅ ดี: ProductKey: 1, 2, 3, ... (Integer, Cardinality ต่ำ)
❌ ไม่ดี: ProductID: "P-001-ABC-DEF" (Text, Cardinality สูง)
```

**2. ใช้ Dimension Tables:**
```
✅ ดี: FactSales → ProductKey → DimProduct
❌ ไม่ดี: FactSales → ProductName (Text ใน Fact Table)
```

---

### 8. Sorted Data เพื่อสนับสนุน Encoding ⭐

#### 8.1 ความสำคัญ

**ทำไมต้องเรียงข้อมูล?**
- Sorted Data = RLE Encoding ดี = ประหยัด Memory = เร็ว

**ตัวอย่าง:**
```
❌ ไม่เรียง:
ProductKey: 3, 1, 2, 1, 3, 2, 1, 3
→ RLE Encoding ไม่มีประสิทธิภาพ

✅ เรียงแล้ว:
ProductKey: 1, 1, 1, 2, 2, 3, 3, 3
→ RLE Encoding มีประสิทธิภาพ (1:3, 2:2, 3:3)
```

#### 8.2 วิธีเรียงข้อมูล

**1. เรียงตาม Foreign Keys:**
```
✅ เรียง FactResellerSales ตาม ProductKey
→ ProductKey ซ้ำติดกัน → RLE Encoding ดี
```

**2. เรียงตาม Date:**
```
✅ เรียง FactResellerSales ตาม OrderDateKey
→ OrderDateKey ซ้ำติดกัน → RLE Encoding ดี
```

**3. เรียงใน Power Query:**
```
1. เปิด Power Query Editor
2. Sort Column (ตาม Foreign Key หรือ Date)
3. Apply Changes
```

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ แยกแยะระหว่าง Measures และ Dimension Attributes ได้
- ✅ เข้าใจโครงสร้าง Star Schema และ Snowflake Schema
- ✅ เข้าใจแนวคิด Conformed Dimensions
- ✅ ออกแบบ Dimensional Model ที่เหมาะสมกับ Power BI
- ✅ ใช้ประโยชน์จาก VertiPaq Engine ผ่าน Dimensional Model
- ✅ เข้าใจความสำคัญของ Low Cardinality และ Sorted Data

---

## 📝 สรุป

### 🎯 Key Points

1. **Measures vs Dimension Attributes:**
   - Measures = ค่าที่ต้องคำนวณ
   - Dimension Attributes = คุณลักษณะที่ใช้กรองหรือจัดกลุ่ม

2. **Star Schema (แนะนำ):**
   - Fact Table ตรงกลาง + Dimension Tables รอบๆ
   - ประสิทธิภาพสูง, ขนาดเล็ก, เข้าใจง่าย

3. **Snowflake Schema:**
   - มีหลายชั้น
   - มีปัญหาใน Power BI → แนะนำให้ลดรูปเป็น Star Schema

4. **Conformed Dimensions:**
   - Dimension Table เดียวใช้กับ Fact Tables หลายตัว
   - ลดความซ้ำซ้อน, ง่ายต่อการบำรุงรักษา

5. **Bus Matrix และ Granularity:**
   - Bus Matrix แสดงความสัมพันธ์ระหว่าง Fact Tables และ Dimensions
   - Granularity = ระดับความละเอียดของข้อมูล (Daily, Monthly, Yearly)
   - Heterogeneous Granularity = Fact Tables มี Granularity ต่างกัน → ต้อง Aggregate ก่อนเปรียบเทียบ

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](../README.md) - โครงสร้างหลักสูตร
- **01-Introduction & VertiPaq Engine** - เข้าใจ VertiPaq Engine
- **02-Data-Sources** - การเตรียมข้อมูล
- **04-Relationships** - Relationships ใน Star Schema
- **05-Dimension-Table-Design** - การออกแบบ Dimension Tables
- [Microsoft Learn: Power BI Data Modeling](https://learn.microsoft.com/power-bi/)
- [sqlbi.com: Star Schema](https://www.sqlbi.com/)

---

## 💡 Tips สำหรับการสอบ

1. **จำ Star Schema**: Fact Table ตรงกลาง, Dimension Tables รอบๆ
2. **จำ Measures vs Attributes**: Measures = คำนวณ, Attributes = กรอง/จัดกลุ่ม
3. **จำ Conformed Dimensions**: Dimension เดียวใช้กับ Fact Tables หลายตัว
4. **จำ Bus Matrix และ Granularity**: แสดงความสัมพันธ์ + ระดับความละเอียดของข้อมูล
5. **เข้าใจ Heterogeneous Granularity**: Fact Tables มี Granularity ต่างกัน → ต้อง Aggregate ก่อนเปรียบเทียบ
6. **เข้าใจ Low Cardinality และ Sorted Data**: สำคัญสำหรับ VertiPaq Engine
