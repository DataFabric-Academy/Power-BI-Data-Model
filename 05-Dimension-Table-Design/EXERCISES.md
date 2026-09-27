# แบบฝึกหัด - Dimension Table Design

## 📚 เอกสารแบบฝึกหัดสำหรับการฝึกปฏิบัติ

ไฟล์นี้รวบรวมแบบฝึกหัดแบบ Step-by-Step สำหรับการออกแบบ Dimension Table โดยเฉพาะ SCD Type 2 และ Attribute Hierarchies โดยรวม Code Examples และคำอธิบายที่ละเอียด

> **ไฟล์ตัวอย่าง:** `Data Model SCD.pbix`
> **หมายเหตุ:** ไฟล์ตัวอย่างเป็นสื่อการสอนของผู้สอน (Trainer Material) — ขอไฟล์ได้จากผู้สอนระหว่างเรียน ไม่ได้แจกจ่ายผ่าน repository นี้

---

## 🎯 แบบฝึกหัดที่ 1: เข้าใจโครงสร้าง SCD Type 2

### วัตถุประสงค์

เข้าใจโครงสร้างของ SCD Type 2 และคอลัมน์ต่างๆ ที่ใช้ในการเก็บประวัติการเปลี่ยนแปลง

---

### ขั้นตอนที่ 1: เปิดไฟล์ตัวอย่าง

**Step 1.1:** เปิด Power BI Desktop

**Step 1.2:** เปิดไฟล์ `Data Model SCD.pbix`

**Step 1.3:** ไปที่ **Data View** หรือ **Model View**

**Step 1.4:** เลือกตาราง `FullLoad` เพื่อดูโครงสร้าง

---

### ขั้นตอนที่ 2: ตรวจสอบคอลัมน์ทั้งหมด

**Step 2.1:** สังเกตคอลัมน์ในตาราง `FullLoad`:

**คอลัมน์หลัก:**
- **SalesRepID** - Surrogate Key (Primary Key)
- **RepSourceID** - Business Key (มาจากระบบต้นทาง)
- **FirstName**, **LastName**, **Region** - Dimension Attributes
- **StartDate** - วันที่เริ่มต้น
- **EndDate** - วันที่สิ้นสุด
- **IsCurrent** - Flag ว่าบันทึกปัจจุบันหรือไม่
- **Hash** - สำหรับตรวจสอบการเปลี่ยนแปลง

**Step 2.2:** ตรวจสอบข้อมูลตัวอย่าง:
- สังเกตว่ามีหลาย Records สำหรับ RepSourceID เดียวกัน
- แต่ละ Record มี StartDate และ EndDate ที่แตกต่างกัน
- มีเพียง Record เดียวที่มี IsCurrent = TRUE

---

### ขั้นตอนที่ 3: เข้าใจ Surrogate Key และ Business Key

**Step 3.1:** เข้าใจ Surrogate Key:

**SalesRepID (Surrogate Key):**
- เป็น Key ที่สร้างขึ้นเอง (ไม่ใช่จากระบบต้นทาง)
- ใช้สำหรับเชื่อมโยงกับ Fact Table
- เป็น Unique (แต่ละแถวมี SalesRepID ที่แตกต่างกัน)
- ใช้เป็น Primary Key ใน Dimension Table

**Step 3.2:** เข้าใจ Business Key:

**RepSourceID (Business Key):**
- เป็น Key ที่มาจากระบบต้นทาง
- ใช้เพื่อระบุตัวตนทางธุรกิจ (เช่น Employee ID จากระบบ HR)
- **ไม่ Unique** (อาจมีหลาย Records สำหรับ RepSourceID เดียวกัน - เพราะเก็บประวัติ)

---

### ขั้นตอนที่ 4: เข้าใจ Hash

**Step 4.1:** เข้าใจว่า Hash คืออะไร:

**Hash** = ค่า Hash ที่สร้างจาก Dimension Attributes ที่สำคัญ

**วัตถุประสงค์:**
- ใช้เพื่อตรวจสอบการเปลี่ยนแปลง
- สร้างจาก Attributes ที่สำคัญ (เช่น FirstName, LastName, Region)
- ใช้เปรียบเทียบระหว่าง Source และ Dimension

**Step 4.2:** วิธีสร้าง Hash:

**Power Query M:**
```m
Hash = 
Binary.ToText(
    Text.ToBinary(
        Text.Combine(
            List.Transform({[RepSourceID],[FirstName],[LastName],[Region]}, 
                each if _ = null then "" else Text.From(_)
            ), "|"
        )
    ), BinaryEncoding.Hex
)
```

**อธิบาย:**
- รวม Attributes (RepSourceID, FirstName, LastName, Region) ด้วย "|"
- แปลงเป็น Binary แล้วเป็น Hex Text
- ใช้เปรียบเทียบว่า Attributes เปลี่ยนไปหรือไม่

---

### ขั้นตอนที่ 5: เข้าใจ IsCurrent Flag

**Step 5.1:** เข้าใจว่า IsCurrent คืออะไร:

**IsCurrent** = Flag ที่บอกว่าบันทึกเป็นปัจจุบันหรือไม่

**ค่า:**
- **TRUE** = บันทึกปัจจุบัน (ยังใช้อยู่)
- **FALSE** = บันทึกเก่า (มี Records ใหม่แล้ว)

**Step 5.2:** ตัวอย่างข้อมูล:

