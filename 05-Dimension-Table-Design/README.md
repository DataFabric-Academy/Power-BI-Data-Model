# 06 - Dimension Table Design

## เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับการออกแบบ Dimension Tables ที่มีประสิทธิภาพและถูกต้องตามหลักการ Dimensional Modeling

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 📋 หัวข้อการเรียนรู้

### 1. องค์ประกอบของ Dimension Table

#### 1.1 Surrogate Key

**Surrogate Key** เป็นคีย์หลักที่สร้างขึ้นเพื่อระบุแถวในตาราง Dimension อย่างเฉพาะเจาะจง

**คุณสมบัติ:**
- มักเป็นเลขจำนวนเต็มที่ไม่ซ้ำกัน
- ใช้ในการเชื่อมโยงกับตาราง Fact
- ไม่มีความหมายทางธุรกิจ

**ตัวอย่าง (AdventureWorksDW2025):**
- `DimProduct[ProductKey]` - ProductKey เป็น Surrogate Key
- `DimDate[DateKey]` - DateKey เป็น Surrogate Key

#### 1.2 Business Key

**Business Key** เป็นคีย์ที่มาจากระบบต้นทาง

**คุณสมบัติ:**
- มีความหมายทางธุรกิจ
- อาจเปลี่ยนแปลงได้
- ไม่ใช่ Primary Key ใน Semantic Model

**ตัวอย่าง (AdventureWorksDW2025):**
- `DimProduct[ProductAlternateKey]` - ProductAlternateKey เป็น Business Key

#### 1.3 Dimension Attributes

**Dimension Attributes** เป็นคุณลักษณะที่ให้รายละเอียดเพิ่มเติมเกี่ยวกับข้อมูล

**ตัวอย่าง (AdventureWorksDW2025):**
- `DimProduct[EnglishProductName]` - ชื่อสินค้า
- `DimProduct[Color]` - สี
- `DimProduct[Size]` - ขนาด

**วัตถุประสงค์:**
- ใช้ในการกรอง
- ใช้ในการจัดกลุ่มข้อมูล
- ใช้เป็น Hierarchy Members

---

### 2. Date Dimension

**Date Dimension** เป็น Dimension ที่สำคัญที่สุดใน Semantic Model และมีความซับซ้อนมากกว่าวิธีการออกแบบ Dimension Tables อื่นๆ

**หมายเหตุ:** เนื้อหารายละเอียดเกี่ยวกับ Date Dimension ได้ถูกย้ายไปยังโมดูล **06-Date-Dimensions-Relationships** ซึ่งครอบคลุม:
- การออกแบบ Date Dimension ที่ถูกต้อง
- Conformed Date Dimension Pattern
- Role-Playing Dimensions สำหรับ Date
- Multiple Date Relationships
- USERELATIONSHIP() กับ Date Relationships
- Time Intelligence ผ่าน Relationships

**👉 ดูรายละเอียด:** [06-Date-Dimensions-Relationships](../06-Date-Dimensions-Relationships/README.md)

---

### 3. Time Dimension (ตารางมิติเวลา)

**เหตุผล:**
- Model บน Power BI หรือ Tabular Data Model บน SSAS นั้นรองรับ Granularity ระดับวันเท่านั้น
- หากต้องการดูเวลา ต้องสร้างตารางมิติเวลาแยกต่างหาก

**โครงสร้าง:**
- `TimeKey` - Primary Key
- `Hour`, `Minute`, `Second`
- `HourOfDay` - ชั่วโมง (0-23)
- `AM/PM`

---

### 4. Role-Playing Dimensions

**Role-Playing Dimension** คือการใช้ตารางมิติเดียวกันหลายครั้งในตารางข้อเท็จจริง (Fact Table) เพื่อสะท้อนบทบาทหรือมุมมองที่แตกต่างกัน

**ตัวอย่างที่พบบ่อยที่สุด:** Date Dimension
- `FactResellerSales` มี:
  - `OrderDateKey` → DimDate (Role: Order Date)
  - `ShipDateKey` → DimDate (Role: Ship Date)
  - `DueDateKey` → DimDate (Role: Due Date)

**หมายเหตุ:** เนื้อหารายละเอียดเกี่ยวกับ Role-Playing Dimensions โดยเฉพาะกับ Date Dimension ได้ถูกย้ายไปยังโมดูล **06-Date-Dimensions-Relationships** ซึ่งครอบคลุม:
- ความหมายและวิธีการสร้าง Role-Playing Dimensions
- การใช้ Inactive Relationships
- ตัวอย่างการใช้งานกับ AdventureWorksDW2025
- การใช้ USERELATIONSHIP() กับ Role-Playing

