# 07 - Date Dimensions & Relationships

## เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับการสร้างและใช้ Date Dimensions ซึ่งเป็น Dimension ที่สำคัญที่สุดใน Semantic Model และการใช้ Relationships กับ Date Tables โดยเฉพาะ Conformed Date Dimension Pattern

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 📋 หัวข้อการเรียนรู้

### 1. Date Dimension (ตารางมิติวันที่)

**Date Dimension** เป็นองค์ประกอบสำคัญที่สุดใน Data Warehouse และ Semantic Model ที่ใช้ในการวิเคราะห์ข้อมูลเชิงเวลา

#### 1.1 ลักษณะที่ควรพิจารณาในการออกแบบ Date Dimension

**1. ช่วงวันที่ครอบคลุมเพียงพอ**
- ครอบคลุมวันที่ทั้งหมดที่ใช้ใน Fact Tables
- มักสร้างตั้งแต่ปีที่ผ่านมาไปจนถึงปีอนาคต
- ควรมีวันที่มากพอสำหรับการวิเคราะห์ย้อนหลังและอนาคต

**2. Surrogate Key**
- สร้างคีย์หลักที่ไม่ซ้ำกันสำหรับแต่ละวัน
- ใช้ในการเชื่อมโยงกับตาราง Fact
- มักเป็นเลขจำนวนเต็ม (เช่น DateKey = 20240101 สำหรับ 2024-01-01)

**3. คอลัมน์สำหรับ Sort**
- ใช้สำหรับเรียงลำดับวันที่ให้ถูกต้อง
- เช่น `DimDate[DateKey]` หรือ `DimDate[FullDateAlternateKey]`
- ควรใช้คอลัมน์ Date สำหรับ Sort โดยตรง

**4. คอลัมน์รายละเอียดของวันที่**

**Calendar Year Attributes:**
- `CalendarYear` - ปีปฏิทิน (เช่น 2024)
- `CalendarQuarter` - ไตรมาส (1, 2, 3, 4)
- `CalendarMonth` - เดือน (1-12)
- `CalendarMonthName` - ชื่อเดือน (January, February, ...)
- `DayOfWeek` - วันในสัปดาห์ (1-7)
- `DayOfMonth` - วันในเดือน (1-31)
- `DayOfYear` - วันในปี (1-365)
- `WeekNumber` - สัปดาห์ในปี (1-52/53)
- `DayName` - ชื่อวัน (Monday, Tuesday, ...)

**5. สามารถมีหลายปฏิทินในตารางมิติวันที่เดียวกัน**
- Calendar Year (ปฏิทินทั่วไป)
- Fiscal Year (ปีงบประมาณ)
- Holiday Calendar (ปฏิทินวันหยุด)

#### 1.2 ปฏิทินหลายแบบใน DimDate เดียวกัน

**Calendar Year (ปฏิทินทั่วไป):**
- `CalendarYear` - แสดงปีปฏิทิน (2024)
- `CalendarQuarter` - แสดงไตรมาส (1-4)
- `CalendarMonth` - แสดงเดือน (1-12)
- `CalendarMonthName` - ชื่อเดือน (January-December)

**Fiscal Year (ปีงบประมาณ):**
- `FiscalYear` - แสดงปีงบประมาณ (อาจแตกต่างจาก Calendar Year)
- `FiscalQuarter` - แสดงไตรมาสของปีงบประมาณ
- `FiscalMonth` - แสดงเดือนในปีงบประมาณ
- **หมายเหตุ:** Fiscal Year อาจเริ่มต้นที่เดือนอื่น (เช่น ตุลาคม-กันยายน)

**Holiday Calendar (ปฏิทินวันหยุด):**
- `IsHoliday` - ค่าบูลีนที่ระบุว่าวันนั้นเป็นวันหยุดหรือไม่ (TRUE/FALSE)
- `HolidayName` - ชื่อของวันหยุด (ถ้ามี เช่น "New Year", "Christmas")
- `IsWeekend` - วันหยุดสุดสัปดาห์ (TRUE/FALSE)

