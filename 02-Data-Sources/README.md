# 02 - Data Sources

## 📚 เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับการเชื่อมต่อและจัดการแหล่งข้อมูลใน Power BI รวมถึงการเตรียมข้อมูลให้เหมาะสมกับการใช้งานใน Semantic Model

> **Data Source หลักของหลักสูตรนี้:** หลักสูตรนี้ใช้ **AdventureWorksDW2025** เป็น Data Source หลักสำหรับตัวอย่างและแบบฝึกหัดทั้งหมด

---

## 📋 หัวข้อการเรียนรู้

### 1. ประเภทของแหล่งข้อมูล (Data Source Types)

#### 1.1 ไฟล์ (Files)

**ประเภทที่รองรับ:**
- **Excel** (.xlsx, .xls)
- **CSV** (.csv)
- **JSON** (.json)
- **XML** (.xml)
- **PDF** (.pdf)
- **Text** (.txt)

**ข้อดี:**
- ✅ ใช้งานง่าย
- ✅ ไม่ต้องมี Database Server

**ข้อเสีย:**
- ❌ ไม่เหมาะกับข้อมูลขนาดใหญ่
- ❌ ต้อง Refresh เอง

#### 1.2 ฐานข้อมูล (Databases)

**ประเภทที่รองรับ:**
- **SQL Server**
- **Azure SQL Database**
- **MySQL**
- **PostgreSQL**
- **Oracle**

**ข้อดี:**
- ✅ เหมาะกับข้อมูลขนาดใหญ่
- ✅ Query Performance ดี
- ✅ Refresh อัตโนมัติได้

**ข้อเสีย:**
- ❌ ต้องมี Database Server
- ❌ ต้องมี Connection String

#### 1.3 เว็บและบริการออนไลน์ (Web & Online Services)

**ประเภทที่รองรับ:**
- **Web** (URL)
- **SharePoint**
- **Dynamics 365**
- **Google Analytics**
- **Salesforce**

**ข้อดี:**
- ✅ เข้าถึงข้อมูลออนไลน์ได้
- ✅ Real-time Data

**ข้อเสีย:**
- ❌ ต้องมี Internet Connection
- ❌ อาจมี Rate Limiting

---

### 2. การเชื่อมต่อแหล่งข้อมูล

#### 2.1 ขั้นตอนการเชื่อมต่อ

**วิธีทั่วไป:**
1. เปิด Power BI Desktop
2. เลือก **Get Data**
3. เลือกประเภท Data Source
4. กรอกข้อมูลการเชื่อมต่อ
5. กด **Connect**

**ตัวอย่าง: เชื่อมต่อ SQL Server**
```
1. Get Data > SQL Server
2. Server: server-name
3. Database: database-name
4. Authentication: Windows/Database
5. Connect
```

#### 2.2 AdventureWorksDW2025 - Data Source หลัก ⭐

**AdventureWorksDW2025** เป็น Data Warehouse ตัวอย่างจาก Microsoft สำหรับ SQL Server 2025 ที่มีโครงสร้าง Star Schema ที่สมบูรณ์แบบสำหรับการเรียนรู้ (โครงสร้างตารางเหมือนกับ AdventureWorksDW2025 เวอร์ชันก่อนหน้า)

##### การเชื่อมต่อไปยัง Azure SQL Database

**ข้อมูลการเชื่อมต่อ:**
- **Server Name**: `ake.database.windows.net`
- **Database Name**: `AdventureWorksDW2025`
- **Authentication**: Database Authentication
- **Login Name**: `dwuser`
- **Password**: `<password ที่ได้รับจากผู้สอน>`

**ขั้นตอนการเชื่อมต่อ:**
1. ใน Power BI Desktop เลือก **Get Data** > **Azure** > **Azure SQL Database**
2. กรอกข้อมูลการเชื่อมต่อ:
   - Server: `ake.database.windows.net`
   - Database: `AdventureWorksDW2025`