**👉 ดูรายละเอียด:** [06-Date-Dimensions-Relationships](../06-Date-Dimensions-Relationships/README.md#2-role-playing-dimensions-ตารางมิติบทบาท)

---

### 5. Slow Changing Dimension (SCD)

**Slowly Changing Dimensions (SCD)** เป็นแนวคิดในคลังข้อมูลที่ใช้จัดการกับการเปลี่ยนแปลงของข้อมูลในตารางมิติ

#### 5.1 SCD Type 1: การเขียนทับข้อมูลเดิม

**ลักษณะ:**
- เมื่อมีการเปลี่ยนแปลงข้อมูลในมิติ ข้อมูลเก่าจะถูกเขียนทับด้วยข้อมูลใหม่
- ไม่เก็บประวัติการเปลี่ยนแปลง

**ตัวอย่าง:**
- ถ้าลูกค้าย้ายที่อยู่ จะอัพเดทที่อยู่ใหม่ทับที่อยู่เก่า

**เมื่อไหร่ใช้:**
- เมื่อไม่ต้องการเก็บประวัติ
- เมื่อข้อมูลที่เปลี่ยนไม่สำคัญต่อการวิเคราะห์

#### 5.2 SCD Type 2: การเก็บประวัติการเปลี่ยนแปลง

**ลักษณะ:**
- เมื่อมีการเปลี่ยนแปลงข้อมูลในมิติ จะเพิ่มแถวใหม่ในตารางมิติ
- แถวเก่าจะถูกเก็บไว้เพื่อรักษาประวัติการเปลี่ยนแปลง
- มีคอลัมน์ `StartDate` และ `EndDate` เพื่อระบุช่วงเวลาที่แต่ละแถวใช้ได้

**โครงสร้างคอลัมน์ใน SCD Type 2:**
- **Surrogate Key**: Key ที่สร้างขึ้นเอง (เช่น `SalesRepID`)
- **Business Key**: Key จากระบบต้นทาง (เช่น `RepSourceID`)
- **Dimension Attributes**: Attributes ที่อาจเปลี่ยนแปลง (เช่น `FirstName`, `LastName`, `Region`)
- **StartDate**: วันที่เริ่มต้น (วันที่ Record เริ่มใช้ได้)
- **EndDate**: วันที่สิ้นสุด (วันที่ Record สิ้นสุด - มักใช้ 9999-12-31 สำหรับ Record ปัจจุบัน)
- **IsCurrent**: Flag ที่บอกว่า Record เป็นปัจจุบันหรือไม่ (TRUE/FALSE)
- **Hash**: Hash Value ที่สร้างจาก Dimension Attributes เพื่อตรวจสอบการเปลี่ยนแปลง

**ตัวอย่างจากไฟล์ตัวอย่าง (`Data Model SCD.pbix`):**

**ตาราง FullLoad (SCD Type 2 Dimension):**

| SalesRepID | RepSourceID | FirstName | LastName | Region | StartDate | EndDate | IsCurrent | Hash |
|-----------|-------------|-----------|----------|--------|-----------|---------|-----------|------|
| 1 | 101 | John | Doe | North | 2023-01-01 | 2023-06-30 | FALSE | ... |
| 2 | 101 | John | Doe | South | 2023-07-01 | 9999-12-31 | TRUE | ... |
| 3 | 102 | Jane | Smith | East | 2023-01-01 | 9999-12-31 | TRUE | ... |

**การทำงาน:**
- SalesRepID = 1: Record เก่า (John Doe อยู่ใน North จนถึง 2023-06-30)
- SalesRepID = 2: Record ใหม่ (John Doe ย้ายไป South ตั้งแต่วันที่ 2023-07-01)
- RepSourceID = 101: Business Key เดียวกัน (John Doe คนเดียวกัน)

**เมื่อไหร่ใช้:**
- เมื่อต้องการเก็บประวัติการเปลี่ยนแปลง
- เมื่อต้องการวิเคราะห์ข้อมูลตามช่วงเวลา
- เมื่อต้องการทราบประวัติการเปลี่ยนแปลงของ Dimension Attributes

**การเตรียม SCD Type 2 ด้วย Power Query:**

**ขั้นตอนการทำงาน:**

1. **สร้าง Hash** - สร้าง Hash จาก Dimension Attributes ที่สำคัญ
   ```m
   Hash = Binary.ToText(
     Text.ToBinary(
       Text.Combine(
         List.Transform({[RepSourceID],[FirstName],[LastName],[Region]}, 
           each if _ = null then "" else Text.From(_)
         ), "|"
       )
     ), BinaryEncoding.Hex
   )
   ```

2. **เปรียบเทียบ Source กับ Dimension** - หา Records ใหม่และ Records ที่เปลี่ยนแปลง
   - **NewRecords**: Hash ใหม่ (ไม่มีใน Dimension)
   - **RecordsToUpdate**: Hash เปลี่ยน (มีใน Dimension แต่ Hash ไม่ตรง)

3. **สร้าง NewRecords** - เพิ่ม Records ใหม่
   - สร้าง Surrogate Key ใหม่
   - ตั้ง StartDate = วันนี้
   - ตั้ง EndDate = 9999-12-31
   - ตั้ง IsCurrent = TRUE

4. **อัพเดท RecordsToUpdate** - ปิด Records เก่า
   - ตั้ง IsCurrent = FALSE
   - ตั้ง EndDate = วันนี้

5. **รวมผลลัพธ์** - รวม NewRecords และ RecordsToUpdate เข้ากับ Dimension เดิม

**👉 ดูตัวอย่างโค้ดที่ละเอียด:** [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

**👉 ดูแบบฝึกหัด:** [EXERCISES.md](./EXERCISES.md)

**ไฟล์ตัวอย่าง:**
- `Data Model SCD.pbix` - Semantic Model ที่มี SCD Type 2 Implementation พร้อม Report ที่แสดงผลลัพธ์

> **หมายเหตุ:** ไฟล์ตัวอย่างเป็นสื่อการสอนของผู้สอน (Trainer Material) — ขอไฟล์ได้จากผู้สอนระหว่างเรียน ไม่ได้แจกจ่ายผ่าน repository นี้

---

### 6. การปิดใช้งาน IsAvailableInMDX

**IsAvailableInMDX** เป็นคุณสมบัติของแอตทริบิวต์ในตารางมิติ

**ประโยชน์ของการปิด (ตั้งค่า FALSE):**
- ลดขนาดของโมเดลข้อมูล
- ปรับปรุงประสิทธิภาพการประมวลผล

**ข้อควรระวัง:**
- ถ้า Attributes ที่ต้องนำไปสร้าง Hierarchies ไม่สามารถกำหนด IsAvailableInMDX เป็น FALSE ได้
- Excel ใช้ MDX ในการเชื่อมต่อกับโมเดลข้อมูล การตั้งค่า IsAvailableInMDX เป็น FALSE ทำให้ผู้ใช้ Excel ไม่สามารถเข้าถึงแอตทริบิวต์นั้นใน PivotTable ได้

**วิธีการปิดใช้งาน:**

**วิธีที่ 1: ใช้ TMDL View ใน Power BI Desktop (แนะนำ - GA)**
1. เลือกไปที่มุมมอง TMDL View ใน Power BI Desktop
2. ใน Data pane เลือก Model > Tables
3. ลากคอลัมน์จากตาราง Dimension ลงใน Design pane
4. จะปรากฏ TMDL เฉพาะส่วนของคอลัมน์ที่เลือก
5. เพิ่ม `IsAvailableInMDX = false` ลงใน Script
6. กดปุ่ม APPLY

> **ของใหม่:** TMDL View ใน Power BI Desktop เป็น GA แล้ว (ตั้งแต่กันยายน 2025) — ดู [Work with TMDL view - Microsoft Learn](https://learn.microsoft.com/power-bi/transform-model/desktop-tmdl-view)

**วิธีที่ 2: ใช้ Tabular Editor**
1. ในเมนู External Tools ของ Power BI Desktop เลือก Tabular Editor
2. ใน TOM Explorer เลือกคอลัมน์จากตาราง Dimension
3. จะพบ Property ชื่อ `Available in MDX` ให้ตั้งค่าเป็น False
4. กด "Save the changes to the connected database"

---

### 7. Calculated Columns สำหรับ Dimension Tables

**Calculated Columns** ใน Dimension Tables มักใช้เพื่อ:
- ดึงข้อมูลจาก Related Tables (ใช้ RELATED())
- สร้าง Attributes จาก Relationships
- คำนวณค่าคงที่ที่ใช้ในการ Filter หรือ Sort

#### 7.1 เมื่อไหร่ควรใช้ Calculated Columns ใน Dimension Tables

✅ **ใช้ Calculated Columns เมื่อ:**
- ต้องการดึงข้อมูลจาก Related Table (ใช้ RELATED())
- ต้องการค่าคงที่ที่คำนวณจากหลายคอลัมน์
- ต้องการใช้ในการ Filter หรือ Sort
- ต้องการใช้ในการสร้าง Hierarchies

❌ **ไม่ควรใช้ Calculated Columns เมื่อ:**
- ต้องการคำนวณแบบ Aggregate (SUM, COUNT, AVERAGE) → ใช้ Measures แทน
- ต้องการค่าที่เปลี่ยนตาม Visual Context → ใช้ Measures แทน

#### 7.2 ตัวอย่าง: การใช้ RELATED() ใน Dimension Tables

**ตัวอย่างที่ 1: ดึงข้อมูลจาก Related Table**

**สถานการณ์:** สร้าง Calculated Column ในตาราง `DimProduct` เพื่อดึงชื่อหมวดหมู่สินค้า

```dax
Category Name = RELATED(DimProductCategory[EnglishProductCategoryName])
```

**อธิบาย:**
- ใช้ `RELATED()` เพื่อดึงข้อมูลจาก Related Table
- ต้องมี Relationship ระหว่าง DimProduct และ DimProductCategory

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

#### 7.3 Calculated Tables สำหรับ Dimension Tables

**Calculated Tables** มักใช้เพื่อ:
- สร้าง Dimension Tables แบบ Dynamic
- รวมข้อมูลจากหลาย Sources
- สร้าง Lookup Tables

**ตัวอย่าง:**
```dax
Product Categories = 
DISTINCT(
    SELECTCOLUMNS(
        DimProduct,
        "CategoryName", RELATED(DimProductCategory[EnglishProductCategoryName]),
        "SubcategoryName", RELATED(DimProductSubcategory[EnglishProductSubcategoryName])
    )
)
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 8. Attribute Hierarchies

**Attribute Hierarchies** เป็นส่วนสำคัญของ Dimension Tables Design ช่วยให้การวิเคราะห์ข้อมูลหลายมุมมองง่ายขึ้น

#### 8.1 Natural Hierarchy (ลำดับชั้นตามธรรมชาติ)

**Natural Hierarchy** คือโครงสร้างที่แต่ละระดับของลำดับชั้นมีความสัมพันธ์แบบหนึ่งต่อหนึ่งหรือหนึ่งต่อกลุ่มกับระดับถัดไป

**ตัวอย่าง:**
- ลำดับชั้นทางภูมิศาสตร์: ประเทศ > จังหวัด > อำเภอ > ตำบล
- ลำดับชั้นทางเวลา: ปี > ไตรมาส > เดือน > วัน

**คุณสมบัติ:**
- ข้อมูลในลำดับชั้นมีความสอดคล้องและเป็นไปตามตรรกะที่ชัดเจน
- แต่ละระดับมีความสัมพันธ์แบบ One-to-Many กับระดับถัดไป

---

#### 8.2 Non-Natural Hierarchy (ลำดับชั้นที่ไม่เป็นธรรมชาติ)

**Non-Natural Hierarchy** คือโครงสร้างที่ความสัมพันธ์ระหว่างระดับต่าง ๆ ไม่เป็นไปตามตรรกะที่ชัดเจน หรือมีความสัมพันธ์แบบหลายต่อหลายกับระดับถัดไป

**ตัวอย่าง:**
- ลำดับชั้นผลิตภัณฑ์: หมวดหมู่สินค้า > ผู้จัดจำหน่าย > ยี่ห้อ
- ลำดับชั้นพนักงาน: ฝ่าย > กลุ่ม > ตำแหน่ง

**คุณสมบัติ:**
- ความสัมพันธ์ระหว่างระดับไม่เป็นไปตามตรรกะที่ชัดเจน
- อาจมีความสัมพันธ์แบบ Many-to-Many กับระดับถัดไป

---

#### 8.3 การสร้างหลาย Hierarchies

**ใน Date Dimension** สามารถสร้างหลาย Hierarchies เพื่อช่วยให้การวิเคราะห์หลายมุมมองง่ายขึ้น

**ตัวอย่าง Hierarchies ใน Date Dimension:**
- Calendar Year Hierarchy: Year > Quarter > Month > Day
- Fiscal Year Hierarchy: Fiscal Year > Fiscal Quarter > Fiscal Month > Day
- Holiday Calendar Hierarchy: Year > Is Holiday > Day

---

#### 8.4 Parent-Child Hierarchy

**Parent-Child Hierarchy** คือโครงสร้างข้อมูลที่แต่ละแถวข้อมูลในตาราง Dimension มีความสัมพันธ์กับแถวข้อมูลอื่น สามารถนำไปจัดลำดับชั้นได้

**ตัวอย่าง:**
- Employee Organization Structure
- Account Structure
- Product Category Structure

**โครงสร้าง:**
- มีคอลัมน์ `EmployeeKey` - Key ของ Employee
- มีคอลัมน์ `ParentEmployeeKey` - Key ของ Manager/Parent

---

#### 8.5 PATH Functions สำหรับ Parent-Child Hierarchy

**PATH()** - สร้างเส้นทางจากลูกไปยังพ่อแม่ทั้งหมด
```dax
Path = PATH(Employee[EmployeeKey], Employee[ParentEmployeeKey])
```

**PATHITEM()** - ดึงค่าที่อยู่ในตำแหน่งที่กำหนดในเส้นทาง
```dax
Org Level 1 = PATHITEM(Employee[Path], 1, INTEGER)
```

**PATHLENGTH()** - หาความยาวของเส้นทาง (จำนวนระดับ)
```dax
PathLEN = PATHLENGTH(Employee[Path])
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

#### 8.6 การสร้าง Parent-Child Hierarchy

**ขั้นตอนการสร้าง:**

1. **เตรียมข้อมูล:**
   - มีคอลัมน์ EmployeeKey (Child)
   - มีคอลัมน์ ParentEmployeeKey (Parent)

2. **สร้าง Calculated Columns:**
   - `Path = PATH(Employee[EmployeeKey], Employee[ParentEmployeeKey])`
   - `PathLEN = PATHLENGTH(Employee[Path])`

3. **สร้าง Org Level Columns:**
   ```dax
   Org Level 1 = 
   LOOKUPVALUE(
       Employee[FullName],
       Employee[EmployeeKey],
       PATHITEM(Employee[Path], 1, INTEGER)
   )
   ```

4. **สร้าง Hierarchy:**
   - สร้าง Hierarchy ชื่อ "Organization"
   - เพิ่ม Levels: Org Level 1, Org Level 2, Org Level 3, ...

**👉 ตัวอย่าง:** Parent-Child Hierarchy จาก AdventureWorksDW2025

---

#### 8.7 Best Practices สำหรับ Hierarchies

**การซ่อน Attributes:**
- ซ่อน Dimension Key (EmployeeKey, ParentEmployeeKey)
- ซ่อน Path Column (ใช้สำหรับคำนวณเท่านั้น)
- ซ่อน Org Level Columns ที่ไม่ใช้
- ซ่อน Attributes ที่ถูกนำไปสร้าง Hierarchies แล้ว

**Performance:**
- Parent-Child Hierarchy อาจมี Performance ต่ำกว่าปกติ
- พิจารณาใช้ Flattened Hierarchy แทนถ้าเป็นไปได้

---

#### 8.8 Parent-Child แบบรองรับความลึกที่เปลี่ยนแปลง (Dynamic Levels) ⭐

**ปัญหา:** User Hierarchy ต้องมี physical columns จำนวนคงที่ — ถ้าสร้าง `Org Level 1-5` ตามความลึกที่เห็น "วันนี้" แล้วองค์กรเพิ่มชั้นที่ 6 ในอนาคต การ drill-down จะ**ตัดขาดเงียบ ๆ โดยไม่มี error** (ผู้ใช้ไม่รู้ว่าเสียข้อมูล)

**วิธีรับมือ 3 ชั้น:**

1. **สร้าง Level เผื่อตาม `PATHLENGTH` ไม่ใช่ตามสายตา** — ข้อมูลปัจจุบันของ DimEmployee ลึกสุด 5 ชั้น (พนักงาน 296 คน) แต่สร้างคอลัมน์ไว้ถึง Org Level 7:

   ```dax
   Org Level 6 =
   LOOKUPVALUE(
       DimEmployee[EmployeeName],
       DimEmployee[EmployeeKey],
       PATHITEM(DimEmployee[EmpPath], 6, INTEGER)
   )
   ```

2. **เปิด `Hide blank members` (HideBlankMembers) ที่ Hierarchy** — ชั้นที่ blank (เกินความลึกของกิ่งนั้น) จะไม่แสดงใน visual กิ่งสั้น drill สั้น กิ่งลึก drill ลึก แม้ hierarchy มี 7 ชั้น ผู้ใช้เห็น "เท่าที่ข้อมูลมี" — นี่คือความ dynamic ที่ Power BI ให้ได้จริง

3. **Measure เฝ้าความลึก** เป็นตัวเตือนเมื่อข้อมูลลึกเกิน column ที่เผื่อไว้:

   ```dax
   'Max Org Depth' = MAX ( DimEmployee[EmpPathLength] )
   ```

   ถ้าค่านี้เกิน 7 → ต้องเพิ่ม `Org Level 8` และใส่เข้า Hierarchy ทันที (ไม่งั้นการ drill ตัดขาดโดยไม่มีสัญญาณเตือน)

**ไฟล์ตัวอย่าง:** `Data Model - Reseller Sales.pbix` — *Trainer Material — ขอไฟล์จากผู้สอน*

---

### 9. การซ่อน Attributes

**ควรซ่อน Attributes ต่อไปนี้:**

**ตาราง Dimension:**
- Dimension Key (เช่น `ProductKey`, `DateKey`) - ใช้สำหรับ Relationships เท่านั้น
- Attributes ที่ถูกนำไปสร้าง Hierarchies แล้ว
- Attributes ที่ไม่ใช้

**ตาราง Fact:**
- Dimension Keys - ใช้สำหรับ Relationships เท่านั้น
- ยกเลิก Summarized และซ่อน Implicit Measure

**ประโยชน์:**
- ลดความซับซ้อนในรายงาน
- ป้องกันการใช้งานผิดพลาด
- ปรับปรุงประสิทธิภาพ

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ ออกแบบ Dimension Table ที่มีประสิทธิภาพ
- ✅ เข้าใจโครงสร้างของ Dimension Tables (Surrogate Key, Business Key, Attributes)
- ✅ ออกแบบ Time Dimension สำหรับข้อมูลระดับเวลา
- ✅ เข้าใจ Role-Playing Dimensions (ดูรายละเอียดในโมดูล 06)
- ✅ จัดการ SCD Type 1 และ Type 2
- ✅ สร้าง Calculated Columns สำหรับ Dimension Tables
- ✅ สร้าง Calculated Tables สำหรับ Dimension Tables
- ✅ สร้าง Attribute Hierarchies (Natural, Non-Natural, Parent-Child)
- ✅ ใช้ PATH(), PATHITEM(), PATHLENGTH() Functions
- ✅ ปิดใช้งาน IsAvailableInMDX
- ✅ ซ่อน Attributes ที่ไม่จำเป็น

---

## 📚 เอกสารที่เกี่ยวข้อง

- **03-Data-Modeling-Basics**: พื้นฐาน Dimensional Modeling
- **04-Relationships**: Relationships และ RELATED() Function
- **06-Date-Dimensions-Relationships**: Date Dimensions, Role-Playing Dimensions, และ Date Relationships
- **[CODE-EXAMPLES.md](./CODE-EXAMPLES.md)** - ตัวอย่างโค้ด Power Query และ DAX
- **[EXERCISES.md](./EXERCISES.md)** - แบบฝึกหัด

---

---

## 📚 เอกสารเพิ่มเติม

- **[CODE-EXAMPLES.md](./CODE-EXAMPLES.md)** - ตัวอย่างโค้ด Power Query และ DAX สำหรับ SCD Type 2
- **[EXERCISES.md](./EXERCISES.md)** - แบบฝึกหัด SCD Type 2

### ไฟล์ตัวอย่างที่แนะนำ

**หมายเหตุ:** ตัวอย่างในโมดูลนี้ใช้ AdventureWorksDW2025 เป็น Data Source

---

**🎉 ขอแสดงความยินดี! คุณได้เรียนจบโมดูล Dimension Table Design แล้ว!**

**ขั้นตอนต่อไป:** 
- ฝึกปฏิบัติตาม [EXERCISES.md](./EXERCISES.md)
- ดูตัวอย่างโค้ดใน [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)
- เรียนโมดูล 06-Date-Dimensions-Relationships

