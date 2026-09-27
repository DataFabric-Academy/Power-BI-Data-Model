# แบบฝึกหัด - Advanced Modeling Patterns

## 📚 เอกสารแบบฝึกหัดสำหรับการฝึกปฏิบัติ

ไฟล์นี้รวบรวมแบบฝึกหัดแบบ Step-by-Step สำหรับ Advanced Modeling Patterns รวมถึง Heterogeneous Granularity, Bidirectional Filters, และเทคนิคขั้นสูงอื่นๆ โดยรวม Code Examples และคำอธิบายที่ละเอียด

> **หมายเหตุ:** แบบฝึกหัดสำหรับ Calculation Groups ได้ย้ายไปอยู่ใน **07-Fact-Tables-Design/EXERCISES.md** แล้ว

> **ไฟล์ตัวอย่าง:**
> - `Data Model Conformed Date Dimension.pbix`
> - `AdventureWorksDW2025` - ตัวอย่าง FactSalesQuota และ FactResellerSales
> **หมายเหตุ:** ไฟล์ตัวอย่างเป็นสื่อการสอนของผู้สอน (Trainer Material) — ขอไฟล์ได้จากผู้สอนระหว่างเรียน ไม่ได้แจกจ่ายผ่าน repository นี้

---

## 🎯 แบบฝึกหัดที่ 1: เข้าใจปัญหา Heterogeneous Granularity

### วัตถุประสงค์

เข้าใจปัญหา Heterogeneous Granularity และวิธีแก้ไข

---

### ขั้นตอนที่ 1: เข้าใจปัญหา

**Step 1.1:** เข้าใจสถานการณ์:

**สถานการณ์:**
- FactSalesQuota มี Granularity ระดับเดือน
- FactResellerSales มี Granularity ระดับวัน

**Step 1.2:** ตรวจสอบข้อมูล:

1. ไปที่ **Data View**
2. เปิดตาราง `FactSalesQuota`
3. สังเกตว่าแต่ละแถวแทนข้อมูลระดับเดือน (1 แถวต่อเดือน)

4. เปิดตาราง `FactResellerSales`
5. สังเกตว่าแต่ละแถวแทนข้อมูลระดับวัน (หลายแถวต่อวัน)

---

### ขั้นตอนที่ 2: เข้าใจปัญหา

**Step 2.1:** สร้าง Measures:

```dax
Sales Quota = SUM(FactSalesQuota[SalesAmountQuota])
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

**Step 2.2:** ทดสอบใน Visual:

1. สร้าง Table Visual:
   - **Rows**: DimDate[Day] (ระดับวัน)
   - **Values**: Sales Quota, Reseller Sales Revenue

2. สังเกตผลลัพธ์:
   - `Sales Quota` → ไม่มีข้อมูล (NULL) เพราะไม่มีข้อมูลระดับวัน
   - `Reseller Sales Revenue` → มีข้อมูล ระดับวัน

**ปัญหา:** ไม่สามารถเปรียบเทียบ Measures ได้โดยตรงเพราะมี Granularity แตกต่างกัน

---

### ขั้นตอนที่ 3: เข้าใจ Granularity

**Step 3.1:** เข้าใจว่า Granularity คืออะไร:

**Granularity** = ระดับความละเอียดของข้อมูล

**ระดับความละเอียด:**
- **ระดับสูง**: Year, Quarter, Month (ข้อมูลรวม)
- **ระดับต่ำ**: Day, Transaction, Line Item (ข้อมูลละเอียด)

**Step 3.2:** ตัวอย่าง Granularity:

**FactSalesQuota:**
- Granularity = **เดือน** (1 แถวต่อเดือน)
- ข้อมูล: เป้าหมายยอดขายรายเดือน

**FactResellerSales:**
- Granularity = **วัน** (หลายแถวต่อวัน)
- ข้อมูล: ยอดขายรายวัน

---

### ขั้นตอนที่ 4: วิธีแก้ไขปัญหา Heterogeneous Granularity

**Step 4.1:** วิธีที่ 1: ใช้ Hierarchy เพื่อควบคุม Granularity

**วิธี:**
- สร้าง Date Hierarchy: Calendar Year > Calendar Quarter > Calendar Month > Day
- ผู้ใช้เลือกระดับความละเอียดที่ต้องการ

**Step 4.2:** วิธีที่ 2: ใช้ Time Intelligence Functions เพื่อ Aggregate ข้อมูล

**วิธี:**
- Aggregate ข้อมูลระดับวันเป็นระดับเดือน
- ใช้ ALL() และ VALUES() เพื่อควบคุม Filter Context

**Step 4.3:** วิธีที่ 3: ใช้ Conformed Date Dimension

**วิธี:**
- ใช้ DimDate เดียวสำหรับทุก Fact Table
- สร้าง Relationships ที่เหมาะสมตาม Granularity

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมไม่สามารถเปรียบเทียบ Measures จากทั้งสอง Fact Tables ได้โดยตรง?**
   - **คำตอบ:** เพราะ Fact Tables มีระดับความละเอียดต่างกัน - FactSalesQuota มีข้อมูลระดับเดือน แต่ FactResellerSales มีข้อมูลระดับวัน

2. **ระดับความละเอียด (Granularity) คืออะไร?**
   - **คำตอบ:** ระดับความละเอียดของข้อมูล เช่น ระดับสูง (Year, Quarter, Month) หรือระดับต่ำ (Day, Transaction, Line Item)

3. **ควรจัดการกับปัญหา Heterogeneous Granularity อย่างไร?**
   - **คำตอบ:** ใช้ Hierarchy เพื่อควบคุม Granularity, ใช้ Time Intelligence Functions เพื่อ Aggregate ข้อมูล, หรือใช้ Conformed Date Dimension

---

## 🎯 แบบฝึกหัดที่ 2: Aggregate ข้อมูลระดับวันเป็นระดับเดือน

### วัตถุประสงค์

เรียนรู้วิธี Aggregate ข้อมูลระดับวันเป็นระดับเดือนเพื่อเปรียบเทียบกับ FactSalesQuota

---

### ขั้นตอนที่ 1: เข้าใจสถานการณ์

**Step 1.1:** ข้อมูลที่มี:

- มี Measure `Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])`
- FactResellerSales มี Granularity ระดับวัน
- ต้องการ Aggregate เป็นระดับเดือนเพื่อเปรียบเทียบกับ FactSalesQuota

**Step 1.2:** ปัญหา:

- `Reseller Sales Revenue` แสดงข้อมูลในระดับวัน
- `Sales Quota` แสดงข้อมูลในระดับเดือน
- ไม่สามารถเปรียบเทียบได้โดยตรง

---

### ขั้นตอนที่ 2: สร้าง Base Measure

**Step 2.1:** ตรวจสอบว่า Measure `Reseller Sales Revenue` มีอยู่แล้ว:

```dax
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

**ถ้ายังไม่มี ให้สร้าง:**
1. เลือก Table `FactResellerSales`
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

---

### ขั้นตอนที่ 3: สร้าง Measure ที่ Aggregate เป็นระดับเดือน

**Step 3.1:** สร้าง Measure ใหม่:

1. เลือก Table `FactResellerSales` (หรือ Table ที่เหมาะสม)
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Reseller Sales Revenue (Monthly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day]),
    VALUES('Date'[MonthKey])
)
```

**อธิบายสูตร:**
- `CALCULATE(...)` เปลี่ยน Filter Context
- `ALL('Date'[Day])` ลบ Filter Context ในระดับวัน (ลบ Filter จาก Day)
- `VALUES('Date'[MonthKey])` กำหนด Filter Context ในระดับเดือน (Filter ด้วย MonthKey)
- ผลลัพธ์ = ยอดขายรวมในแต่ละเดือน

---

### ขั้นตอนที่ 4: ทดสอบ Measure

**Step 4.1:** สร้าง Visual:

1. สร้าง Table Visual:
   - **Rows**: DimDate[CalendarMonth] (ระดับเดือน)
   - **Values**: Reseller Sales Revenue (Monthly), Sales Quota

2. ตรวจสอบผลลัพธ์:
   - ทั้งสอง Measures ควรแสดงข้อมูลในระดับเดือน
   - สามารถเปรียบเทียบได้

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องใช้ ALL('Date'[Day])?**
   - **คำตอบ:** เพื่อลบ Filter Context ในระดับวัน เพราะต้องการ Aggregate เป็นระดับเดือน

2. **ทำไมต้องใช้ VALUES('Date'[MonthKey])?**
   - **คำตอบ:** เพื่อกำหนด Filter Context ในระดับเดือน กำหนดให้แสดงผลในระดับเดือน

---

## 🎯 แบบฝึกหัดที่ 3: Aggregate ข้อมูลเป็นหลายระดับ

### วัตถุประสงค์

เรียนรู้วิธี Aggregate ข้อมูลเป็นหลายระดับ (Monthly, Quarterly, Yearly)

---

### ขั้นตอนที่ 1: สร้าง Base Measure

**Step 1.1:** ตรวจสอบว่า Measure `Reseller Sales Revenue` มีอยู่แล้ว:

```dax
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