> **ของใหม่ (Preview): Enhanced DAX Time Intelligence** — นับจาก Power BI Desktop กันยายน 2025 สามารถนิยาม **Custom Calendar** (เช่น ปีงบประมาณ หรือปฏิทิน 4-5-4 ของค้าปลีก) สำหรับ Time Intelligence ได้โดยตรง พร้อมฟังก์ชันใหม่อย่าง `TOTALWTD`, `PREVIOUSWEEK` — ดู [Implement time-based calculations in Power BI - Microsoft Learn](https://learn.microsoft.com/power-bi/transform-model/desktop-time-intelligence) (ขณะนี้ยังเป็น Preview — ในคอร์สนี้เราใช้วิธีสร้าง DimDate แบบ Conformed Dimension ตามที่อธิบายด้านล่างเป็นหลัก)

**ตัวอย่างโครงสร้าง (AdventureWorksDW2025):**
```
DimDate
├── DateKey (Surrogate Key)
├── FullDateAlternateKey (Date for Sort)
├── CalendarYear
├── CalendarQuarter
├── CalendarMonth
├── CalendarMonthName
├── FiscalYear
├── FiscalQuarter
├── FiscalMonth
├── IsHoliday
├── HolidayName
└── ...
```

#### 1.3 การ Mark as Date Table

**เมื่อไหร่ใช้:**
- เมื่อสร้าง DimDate ขึ้นมาเอง (ไม่ใช่ Auto Date/Time)
- เมื่อตั้งใจใช้เป็น Conformed Dimension
- เมื่อต้องการใช้ Time Intelligence Functions อย่างเต็มรูปแบบ

**ประโยชน์:**
- รองรับ Time Intelligence Functions ต่างๆ ได้สมบูรณ์ (DATESYTD, DATESQTD, SAMEPERIODLASTYEAR, etc.)
- Power BI จะรู้ว่าตารางนี้เป็น Date Table
- คุณสมบัติ Auto date/time บนทุกตารางจะถูกยกเลิกอัตโนมัติ

**ข้อกำหนด:**
- ตาราง DimDate ต้องมีคอลัมน์ที่มีชนิดข้อมูลเป็น Date หรือ Datetime
- ความละเอียดของคอลัมน์ที่สูงสุดต้องไม่เกินระดับ 1 วัน
- ต้องมีข้อมูลครบทุกวันในช่วงเวลาที่กำหนด (ไม่ควรมีวันที่ขาดหายไป)

**วิธีการ Mark as Date Table:**
1. เปิด Power BI Desktop
2. ไปที่ **Model View**
3. คลิกขวาที่ตาราง DimDate
4. เลือก **"Mark as Date Table"**
5. เลือกคอลัมน์ Date Key (เช่น `FullDateAlternateKey`)

#### 1.4 ทำไมจึงควรปิด Auto Date/Time

**ปัญหาของ Auto Date/Time:**

**1. สร้าง Hierarchies อัตโนมัติ:**
- ถ้าเปิดไว้ Power BI จะตรวจจับคอลัมน์ที่มี Type เป็น Date/Datetime แล้วสร้างเป็น Hierarchy ขึ้นมาอัตโนมัติ
- แต่ละ Fact Table จะมี Date Hierarchy ของตัวเองแยกกัน

**2. ไม่สามารถดู Measures ร่วมกันได้:**
- หากต้องการนำ Measure จากต่าง Fact Table มาดูร่วมกันในมิติเวลา จะไม่สามารถทำได้
- แต่ละ Fact Table จะมี Date Hierarchy ของตัวเอง (Date ใคร Date มัน)
- ไม่สามารถใช้ DimDate ร่วมกันได้

**3. ความไม่สอดคล้อง:**
- Date Hierarchies ถูกสร้างอัตโนมัติแยกกัน
- ไม่สามารถควบคุมโครงสร้างได้
- ไม่สอดคล้องกับการใช้ Conformed Date Dimension

**วิธีแก้ไข:**
- สร้าง Conformed Date Dimension ขึ้นมาเอง
- ยกเลิก Auto Date/Time

**การปิด Auto Date/Time:**

**ระดับ Global (แนะนำ):**
1. ไปที่ **File** → **Options and Settings** → **Options**
2. เลือก **Global** → **Data Load**
3. ยกเลิกการติ๊ก **"Auto date/time"**
4. คลิก **OK**
5. ตั้งค่านี้จะมีผลกับไฟล์ Power BI ที่สร้างขึ้นใหม่ทันที

**ระดับ Current File:**
1. ไปที่ **File** → **Options and Settings** → **Options**
2. เลือก **Current File** → **Data Load**
3. ยกเลิกการติ๊ก **"Auto date/time"**
4. คลิก **OK**
5. ตั้งค่านี้จะมีผลกับไฟล์ปัจจุบันเท่านั้น

---

### 3. Conformed Date Dimension Pattern

**Conformed Dimension** หมายถึง Dimension ที่มีโครงสร้างและข้อมูลเหมือนกัน และถูกใช้ร่วมกันในหลาย Fact Tables เพื่อให้การวิเคราะห์สอดคล้องกัน

**Conformed Date Dimension** คือ Date Dimension ที่ใช้ร่วมกันระหว่างหลาย Fact Tables เพื่อให้การวิเคราะห์ข้อมูลมีความสอดคล้องกัน


#### 3.1 ความหมายและประโยชน์

**Conformed Date Dimension Pattern** ประกอบด้วย:
- **ตาราง DimDate เดียว** - ใช้ร่วมกันทุก Fact Table
- **Multiple Date Relationships** - สร้าง Relationships หลายตัวผ่าน Date Keys ที่แตกต่างกัน
- **Role-Playing Pattern** - ใช้ DimDate เดียวในบทบาทต่างๆ (Order Date, Ship Date, Due Date)

**ตัวอย่าง (AdventureWorksDW2025):**
- `DimDate` → Conformed Dimension (ตารางเดียว)
- `FactInternetSales` → เชื่อมผ่าน OrderDateKey, ShipDateKey, DueDateKey
- `FactResellerSales` → เชื่อมผ่าน OrderDateKey, ShipDateKey, DueDateKey

**ประโยชน์:**
- ✅ ความสอดคล้องของข้อมูล - ทุก Fact Table ใช้ DimDate เดียวกัน
- ✅ ลดความซ้ำซ้อน - ไม่ต้องสร้าง DimDate หลายตัว
- ✅ ง่ายต่อการบำรุงรักษา - อัพเดท DimDate เดียว
- ✅ สามารถดู Measures จากหลาย Fact Tables ร่วมกันได้ - ใช้ DimDate ร่วมกัน

**👉 ดูตัวอย่างที่สมบูรณ์:** `Data Model Conformed Date Dimension.pbix`

> **หมายเหตุ:** ไฟล์ตัวอย่างเป็นสื่อการสอนของผู้สอน (Trainer Material) — ขอไฟล์ได้จากผู้สอนระหว่างเรียน ไม่ได้แจกจ่ายผ่าน repository นี้

#### 3.2 Role-Playing Dimensions กับ Multiple Date Relationships

**Role-Playing Dimensions** คือแนวคิดที่ใช้ตารางมิติเดียวกัน (DimDate) ในบทบาทต่างๆ ผ่าน **Multiple Date Relationships**

**ตัวอย่างโครงสร้าง Relationships:**

```
DimDate (Conformed Dimension)
│
├── FactInternetSales
│   ├── OrderDateKey → DimDate[DateKey] (Active) ✅
│   ├── ShipDateKey → DimDate[DateKey] (Inactive) ⚠️
│   └── DueDateKey → DimDate[DateKey] (Inactive) ⚠️
│
└── FactResellerSales
    ├── OrderDateKey → DimDate[DateKey] (Active) ✅
    ├── ShipDateKey → DimDate[DateKey] (Inactive) ⚠️
    └── DueDateKey → DimDate[DateKey] (Inactive) ⚠️
```

**Role-Playing ในตัวอย่างนี้:**
- DimDate ในบทบาท "Order Date" → Active Relationship
- DimDate ในบทบาท "Ship Date" → Inactive Relationship
- DimDate ในบทบาท "Due Date" → Inactive Relationship

**Active vs Inactive Relationships:**

**Active Relationship:**
- ใช้สำหรับวันที่ที่ใช้บ่อยที่สุด (OrderDate)
- ถูกใช้เป็นค่าเริ่มต้นเมื่อสร้าง Measures
- เมื่อกรอง DimDate จะกรองตาม Active Relationship อัตโนมัติ
- **บทบาท:** DimDate เป็น "Order Date"

**Inactive Relationships:**
- ใช้สำหรับวันที่ที่ใช้เฉพาะบาง Measures (ShipDate, DueDate)
- ต้องใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน
- ไม่ถูกใช้โดยอัตโนมัติ
- **บทบาท:** DimDate เป็น "Ship Date" หรือ "Due Date"

**หลักการเลือก Active Relationship:**
- เลือกวันที่ที่ใช้บ่อยที่สุด (เช่น OrderDate)
- ถ้าไม่แน่ใจ ให้เลือก OrderDate เป็น Active
- สามารถเปลี่ยน Active/Inactive ได้ในภายหลัง

#### 3.3 วิธีการสร้าง Role-Playing Dimensions ใน Power BI

**วิธีที่ 1: Inactive Relationships (แนะนำ - ใช้ Conformed Dimension)**

**ขั้นตอน:**

**1. สร้าง Multiple Relationships:**
- สร้างความสัมพันธ์หลายแบบระหว่าง DimDate และ Fact Table
- Power BI อนุญาตให้มีความสัมพันธ์ที่ใช้งานอยู่ (Active) เพียงหนึ่งเดียว
- ความสัมพันธ์อื่นๆ จะถูกตั้งค่าเป็นไม่ใช้งาน (Inactive)

**2. กำหนด Active Relationship:**
- เลือกวันที่ที่ใช้บ่อยที่สุดเป็น Active (เช่น OrderDate)
- ตั้งค่าวันที่อื่นๆ เป็น Inactive (เช่น ShipDate, DueDate)

**3. ใช้ USERELATIONSHIP() ใน Measures:**
- เมื่อต้องการใช้ Inactive Relationship ใช้ USERELATIONSHIP() ใน CALCULATE()
- เปิดใช้งาน Inactive Relationship ชั่วคราวสำหรับ Measure นั้นๆ

**ข้อดี:**
- ✅ ใช้ DimDate เดียว (Conformed Dimension)
- ✅ ลดความซ้ำซ้อนของข้อมูล
- ✅ ง่ายต่อการบำรุงรักษา (อัพเดท DimDate เดียว)
- ✅ สามารถดู Measures จากหลาย Fact Tables ร่วมกันได้
- ✅ ประหยัดพื้นที่จัดเก็บ

**ข้อเสีย:**
- ⚠️ ต้องใช้ USERELATIONSHIP() ใน Measures สำหรับ Inactive Relationships
- ⚠️ อาจซับซ้อนสำหรับผู้เริ่มต้น

**วิธีที่ 2: Duplicate Tables**

**ขั้นตอน:**
1. Duplicate ตาราง DimDate เป็น 3 ตาราง:
   - `Order Date` (Duplicate ของ DimDate)
   - `Ship Date` (Duplicate ของ DimDate)
   - `Due Date` (Duplicate ของ DimDate)

2. สร้าง Relationships แยกกัน:
   - `FactResellerSales[OrderDateKey]` → `Order Date[DateKey]` (Active)
   - `FactResellerSales[ShipDateKey]` → `Ship Date[DateKey]` (Active)
   - `FactResellerSales[DueDateKey]` → `Due Date[DateKey]` (Active)

**ข้อดี:**
- ✅ ไม่ต้องใช้ USERELATIONSHIP()
- ✅ เข้าใจง่ายกว่า

**ข้อเสีย:**
- ❌ ซ้ำซ้อนของข้อมูล (DimDate ถูกลอกหลายครั้ง)
- ❌ ใช้พื้นที่จัดเก็บมากขึ้น
- ❌ การบำรุงรักษายากขึ้น (ต้องอัพเดทหลายตาราง)
- ❌ ไม่สามารถดู Measures จากหลาย Fact Tables ร่วมกันได้ง่าย
- ❌ ไม่ใช่ Conformed Dimension

**สรุป:** วิธีที่ 1 (Inactive Relationships) เป็น Best Practice สำหรับ Role-Playing Dimensions

---

### 4. USERELATIONSHIP() กับ Date Relationships

**USERELATIONSHIP()** เป็นฟังก์ชัน DAX ที่ใช้เพื่อเปิดใช้งาน Inactive Relationship ชั่วคราวในบริบทของการคำนวณ Measure

#### 4.1 ทำไมต้องใช้ USERELATIONSHIP()?

**สถานการณ์:** 
- มี Fact Table ที่มี Date Keys หลายตัว (OrderDate, ShipDate, DueDate)
- แต่ละ Date Key เชื่อมกับ DimDate แบบ Inactive Relationships (ยกเว้น 1 Active)
- เมื่อสร้าง Measure ที่ต้องการใช้ ShipDate หรือ DueDate ต้องเปิดใช้งาน Inactive Relationship

**USERELATIONSHIP()** ช่วยให้เราสามารถ:
- เปิดใช้งาน Inactive Relationship ชั่วคราว
- ใช้ Date Context ที่แตกต่างกันใน Measure เดียวกัน
- ไม่ต้องสร้าง Duplicate Tables

#### 4.2 Syntax และการใช้งาน

**Syntax:**
```dax
CALCULATE(
    [Measure],
    USERELATIONSHIP(DimensionTable[KeyColumn], FactTable[ForeignKeyColumn])
)
```

**กฎสำคัญ:**
1. ต้องใช้ภายใน `CALCULATE()` เสมอ
2. Parameter แรก: คอลัมน์จาก Dimension Table (เช่น `DimDate[DateKey]`)
3. Parameter ที่สอง: คอลัมน์จาก Fact Table (เช่น `FactResellerSales[ShipDateKey]`)
4. Relationship ที่จะใช้ต้องเป็น **Inactive Relationship** เท่านั้น
5. ใช้ได้เพียง 1 USERELATIONSHIP() ต่อ 1 CALCULATE() (ถ้ามีหลาย Inactive Relationships ต้องสร้างหลาย Measures)

#### 4.3 ตัวอย่างที่ 1: Sales by Order Date vs Ship Date

**สถานการณ์:** ต้องการดูยอดขายตาม Order Date และ Ship Date แยกกัน

**โครงสร้าง Relationships:**
```
FactResellerSales
├── OrderDateKey → DimDate[DateKey] (Active) ✅
├── ShipDateKey → DimDate[DateKey] (Inactive) ⚠️
└── DueDateKey → DimDate[DateKey] (Inactive) ⚠️
```

**ตัวอย่าง Measures:**

**1. Sales by Order Date (ใช้ Active Relationship):**
```dax
Sales by Order Date = 
SUM(FactResellerSales[SalesAmount])
```
- ไม่ต้องใช้ USERELATIONSHIP() เพราะใช้ Active Relationship
- เมื่อกรอง DimDate จะกรองตาม OrderDate อัตโนมัติ

**2. Sales by Ship Date (ใช้ Inactive Relationship):**
```dax
Sales by Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```
- ใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationship (ShipDateKey)
- เมื่อกรอง DimDate จะกรองตาม ShipDate แทน

**3. Sales by Due Date (ใช้ Inactive Relationship):**
```dax
Sales by Due Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[DueDateKey])
)
```

#### 4.4 ตัวอย่างที่ 2: Time Intelligence กับ USERELATIONSHIP()

**สถานการณ์:** ต้องการดู Sales YTD ทั้ง Order Date และ Ship Date

**1. Sales YTD by Order Date:**
```dax
Sales YTD by Order Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey])
)
```
- ใช้ Active Relationship (OrderDate)
- DATESYTD() จะคำนวณตาม OrderDate

**2. Sales YTD by Ship Date:**
```dax
Sales YTD by Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```
- ใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationship
- DATESYTD() จะคำนวณตาม ShipDate แทน

**3. Sales Previous Year by Order Date:**
```dax
Sales PY by Order Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)
```

**4. Sales Previous Year by Ship Date:**
```dax
Sales PY by Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

#### 4.5 ตัวอย่างที่ 3: รวมหลาย Measures สำหรับ Role-Playing

**สร้าง Measures แยกตาม Role:**

**ในตาราง FactResellerSales:**
```dax
// Base Measure
Reseller Sale Revenue = SUM(FactResellerSales[SalesAmount])

// Order Date Measures (Active Relationship)
Ordered Reseller Sale Revenue = [Reseller Sale Revenue]

// Ship Date Measures (Inactive Relationship)
Shipped Reseller Sale Revenue = 
CALCULATE(
    [Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)

// Due Date Measures (Inactive Relationship)
Due Reseller Sale Revenue = 
CALCULATE(
    [Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[DueDateKey])
)
```

**สร้าง Measures ที่รวม Time Intelligence:**

```dax
// YTD by Order Date
Ordered Reseller Sale Revenue YTD = 
CALCULATE(
    [Ordered Reseller Sale Revenue],
    DATESYTD(DimDate[FullDateAlternateKey])
)

// YTD by Ship Date
Shipped Reseller Sale Revenue YTD = 
CALCULATE(
    [Shipped Reseller Sale Revenue],
    DATESYTD(DimDate[FullDateAlternateKey])
)

// Previous Year by Order Date
Ordered Reseller Sale Revenue PY = 
CALCULATE(
    [Ordered Reseller Sale Revenue],
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)

// Previous Year by Ship Date
Shipped Reseller Sale Revenue PY = 
CALCULATE(
    [Shipped Reseller Sale Revenue],
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)
```

#### 4.6 ข้อควรระวัง

**1. ใช้ได้เฉพาะ Inactive Relationships:**
- USERELATIONSHIP() ใช้ได้เฉพาะกับ Inactive Relationships
- ถ้า Relationship เป็น Active แล้ว ใช้ไม่ได้

**2. ใช้ได้เพียง 1 USERELATIONSHIP() ต่อ CALCULATE():**
- ถ้ามีหลาย Inactive Relationships ต้องสร้างหลาย Measures
- ไม่สามารถใช้ USERELATIONSHIP() หลายตัวใน CALCULATE() เดียวกันได้

**3. ต้องใช้ภายใน CALCULATE():**
- USERELATIONSHIP() ใช้ได้เฉพาะใน CALCULATE() เท่านั้น
- ไม่สามารถใช้ใน Measures ที่ไม่มี CALCULATE() ได้

**4. ลำดับของ Parameters:**
- Parameter แรก: Dimension Table[Key] (เช่น `DimDate[DateKey]`)
- Parameter ที่สอง: Fact Table[ForeignKey] (เช่น `FactResellerSales[ShipDateKey]`)
- อย่าสลับลำดับ

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 5. การปิด Auto Date/Time

**ทำไมต้องปิด Auto Date/Time:**
- ถ้าเปิดไว้ จะมีการ Detect คอลัมน์ที่มี Type เป็น Date/Datetime แล้วสร้างเป็น Hierarchy ขึ้นมาอัตโนมัติ
- หากต้องการนำ Measure จากต่าง Fact Table มาดูร่วมกัน ในมิติเวลา จะไม่สามารถทำได้ หากแยกเป็น Date ใคร Date มัน

**วิธีแก้ไข:**
- สร้าง Conformed Date Dimension ขึ้นมาใหม่
- ยกเลิก Auto Date/Time

**การปิด Auto Date/Time:**
- **ระดับ Global**: กำหนดให้ยกเลิก Auto date/time กับไฟล์ Power BI ที่สร้างขึ้นใหม่ทันที
- **ระดับ Current File**: หากไฟล์เคยเปิดคุณสมบัติ Auto date/time ไปแล้ว สามารถยกเลิกได้ในส่วนของ Current File

---

### 6. การ Mark as Date Table

**เมื่อไหร่ใช้:**
- เมื่อสร้าง DimDate ขึ้นมาเอง
- เมื่อตั้งใจใช้เป็น Conformed Dimension

**ประโยชน์:**
- รองรับ Time Intelligence Functions ต่างๆ ได้สมบูรณ์
- Power BI จะรู้ว่าตารางนี้เป็น Date Table
- คุณสมบัติ Auto date/time บนทุกตารางจะถูกยกเลิก

**ข้อกำหนด:**
- ตาราง DimDate ต้องมีคอลัมน์ที่มีชนิดข้อมูลเป็น Date หรือ Datetime
- ความละเอียดของคอลัมน์ที่สูงสุดต้องไม่เกินระดับ 1 วัน

---

### 7. Time Intelligence ผ่าน Relationships

**Time Intelligence Functions ที่ใช้ Relationships:**

- **DATESYTD()** - Year-to-Date ผ่าน Relationships
- **DATESQTD()** - Quarter-to-Date ผ่าน Relationships
- **DATESMTD()** - Month-to-Date ผ่าน Relationships
- **SAMEPERIODLASTYEAR()** - Last Year ผ่าน Relationships

**ตัวอย่าง:**
```dax
Sales YTD = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey])
)