3. เลือก **Database Authentication**
4. กรอก Login Name และ Password
5. กด **Connect**

**หมายเหตุ:**
- ใช้การเชื่อมต่อแบบ **Import Mode** สำหรับตัวอย่างในหลักสูตร
- ถ้าต้องการใช้ DirectQuery ให้ระวังเรื่อง Performance

##### Fact Tables (ตารางข้อเท็จจริง)

**Fact Tables ใน AdventureWorksDW2025:**
- **FactInternetSales** - การขายผ่าน Internet
- **FactResellerSales** - การขายผ่าน Reseller  
- **FactSalesQuota** - โควต้าขาย
- **FactCurrencyRate** - อัตราแลกเปลี่ยน

**ลักษณะ Fact Tables:**
- มี Measures (เช่น SalesAmount, OrderQuantity)
- มี Foreign Keys (ProductKey, DateKey, CustomerKey)
- มีจำนวน Rows มาก

##### Dimension Tables (ตารางมิติ)

**Dimension Tables ใน AdventureWorksDW2025:**
- **DimProduct** - สินค้า (Product Attributes)
- **DimCustomer** - ลูกค้า (Customer Attributes)
- **DimReseller** - ผู้จำหน่าย (Reseller Attributes)
- **DimDate** - วันที่ (Date Attributes)
- **DimGeography** - ภูมิศาสตร์ (Geography Attributes)
- **DimEmployee** - พนักงาน (Employee Attributes)
- **DimPromotion** - โปรโมชัน (Promotion Attributes)
- **DimCurrency** - สกุลเงิน (Currency Attributes)
- **DimSalesTerritory** - อาณาเขตขาย (Sales Territory Attributes)

**ลักษณะ Dimension Tables:**
- มี Attributes (เช่น ProductName, Category, Brand)
- มี Surrogate Key (ProductKey, CustomerKey)
- มีจำนวน Rows น้อยกว่า Fact Tables

##### วิธีการดาวน์โหลดและติดตั้ง AdventureWorksDW2025