---

### ขั้นตอนที่ 2: สร้าง Measure - Monthly

**Step 2.1:** สร้าง Measure `Reseller Sales Revenue (Monthly)`:

```dax
Reseller Sales Revenue (Monthly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day]),
    VALUES('Date'[MonthKey])
)
```

**อธิบาย:**
- ลบ Filter Context ในระดับวัน
- กำหนด Filter Context ในระดับเดือน

---

### ขั้นตอนที่ 3: สร้าง Measure - Quarterly

**Step 3.1:** สร้าง Measure `Reseller Sales Revenue (Quarterly)`:

```dax
Reseller Sales Revenue (Quarterly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day], 'Date'[MonthKey]),
    VALUES('Date'[CalendarQuarter])
)
```

**อธิบาย:**
- ลบ Filter Context ในระดับวันและเดือน
- กำหนด Filter Context ในระดับไตรมาส

---

### ขั้นตอนที่ 4: สร้าง Measure - Yearly

**Step 4.1:** สร้าง Measure `Reseller Sales Revenue (Yearly)`:

```dax
Reseller Sales Revenue (Yearly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day], 'Date'[MonthKey], 'Date'[CalendarQuarter]),
    VALUES('Date'[CalendarYear])
)
```

**อธิบาย:**
- ลบ Filter Context ในระดับวัน, เดือน, และไตรมาส
- กำหนด Filter Context ในระดับปี

---

### ขั้นตอนที่ 5: ทดสอบ Measures

**Step 5.1:** สร้าง Visual:

1. สร้าง Table Visual:
   - **Rows**: DimDate[CalendarYear]
   - **Values**: Reseller Sales Revenue (Yearly), Reseller Sales Revenue (Quarterly), Reseller Sales Revenue (Monthly)

2. ตรวจสอบผลลัพธ์:
   - แต่ละ Measure ควรแสดงผลในระดับที่แตกต่างกัน
   - Yearly > Quarterly > Monthly

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องลบ Filter Context ในหลายระดับ (ALL('Date'[Day], 'Date'[MonthKey]))?**
   - **คำตอบ:** เพื่อ Aggregate ข้อมูลให้เป็นระดับที่สูงขึ้น เช่น Aggregate จากระดับวันเป็นระดับไตรมาส ต้องลบ Filter Context ในระดับวันและเดือน

---

## 🎯 แบบฝึกหัดที่ 4: เปรียบเทียบ Sales Revenue กับ Sales Quota

### วัตถุประสงค์

เรียนรู้วิธีเปรียบเทียบ Measures จาก Fact Tables ที่มี Granularity แตกต่างกัน

---

### ขั้นตอนที่ 1: สร้าง Base Measures

**Step 1.1:** สร้าง Measure `Sales Quota`:

```dax
Sales Quota = SUM(FactSalesQuota[SalesAmountQuota])
```

**Step 1.2:** สร้าง Measure `Reseller Sales Revenue`:

```dax
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

---

### ขั้นตอนที่ 2: Aggregate Reseller Sales Revenue เป็นระดับเดือน

**Step 2.1:** สร้าง Measure `Reseller Sales Revenue (Monthly)`:

```dax
Reseller Sales Revenue (Monthly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day]),
    VALUES('Date'[MonthKey])
)
```

**อธิบาย:**
- Aggregate ข้อมูลระดับวันเป็นระดับเดือน
- ทำให้ Granularity ตรงกับ `Sales Quota`

---

### ขั้นตอนที่ 3: สร้าง Measure เปรียบเทียบ

**Step 3.1:** สร้าง Measure `Variance vs Quota`:

```dax
Variance vs Quota = 
[Reseller Sales Revenue (Monthly)] - [Sales Quota]
```

**อธิบาย:**
- คำนวณความแตกต่างระหว่างยอดขายกับเป้าหมาย
- ผลลัพธ์บวก = เกินเป้าหมาย
- ผลลัพธ์ลบ = ต่ำกว่าเป้าหมาย

**Step 3.2:** สร้าง Measure `Variance vs Quota %`:

```dax
Variance vs Quota % = 
DIVIDE(
    [Variance vs Quota],
    [Sales Quota],
    0
)
```

**อธิบาย:**
- คำนวณเปอร์เซ็นต์ความแตกต่าง
- ใช้ `DIVIDE()` เพื่อป้องกัน Division by Zero

---

### ขั้นตอนที่ 4: ทดสอบ Measures

**Step 4.1:** สร้าง Visual:

1. สร้าง Table Visual:
   - **Rows**: DimDate[CalendarMonth]
   - **Values**: 
     - Sales Quota
     - Reseller Sales Revenue (Monthly)
     - Variance vs Quota
     - Variance vs Quota %

2. ตรวจสอบผลลัพธ์:
   - ทั้งสี่ Measures ควรแสดงผลในระดับเดือน
   - Variance vs Quota และ Variance vs Quota % ควรคำนวณถูกต้อง

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้อง Aggregate `Reseller Sales Revenue` เป็นระดับเดือน?**
   - **คำตอบ:** เพื่อให้ Granularity ตรงกับ `Sales Quota` (ระดับเดือน) เพื่อให้สามารถเปรียบเทียบได้

2. **ทำไมต้องใช้ DIVIDE() แทนการหารด้วย /?**
   - **คำตอบ:** เพื่อป้องกัน Division by Zero (คืนค่า 0 หรือ Blank แทน Error)

---

## 🎯 แบบฝึกหัดที่ 5: ใช้ Hierarchy เพื่อควบคุม Granularity

### วัตถุประสงค์

เรียนรู้วิธีใช้ Hierarchy เพื่อควบคุม Granularity อัตโนมัติ

---

### ขั้นตอนที่ 1: สร้าง Date Hierarchy

**Step 1.1:** ไปที่ **Model View**

**Step 1.2:** Right-click ที่ `DimDate` Table

**Step 1.3:** เลือก **New hierarchy**

**Step 1.4:** ตั้งชื่อเป็น `Calendar YQMD`

**Step 1.5:** เพิ่ม Levels:
1. ลาก `CalendarYear` ไปยัง Hierarchy
2. ลาก `CalendarQuarter` ไปยัง Hierarchy
3. ลาก `CalendarMonth` ไปยัง Hierarchy
4. ลาก `Day` ไปยัง Hierarchy

---

### ขั้นตอนที่ 2: เข้าใจการทำงานของ Hierarchy

**Step 2.1:** สร้าง Measures:

```dax
Sales Quota = SUM(FactSalesQuota[SalesAmountQuota])
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

**Step 2.2:** ทดสอบใน Visual:

1. สร้าง Visual ที่ใช้ Hierarchy:
   - **Axis**: Calendar YQMD Hierarchy
   - **Values**: Sales Quota, Reseller Sales Revenue

2. ทดสอบแต่ละระดับ:
   - Expand/Collapse Hierarchy
   - สังเกตว่า Measures ทำงานอย่างไร

---

### ขั้นตอนที่ 3: วิเคราะห์ผลลัพธ์

**Step 3.1:** เมื่อเลือก **Calendar Month**:

**ผลลัพธ์:**
- `Sales Quota` → แสดงข้อมูลได้ (มีข้อมูลระดับเดือน) ✅
- `Reseller Sales Revenue` → แสดงข้อมูลได้ (สามารถ Aggregate เป็นระดับเดือนได้) ✅

