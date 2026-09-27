# แบบฝึกหัด - Data Sources

## 📚 เอกสารแบบฝึกหัดสำหรับการฝึกปฏิบัติ

ไฟล์นี้รวบรวมแบบฝึกหัดแบบ Step-by-Step สำหรับโมดูล Data Sources โดยรวม Code Examples และคำอธิบายที่ละเอียด

---

## 🎯 แบบฝึกหัดที่ 1: เชื่อมต่อ AdventureWorksDW2025

### วัตถุประสงค์

เรียนรู้วิธีเชื่อมต่อกับ AdventureWorksDW2025 ซึ่งเป็น Data Source หลักของหลักสูตรนี้

---

### ขั้นตอนที่ 1: เปิด Power BI Desktop

**Step 1.1:** เปิด Power BI Desktop

**Step 1.2:** ถ้ามีไฟล์เปิดอยู่ ให้ปิดไฟล์ก่อน (File → Close)

**Step 1.3:** สร้างไฟล์ใหม่ (ถ้ายังไม่มี) - Power BI Desktop จะสร้างไฟล์ใหม่ให้อัตโนมัติเมื่อเปิด

---

### ขั้นตอนที่ 2: เชื่อมต่อกับ Azure SQL Database

**Step 2.1:** ไปที่ **Home** → **Get Data** → **Azure** → **Azure SQL Database**

**Step 2.2:** กรอกข้อมูลการเชื่อมต่อ:
- **Server:** `ake.database.windows.net`
- **Database:** `AdventureWorksDW2025`
- คลิก **OK**

**Step 2.3:** เลือก Authentication Mode:
- เลือก **Database** (Database Authentication)
- **Username:** `dwuser`
- **Password:** `<password ที่ได้รับจากผู้สอน>`
- คลิก **Connect**

**Step 2.4:** รอให้ Power BI Desktop เชื่อมต่อกับ Azure SQL Database (อาจใช้เวลาสักครู่)

---

### ขั้นตอนที่ 3: เลือก Storage Mode (Import Mode)

**Step 3.1:** หลังจากเชื่อมต่อสำเร็จ จะเห็นหน้าต่าง Navigator

**Step 3.2:** ที่มุมขวาล่างของหน้าต่าง Navigator จะมีปุ่ม **Connect** และมี Dropdown เลือก Storage Mode

**Step 3.3:** เลือก **Import** จาก Dropdown:
- **Import**: ข้อมูลจะถูก Import เข้า Power BI Desktop (เก็บใน Memory)
- เป็น Storage Mode ที่แนะนำสำหรับการเรียนรู้ (Performance ดี, รองรับ DAX Functions ทั้งหมด)

**เหตุผลที่เลือก Import Mode:**
- ✅ Performance ดีมาก (In-Memory)
- ✅ ใช้ VertiPaq Engine เต็มที่
- ✅ รองรับ DAX Functions ทั้งหมด
- ✅ เหมาะสำหรับการเรียนรู้และ Data Analytics

---

### ขั้นตอนที่ 4: เลือก Tables ที่ต้องการ

**Step 4.1:** ใน Navigator จะเห็น Tables ทั้งหมดใน AdventureWorksDW2025

**Step 4.2:** เลือก Tables ต่อไปนี้ (ติ๊กถูก):
- ✅ **FactResellerSales** (Fact Table)
- ✅ **DimProduct** (Dimension Table)
- ✅ **DimDate** (Dimension Table)
- ✅ **DimReseller** (Dimension Table)

**Step 4.3:** คลิก **Load** เพื่อ Import ข้อมูล

**Step 4.4:** รอให้ Power BI Desktop Import ข้อมูล (อาจใช้เวลาสักครู่ขึ้นอยู่กับขนาดข้อมูล)

---

### ขั้นตอนที่ 5: ตรวจสอบผลลัพธ์

**Step 5.1:** หลังจาก Import เสร็จ ไปที่ **Model View** (ไอคอนรูปแผนผัง)

**Step 5.2:** ตรวจสอบว่า Tables ทั้ง 4 ตารางแสดงผลถูกต้อง:
- FactResellerSales
- DimProduct
- DimDate
- DimReseller

**Step 5.3:** ตรวจสอบ Relationships:
- Power BI Desktop อาจสร้าง Relationships อัตโนมัติ (Auto-detect)
- ถ้ามี Relationships แสดงว่า Auto-detect ทำงาน