**วิธีที่ 1: ดาวน์โหลดไฟล์ `.bak` โดยตรง**
1. ดาวน์โหลด [AdventureWorksDW2025.bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2025.bak)
2. Restore ฐานข้อมูลใน SQL Server หรือ Azure SQL Managed Instance ผ่าน SSMS
3. ดูรายละเอียดทั้งหมดได้ที่ [AdventureWorks sample databases - Microsoft Learn](https://learn.microsoft.com/sql/samples/adventureworks-install-configure)

**วิธีที่ 2: ดาวน์โหลดจาก GitHub Releases**
1. ไปที่ [Microsoft SQL Server Samples Releases](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)
2. เลือกไฟล์ `AdventureWorksDW2025.bak` (Data Warehouse version สำหรับ SQL Server 2025)
3. Restore ฐานข้อมูลใน SQL Server

**วิธีที่ 3: ใช้ Azure SQL Database ที่เตรียมให้**
- เชื่อมต่อไปยัง `ake.database.windows.net` / `AdventureWorksDW2025` ตามข้อมูลการเชื่อมต่อด้านบน

> **หมายเหตุ:** AdventureWorksDW2025 มีโครงสร้างตาราง (Schema) เหมือนกับ AdventureWorksDW2025 เวอร์ชันก่อนหน้า ทั้ง Fact Tables (FactInternetSales, FactResellerSales, FactSalesQuota, FactCurrencyRate) และ Dimension Tables (DimProduct, DimCustomer, DimDate ฯลฯ) จึงใช้กับแบบฝึกหัดทุกโมดูลของหลักสูตรนี้ได้โดยตรง

---

### 3. Import Mode vs DirectQuery Mode vs Dual Mode vs Direct Lake Mode

#### 3.1 Import Mode ⭐ (แนะนำสำหรับหลักสูตรนี้)

**วิธีการทำงาน:**
- ดึงข้อมูลทั้งหมดจาก Data Source
- เก็บข้อมูลใน Memory (VertiPaq Engine)
- Query เร็วมาก (เพราะข้อมูลอยู่ใน Memory)
- **ไม่มีการใช้ On Demand Loading**: โหลดข้อมูลทั้งหมดก่อน (Pre-loading)

**ความแตกต่างจาก DirectQuery/Direct Lake:**
- Import Mode = **Pre-loading** (โหลดข้อมูลทั้งหมดก่อน)
- DirectQuery/Direct Lake = **On Demand Loading** (โหลดเฉพาะที่ต้องการ)

**ข้อดี:**
- ✅ Query เร็วมาก (ข้อมูลอยู่ใน Memory แล้ว)
- ✅ ทำงาน Offline ได้
- ✅ รองรับ DAX Functions ทั้งหมด
- ✅ เหมาะกับข้อมูลขนาดเล็ก-กลาง

**ข้อเสีย:**
- ❌ ใช้ Memory มาก (โหลดข้อมูลทั้งหมด)
- ❌ Refresh ช้า (ถ้าข้อมูลเยอะ - ต้องโหลดทั้งหมดใหม่)
- ❌ ต้อง Refresh เพื่ออัพเดทข้อมูล

**ใช้เมื่อ:**
- ข้อมูลขนาดเล็ก-กลาง (< 1GB)
- ต้องการ Performance สูง
- ไม่จำเป็นต้อง Real-time

#### 3.2 DirectQuery Mode

**วิธีการทำงาน:**
- ไม่เก็บข้อมูลใน Memory
- Query โดยตรงไปที่ Data Source ทุกครั้ง
- ข้อมูล Always Up-to-date
- **On Demand Loading (Lazy Loading)**: Query เฉพาะข้อมูลที่ต้องการเท่านั้น ไม่โหลดข้อมูลทั้งหมดก่อน

**On Demand Loading คืออะไร? ⭐**
- **On Demand Loading** = การโหลดข้อมูลแบบตามต้องการ (Lazy Loading)
- Query เฉพาะข้อมูลที่จำเป็นเมื่อมีการใช้งานจริง
- ไม่โหลดข้อมูลทั้งหมดเข้า Memory ล่วงหน้า
- **ตัวอย่าง**: ถ้า User เลือกดู Sales แยกตาม Product Category เท่านั้น → Query เฉพาะข้อมูลที่เกี่ยวข้องเท่านั้น

**ข้อดี:**
- ✅ ข้อมูล Always Up-to-date
- ✅ **ใช้ Memory น้อยมาก** (เพราะ On Demand Loading - โหลดเฉพาะที่ต้องการ)
- ✅ เหมาะกับข้อมูลขนาดใหญ่
- ✅ ไม่ต้องโหลดข้อมูลทั้งหมดก่อน

**ข้อเสีย:**
- ❌ Query ช้า (ต้อง Query ไปที่ Database)
- ❌ ไม่สามารถทำงาน Offline ได้
- ❌ รองรับ DAX Functions จำกัด
- ❌ ต้องมี Connection ต่อเนื่อง

**ใช้เมื่อ:**
- ข้อมูลขนาดใหญ่ (> 1GB)
- ต้องการ Real-time Data
- มี Database Performance ดี

#### 3.3 Dual Mode

**วิธีการทำงาน:**
- รองรับทั้ง Import และ DirectQuery
- Power BI เลือก Mode อัตโนมัติ

**ข้อดี:**
- ✅ ยืดหยุ่น
- ✅ รองรับทั้งสอง Mode

**ข้อเสีย:**
- ❌ ซับซ้อน
- ❌ อาจสับสน

**ใช้เมื่อ:**
- มีทั้งข้อมูลขนาดเล็กและใหญ่
- ต้องการความยืดหยุ่น

#### 3.4 Direct Lake Mode ⭐ (Microsoft Fabric / Power BI Premium)

**Direct Lake Mode** = ฟีเจอร์ใหม่ใน Power BI ที่ให้ Performance ใกล้เคียง Import Mode แต่ไม่ต้องใช้ Memory มาก และข้อมูล Always Up-to-date

**วิธีการทำงาน:**
- อ่านข้อมูลโดยตรงจาก **OneLake** (Microsoft Fabric Lakehouse) ในรูปแบบ **Delta Lake**
- ไม่ต้อง Import ข้อมูลเข้า Memory
- ไม่ต้อง Query ไปที่ Database (ไม่เหมือน DirectQuery)
- ใช้ **Parquet format** ที่เป็น Columnar Storage
- **On Demand Loading (Lazy Loading)**: อ่านเฉพาะส่วนที่จำเป็นจาก Parquet files ไม่โหลดข้อมูลทั้งหมดเข้า Memory

**On Demand Loading ใน Direct Lake Mode ⭐**
- **On Demand Loading** = การอ่านข้อมูลแบบตามต้องการ
- อ่านเฉพาะ **Columns และ Rows ที่จำเป็น** จาก Parquet files
- ไม่โหลดข้อมูลทั้งหมดเข้า Memory (ต่างจาก Import Mode)
- ใช้ประโยชน์จาก **Columnar Storage** (Parquet) → อ่านเฉพาะ Columns ที่ต้องการ
- **ตัวอย่าง**: ถ้า Query ต้องการแค่ ProductKey และ SalesAmount → อ่านเฉพาะ 2 Columns นี้ ไม่ต้องอ่าน Columns อื่น

**ทำไม Direct Lake เร็วกว่า DirectQuery?**
- DirectQuery: Query ไปที่ Database → ต้องรอ Database ประมวลผล → ช้า
- Direct Lake: อ่านจาก Parquet files โดยตรง (Columnar Storage) → ไม่ต้องรอ Database → เร็วมาก
- ทั้งสองใช้ On Demand Loading แต่ Direct Lake เร็วกว่ามาก

**ข้อดี:**
- ✅ **Performance ดีมาก** (ใกล้เคียง Import Mode)
- ✅ **ข้อมูล Always Up-to-date** (ไม่ต้อง Refresh)
- ✅ **ใช้ Memory น้อยมาก** (On Demand Loading - อ่านเฉพาะส่วนที่จำเป็น)
- ✅ **รองรับข้อมูลขนาดใหญ่** (ไม่มีขีดจำกัดเหมือน Import Mode)
- ✅ **รองรับ DAX Functions ทั้งหมด** (เหมือน Import Mode)
- ✅ **ใช้ประโยชน์จาก VertiPaq Engine** เต็มที่
- ✅ **On Demand Loading** = อ่านเฉพาะ Columns และ Rows ที่ต้องการ → ประหยัด Memory และเร็ว

**ข้อเสีย:**
- ❌ **ต้องใช้ Microsoft Fabric** หรือ **Power BI Premium/PPU**
- ❌ **ข้อมูลต้องอยู่ใน OneLake** (Delta Lake format)
- ❌ **ต้องมี Lakehouse หรือ Data Warehouse ใน Fabric**

**ใช้เมื่อ:**
- ✅ มี **Microsoft Fabric** หรือ **Power BI Premium/PPU**
- ✅ ข้อมูลอยู่ใน **OneLake** (Lakehouse หรือ Data Warehouse)
- ✅ ต้องการ **Performance สูง + ข้อมูล Real-time**
- ✅ ข้อมูลขนาดใหญ่ (เกินขนาดที่ Import Mode รองรับ)

**ของใหม่: Direct Lake ใน Power BI Desktop (GA ตั้งแต่กันยายน 2025) ⭐**
- **Live edit** Direct Lake semantic model ได้จาก Power BI Desktop โดยตรง การแก้ไขถูก apply ไปที่ Fabric ทันที (Remote Modeling)
- รวมตาราง **Direct Lake และ Import ไว้ใน semantic model เดียวกันได้** (ตั้งแต่พฤษภาคม 2025) — ยืดหยุ่นขึ้นสำหรับ Composite Models
- ใช้ร่วมกับ TMDL View และ DAX Query View ใน Desktop ได้
- ดูเพิ่มเติม: [Direct Lake in Power BI Desktop - Microsoft Learn](https://learn.microsoft.com/fabric/fundamentals/direct-lake-power-bi-desktop)

**ข้อกำหนด:**
1. **ต้องใช้ Microsoft Fabric** หรือ **Power BI Premium/PPU**
2. **ข้อมูลต้องอยู่ใน OneLake** ในรูปแบบ **Delta Lake** (Parquet format)
3. **ต้องมี Lakehouse** หรือ **Data Warehouse** ใน Fabric
4. **Power BI Dataset ต้องเชื่อมต่อกับ Fabric Lakehouse/Warehouse**

**ตัวอย่างการใช้งาน:**
```
1. สร้าง Lakehouse ใน Microsoft Fabric
2. อัพโหลดข้อมูลเป็น Delta Lake (Parquet format)
3. สร้าง Power BI Dataset เชื่อมต่อกับ Lakehouse
4. เลือก Direct Lake Mode
5. ไม่ต้อง Import หรือ DirectQuery
```

**เปรียบเทียบกับ Import Mode และ DirectQuery:**

| ลักษณะ | Import Mode | DirectQuery Mode | Direct Lake Mode |
|--------|-------------|------------------|------------------|
| **Performance** | ⭐⭐⭐⭐⭐ เร็วมาก | ⭐⭐ ช้า | ⭐⭐⭐⭐⭐ เร็วมาก |
| **ข้อมูล Real-time** | ❌ ต้อง Refresh | ✅ Always Up-to-date | ✅ Always Up-to-date |
| **ใช้ Memory** | ❌ มาก (โหลดทั้งหมด) | ✅ น้อยมาก (On Demand) | ✅ น้อยมาก (On Demand) |
| **On Demand Loading** | ❌ ไม่ใช่ | ✅ Query เฉพาะที่ต้องการ | ✅ อ่านเฉพาะ Columns/Rows ที่ต้องการ |
| **รองรับข้อมูลขนาดใหญ่** | ❌ จำกัด | ✅ ไม่จำกัด | ✅ ไม่จำกัด |
| **รองรับ DAX Functions** | ✅ ทั้งหมด | ❌ จำกัด | ✅ ทั้งหมด |
| **ต้องการ Premium** | ❌ ไม่ต้อง | ❌ ไม่ต้อง | ✅ ต้อง |
| **ข้อมูลต้องอยู่ที่** | Source DB/File | Source Database | OneLake (Delta Lake) |

**สรุป:**
- **Direct Lake Mode** = Performance ของ Import Mode + Real-time ของ DirectQuery + On Demand Loading
- **On Demand Loading** = Key Feature ที่ทำให้ใช้ Memory น้อย แต่ Performance ยังดี
- เหมาะสำหรับองค์กรที่มี **Microsoft Fabric** และต้องการ Performance สูง + ข้อมูล Real-time
- เป็นทางเลือกที่ดีกว่า DirectQuery เมื่อต้องการ Performance สูง

---

### 4. Composite Models

#### 4.1 แนวคิด

**Composite Models:**
- ผสมผสาน Import, DirectQuery, และ/หรือ Direct Lake ใน Model เดียว
- บาง Table ใช้ Import, บาง Table ใช้ DirectQuery หรือ Direct Lake

**ตัวอย่าง:**
```
Import Tables:
- DimProduct (เล็ก, เปลี่ยนไม่บ่อย)
- DimDate (เล็ก, เปลี่ยนไม่บ่อย)

DirectQuery Tables:
- FactSales (ใหญ่, เปลี่ยนบ่อย, ไม่มีใน OneLake)

Direct Lake Tables:
- FactInventory (ใหญ่, เปลี่ยนบ่อย, อยู่ใน OneLake/Fabric)
```

#### 4.2 ข้อควรระวัง

**ปัญหา:**
- ❌ Performance อาจลดลง
- ❌ Relationships อาจซับซ้อน

**ใช้เมื่อ:**
- จำเป็นต้องผสมผสานทั้งสอง Mode
- มี Table ที่เหมาะสมกับแต่ละ Mode

---

### 5. Query Folding และผลกระทบต่อ Performance

#### 5.1 Query Folding คืออะไร?

**Query Folding:**
- Power Query แปลง M Code เป็น SQL Query
- Query ทำงานบน Database Server แทนที่จะทำงานใน Power BI
- ทำให้ Performance ดีขึ้น

**ตัวอย่าง:**
```
M Code:
= Table.SelectRows(Source, each [Status] = "Active")

Query Folding → SQL:
SELECT * FROM Table WHERE Status = 'Active'
```

#### 5.2 ตรวจสอบ Query Folding

**วิธีตรวจสอบ:**
1. เปิด Power Query Editor
2. Right-click ที่ Step
3. ดูว่า "View Native Query" แสดงขึ้นมาหรือไม่
4. ถ้ามี = Query Folding ทำงาน
5. ถ้าไม่มี = Query Folding ไม่ทำงาน

**ถ้า Query Folding ไม่ทำงาน:**
- ❌ Performance อาจลดลง
- ❌ ข้อมูลถูก Load มาใน Memory ก่อน Filter

#### 5.3 Best Practices สำหรับ Query Folding

**หลักการ:**
- ✅ ใช้ Native Functions ที่ Query Folding รองรับ
- ✅ Filter ข้อมูลใน Power Query ก่อน Import
- ✅ หลีกเลี่ยง Custom Functions ที่ซับซ้อน

**ตัวอย่างที่ Query Folding รองรับ:**
```
✅ Table.SelectRows (WHERE clause)
✅ Table.SelectColumns (SELECT columns)
✅ Table.Sort (ORDER BY)
✅ Table.Group (GROUP BY)
```

**ตัวอย่างที่ Query Folding ไม่รองรับ:**
```
❌ Table.AddColumn (Custom Calculations)
❌ Table.TransformColumns (Complex Transformations)
```

---

### 6. การทำ Data Transformation ด้วย Power Query

#### 6.1 Data Cleaning (การทำความสะอาดข้อมูล)

**สิ่งที่ต้องทำ:**
- ลบ Rows ที่ไม่จำเป็น (เช่น Header Rows, Footer Rows)
- ลบ Columns ที่ไม่ใช้
- แก้ไข Data Types
- แก้ไข Errors (เช่น NULL, Invalid Values)

**ตัวอย่าง:**
```
1. ลบ Rows ที่เป็น Header: Table.Skip()
2. ลบ Columns: Table.RemoveColumns()
3. เปลี่ยน Data Type: Table.TransformColumnTypes()
4. แก้ไข Errors: Table.ReplaceErrorValues()
```

#### 6.2 Data Preparation (การเตรียมข้อมูล)

**สิ่งที่ต้องทำ:**
- รวม Tables (Merge, Append)
- แบ่ง Columns
- สร้าง Calculated Columns
- เปลี่ยนชื่อ Columns

**ตัวอย่าง:**
```
1. Merge Tables: Table.NestedJoin()
2. Append Tables: Table.Combine()
3. แบ่ง Columns: Table.SplitColumn()
4. สร้าง Calculated Columns: Table.AddColumn()
```

---

### 7. การเตรียมข้อมูลสำหรับ VertiPaq Engine ⭐

#### 7.1 การลด Cardinality

**ทำไมต้องลด Cardinality?**
- Low Cardinality = Dictionary Encoding ดี = ประหยัด Memory = เร็ว

**วิธีลด Cardinality:**

**1. แยกคอลัมน์ที่มี Cardinality สูงออกเป็น Dimension Tables**
```
❌ ไม่ดี (Cardinality สูง):
FactSales: ProductName (Cardinality = 1000)

✅ ดี (Cardinality ต่ำ):
FactSales: ProductKey (Cardinality = 100)
DimProduct: ProductName
```

**2. สร้าง Surrogate Keys แทนการใช้ Natural Keys ที่มี Cardinality สูง**
```
❌ ไม่ดี (Natural Key):
CustomerID: "CUST-2024-001-ABC-DEF" (Text, Cardinality สูง)

✅ ดี (Surrogate Key):
CustomerKey: 1, 2, 3, ... (Integer, Cardinality ต่ำ)
```

**3. แปลง DateTime เป็น Date และ Time แยกกัน**
```
❌ ไม่ดี (Cardinality สูง):
OrderDateTime: 2024-01-15 10:23:45.123 (Cardinality = 1,000,000)

✅ ดี (Cardinality ต่ำ):
OrderDate: 2024-01-15 (Cardinality = 365)
OrderTime: 10:23 (Cardinality = 1,440)
```

**4. แบ่ง Text Fields ที่ยาวออกเป็นหลายคอลัมน์**
```
❌ ไม่ดี:
FullAddress: "123 Main St, City, State, ZIP" (Cardinality สูง)

✅ ดี:
Street: "123 Main St" (Cardinality ต่ำ)
City: "City" (Cardinality ต่ำ)
State: "State" (Cardinality ต่ำ)
ZIP: "12345" (Cardinality ต่ำ)
```

#### 7.2 การเรียงข้อมูล (Sorting)

**ทำไมต้องเรียงข้อมูล?**
- Sorted Data = RLE Encoding ดี = ประหยัด Memory = เร็ว

**วิธีเรียงข้อมูล:**

**1. เรียงข้อมูลตาม Foreign Keys ก่อน Import**
```
✅ ดี: เรียง FactResellerSales ตาม ProductKey
→ ProductKey ซ้ำติดกัน → RLE Encoding ดี
```

**2. เรียงข้อมูลตาม Date หรือ Time Dimension**
```
✅ ดี: เรียง FactResellerSales ตาม OrderDateKey
→ OrderDateKey ซ้ำติดกัน → RLE Encoding ดี
```

**3. ใช้ Sort ใน Power Query ก่อน Import**
```
ขั้นตอน:
1. เปิด Power Query Editor
2. เลือก Table
3. เลือก Column ที่ต้องการ Sort
4. Sort Ascending หรือ Descending
5. Apply Changes
```

**4. ผลกระทบของการเรียงข้อมูลต่อ RLE Encoding**
```
❌ ไม่เรียง:
ProductKey: 1, 2, 1, 3, 2, 1, 3, 2
→ RLE Encoding ไม่มีประสิทธิภาพ

✅ เรียงแล้ว:
ProductKey: 1, 1, 1, 2, 2, 2, 3, 3
→ RLE Encoding มีประสิทธิภาพ (1:3, 2:3, 3:2)
```

#### 7.3 Data Cleaning และ Preparation

**สิ่งที่ต้องทำ:**
- ลบ NULL Values ที่ไม่จำเป็น
- แก้ไข Data Types
- Standardize Values (เช่น "Yes/No" เป็น "Y/N")
- Remove Duplicates

#### 7.4 Parameters และ Query Parameters

**Parameters:**
- สร้าง Parameters เพื่อให้ Query ยืดหยุ่น
- เปลี่ยนค่าที่ไม่ต้องแก้ Query

**ตัวอย่าง:**
```
Parameter: StartDate = 2024-01-01
Parameter: EndDate = 2024-12-31

Query:
= Table.SelectRows(Source, each 
    [Date] >= StartDate and [Date] <= EndDate
)
```

#### 7.5 การใช้ Functions และ Custom Functions

**Built-in Functions:**
- ใช้ Functions ที่มีใน Power Query
- เช่น `Table.SelectRows()`, `Table.AddColumn()`

**Custom Functions:**
- สร้าง Functions เองเพื่อใช้ซ้ำ
- ทำให้ Query Clean และ Maintainable

---

### 8. การเชื่อมโยงกับ VertiPaq Engine

**ความสำคัญ:**
- ✅ ข้อมูลที่เตรียมด้วย Low Cardinality จะทำงานได้ดีกับ VertiPaq Engine
- ✅ ข้อมูลที่เรียงลำดับจะเพิ่มประสิทธิภาพ RLE Encoding
- ✅ การเตรียมข้อมูลที่ดีจะส่งผลต่อ Performance ของ Relationships

**สรุป:**
```
การเตรียมข้อมูลที่ดี:
1. ลด Cardinality → Dictionary Encoding ดี
2. เรียงข้อมูล → RLE Encoding ดี
3. ทำความสะอาดข้อมูล → Model สะอาด
4. ใช้ Parameters → Query ยืดหยุ่น
```

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ เชื่อมต่อกับแหล่งข้อมูลต่างๆ ได้
- ✅ เลือกระหว่าง Import, DirectQuery, และ Direct Lake Mode ได้อย่างเหมาะสม
- ✅ เข้าใจ Query Folding และผลกระทบต่อ Performance
- ✅ **เตรียมข้อมูลเพื่อลด Cardinality** ได้
- ✅ **เตรียมข้อมูลโดยการเรียงลำดับ** เพื่อเพิ่มประสิทธิภาพ Encoding ได้
- ✅ ทำ Data Transformation พื้นฐานได้
- ✅ ใช้ Parameters และ Functions ได้

---

## 📝 สรุป

### 🎯 Key Points

1. **ประเภท Data Sources:**
   - Files (Excel, CSV, JSON)
   - Databases (SQL Server, MySQL)
   - Web & Online Services

2. **Import vs DirectQuery vs Direct Lake:**
   - Import = เร็ว, ใช้ Memory มาก, ต้อง Refresh, ไม่มี On Demand Loading
   - DirectQuery = ช้า, ข้อมูล Real-time, ใช้ Memory น้อย, **มี On Demand Loading**
   - Direct Lake = เร็วมาก (ใกล้เคียง Import), ข้อมูล Real-time, ใช้ Memory น้อย, **มี On Demand Loading**, ต้องใช้ Fabric/Premium

3. **Query Folding:**
   - แปลง M Code เป็น SQL
   - Performance ดีขึ้น

4. **การเตรียมข้อมูล:**
   - ลด Cardinality
   - เรียงข้อมูล
   - ทำความสะอาดข้อมูล

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](../README.md) - โครงสร้างหลักสูตร
- **01-Introduction & VertiPaq Engine** - เข้าใจ VertiPaq Engine
- **03-Data-Modeling-Basics** - Star Schema และ Fact/Dimension Tables
- **08-Performance-Optimization** - Performance Optimization
- [Microsoft Learn: Power BI Data Sources](https://learn.microsoft.com/power-bi/connect-data/)

---

## 💡 Tips สำหรับการสอบ

1. **จำ Import vs DirectQuery vs Direct Lake**: 
   - Import = เร็ว, ใช้ Memory มาก, ไม่มี On Demand Loading
   - DirectQuery = Real-time, Query ช้า, **On Demand Loading**
   - Direct Lake = เร็วมาก + Real-time + **On Demand Loading**, ต้องใช้ Fabric/Premium
   
2. **จำ On Demand Loading**: 
   - โหลดเฉพาะข้อมูลที่ต้องการเท่านั้น
   - DirectQuery = Query เฉพาะที่ต้องการ
   - Direct Lake = อ่านเฉพาะ Columns/Rows ที่ต้องการจาก Parquet files
   - ช่วยประหยัด Memory และเพิ่ม Performance
2. **จำ Query Folding**: M Code → SQL Query
3. **จำการลด Cardinality**: ใช้ Dimension Tables, Surrogate Keys
4. **จำการเรียงข้อมูล**: เรียงตาม Foreign Keys หรือ Date