**RepSourceID = 1:**
- Record 1: StartDate = 2020-01-01, EndDate = 2023-12-31, IsCurrent = FALSE
- Record 2: StartDate = 2024-01-01, EndDate = 9999-12-31, IsCurrent = TRUE

**อธิบาย:**
- Record 1 = บันทึกเก่า (Region = "North")
- Record 2 = บันทึกปัจจุบัน (Region = "South")

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ตาราง `FullLoad` มีคอลัมน์อะไรบ้าง?**
   - **คำตอบ:** SalesRepID (Surrogate Key), RepSourceID (Business Key), FirstName, LastName, Region (Dimension Attributes), StartDate, EndDate (Date Range), IsCurrent (Current Flag), Hash (สำหรับตรวจสอบการเปลี่ยนแปลง)

2. **คอลัมน์ `SalesRepID` เป็น Key ประเภทอะไร?**
   - **คำตอบ:** Surrogate Key - เป็น Key ที่สร้างขึ้นเอง ใช้สำหรับเชื่อมโยงกับ Fact Table

3. **คอลัมน์ `RepSourceID` เป็น Key ประเภทอะไร?**
   - **คำตอบ:** Business Key - เป็น Key ที่มาจากระบบต้นทาง ใช้เพื่อระบุตัวตนทางธุรกิจ

4. **คอลัมน์ `Hash` ใช้ทำอะไร?**
   - **คำตอบ:** ใช้เพื่อตรวจสอบการเปลี่ยนแปลง โดยสร้างจาก Dimension Attributes ที่สำคัญ ใช้เปรียบเทียบระหว่าง Source และ Dimension

5. **คอลัมน์ `IsCurrent` ใช้ทำอะไร?**
   - **คำตอบ:** Flag ที่บอกว่าบันทึกเป็นปัจจุบันหรือไม่ (TRUE = บันทึกปัจจุบัน, FALSE = บันทึกเก่า)

---

## 🎯 แบบฝึกหัดที่ 2: เข้าใจกระบวนการ SCD Type 2

### วัตถุประสงค์

เข้าใจกระบวนการทำ SCD Type 2 ตั้งแต่ต้นจนจบ รวมถึง Power Query Expressions ที่เกี่ยวข้อง

---

### ขั้นตอนที่ 1: เข้าใจกระบวนการโดยรวม

**Step 1.1:** เข้าใจวัตถุประสงค์:

**SCD Type 2** = การเก็บประวัติการเปลี่ยนแปลง

**เมื่อไหร่ใช้:**
- เมื่อต้องการเก็บประวัติการเปลี่ยนแปลงของ Dimension Attributes
- เช่น พนักงานย้ายเขตการขาย, ราคาสินค้าเปลี่ยนแปลง, สถานะลูกค้าเปลี่ยน

**Step 1.2:** ขั้นตอนกระบวนการ:

**ขั้นตอนที่ 1**: สร้าง Hash จาก Dimension Attributes
**ขั้นตอนที่ 2**: เปรียบเทียบ Source กับ Dimension
**ขั้นตอนที่ 3**: หา NewRecords และ RecordsToUpdate
**ขั้นตอนที่ 4**: อัพเดท Dimension (ปิดเก่า + เพิ่มใหม่)

---

### ขั้นตอนที่ 2: เข้าใจ NewRecords

**Step 2.1:** เข้าใจว่า NewRecords คืออะไร:

**NewRecords** = Records ใหม่ที่ไม่มีใน Dimension เดิม

**ลักษณะ:**
- Records ที่มี Hash ใหม่ (ไม่มีใน Dimension เดิม)
- ตั้ง StartDate = วันนี้
- ตั้ง EndDate = 9999-12-31 (แสดงว่ายังใช้ได้)
- ตั้ง IsCurrent = TRUE

**Step 2.2:** ตัวอย่าง:

**Source มี:**
- RepSourceID = 5, FirstName = "สมชาย", LastName = "ใจดี", Region = "North"

**Dimension ไม่มี Hash นี้:**
- → สร้าง Record ใหม่
- StartDate = 2024-01-15 (วันนี้)
- EndDate = 9999-12-31
- IsCurrent = TRUE

---

### ขั้นตอนที่ 3: เข้าใจ RecordsToUpdate

**Step 3.1:** เข้าใจว่า RecordsToUpdate คืออะไร:

**RecordsToUpdate** = Records เก่าที่ต้องอัพเดท

**ลักษณะ:**
- Records ที่มี Hash เปลี่ยนไป (มีใน Dimension แต่ Hash ไม่ตรงกับ Source)
- ตั้ง IsCurrent = FALSE (ปิด Records เก่า)
- ตั้ง EndDate = วันนี้ (จบ Records เก่า)

**Step 3.2:** ตัวอย่าง:

**Dimension มี:**
- RepSourceID = 1, Region = "North", StartDate = 2020-01-01, EndDate = 9999-12-31, IsCurrent = TRUE

**Source มี:**
- RepSourceID = 1, Region = "South" (เปลี่ยนจาก "North")

**การทำงาน:**
1. ปิด Record เก่า: IsCurrent = FALSE, EndDate = 2024-01-15 (วันนี้)
2. สร้าง Record ใหม่: Region = "South", StartDate = 2024-01-15, EndDate = 9999-12-31, IsCurrent = TRUE

---

### ขั้นตอนที่ 4: เข้าใจกระบวนการโดยละเอียด