**Step 5.4:** ไปที่ **Data View** (ไอคอนรูปตาราง)

**Step 5.5:** ตรวจสอบข้อมูลในแต่ละ Table:
- คลิกที่ Table ใน Fields Pane (ด้านขวา)
- ดูข้อมูลใน Data View

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **เชื่อมต่อสำเร็จหรือไม่?**
   - **คำตอบ:** ตรวจสอบว่า Tables ทั้ง 4 ตารางแสดงผลใน Model View

2. **มีตารางอะไรบ้างใน AdventureWorksDW2025?**
   - **คำตอบ:** AdventureWorksDW2025 มี Tables หลายตาราง เช่น FactInternetSales, FactResellerSales, FactSalesQuota, DimProduct, DimDate, DimReseller, DimCustomer, DimGeography, DimEmployee, DimPromotion

3. **Fact Tables และ Dimension Tables มีอะไรบ้าง?**
   - **Fact Tables:** FactInternetSales, FactResellerSales, FactSalesQuota
   - **Dimension Tables:** DimProduct, DimDate, DimReseller, DimCustomer, DimGeography, DimEmployee, DimPromotion

---

## 🎯 แบบฝึกหัดที่ 2: เตรียมข้อมูลเพื่อลด Cardinality

### วัตถุประสงค์

เรียนรู้วิธีแยก DateTime ออกเป็น Date และ Time เพื่อลด Cardinality และเพิ่มประสิทธิภาพ VertiPaq Engine

---

### ขั้นตอนที่ 1: เข้าใจปัญหา

**Step 1.1:** ดูข้อมูลตัวอย่างที่มี DateTime แบบเต็มรูปแบบ:

```
OrderDateTime
2024-01-15 10:23:45.123
2024-01-15 10:23:45.456
2024-01-15 10:23:45.789
```

**Step 1.2:** วิเคราะห์ปัญหา:
- แต่ละแถวมีค่า DateTime ที่แตกต่างกัน (แตกต่างกันที่ Milliseconds)
- **Cardinality = 3** (สูงมาก ❌ - เท่ากับจำนวนแถว)
- Dictionary Encoding และ RLE Encoding ทำงานได้ไม่ดี
- ใช้ Memory มาก → Performance ต่ำ

---

### ขั้นตอนที่ 2: แก้ไขโดยการแยก DateTime

**Step 2.1:** เข้าใจแนวทางแก้ไข:
- แยก DateTime ออกเป็น **Date** และ **Time**
- Date จะมี Cardinality ต่ำกว่า (เพราะมีค่าซ้ำกัน)
- เพิ่มประสิทธิภาพ VertiPaq Engine

**Step 2.2:** ผลลัพธ์ที่ต้องการ:

```
OrderDate      | OrderTime
2024-01-15     | 10:23:45.123
2024-01-15     | 10:23:45.456
2024-01-15     | 10:23:45.789
```

- **OrderDate:** Cardinality = 1 ✅ (ต่ำมาก)
- **OrderTime:** Cardinality = 3 (ยังสูงอยู่ แต่น้อยกว่า DateTime)

---

### ขั้นตอนที่ 3: ใช้ Power Query Editor

**Step 3.1:** เปิด Power Query Editor:
- ไปที่ **Home** → **Transform Data** (หรือกด Ctrl+Shift+E)

**Step 3.2:** เลือก Table ที่มี DateTime Column (เช่น FactResellerSales)

**Step 3.3:** เลือก Column ที่มี DateTime (เช่น OrderDate)

**Step 3.4:** ไปที่ **Add Column** → **Date** → **Date Only**

**หรือใช้ Advanced Editor:**

**Step 3.5:** ไปที่ **View** → **Advanced Editor**

**Step 3.6:** พิมพ์ Power Query M Code:

```m
let
    Source = Table.Navigation(...),  // Source Table
    #"Changed Type" = Table.TransformColumnTypes(Source, {{"OrderDateTime", type datetime}}),
    #"Added Date Column" = Table.AddColumn(#"Changed Type", "OrderDate", each Date.From([OrderDateTime])),
    #"Added Time Column" = Table.AddColumn(#"Added Date Column", "OrderTime", each Time.From([OrderDateTime])),
    #"Removed Columns" = Table.RemoveColumns(#"Added Time Column", {"OrderDateTime"})
in
    #"Removed Columns"
```