**อธิบาย:**
- Hierarchy ควบคุม Granularity ให้อยู่ในระดับเดือน
- ทั้งสอง Measures สามารถแสดงผลได้

**Step 3.2:** เมื่อเลือก **Day**:

**ผลลัพธ์:**
- `Sales Quota` → ไม่แสดงข้อมูลได้ (ไม่มีข้อมูลระดับวัน) ❌
- `Reseller Sales Revenue` → แสดงข้อมูลได้ (มีข้อมูลระดับวัน) ✅

**อธิบาย:**
- Hierarchy ควบคุม Granularity ให้อยู่ในระดับวัน
- `Sales Quota` ไม่มีข้อมูลระดับวัน → แสดงผลเป็น Blank
- `Reseller Sales Revenue` มีข้อมูลระดับวัน → แสดงผลได้

---

### ขั้นตอนที่ 4: เข้าใจประโยชน์ของ Hierarchy

**Step 4.1:** ประโยชน์ที่ 1: ควบคุม Granularity อัตโนมัติ

**อธิบาย:**
- Hierarchy ควบคุม Granularity ให้เหมาะสมกับระดับที่เลือก
- ไม่ต้องเขียน DAX ซับซ้อน

**Step 4.2:** ประโยชน์ที่ 2: ผู้ใช้เลือกระดับความละเอียดได้เอง

**อธิบาย:**
- ผู้ใช้สามารถ Expand/Collapse Hierarchy ได้
- เลือกระดับความละเอียดที่ต้องการได้เอง

**Step 4.3:** ประโยชน์ที่ 3: ไม่ต้องเขียน DAX ซับซ้อน

**อธิบาย:**
- ไม่ต้องสร้าง Measures แยกสำหรับแต่ละระดับ
- Hierarchy ทำหน้าที่ควบคุม Granularity อัตโนมัติ

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **เมื่อเลือก Calendar Month ใน Hierarchy → Measures ไหนจะแสดงข้อมูลได้บ้าง?**
   - **คำตอบ:** Sales Quota และ Reseller Sales Revenue ทั้งสองจะแสดงข้อมูลได้ เพราะทั้งสองมีข้อมูลในระดับเดือน (หรือสามารถ Aggregate เป็นระดับเดือนได้)

2. **เมื่อเลือก Day ใน Hierarchy → Measures ไหนจะแสดงข้อมูลได้บ้าง?**
   - **คำตอบ:** เฉพาะ Reseller Sales Revenue จะแสดงข้อมูลได้ เพราะมีข้อมูลระดับวัน แต่ Sales Quota จะไม่แสดงข้อมูลได้เพราะไม่มีข้อมูลระดับวัน

3. **ทำไมต้องใช้ Hierarchy เพื่อควบคุม Granularity?**
   - **คำตอบ:** เพื่อควบคุม Granularity อัตโนมัติ, ให้ผู้ใช้เลือกระดับความละเอียดที่ต้องการได้เอง, และไม่ต้องเขียน DAX ซับซ้อน

---

## 🎯 แบบฝึกหัดที่ 6: เข้าใจ Bidirectional Filters

### วัตถุประสงค์

เข้าใจ Bidirectional Filters และผลกระทบต่อ Performance

---

### ขั้นตอนที่ 1: เข้าใจ Bidirectional Filters

**Step 1.1:** เข้าใจว่า Bidirectional Filters คืออะไร:

**Bidirectional Filters** = การตั้งค่า Cross-Filter Direction เป็น "Both"

**ลักษณะ:**
- Filter Context สามารถส่งผ่าน Relationship ได้ทั้งสองทิศทาง
- จาก Dimension → Fact (ปกติ)
- จาก Fact → Dimension (ย้อนกลับ)

**Step 1.2:** ตัวอย่าง:

**Relationship:**
- DimProduct → FactResellerSales
- Cross-Filter Direction: **Both**

**ผลกระทบ:**
- เมื่อกรอง Product → Filter Reseller Sales (ปกติ)
- เมื่อกรอง Reseller Sales → Filter Product (ย้อนกลับ)

---

### ขั้นตอนที่ 2: ทดสอบ Bidirectional Filters