Sales YTD - Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 8. Multiple Hierarchies ใน Date Dimension

**ตัวอย่าง Hierarchies:**

**Calendar YSQMD Hierarchy:**
```
Year
└── Semester
    └── Quarter
        └── Month
            └── Day
```

**Calendar YQMD Hierarchy:**
```
Year
└── Quarter
    └── Month
        └── Day
```

**FiscalYear YQMD Hierarchy:**
```
Fiscal Year
└── Fiscal Quarter
    └── Month
        └── Day
```

**ประโยชน์:**
- สามารถดูข้อมูลหลายมุมมองได้
- รองรับทั้ง Calendar Year และ Fiscal Year

---

### 9. Dummy Table Pattern สำหรับรวม Measures

**สถานการณ์:** เมื่อต้องการรวม Measures จากหลาย Fact Tables ในตารางเดียวกัน

**ปัญหา:**
- Measures อยู่ใน Fact Tables ต่างกัน
- ไม่สามารถรวม Measures จากหลายตารางในตารางเดียวกันได้โดยตรง

**แนวทางแก้ไข:** สร้าง Dummy Table ด้วย `DATATABLE()`

#### 9.1 สร้าง Dummy Table

**สร้าง Calculated Table:**
```dax
All Measures = 
DATATABLE(
    "DummyColumn", INTEGER,
    {
        {1}
    }
)
```