**อธิบายแต่ละขั้นตอน:**
- `Date.From([OrderDateTime])` → แยก Date ออกจาก DateTime
- `Time.From([OrderDateTime])` → แยก Time ออกจาก DateTime
- `Table.RemoveColumns(...)` → ลบ Column เดิม (OrderDateTime)

---

### ขั้นตอนที่ 4: Apply Changes

**Step 4.1:** คลิก **Close & Apply** เพื่อ Apply Changes

**Step 4.2:** รอให้ Power BI Desktop ประมวลผล

**Step 4.3:** ตรวจสอบผลลัพธ์:
- ไปที่ **Data View**
- ตรวจสอบว่า Column ใหม่ (OrderDate, OrderTime) แสดงผลถูกต้อง
- ตรวจสอบว่า Column เดิม (OrderDateTime) ถูกลบแล้ว

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องแยก DateTime เป็น Date และ Time?**
   - **คำตอบ:** เพราะ DateTime มี Cardinality สูงมาก (แต่ละแถวมีค่าไม่ซ้ำกัน) การแยกเป็น Date และ Time จะทำให้ Date มี Cardinality ต่ำกว่า (เพราะมีค่าซ้ำกัน) ซึ่งจะเพิ่มประสิทธิภาพ VertiPaq Engine

2. **ผลลัพธ์ที่ได้คืออะไร?**
   - **คำตอบ:** OrderDate (Cardinality ต่ำมาก) และ OrderTime (Cardinality ต่ำกว่า DateTime เดิม)

3. **ประโยชน์ที่ได้รับคืออะไร?**
   - **คำตอบ:** ลด Cardinality, Dictionary Encoding และ RLE Encoding ทำงานได้ดีขึ้น, ใช้ Memory น้อยลง, เพิ่ม Performance

---

## 🎯 แบบฝึกหัดที่ 3: เรียงข้อมูลก่อน Import

### วัตถุประสงค์

เรียนรู้วิธีเรียงข้อมูลก่อน Import เพื่อเพิ่มประสิทธิภาพ RLE Encoding

---

### ขั้นตอนที่ 1: เข้าใจความสำคัญ

**Step 1.1:** เข้าใจว่าทำไมต้องเรียงข้อมูล:
- **RLE Encoding** ทำงานได้ดีกับข้อมูลที่เรียงลำดับ
- เมื่อค่าซ้ำกันเรียงติดกัน RLE จะบีบอัดได้ดีมาก
- เพิ่มประสิทธิภาพ Relationships

**Step 1.2:** ตัวอย่างข้อมูลที่ไม่เรียงลำดับ:

```
รหัสใบเสร็จ   | รหัสสินค้า
INV-001      | P002
INV-002      | P001
INV-003      | P002
INV-004      | P001
```

- ค่าไม่เรียงติดกัน → RLE ไม่สามารถบีบอัดได้ดี

---

### ขั้นตอนที่ 2: เรียงข้อมูลใน Power Query

**Step 2.1:** เปิด Power Query Editor:
- ไปที่ **Home** → **Transform Data**

**Step 2.2:** เลือก Table ที่ต้องการเรียง (เช่น FactResellerSales)

**Step 2.3:** เลือก Columns ที่ต้องการเรียง:
- ควรเรียงตาม Foreign Keys (เช่น ProductKey, OrderDateKey)
- หรือเรียงตาม Date Key

**Step 2.4:** ไปที่ **Home** → **Sort** → เลือก Column ที่ต้องการเรียง

**หรือใช้ Advanced Editor:**

**Step 2.5:** ไปที่ **View** → **Advanced Editor**

**Step 2.6:** พิมพ์ Power Query M Code:

```m
let
    Source = Table.Navigation(...),  // Source Table
    #"Sorted Rows" = Table.Sort(Source, {
        {"ProductKey", Order.Ascending},
        {"OrderDateKey", Order.Ascending}
    })
in
    #"Sorted Rows"
```

**อธิบาย:**
- `Table.Sort(...)` → เรียงข้อมูล
- `{{"ProductKey", Order.Ascending}, ...}` → เรียงตาม ProductKey ก่อน (น้อยไปมาก)
- ถ้า ProductKey เท่ากัน จะเรียงตาม OrderDateKey

---