**Step 4.1:** ขั้นตอนที่ 1: สร้าง Hash

**Power Query M:**
```m
Hash = Table.AddColumn(SourceTable, "Hash", 
  each Binary.ToText(
    Text.ToBinary(
      Text.Combine(
        List.Transform({[EmployeeID],[Name],[Department]}, 
          each if _ = null then "" else Text.From(_)
        ), "|"
      )
    ), BinaryEncoding.Hex
  )
)
```

**อธิบาย:**
- สร้าง Hash จาก Attributes ที่สำคัญ
- ใช้เพื่อเปรียบเทียบการเปลี่ยนแปลง

**Step 4.2:** ขั้นตอนที่ 2: เปรียบเทียบ Source กับ Dimension

**Power Query M:**
```m
CompareStoM = Table.NestedJoin(
    SourceTable, {"Hash"}, 
    AggregatedDimHash, {"Hash"}, 
    "AggregatedDimHash", 
    JoinKind.LeftAnti
)
```

**อธิบาย:**
- ใช้ LeftAnti Join เพื่อหา Hash ที่มีใน SourceTable แต่ไม่มีใน Dimension
- นี่คือ Records ใหม่ที่ต้องเพิ่ม

**Step 4.3:** ขั้นตอนที่ 3: หา NewRecords และ RecordsToUpdate

**NewRecords:**
- Hash ที่มีใน SourceTable แต่ไม่มีใน Dimension

**RecordsToUpdate:**
- Hash ที่มีใน Dimension แต่ไม่มีใน SourceTable (Hash เปลี่ยนไป)

**Step 4.4:** ขั้นตอนที่ 4: อัพเดท Dimension

**การทำงาน:**
- ลบ Records ที่ต้องอัพเดทออกจาก Dimension เดิม
- เพิ่ม NewRecords ใหม่
- เพิ่ม RecordsToUpdate ที่อัพเดทแล้ว (IsCurrent = FALSE, EndDate = วันนี้)

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Power Query Expression `NewRecords` ทำอะไร?**
   - **คำตอบ:** สร้าง Records ใหม่ที่มี Hash ใหม่ (ไม่มีใน Dimension เดิม) ตั้ง StartDate = วันนี้, EndDate = 9999-12-31, IsCurrent = TRUE

2. **Power Query Expression `RecordsToUpdate` ทำอะไร?**
   - **คำตอบ:** อัพเดท Records เก่าที่มี Hash เปลี่ยนไป (มีใน Dimension แต่ Hash ไม่ตรงกับ Source) ตั้ง IsCurrent = FALSE, EndDate = วันนี้

3. **กระบวนการทำ SCD Type 2 มีขั้นตอนอะไรบ้าง?**
   - **คำตอบ:** 
     1. สร้าง Hash จาก Dimension Attributes
     2. เปรียบเทียบ Source กับ Dimension
     3. หา NewRecords และ RecordsToUpdate
     4. อัพเดท Dimension (ปิดเก่า + เพิ่มใหม่)

---

## 🎯 แบบฝึกหัดที่ 3: เขียน Power Query สำหรับ SCD Type 2

### วัตถุประสงค์

เรียนรู้วิธีเขียน Power Query M Expressions สำหรับ SCD Type 2

---

### ขั้นตอนที่ 1: เข้าใจโครงสร้างตาราง

**Step 1.1:** SourceTable:

**Columns:**
- EmployeeID (Business Key)
- Name
- Department

**Step 1.2:** Dimension Table:

**Columns:**
- EmployeeKey (Surrogate Key - Primary Key)
- EmployeeID (Business Key)
- Name, Department (Dimension Attributes)
- StartDate, EndDate (Date Range)
- IsCurrent (Current Flag)
- Hash

---

### ขั้นตอนที่ 2: สร้าง Hash จาก SourceTable

**Step 2.1:** เปิด Power Query Editor

**Step 2.2:** เลือก SourceTable

**Step 2.3:** ไปที่ **Add Column** → **Custom Column**

**Step 2.4:** พิมพ์สูตร:

```m
Hash = Binary.ToText(
    Text.ToBinary(
        Text.Combine(
            List.Transform({[EmployeeID],[Name],[Department]}, 
                each if _ = null then "" else Text.From(_)
            ), "|"
        )
    ), BinaryEncoding.Hex
)
```

**อธิบาย:**
- `List.Transform(...)` แปลงแต่ละ Attribute เป็น Text
- `Text.Combine(..., "|")` รวม Attributes ด้วย "|"
- `Text.ToBinary(...)` แปลงเป็น Binary
- `Binary.ToText(..., BinaryEncoding.Hex)` แปลงเป็น Hex Text

**Step 2.5:** Apply Changes

---

### ขั้นตอนที่ 3: หา LastID จาก Dimension

**Step 3.1:** สร้าง Query ใหม่ชื่อ `LastID`

**Step 3.2:** ไปที่ **Home** → **Advanced Editor**

**Step 3.3:** พิมพ์สูตร:

```m
let
  Source = Dimension,
  #"Drill down" = Source[EmployeeKey],
  #"Calculated maximum" = List.Max(#"Drill down"),
  Custom = try #"Calculated maximum" + 1 otherwise 1
in
  Custom
```

**อธิบาย:**
- `List.Max(#"Drill down")` หา EmployeeKey สูงสุด
- `try ... otherwise 1` ถ้าไม่มีค่า (Dimension ใหม่) คืนค่า 1
- `+ 1` คำนวณ ID ต่อไป