**อธิบาย:**
- ใช้ `DATATABLE()` เพื่อสร้างตาราง Dummy
- ตารางนี้มี 1 แถว 1 คอลัมน์ (DummyColumn = 1)
- ใช้เป็นที่รวม Measures จากหลาย Fact Tables

#### 9.2 สร้าง Measures ในแต่ละ Fact Table

**ในตาราง FactInternetSales:**
```dax
Internet Sale Revenue = SUM(FactInternetSales[SalesAmount])
```

**ในตาราง FactResellerSales:**
```dax
Reseller Sale Revenue = SUM(FactResellerSales[SalesAmount])
```

#### 9.3 สร้าง Measures บน Dummy Table

**รวม Revenue จากทุก Fact Table:**
```dax
Revenue = [Internet Sale Revenue] + [Reseller Sale Revenue]
```

**อธิบาย:**
- Measures นี้อยู่ในตาราง "All Measures"
- สามารถ Reference Measures จากตารางอื่นได้โดยตรง
- ผลลัพธ์คือผลรวมของ Revenue จากทุก Fact Table

#### 9.4 สร้าง Measures สำหรับ Role Playing

**ในตาราง FactInternetSales:**
```dax
Shipped Internet Sales Revenue = 
CALCULATE(
    [Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[ShipDateKey])
)

Due Internet Sales Revenue = 
CALCULATE(
    [Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[DueDateKey])
)
```