### ขั้นตอนที่ 3: ตรวจสอบผลลัพธ์

**Step 3.1:** ดูข้อมูลที่เรียงแล้ว:
- ข้อมูลควรเรียงตาม ProductKey ก่อน
- ถ้า ProductKey เท่ากัน จะเรียงตาม OrderDateKey

**Step 3.2:** Apply Changes:
- คลิก **Close & Apply**

**Step 3.3:** ตรวจสอบผลลัพธ์:
- ไปที่ **Data View**
- ตรวจสอบว่าข้อมูลเรียงลำดับถูกต้อง

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องเรียงข้อมูลก่อน Import?**
   - **คำตอบ:** เพราะ RLE Encoding ทำงานได้ดีกับข้อมูลที่เรียงลำดับ เมื่อค่าซ้ำกันเรียงติดกัน RLE จะบีบอัดได้ดีมาก ซึ่งจะช่วยเพิ่ม Performance

2. **ควรเรียงข้อมูลตามอะไร?**
   - **คำตอบ:** ควรเรียงตาม Foreign Keys (เช่น ProductKey, OrderDateKey) หรือ Date Keys เพื่อให้ค่าซ้ำกันเรียงติดกัน

3. **ประโยชน์ที่ได้รับคืออะไร?**
   - **คำตอบ:** เพิ่มประสิทธิภาพ RLE Encoding, เพิ่ม Performance ของ Relationships, ลดขนาดข้อมูล

---

## 🎯 แบบฝึกหัดที่ 4: Import Mode vs DirectQuery Mode vs Direct Lake Mode

### วัตถุประสงค์

เข้าใจความแตกต่างระหว่าง Import Mode, DirectQuery Mode, และ Direct Lake Mode และรู้ว่าควรใช้เมื่อไหร่

---

### ขั้นตอนที่ 1: เข้าใจ Import Mode

**Step 1.1:** เข้าใจ Import Mode:

**Import Mode** คือ:
- ข้อมูลถูก Import เข้า Power BI Desktop (เก็บใน Memory)
- ข้อมูลถูกโหลดทั้งหมดก่อน (Pre-loading)
- **ไม่มี On Demand Loading**

**Step 1.2:** ข้อดีของ Import Mode:
- ✅ Performance ดีมาก (In-Memory)
- ✅ ใช้ VertiPaq Engine เต็มที่
- ✅ รองรับ DAX Functions ทั้งหมด
- ✅ ทำงาน Offline ได้

**Step 1.3:** ข้อเสียของ Import Mode:
- ❌ ต้อง Refresh ข้อมูล
- ❌ ใช้ Memory มาก (โหลดข้อมูลทั้งหมด)
- ❌ จำกัดขนาดข้อมูล

**Step 1.4:** ใช้เมื่อไหร่:
- ข้อมูลไม่ต้องการ Real-time
- ต้องการ Performance สูงสุด
- ข้อมูลไม่ใหญ่มาก (< 1GB)
- ต้องการใช้ DAX Functions ทั้งหมด

---

### ขั้นตอนที่ 2: เข้าใจ DirectQuery Mode

**Step 2.1:** เข้าใจ DirectQuery Mode:

**DirectQuery Mode** คือ:
- ข้อมูลถูก Query จาก Source Database โดยตรงทุกครั้ง
- **มี On Demand Loading** - Query เฉพาะข้อมูลที่ต้องการ
- ไม่โหลดข้อมูลทั้งหมดเข้า Memory

**Step 2.2:** ข้อดีของ DirectQuery Mode:
- ✅ ข้อมูล Real-time (Always Up-to-date)
- ✅ ไม่ต้อง Refresh
- ✅ ไม่ใช้ Memory มาก (**On Demand Loading**)
- ✅ รองรับข้อมูลขนาดใหญ่

**Step 2.3:** ข้อเสียของ DirectQuery Mode:
- ❌ Performance ช้ากว่า Import Mode
- ❌ ไม่รองรับ DAX Functions บางตัว
- ❌ ต้องมี Connection ต่อเนื่อง
- ❌ ขึ้นอยู่กับ Performance ของ Source Database

**Step 2.4:** ใช้เมื่อไหร่:
- ต้องการข้อมูล Real-time
- ข้อมูลใหญ่มาก (> 1GB)
- Source Database มี Performance ดี
- ไม่ต้องการ Refresh ข้อมูล
- ไม่มี Fabric/Premium