**Step 3.4:** Apply Changes

---

### ขั้นตอนที่ 4: สร้าง NewRecords

**Step 4.1:** สร้าง Query ใหม่ชื่อ `NewRecords`

**Step 4.2:** ไปที่ **Home** → **Advanced Editor**

**Step 4.3:** พิมพ์สูตร:

```m
let
  Source = CompareStoM, // Records ใหม่ที่หาได้จากขั้นตอนก่อนหน้า
  #"Added index" = Table.AddIndexColumn(Source, "Index", LastID, 1, Int64.Type),
  #"Added custom" = Table.TransformColumnTypes(
    Table.AddColumn(#"Added index", "StartDate", 
      each Date.From(DateTime.LocalNow())
    ), 
    {{"StartDate", type date}}
  ),
  #"Added custom 1" = Table.TransformColumnTypes(
    Table.AddColumn(#"Added custom", "EndDate", 
      each #date(9999,12,31)
    ), 
    {{"EndDate", type date}}
  ),
  #"Added custom 2" = Table.TransformColumnTypes(
    Table.AddColumn(#"Added custom 1", "IsCurrent", 
      each true
    ), 
    {{"IsCurrent", type logical}}
  ),
  #"Renamed columns" = Table.RenameColumns(#"Added custom 2", {
    {"Index", "EmployeeKey"}
  })
in
  #"Renamed columns"
```

**อธิบายแต่ละขั้นตอน:**
- `Table.AddIndexColumn(..., LastID, 1, ...)` สร้าง EmployeeKey ใหม่ (เริ่มจาก LastID)
- `Table.AddColumn(..., "StartDate", ...)` เพิ่ม StartDate = วันนี้
- `Table.AddColumn(..., "EndDate", ...)` เพิ่ม EndDate = 9999-12-31
- `Table.AddColumn(..., "IsCurrent", ...)` เพิ่ม IsCurrent = TRUE
- `Table.RenameColumns(...)` เปลี่ยนชื่อ "Index" เป็น "EmployeeKey"

**Step 4.4:** Apply Changes

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องสร้าง Hash?**
   - **คำตอบ:** เพื่อตรวจสอบการเปลี่ยนแปลง โดยเปรียบเทียบ Hash ระหว่าง Source และ Dimension

2. **LastID ใช้ทำอะไร?**
   - **คำตอบ:** หา EmployeeKey ล่าสุดจาก Dimension เพื่อสร้าง EmployeeKey ใหม่สำหรับ NewRecords

3. **NewRecords มีโครงสร้างอย่างไร?**
   - **คำตอบ:** มี EmployeeKey (Surrogate Key), EmployeeID (Business Key), Attributes, StartDate, EndDate, IsCurrent, Hash

---

## 🎯 แบบฝึกหัดที่ 4: เขียน DAX สำหรับ SCD Type 2

### วัตถุประสงค์

เรียนรู้วิธีเขียน DAX Measures สำหรับ SCD Type 2

---

### ขั้นตอนที่ 1: สร้าง Measure สำหรับ Current Reps

**Step 1.1:** เข้าใจความต้องการ:

**ต้องการ:** ยอดขายของ Sales Rep ปัจจุบันเท่านั้น

**Step 1.2:** สร้าง Measure:

1. เลือก Table `FactSales` (หรือตาราง Fact ที่เกี่ยวข้อง)
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Sales - Current Reps = 
CALCULATE(
    SUM(FactSales[SalesAmount]),
    FullLoad[IsCurrent] = TRUE
)
```

**อธิบายสูตร:**
- `CALCULATE(...)` เปลี่ยน Filter Context
- `FullLoad[IsCurrent] = TRUE` Filter เฉพาะ Records ที่เป็นปัจจุบัน
- ผลลัพธ์ = ยอดขายของ Sales Rep ปัจจุบันเท่านั้น

**Step 1.3:** ทดสอบ Measure:
- สร้าง Visual ที่แสดง Sales - Current Reps
- ตรวจสอบว่าผลลัพธ์ถูกต้อง

---

### ขั้นตอนที่ 2: สร้าง Measure สำหรับ Historical Sales

**Step 2.1:** เข้าใจความต้องการ:

**ต้องการ:** ยอดขายทั้งหมด (รวมประวัติ)

**Step 2.2:** สร้าง Measure:

```dax
Sales - Historical = 
SUM(FactSales[SalesAmount])
```

**อธิบายสูตร:**
- ไม่ใช้ Filter IsCurrent
- รวมทุก Records (ทั้งปัจจุบันและเก่า)
- ผลลัพธ์ = ยอดขายทั้งหมด

---

### ขั้นตอนที่ 3: สร้าง Measure สำหรับ Current Region

**Step 3.1:** เข้าใจความต้องการ:

**ต้องการ:** Region ปัจจุบันของ Sales Rep

**Step 3.2:** สร้าง Measure:

```dax
Current Region = 
CALCULATE(
    MAX(FullLoad[Region]),
    FullLoad[IsCurrent] = TRUE,
    FullLoad[RepSourceID] = SELECTEDVALUE(FactSales[RepSourceID])
)
```

**อธิบายสูตร:**
- `MAX(FullLoad[Region])` ดึง Region (ใช้ MAX เพราะอาจมีหลาย Records)
- `FullLoad[IsCurrent] = TRUE` Filter เฉพาะ Records ปัจจุบัน
- `FullLoad[RepSourceID] = SELECTEDVALUE(...)` Filter เฉพาะ RepSourceID ที่เลือก

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้อง Filter ด้วย IsCurrent = TRUE?**
   - **คำตอบ:** เพื่อให้ได้เฉพาะข้อมูลปัจจุบัน เพราะ SCD Type 2 มีหลาย Records สำหรับ RepSourceID เดียวกัน

2. **Sales - Historical แตกต่างจาก Sales - Current Reps อย่างไร?**
   - **คำตอบ:** Sales - Historical รวมทุก Records (ทั้งปัจจุบันและเก่า), Sales - Current Reps รวมเฉพาะ Records ปัจจุบัน

---

## 🎯 แบบฝึกหัดที่ 5: การสร้าง Relationships กับ SCD Type 2

### วัตถุประสงค์

เข้าใจวิธีสร้าง Relationships กับ SCD Type 2 Dimension

---

### ขั้นตอนที่ 1: เข้าใจปัญหา

**Step 1.1:** ปัญหา:

- ใน SCD Type 2 Dimension อาจมีหลาย Records สำหรับ Business Key เดียวกัน
- Fact Table อาจมี Business Key หรือ Surrogate Key

**Step 1.2:** คำถาม:

- ควรใช้ Key อะไรในการสร้าง Relationship?
- ถ้ามีหลาย Records จะสร้าง Relationship อย่างไร?

---

### ขั้นตอนที่ 2: สร้าง Relationship ด้วย Surrogate Key

**Step 2.1:** วิธีที่แนะนำ:

**ใช้ Surrogate Key:**
- FactSales[SalesRepID] → FullLoad[SalesRepID]
- Surrogate Key เป็น Unique
- แต่ละ SalesRepID ใน Fact Table จะชี้ไปยัง FullLoad Record ที่ถูกต้อง

**Step 2.2:** สร้าง Relationship:

1. ไปที่ **Model View**
2. ลาก `SalesRepID` จาก `FactSales` ไปยัง `FullLoad[SalesRepID]`
3. ตรวจสอบ Cardinality: One-to-Many (1:*)
4. คลิก **OK**

---

### ขั้นตอนที่ 3: Filter Current Records

**Step 3.1:** เข้าใจปัญหา:

- Relationship อาจชี้ไปยัง Records เก่า (IsCurrent = FALSE)
- ต้อง Filter Current Records ใน Measures

**Step 3.2:** ใช้ Filter ใน DAX:

```dax
Sales - Current = 
CALCULATE(
    SUM(FactSales[SalesAmount]),
    FullLoad[IsCurrent] = TRUE
)
```

**อธิบาย:**
- Filter `IsCurrent = TRUE` ใน Measure
- ใช้ได้กับทุก Measure ที่ต้องการ Current Data

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ในการสร้าง Relationship กับ SCD Type 2 Dimension ควรใช้ Key อะไร?**
   - **คำตอบ:** ควรใช้ Surrogate Key (เช่น SalesRepID) เพราะเป็น Unique และใช้สำหรับเชื่อมโยงกับ Fact Table

2. **ทำไมต้อง Filter ด้วย IsCurrent = TRUE?**
   - **คำตอบ:** เพื่อให้ได้เฉพาะข้อมูลปัจจุบัน เพราะ SCD Type 2 มีหลาย Records สำหรับ Business Key เดียวกัน

---

## 🎯 แบบฝึกหัดที่ 6: Attribute Hierarchies - Parent-Child Hierarchy

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Parent-Child Hierarchy จาก Employee Table

---

### ขั้นตอนที่ 1: ตรวจสอบโครงสร้างตาราง Employee

**Step 1.1:** เปิด AdventureWorksDW2025

**Step 1.2:** ตรวจสอบตาราง `DimEmployee`:

**Columns ที่เกี่ยวข้อง:**
- **EmployeeKey** - Key ของ Employee (Primary Key)
- **ParentEmployeeKey** - Key ของ Manager/Parent (ชี้ไปยัง EmployeeKey ของ Manager)

**Step 1.3:** สังเกตข้อมูล:
- Employee ที่ไม่มี Manager → ParentEmployeeKey = NULL (CEO)
- Employee ที่มี Manager → ParentEmployeeKey = EmployeeKey ของ Manager

---

### ขั้นตอนที่ 2: สร้าง Calculated Columns ด้วย PATH Functions

**Step 2.1:** สร้าง Calculated Column: **Path**

1. เลือก Table `DimEmployee`
2. ไปที่ **Table tools** → **New column**
3. พิมพ์สูตร:

```dax
Path = PATH(DimEmployee[EmployeeKey], DimEmployee[ParentEmployeeKey])
```

**อธิบายสูตร:**
- `PATH(EmployeeKey, ParentEmployeeKey)` สร้างเส้นทางจาก Employee ไปยัง CEO
- ผลลัพธ์: "1|2|3|4" (แยกด้วย Pipe |) โดยเริ่มจาก CEO

**ตัวอย่างผลลัพธ์:**
- EmployeeKey = 1, ParentEmployeeKey = NULL → `"1"` (CEO)
- EmployeeKey = 2, ParentEmployeeKey = 1 → `"1|2"` (CEO → VP)
- EmployeeKey = 3, ParentEmployeeKey = 2 → `"1|2|3"` (CEO → VP → Manager)

**Step 2.2:** สร้าง Calculated Column: **Path Length**

```dax
PathLEN = PATHLENGTH(DimEmployee[Path])
```

**อธิบายสูตร:**
- `PATHLENGTH(Path)` หาความยาวของ Path (จำนวนระดับ)

**ตัวอย่างผลลัพธ์:**
- Path = `"1"` → PathLEN = `1` (CEO)
- Path = `"1|2"` → PathLEN = `2` (VP)
- Path = `"1|2|3"` → PathLEN = `3` (Manager)

**Step 2.3:** สร้าง Calculated Column: **Level 1 (CEO)**

```dax
Level 1 (CEO) = 
PATHITEM(DimEmployee[Path], 1, INTEGER)
```

**อธิบายสูตร:**
- `PATHITEM(Path, 1, INTEGER)` ดึงค่าในตำแหน่งที่ 1 จาก Path (CEO)

**Step 2.4:** สร้าง Calculated Columns: **Level 2, 3, 4**

```dax
Level 2 (VP) = 
PATHITEM(DimEmployee[Path], 2, INTEGER)