**Step 2.1:** สร้าง Relationship:

1. ไปที่ **Model View**
2. คลิกที่ Relationship ระหว่าง DimProduct และ FactResellerSales
3. ไปที่ **Properties pane** → **Cross filter direction**
4. เลือก **Both**

**Step 2.2:** ทดสอบ:

1. สร้าง Slicer:
   - **Fields**: FactResellerSales[OrderQuantity] (Range: 0-10)

2. สร้าง Table Visual:
   - **Rows**: DimProduct[ProductName]
   - **Values**: Total Sales

3. สังเกตผลลัพธ์:
   - เมื่อเลือก OrderQuantity ใน Slicer
   - Product จะถูก Filter ด้วย (แสดงเฉพาะ Product ที่มี OrderQuantity อยู่ใน Range ที่เลือก)

**อธิบาย:**
- Filter จาก FactResellerSales ส่งผลต่อ DimProduct (ย้อนกลับ)
- นี่คือผลของ Bidirectional Filters

---

### ขั้นตอนที่ 3: เข้าใจผลกระทบต่อ Performance

**Step 3.1:** ผลกระทบที่ 1: Performance อาจลดลง

**ปัญหา:**
- Power BI ต้องคำนวณ Filter ทั้งสองทิศทาง
- Query อาจช้าลง
- ใช้ Memory มากขึ้น

**Step 3.2:** ผลกระทบที่ 2: อาจทำให้เกิด Circular Dependencies

**ปัญหา:**
- ถ้ามีหลาย Relationships ที่เป็น Bidirectional
- อาจทำให้เกิด Circular Dependencies
- อาจทำให้ผลลัพธ์ไม่ถูกต้อง

**Step 3.3:** ผลกระทบที่ 3: Query อาจช้าลง

**ปัญหา:**
- Query Execution Plan ซับซ้อนขึ้น
- ใช้เวลาในการประมวลผลมากขึ้น

---

### ขั้นตอนที่ 4: เมื่อไหร่ควรใช้ Bidirectional Filters

**Step 4.1:** ควรใช้เมื่อ:

**เมื่อไหร่:**
- จำเป็นต้องให้การกรองส่งผลทั้งสองทิศทาง
- เช่น กรอง Product → Filter Sales ทั้ง Product และ Customer
- หรือกรอง Sales → Filter Product เพื่อแสดงเฉพาะ Product ที่ขายได้

**Step 4.2:** ควรใช้ด้วยความระมัดระวัง:

**ข้อควรระวัง:**
- ใช้เฉพาะเมื่อจำเป็นจริงๆ
- ระวังผลกระทบต่อ Performance
- ระวัง Circular Dependencies
- ทดสอบผลลัพธ์ให้ถูกต้อง

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Bidirectional Filters คืออะไร?**
   - **คำตอบ:** การตั้งค่า Cross-Filter Direction เป็น "Both" เพื่อให้ Filter Context สามารถส่งผ่าน Relationship ได้ทั้งสองทิศทาง

2. **ผลกระทบต่อ Performance คืออะไร?**
   - **คำตอบ:** Performance อาจลดลง, อาจทำให้เกิด Circular Dependencies, และ Query อาจช้าลง

3. **ควรใช้ Bidirectional Filters เมื่อไหร่?**
   - **คำตอบ:** เมื่อจำเป็นต้องให้การกรองส่งผลทั้งสองทิศทาง และควรใช้ด้วยความระมัดระวัง

---

## 📝 สรุป

### ✅ สิ่งที่ควรจำ

1. **Heterogeneous Granularity** คือปัญหาเมื่อ Fact Tables มี Granularity แตกต่างกัน
2. **ใช้ Hierarchy** เพื่อควบคุม Granularity อัตโนมัติ
3. **ใช้ Time Intelligence Functions** เพื่อ Aggregate ข้อมูล (ALL() และ VALUES())
4. **ใช้ Conformed Date Dimension** เพื่อให้สอดคล้องกัน
5. **Bidirectional Filters** ควรใช้ด้วยความระมัดระวัง (Performance Impact, Circular Dependencies)

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) - Code Examples เพิ่มเติม