---

### ขั้นตอนที่ 3: เข้าใจ Direct Lake Mode

**Step 3.1:** เข้าใจ Direct Lake Mode:

**Direct Lake Mode** คือ:
- อ่านข้อมูลโดยตรงจาก OneLake (Delta Lake format)
- ไม่ต้อง Import หรือ Query
- **มี On Demand Loading** - อ่านเฉพาะ Columns/Rows ที่ต้องการจาก Parquet files
- ใช้ประโยชน์จาก Columnar Storage

**Step 3.2:** ข้อดีของ Direct Lake Mode:
- ✅ Performance ดีมาก (ใกล้เคียง Import Mode)
- ✅ ข้อมูล Real-time (Always Up-to-date)
- ✅ ใช้ Memory น้อยมาก (**On Demand Loading**)
- ✅ รองรับข้อมูลขนาดใหญ่ (ไม่มีขีดจำกัด)
- ✅ รองรับ DAX Functions ทั้งหมด
- ✅ ใช้ประโยชน์จาก VertiPaq Engine เต็มที่

**Step 3.3:** ข้อเสียของ Direct Lake Mode:
- ❌ ต้องใช้ Microsoft Fabric หรือ Power BI Premium/PPU
- ❌ ข้อมูลต้องอยู่ใน OneLake (Delta Lake format)
- ❌ ต้องมี Lakehouse หรือ Data Warehouse ใน Fabric

**Step 3.4:** ใช้เมื่อไหร่:
- ✅ มี Microsoft Fabric หรือ Power BI Premium/PPU
- ✅ ข้อมูลอยู่ใน OneLake (Lakehouse หรือ Data Warehouse)
- ✅ ต้องการ Performance สูง + ข้อมูล Real-time
- ✅ ข้อมูลขนาดใหญ่

---

### ขั้นตอนที่ 4: เข้าใจ On Demand Loading (Lazy Loading)

**Step 4.1:** เข้าใจ On Demand Loading:

**On Demand Loading (Lazy Loading)** คือ:
- การโหลดข้อมูลแบบตามต้องการ
- โหลดเฉพาะข้อมูลที่จำเป็นเมื่อมีการใช้งานจริง
- ไม่โหลดข้อมูลทั้งหมดก่อน

**Step 4.2:** On Demand Loading ในแต่ละ Mode:

**Import Mode:**
- ❌ ไม่มี On Demand Loading
- โหลดข้อมูลทั้งหมดก่อน (Pre-loading)
- ใช้ Memory มาก

**DirectQuery Mode:**
- ✅ มี On Demand Loading
- Query เฉพาะข้อมูลที่ต้องการเมื่อมีการใช้งานจริง
- ประหยัด Memory

**Direct Lake Mode:**
- ✅ มี On Demand Loading
- อ่านเฉพาะ Columns และ Rows ที่จำเป็นจาก Parquet files
- ใช้ประโยชน์จาก Columnar Storage
- ประหยัด Memory มาก

**Step 4.3:** ตัวอย่าง:

**สมมติว่า Query ต้องการแค่ ProductKey และ SalesAmount จาก FactResellerSales ที่มี 100 Columns:**

**Import Mode:**
- โหลดข้อมูลทั้งหมด (100 Columns) เข้า Memory
- ใช้ Memory มาก
- Query เร็วมาก (เพราะข้อมูลอยู่ใน Memory แล้ว)

**DirectQuery Mode:**
- Query เฉพาะ 2 Columns (ProductKey, SalesAmount) จาก Database
- ไม่โหลด Columns อื่นๆ
- ประหยัด Memory
- Query ช้า (ต้อง Query ไปที่ Database)

**Direct Lake Mode:**
- อ่านเฉพาะ 2 Columns (ProductKey, SalesAmount) จาก Parquet files
- ไม่ต้องอ่าน Columns อื่นๆ (ใช้ประโยชน์จาก Columnar Storage)
- ประหยัด Memory มาก
- Query เร็วมาก (อ่านจาก Parquet files โดยตรง)

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Import Mode, DirectQuery Mode, และ Direct Lake Mode ต่างกันอย่างไร?**
   - **คำตอบ:** Import Mode = Import ข้อมูลเข้า Memory, DirectQuery Mode = Query จาก Database, Direct Lake Mode = อ่านจาก OneLake