Level 3 (Manager) = 
PATHITEM(DimEmployee[Path], 3, INTEGER)

Level 4 (Employee) = 
PATHITEM(DimEmployee[Path], 4, INTEGER)
```

---

### ขั้นตอนที่ 3: สร้าง Org Level Columns ด้วย LOOKUPVALUE

**Step 3.1:** สร้าง Calculated Column: **Org Level 1 (CEO Name)**

```dax
Org Level 1 (CEO) = 
LOOKUPVALUE(
    DimEmployee[FullName],
    DimEmployee[EmployeeKey],
    PATHITEM(DimEmployee[Path], 1, INTEGER)
)
```

**อธิบายสูตร:**
- `PATHITEM(..., 1, INTEGER)` ดึง EmployeeKey ของ CEO
- `LOOKUPVALUE(...)` ดึง FullName จาก EmployeeKey

**Step 3.2:** สร้าง Calculated Columns: **Org Level 2, 3, 4**

```dax
Org Level 2 (VP) = 
LOOKUPVALUE(
    DimEmployee[FullName],
    DimEmployee[EmployeeKey],
    PATHITEM(DimEmployee[Path], 2, INTEGER)
)

Org Level 3 (Manager) = 
LOOKUPVALUE(
    DimEmployee[FullName],
    DimEmployee[EmployeeKey],
    PATHITEM(DimEmployee[Path], 3, INTEGER)
)