**ในตาราง FactResellerSales:**
```dax
Shipped Reseller Sales Revenue = 
CALCULATE(
    [Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)

Due Reseller Sales Revenue = 
CALCULATE(
    [Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[DueDateKey])
)
```

**ในตาราง All Measures:**
```dax
Shipped Revenue = 
[Shipped Internet Sales Revenue] + [Shipped Reseller Sales Revenue]

Due Revenue = 
[Due Internet Sales Revenue] + [Due Reseller Sales Revenue]
```

**ประโยชน์:**
- จัดระเบียบ Measures ที่รวมจากหลาย Fact Tables
- สามารถสร้าง Measures สำหรับ Role Playing ที่รวมทุก Fact Table ได้
- ทำให้ Model มีโครงสร้างที่ชัดเจนขึ้น

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ เข้าใจการออกแบบ Date Dimension ที่ถูกต้อง
- ✅ เข้าใจ Role-Playing Dimensions และวิธีใช้งาน
- ✅ สร้างและจัดการ Multiple Date Relationships ได้
- ✅ ใช้ USERELATIONSHIP() กับ Date Relationships ได้อย่างถูกต้อง
- ✅ เข้าใจ Conformed Date Dimension Pattern
- ✅ Mark as Date Table และปิด Auto Date/Time ได้
- ✅ สร้าง Time Intelligence Measures ผ่าน Relationships ได้
- ✅ สร้าง Multiple Hierarchies ใน Date Dimension ได้
- ✅ รวม Measures จากหลาย Fact Tables ด้วย Dummy Table Pattern ได้