2. **ควรใช้ Import Mode เมื่อไหร่?**
   - **คำตอบ:** เมื่อข้อมูลไม่ต้องการ Real-time, ต้องการ Performance สูงสุด, ข้อมูลไม่ใหญ่มาก, ต้องการใช้ DAX Functions ทั้งหมด

3. **ควรใช้ DirectQuery Mode เมื่อไหร่?**
   - **คำตอบ:** เมื่อต้องการข้อมูล Real-time, ข้อมูลใหญ่มาก, Source Database มี Performance ดี, ไม่ต้องการ Refresh ข้อมูล

4. **ควรใช้ Direct Lake Mode เมื่อไหร่?**
   - **คำตอบ:** เมื่อมี Fabric/Premium, ข้อมูลอยู่ใน OneLake, ต้องการ Performance สูง + Real-time, ข้อมูลขนาดใหญ่

5. **On Demand Loading คืออะไรและมีใน Mode ไหนบ้าง?**
   - **คำตอบ:** On Demand Loading คือการโหลดข้อมูลแบบตามต้องการ (โหลดเฉพาะที่จำเป็น) มีใน DirectQuery Mode และ Direct Lake Mode แต่ไม่มีใน Import Mode (Import Mode = Pre-loading)

---

## 🎯 แบบฝึกหัดที่ 5: On Demand Loading (Lazy Loading) - รายละเอียด

### วัตถุประสงค์

เข้าใจ On Demand Loading (Lazy Loading) อย่างละเอียดและเข้าใจผลกระทบต่อ Memory และ Performance

---

### ขั้นตอนที่ 1: เข้าใจ On Demand Loading

**Step 1.1:** เข้าใจแนวคิด:

**On Demand Loading (Lazy Loading)** คือ:
- การโหลดข้อมูลแบบตามต้องการ
- โหลดเฉพาะข้อมูลที่จำเป็นเมื่อมีการใช้งานจริง
- ไม่โหลดข้อมูลทั้งหมดก่อน (ต่างจาก Import Mode)

**Step 1.2:** เปรียบเทียบกับ Pre-loading:

**Pre-loading (Import Mode):**
- โหลดข้อมูลทั้งหมดก่อน
- ใช้ Memory มาก
- Query เร็วมาก (เพราะข้อมูลอยู่ใน Memory แล้ว)

**On Demand Loading:**
- โหลดเฉพาะส่วนที่จำเป็น
- ประหยัด Memory
- Query ช้ากว่า (ต้อง Query/อ่านข้อมูล)

---

### ขั้นตอนที่ 2: On Demand Loading ในแต่ละ Mode

**Step 2.1:** Import Mode:

**Import Mode:**
- ❌ ไม่มี On Demand Loading
- โหลดข้อมูลทั้งหมดก่อน (Pre-loading)
- ใช้ Memory มาก
- Query เร็วมาก

**Step 2.2:** DirectQuery Mode:

**DirectQuery Mode:**
- ✅ มี On Demand Loading
- Query เฉพาะข้อมูลที่ต้องการเมื่อมีการใช้งานจริง
- ไม่โหลด Columns ที่ไม่ใช้
- ประหยัด Memory
- Query ช้า (ต้อง Query ไปที่ Database)

**ตัวอย่าง:**
- ถ้า Query ต้องการแค่ ProductKey และ SalesAmount
- DirectQuery จะ Query เฉพาะ 2 Columns นี้
- ไม่ Query Columns อื่นๆ

**Step 2.3:** Direct Lake Mode:

**Direct Lake Mode:**
- ✅ มี On Demand Loading
- อ่านเฉพาะ Columns และ Rows ที่จำเป็นจาก Parquet files
- ใช้ประโยชน์จาก Columnar Storage
- ประหยัด Memory มาก
- Query เร็วมาก (อ่านจาก Parquet files โดยตรง)

**ตัวอย่าง:**
- ถ้า Query ต้องการแค่ ProductKey และ SalesAmount
- Direct Lake จะอ่านเฉพาะ 2 Columns นี้จาก Parquet files
- ไม่ต้องอ่าน Columns อื่นๆ (ใช้ประโยชน์จาก Columnar Storage)

---

### ขั้นตอนที่ 3: ตัวอย่างการใช้งาน

**Step 3.1:** สมมติว่า FactResellerSales มี 100 Columns:

**Columns:**
- ProductKey, CustomerKey, DateKey, SalesAmount, OrderQuantity, UnitPrice, DiscountAmount, ... (100 Columns)

**Step 3.2:** Query ต้องการแค่:
- ProductKey และ SalesAmount

**Step 3.3:** เปรียบเทียบการทำงานของแต่ละ Mode:

**Import Mode:**
- โหลดข้อมูลทั้งหมด (100 Columns) เข้า Memory
- ใช้ Memory มาก (100 Columns × จำนวนแถว)
- Query เร็วมาก (เพราะข้อมูลอยู่ใน Memory แล้ว)
- ไม่มี On Demand Loading

**DirectQuery Mode:**
- Query เฉพาะ 2 Columns (ProductKey, SalesAmount) จาก Database
- ไม่ Query Columns อื่นๆ (98 Columns)
- ประหยัด Memory (ใช้แค่ 2 Columns)
- Query ช้า (ต้อง Query ไปที่ Database)
- มี On Demand Loading

**Direct Lake Mode:**
- อ่านเฉพาะ 2 Columns (ProductKey, SalesAmount) จาก Parquet files
- ไม่ต้องอ่าน Columns อื่นๆ (98 Columns) - ใช้ประโยชน์จาก Columnar Storage
- ประหยัด Memory มาก (ใช้แค่ 2 Columns)
- Query เร็วมาก (อ่านจาก Parquet files โดยตรง)
- มี On Demand Loading

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **On Demand Loading (Lazy Loading) คืออะไร?**
   - **คำตอบ:** การโหลดข้อมูลแบบตามต้องการ (โหลดเฉพาะข้อมูลที่จำเป็นเมื่อมีการใช้งานจริง) ไม่โหลดข้อมูลทั้งหมดก่อน

2. **Import Mode, DirectQuery Mode, และ Direct Lake Mode มี On Demand Loading หรือไม่? อย่างไร?**
   - **คำตอบ:** Import Mode = ❌ ไม่มี (Pre-loading), DirectQuery Mode = ✅ มี (Query เฉพาะที่ต้องการ), Direct Lake Mode = ✅ มี (อ่านเฉพาะ Columns/Rows ที่จำเป็น)

3. **On Demand Loading ช่วยประหยัด Memory อย่างไร?**
   - **คำตอบ:** ไม่ต้องโหลดข้อมูลทั้งหมดเข้า Memory, Query/อ่านเฉพาะส่วนที่จำเป็น, ประหยัด Memory และเพิ่ม Performance

4. **ตัวอย่าง: ถ้า Query ต้องการแค่ ProductKey และ SalesAmount จาก FactResellerSales ที่มี 100 Columns แต่ละ Mode จะทำอย่างไร?**
   - **คำตอบ:** Import Mode = โหลดทั้งหมด 100 Columns, DirectQuery Mode = Query เฉพาะ 2 Columns, Direct Lake Mode = อ่านเฉพาะ 2 Columns จาก Parquet files

---

## 📝 สรุป

### ✅ สิ่งที่ควรจำ

1. **เตรียมข้อมูลก่อน Import** - ลด Cardinality และเพิ่ม Sorting เพื่อเพิ่มประสิทธิภาพ VertiPaq Engine
2. **เลือก Storage Mode ตามความเหมาะสม**:
   - Import Mode สำหรับข้อมูลเล็ก-กลาง (ไม่มี On Demand Loading)
   - DirectQuery Mode สำหรับข้อมูลใหญ่ + Real-time (มี On Demand Loading)
   - Direct Lake Mode สำหรับข้อมูลใหญ่ + Real-time + Performance สูง (มี On Demand Loading, ต้องมี Fabric/Premium)
3. **เข้าใจ On Demand Loading**:
   - Import Mode = Pre-loading (โหลดทั้งหมดก่อน)
   - DirectQuery = Query เฉพาะที่ต้องการ
   - Direct Lake = อ่านเฉพาะ Columns/Rows ที่ต้องการจาก Parquet files
4. **ใช้ AdventureWorksDW2025** เป็น Data Source หลักของหลักสูตรนี้
5. **แยก DateTime** เป็น Date และ Time เพื่อลด Cardinality
6. **เรียงข้อมูล** ก่อน Import เพื่อเพิ่มประสิทธิภาพ RLE Encoding

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) - Code Examples เพิ่มเติม