Org Level 4 (Employee) = 
LOOKUPVALUE(
    DimEmployee[FullName],
    DimEmployee[EmployeeKey],
    PATHITEM(DimEmployee[Path], 4, INTEGER)
)
```

---

### ขั้นตอนที่ 4: สร้าง Hierarchy ใน Power BI Desktop

**Step 4.1:** ไปที่ **Model View**

**Step 4.2:** Right-click ที่ `DimEmployee` Table

**Step 4.3:** เลือก **New hierarchy**

**Step 4.4:** ตั้งชื่อเป็น **Organization Hierarchy**

**Step 4.5:** เพิ่ม Levels:
1. ลาก `Level 1 (CEO)` ไปยัง Hierarchy
2. ลาก `Level 2 (VP)` ไปยัง Hierarchy
3. ลาก `Level 3 (Manager)` ไปยัง Hierarchy
4. ลาก `Level 4 (Employee)` ไปยัง Hierarchy

**Step 4.6:** ตรวจสอบ Hierarchy:
- ควรเห็น Hierarchy แบบ: Level 1 → Level 2 → Level 3 → Level 4

---

### ขั้นตอนที่ 5: ทดสอบ Hierarchy

**Step 5.1:** ไปที่ **Report View**

**Step 5.2:** สร้าง Matrix Visual:
1. ลาก `Organization Hierarchy` ไปยัง Rows
2. ลาก `FullName` ไปยัง Rows
3. ลาก `Sales by Organization` ไปยัง Values

**Step 5.3:** Expand Hierarchy:
- คลิกที่ลูกศรเพื่อ Expand แต่ละ Level
- ตรวจสอบว่าโครงสร้างถูกต้องหรือไม่

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **PATH Functions ทำงานอย่างไร?**
   - **คำตอบ:** PATH สร้างเส้นทางจาก Employee ไปยัง CEO, PATHITEM ดึงค่าในตำแหน่งที่กำหนด, PATHLENGTH หาความยาวของ Path

2. **Hierarchy ที่สร้างมีโครงสร้างอย่างไร?**
   - **คำตอบ:** Level 1 (CEO) → Level 2 (VP) → Level 3 (Manager) → Level 4 (Employee)

3. **ทำไมต้องใช้ LOOKUPVALUE() แทน RELATED()?**
   - **คำตอบ:** เพราะ Parent-Child Hierarchy ไม่มี Relationship ระหว่าง Parent และ Child, RELATED() ใช้กับ Relationships เท่านั้น, LOOKUPVALUE() ใช้เพื่อดึงข้อมูลจาก Table โดยไม่ต้องมี Relationship

---

## 🎯 แบบฝึกหัดที่ 7: สร้าง Path Column

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Path Column สำหรับ Parent-Child Hierarchy

---

### ขั้นตอนที่ 1: เข้าใจโครงสร้าง

**Step 1.1:** ตรวจสอบตาราง `Employee`:

**Columns:**
- `EmployeeKey` - Key ของ Employee
- `ParentEmployeeKey` - Key ของ Manager/Parent

**Step 1.2:** สังเกตข้อมูล:
- Employee ที่ไม่มี Manager → ParentEmployeeKey = NULL
- Employee ที่มี Manager → ParentEmployeeKey = EmployeeKey ของ Manager

---

### ขั้นตอนที่ 2: สร้าง Path Column

**Step 2.1:** สร้าง Calculated Column ชื่อ `Path`:

**Step 2.2:** พิมพ์สูตร:

```dax
Path = PATH(Employee[EmployeeKey], Employee[ParentEmployeeKey])
```

**อธิบายสูตร:**
- `PATH(EmployeeKey, ParentEmployeeKey)` สร้างเส้นทางจาก Employee ไปยัง Root (CEO)
- คืนค่าเป็น Text ที่มี Keys คั่นด้วย Pipe (`|`)

**Step 2.3:** ตรวจสอบผลลัพธ์:

**ตัวอย่างผลลัพธ์:**
- EmployeeKey = 1, ParentEmployeeKey = NULL → `"1"` (CEO)
- EmployeeKey = 2, ParentEmployeeKey = 1 → `"1|2"` (CEO → VP)
- EmployeeKey = 3, ParentEmployeeKey = 2 → `"1|2|3"` (CEO → VP → Manager)
- EmployeeKey = 4, ParentEmployeeKey = 3 → `"1|2|3|4"` (CEO → VP → Manager → Employee)

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **PATH() ทำงานอย่างไร?**
   - **คำตอบ:** PATH() สร้างเส้นทางจาก Child (EmployeeKey) ไปยัง Parent (ParentEmployeeKey) ทั้งหมด คืนค่าเป็น Text ที่มี Keys คั่นด้วย Pipe (`|`) โดยเริ่มจาก Root (CEO)

2. **ทำไม Employee ที่ไม่มี ParentEmployeeKey จะได้ Path = "1" (ตัวเดียว)?**
   - **คำตอบ:** เพราะ Employee นั้นคือ Root (CEO) ไม่มี Parent จึง Path มีแค่ EmployeeKey ของตัวเอง

---

## 🎯 แบบฝึกหัดที่ 8: สร้าง Org Level Columns

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Org Level Columns เพื่อใช้ใน Hierarchy

---

### ขั้นตอนที่ 1: เข้าใจความต้องการ

**Step 1.1:** ความต้องการ:

- สร้าง Columns สำหรับแต่ละ Level ใน Organization
- Level 1 = CEO (Root)
- Level 2 = VP
- Level 3 = Manager
- Level 4 = Employee

**Step 1.2:** วิธีทำ:

- ใช้ PATHITEM() เพื่อดึงค่าในตำแหน่งที่กำหนด
- ใช้ LOOKUPVALUE() เพื่อดึง FullName จาก EmployeeKey

---

### ขั้นตอนที่ 2: สร้าง Org Level 1 (CEO)

**Step 2.1:** สร้าง Calculated Column:

```dax
Org Level 1 (CEO) = 
LOOKUPVALUE(
    Employee[FullName],
    Employee[EmployeeKey],
    PATHITEM(Employee[Path], 1, INTEGER)
)
```

**อธิบายสูตร:**
- `PATHITEM(Employee[Path], 1, INTEGER)` ดึงค่าในตำแหน่งที่ 1 จาก Path (CEO)
- `LOOKUPVALUE(...)` ดึง FullName จาก EmployeeKey ที่ได้

---

### ขั้นตอนที่ 3: สร้าง Org Level 2, 3, 4

**Step 3.1:** สร้าง Org Level 2 (VP):

```dax
Org Level 2 (VP) = 
LOOKUPVALUE(
    Employee[FullName],
    Employee[EmployeeKey],
    PATHITEM(Employee[Path], 2, INTEGER)
)
```

**Step 3.2:** สร้าง Org Level 3 (Manager):

```dax
Org Level 3 (Manager) = 
LOOKUPVALUE(
    Employee[FullName],
    Employee[EmployeeKey],
    PATHITEM(Employee[Path], 3, INTEGER)
)
```

**Step 3.3:** สร้าง Org Level 4 (Employee):

```dax
Org Level 4 (Employee) = 
LOOKUPVALUE(
    Employee[FullName],
    Employee[EmployeeKey],
    PATHITEM(Employee[Path], 4, INTEGER)
)
```

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องใช้ LOOKUPVALUE() แทน RELATED()?**
   - **คำตอบ:** เพราะ Parent-Child Hierarchy ไม่มี Relationship ระหว่าง Parent และ Child, RELATED() ใช้กับ Relationships เท่านั้น, LOOKUPVALUE() ใช้เพื่อดึงข้อมูลจาก Table โดยไม่ต้องมี Relationship

2. **PATHITEM() ใช้ทำอะไร?**
   - **คำตอบ:** ดึงค่าที่อยู่ในตำแหน่งที่กำหนดในเส้นทาง (Path) เช่น PATHITEM(Path, 1, INTEGER) ดึงค่าในตำแหน่งที่ 1 (CEO)

---

## 🎯 แบบฝึกหัดที่ 9: เข้าใจ PATH Functions

### วัตถุประสงค์

เข้าใจ PATH Functions ทั้งหมด (PATH, PATHITEM, PATHLENGTH) และการใช้งาน

---

### ขั้นตอนที่ 1: เข้าใจ PATH()

**Step 1.1:** เข้าใจว่า PATH() คืออะไร:

**PATH()** = Function ที่สร้างเส้นทางจาก Child ไปยัง Parent ทั้งหมด

**Syntax:**
```dax
PATH(<childColumn>, <parentColumn>)
```

**ผลลัพธ์:**
- Text ที่มี Keys คั่นด้วย Pipe (`|`)
- เริ่มจาก Root (Parent = NULL) ไปยัง Child

**Step 1.2:** ตัวอย่าง:

```dax
Path = PATH(Employee[EmployeeKey], Employee[ParentEmployeeKey])
```

**ผลลัพธ์:**
- EmployeeKey = 1, ParentEmployeeKey = NULL → `"1"`
- EmployeeKey = 2, ParentEmployeeKey = 1 → `"1|2"`
- EmployeeKey = 3, ParentEmployeeKey = 2 → `"1|2|3"`

---

### ขั้นตอนที่ 2: เข้าใจ PATHITEM()

**Step 2.1:** เข้าใจว่า PATHITEM() คืออะไร:

**PATHITEM()** = Function ที่ดึงค่าที่อยู่ในตำแหน่งที่กำหนดในเส้นทาง

**Syntax:**
```dax
PATHITEM(<path>, <position>[, <type>])
```

**Parameters:**
- `path` - เส้นทางที่สร้างจาก PATH()
- `position` - ตำแหน่งที่ต้องการดึง (เริ่มจาก 1)
- `type` - Type ของผลลัพธ์ (TEXT หรือ INTEGER)

**Step 2.2:** ตัวอย่าง:

```dax
Level 1 = PATHITEM(Employee[Path], 1, INTEGER)
Level 2 = PATHITEM(Employee[Path], 2, INTEGER)
```

**ผลลัพธ์:**
- Path = `"1|2|3"` → Level 1 = `1`, Level 2 = `2`, Level 3 = `3`

---

### ขั้นตอนที่ 3: เข้าใจ PATHLENGTH()

**Step 3.1:** เข้าใจว่า PATHLENGTH() คืออะไร:

**PATHLENGTH()** = Function ที่หาความยาวของเส้นทาง (จำนวนระดับ)

**Syntax:**
```dax
PATHLENGTH(<path>)
```

**ผลลัพธ์:**
- ตัวเลขที่บอกจำนวนระดับ

**Step 3.2:** ตัวอย่าง:

```dax
PathLEN = PATHLENGTH(Employee[Path])
```

**ผลลัพธ์:**
- Path = `"1"` → PathLEN = `1`
- Path = `"1|2"` → PathLEN = `2`
- Path = `"1|2|3"` → PathLEN = `3`

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **PATH() ใช้ทำอะไร?**
   - **คำตอบ:** สร้างเส้นทางจาก Child ไปยัง Parent ทั้งหมด คืนค่าเป็น Text ที่มี Keys คั่นด้วย Pipe (`|`)

2. **PATHITEM() ใช้ทำอะไร?**
   - **คำตอบ:** ดึงค่าที่อยู่ในตำแหน่งที่กำหนดในเส้นทาง ใช้ Type (TEXT/INTEGER) เพื่อกำหนด Type ของผลลัพธ์

3. **PATHLENGTH() ใช้ทำอะไร?**
   - **คำตอบ:** หาความยาวของเส้นทาง (จำนวนระดับ) คืนค่าเป็นตัวเลข

4. **ทำไมต้องใช้ LOOKUPVALUE() แทน RELATED() ใน Parent-Child Hierarchy?**
   - **คำตอบ:** เพราะ Parent-Child Hierarchy ไม่มี Relationship ระหว่าง Parent และ Child, RELATED() ใช้กับ Relationships เท่านั้น, LOOKUPVALUE() ใช้เพื่อดึงข้อมูลจาก Table โดยไม่ต้องมี Relationship

---

## 📝 สรุป

### ✅ สิ่งที่ควรจำ

1. **SCD Type 2** เก็บประวัติการเปลี่ยนแปลงโดยเพิ่ม Records ใหม่และปิด Records เก่า
2. **Hash** ใช้เพื่อตรวจสอบการเปลี่ยนแปลง โดยสร้างจาก Dimension Attributes ที่สำคัญ
3. **IsCurrent** ใช้เพื่อระบุบันทึกปัจจุบัน (TRUE = ปัจจุบัน, FALSE = เก่า)
4. **StartDate และ EndDate** ระบุช่วงเวลาที่ใช้ได้
5. **Surrogate Key** ใช้สำหรับ Relationships (Unique)
6. **Business Key** ใช้เพื่อระบุตัวตนทางธุรกิจ (ไม่ Unique ใน SCD Type 2)
7. **PATH()** สร้างเส้นทางจาก Child ไปยัง Parent
8. **PATHITEM()** ดึงค่าในตำแหน่งที่กำหนด
9. **PATHLENGTH()** หาความยาวของเส้นทาง
10. **LOOKUPVALUE()** ใช้ดึงข้อมูลจาก Parent (แทน RELATED()) ใน Parent-Child Hierarchy

---

**📖 เอกสารที่เกี่ยวข้อง:**
- [README.md](./README.md) - เอกสารหลักของโมดูล
- [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) - Code Examples เพิ่มเติม