---

## 📚 เอกสารที่เกี่ยวข้อง

- **04-Relationships**: พื้นฐานของ Relationships และ USERELATIONSHIP()
- **05-Dimension-Table-Design**: การออกแบบ Dimension Tables อื่นๆ (Product, Customer, etc.)
- **[CODE-EXAMPLES.md](./CODE-EXAMPLES.md)** - ตัวอย่างโค้ด DAX สำหรับ Date Dimensions และ Relationships
- **[EXERCISES.md](./EXERCISES.md)** - แบบฝึกหัดแบบ Step-by-Step

---

## 📝 สรุป

### Best Practices

1. **ใช้ Conformed Date Dimension**
   - ใช้ DimDate เดียวสำหรับทุก Fact Table
   - สร้าง Multiple Relationships กับ Date Keys ต่างๆ

2. **Mark as Date Table**
   - Mark as Date Table เมื่อสร้าง DimDate ขึ้นมาเอง
   - ปิด Auto Date/Time

3. **Active vs Inactive Relationships**
   - Active: สำหรับวันที่ใช้บ่อยที่สุด (OrderDate)
   - Inactive: สำหรับวันที่ใช้เฉพาะบาง Measures (ShipDate, DueDate)

---

### ไฟล์ตัวอย่างที่แนะนำ

**หมายเหตุ:** ตัวอย่างในโมดูลนี้ใช้ AdventureWorksDW2025 เป็น Data Source

---

**🎉 ขอแสดงความยินดี! คุณได้เรียนจบโมดูล Date Dimensions & Relationships แล้ว!**

**ขั้นตอนต่อไป:**
- ฝึกปฏิบัติตาม [EXERCISES.md](./EXERCISES.md)
- ดูตัวอย่างโค้ดใน [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)
- เรียนโมดูล 07-Fact-Tables-Design ✅
