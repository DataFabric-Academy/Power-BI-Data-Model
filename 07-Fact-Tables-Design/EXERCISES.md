# แบบฝึกหัด - Fact Tables Design

## 📚 เอกสารแบบฝึกหัดสำหรับการฝึกปฏิบัติ

ไฟล์นี้รวบรวมแบบฝึกหัดแบบ Step-by-Step สำหรับ Explicit Measures และ Calculation Groups โดยรวม Code Examples และคำอธิบายที่ละเอียด

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 🎯 แบบฝึกหัดที่ 1: Measures พื้นฐาน

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Measures พื้นฐานด้วย Aggregate Functions

---

### ขั้นตอนที่ 1: สร้าง Measure Total Sales

**Step 1.1:** เปิด Power BI Desktop

**Step 1.2:** เปิดไฟล์ที่มี AdventureWorksDW2025 Semantic Model

**Step 1.3:** เลือก Table `FactResellerSales`

**Step 1.4:** ไปที่ **Table tools** → **New measure**

**Step 1.5:** พิมพ์สูตร:

```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
```

**อธิบายสูตร:**
- `SUM()` รวมค่าทั้งหมดในคอลัมน์ SalesAmount
- Measure นี้จะคำนวณตาม Filter Context ที่มีอยู่

**Step 1.6:** กด Enter เพื่อบันทึก Measure

**Step 1.7:** ทดสอบ Measure:
- สร้าง Table Visual ที่แสดง Total Sales
- ตรวจสอบว่าผลลัพธ์ถูกต้อง

---

### ขั้นตอนที่ 2: สร้าง Measure Total Orders

**Step 2.1:** สร้าง Measure ใหม่:

1. เลือก Table `FactResellerSales`
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Total Orders = COUNTROWS(FactResellerSales)
```

**อธิบายสูตร:**
- `COUNTROWS()` นับจำนวนแถวในตาราง
- ผลลัพธ์ = จำนวนรายการขาย

---

### ขั้นตอนที่ 3: สร้าง Measure Average Sales

**Step 3.1:** สร้าง Measure ใหม่:

```dax
Average Sales = AVERAGE(FactResellerSales[SalesAmount])
```

**อธิบายสูตร:**
- `AVERAGE()` คำนวณค่าเฉลี่ย
- ผลลัพธ์ = ยอดขายเฉลี่ยต่อรายการ

---

### ขั้นตอนที่ 4: สร้าง Measure Unique Products

**Step 4.1:** สร้าง Measure ใหม่:

```dax
Unique Products = DISTINCTCOUNT(FactResellerSales[ProductKey])
```

**อธิบายสูตร:**
- `DISTINCTCOUNT()` นับจำนวนค่าที่ไม่ซ้ำกัน
- ผลลัพธ์ = จำนวนสินค้าที่ไม่ซ้ำกัน

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **SUM(), COUNTROWS(), AVERAGE(), DISTINCTCOUNT() ต่างกันอย่างไร?**
   - **คำตอบ:** SUM() รวมค่าทั้งหมด, COUNTROWS() นับจำนวนแถว, AVERAGE() คำนวณค่าเฉลี่ย, DISTINCTCOUNT() นับจำนวนค่าที่ไม่ซ้ำกัน

---

## 🎯 แบบฝึกหัดที่ 2: Measures ที่ใช้ CALCULATE()

### วัตถุประสงค์

เรียนรู้วิธีใช้ CALCULATE() เพื่อ Filter ข้อมูลผ่าน Relationships

---

### ขั้นตอนที่ 1: สร้าง Measure Sales - Bikes

**Step 1.1:** สร้าง Measure:

1. เลือก Table `FactResellerSales`
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Sales - Bikes = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimProduct[ProductCategoryName] = "Bikes"
)
```

**อธิบายสูตร:**
- `CALCULATE(...)` เปลี่ยน Filter Context
- `DimProduct[ProductCategoryName] = "Bikes"` Filter เฉพาะ Product Category = "Bikes"
- Filter ทำงานผ่าน Relationship ระหว่าง FactResellerSales และ DimProduct

**Step 1.2:** ทดสอบ Measure:
- สร้าง Table Visual ที่แสดง Sales - Bikes
- ตรวจสอบว่าผลลัพธ์ถูกต้อง (ควรน้อยกว่า Total Sales)

---

### ขั้นตอนที่ 2: สร้าง Measure Sales % - Bikes

**Step 2.1:** สร้าง Measure:

```dax
Sales % - Bikes = 
DIVIDE(
    [Sales - Bikes],
    [Total Sales]
)
```

**อธิบายสูตร:**
- `DIVIDE(...)` แบ่งค่าอย่างปลอดภัย (จัดการ Error เมื่อหารด้วย 0)
- `[Sales - Bikes]` ยอดขายเฉพาะ Bikes
- `[Total Sales]` ยอดขายรวม
- ผลลัพธ์ = เปอร์เซ็นต์ยอดขาย Bikes จากยอดรวม

**Step 2.2:** ตั้งค่า Format:
- ไปที่ **Measure tools** → **Format**
- เลือก **Percentage** (เช่น 0.00%)

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องใช้ DIVIDE() แทนการหารด้วย / ?**
   - **คำตอบ:** เพราะ DIVIDE() จัดการ Error เมื่อหารด้วย 0 ได้ดีกว่า (คืนค่า 0 หรือ Blank)

2. **CALCULATE() ใช้ทำอะไร?**
   - **คำตอบ:** ใช้เพื่อเปลี่ยน Filter Context และ Filter ข้อมูลผ่าน Relationships

---

## 🎯 แบบฝึกหัดที่ 3: Explicit Measures vs Implicit Measures

### วัตถุประสงค์

เข้าใจความแตกต่างระหว่าง Explicit Measures และ Implicit Measures

---

### ขั้นตอนที่ 1: เข้าใจ Implicit Measures

**Step 1.1:** เข้าใจว่า Implicit Measures คืออะไร:

**Implicit Measures** = Measures ที่ Power BI สร้างอัตโนมัติจากคอลัมน์ตัวเลข

**ลักษณะ:**
- Power BI จะ SUM, COUNT, AVERAGE อัตโนมัติ
- ปรากฏใน Fields pane เมื่อลากคอลัมน์ตัวเลขมาใน Visual
- ไม่สามารถควบคุม Logic การคำนวณได้

**Step 1.2:** ตัวอย่าง Implicit Measures:

- เมื่อลาก `SalesAmount` ไปใน Visual → Power BI จะสร้าง SUM(SalesAmount) อัตโนมัติ
- เมื่อลาก `OrderQuantity` ไปใน Visual → Power BI จะสร้าง SUM(OrderQuantity) อัตโนมัติ

---

### ขั้นตอนที่ 2: เข้าใจ Explicit Measures

**Step 2.1:** เข้าใจว่า Explicit Measures คืออะไร:

**Explicit Measures** = Measures ที่สร้างด้วย DAX อย่างชัดเจน

**ลักษณะ:**
- ผู้พัฒนากำหนด Logic การคำนวณเอง
- สามารถควบคุมได้ทุกขั้นตอน
- ปรากฏใน Fields pane เป็น Measures แยกต่างหาก

**Step 2.2:** ตัวอย่าง Explicit Measures:

```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
Sales - Bikes = CALCULATE(SUM(...), DimProduct[Category] = "Bikes")
```

---

### ขั้นตอนที่ 3: ทำไมต้องใช้ Explicit Measures

**Step 3.1:** เหตุผลที่ 1: ควบคุม Logic การคำนวณได้

**ปัญหา Implicit Measures:**
- ไม่สามารถควบคุม Logic การคำนวณได้
- Power BI เลือก Aggregate Function ให้อัตโนมัติ (อาจไม่ถูกต้อง)

**ข้อดี Explicit Measures:**
- ควบคุม Logic การคำนวณได้ทุกขั้นตอน
- สามารถใช้ CALCULATE(), Filter, และ DAX Functions อื่นๆ ได้

**Step 3.2:** เหตุผลที่ 2: Performance ดีกว่า

**ปัญหา Implicit Measures:**
- Power BI ต้องคำนวณทุกครั้งที่ Visual ถูก Refresh
- Performance อาจไม่ดี

**ข้อดี Explicit Measures:**
- Power BI สามารถ Optimize การคำนวณได้ดีกว่า
- Performance ดีกว่า

**Step 3.3:** เหตุผลที่ 3: สามารถ Reuse ได้

**ปัญหา Implicit Measures:**
- ไม่สามารถ Reuse ใน Measures อื่นได้
- ต้องสร้างใหม่ทุกครั้ง

**ข้อดี Explicit Measures:**
- สามารถ Reuse ใน Measures อื่นได้ (เช่น `[Sales - Bikes] / [Total Sales]`)

**Step 3.4:** เหตุผลที่ 4: รองรับการใช้ใน Excel

**ปัญหา Implicit Measures:**
- Implicit Measures ไม่สามารถใช้งานใน Excel PivotTable ได้

**ข้อดี Explicit Measures:**
- Explicit Measures สามารถใช้งานใน Excel PivotTable ได้

---

### ขั้นตอนที่ 4: ตั้งค่า Don't Summarize

**Step 4.1:** เข้าใจปัญหา:

**ปัญหา:**
- คอลัมน์ตัวเลขใน Fact Table อาจถูกสรุปอัตโนมัติ
- อาจได้ผลลัพธ์ที่ไม่ถูกต้อง

**ตัวอย่าง:**
- `UnitPrice` ใน Fact Table ไม่ควรถูก SUM (ควรเป็น Average หรือใช้ Measure แยก)

**Step 4.2:** ตั้งค่า Don't Summarize:

1. ไปที่ **Data View** หรือ **Fields pane**
2. คลิกขวาที่คอลัมน์ตัวเลขใน Fact Table (เช่น `SalesAmount`)
3. เลือก **Don't Summarize**

**อธิบาย:**
- การตั้งค่า Don't Summarize จะป้องกันไม่ให้ Power BI สร้าง Implicit Measures อัตโนมัติ
- กระตุ้นให้ผู้ใช้สร้าง Explicit Measures แทน

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Implicit Measures และ Explicit Measures ต่างกันอย่างไร?**
   - **คำตอบ:** Implicit Measures = สร้างอัตโนมัติโดย Power BI, Explicit Measures = สร้างด้วย DAX อย่างชัดเจน

2. **ทำไมต้องใช้ Explicit Measures?**
   - **คำตอบ:** ควบคุม Logic การคำนวณได้, Performance ดีกว่า, สามารถ Reuse ได้, รองรับการใช้ใน Excel

3. **ตั้งค่า "Don't Summarize" ที่ไหน?**
   - **คำตอบ:** คลิกขวาที่คอลัมน์ใน Fact Table → เลือก "Don't Summarize"

---

## 🎯 แบบฝึกหัดที่ 4: เข้าใจโครงสร้าง Calculation Groups

### วัตถุประสงค์

เข้าใจโครงสร้างของ Calculation Groups และ Calculation Items

---

### ขั้นตอนที่ 1: เปิดไฟล์ตัวอย่าง

**Step 1.1:** เปิด Power BI Desktop

**Step 1.2:** เปิดไฟล์ `Data Model Time Intelligence with Calculation Group.pbix`

> **หมายเหตุ:** ไฟล์ตัวอย่างเป็นสื่อการสอนของผู้สอน (Trainer Material) — ขอไฟล์ได้จากผู้สอนระหว่างเรียน ไม่ได้แจกจ่ายผ่าน repository นี้

**Step 1.3:** ไปที่ **Model View**

**Step 1.4:** สังเกต Calculation Group:
- ควรเห็น Calculation Group ชื่อ "my 1st Calculation group"
- มี Calculation Items อยู่ภายใน

---

### ขั้นตอนที่ 2: ตรวจสอบโครงสร้าง

**Step 2.1:** ตรวจสอบ Calculation Group:

**Calculation Group ชื่อ:** "my 1st Calculation group"

**Calculation Items:**
- "Reseller Sales Cost"
- "Reseller Sales Revenue"

**Step 2.2:** ตรวจสอบคอลัมน์:

**คอลัมน์ใน Calculation Group:**
- `Measure Selector` - Column ที่ใช้แสดงชื่อ Calculation Items
- `Ordinal` - Column ที่ใช้เรียงลำดับ

---

### ขั้นตอนที่ 3: เข้าใจ Measure Selector

**Step 3.1:** เข้าใจว่า Measure Selector คืออะไร:

**Measure Selector** = Column ที่ใช้แสดงชื่อ Calculation Items

**การใช้งาน:**
- ใช้ใน Slicer เพื่อให้ผู้ใช้เลือก Calculation Item ที่ต้องการ
- แต่ละ Calculation Item จะมีชื่อใน Measure Selector Column

---

### ขั้นตอนที่ 4: เข้าใจ Ordinal

**Step 4.1:** เข้าใจว่า Ordinal คืออะไร:

**Ordinal** = Column ที่ใช้เรียงลำดับ Calculation Items

**การใช้งาน:**
- ใช้สำหรับเรียงลำดับ Calculation Items ใน Slicer หรือ Visual
- ค่าต่ำกว่า = แสดงก่อน

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Calculation Group ชื่ออะไร?**
   - **คำตอบ:** "my 1st Calculation group"

2. **Calculation Group นี้มี Calculation Items อะไรบ้าง?**
   - **คำตอบ:** "Reseller Sales Cost" และ "Reseller Sales Revenue"

3. **คอลัมน์ `Measure Selector` ใช้ทำอะไร?**
   - **คำตอบ:** ใช้แสดงชื่อ Calculation Items สำหรับให้ผู้ใช้เลือก

4. **คอลัมน์ `Ordinal` ใช้ทำอะไร?**
   - **คำตอบ:** ใช้เรียงลำดับ Calculation Items

---

## 🎯 แบบฝึกหัดที่ 5: เข้าใจ SELECTEDMEASURE()

### วัตถุประสงค์

เข้าใจวิธีใช้ SELECTEDMEASURE() ใน Calculation Items

---

### ขั้นตอนที่ 1: เข้าใจ SELECTEDMEASURE()

**Step 1.1:** เข้าใจว่า SELECTEDMEASURE() คืออะไร:

**SELECTEDMEASURE()** = Function ที่คืนค่า Measure ที่อยู่ใน Context ปัจจุบัน

**ลักษณะ:**
- ไม่ต้องส่ง Parameter
- ใช้ใน Calculation Items เท่านั้น
- คืนค่าเป็น Measure ที่กำลังถูกประเมิน

**Step 1.2:** ตัวอย่างการใช้งาน:

```dax
calculationItem Current = SELECTEDMEASURE()
```

**อธิบาย:**
- Calculation Item "Current" คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง
- ใช้เป็น Baseline สำหรับเปรียบเทียบ

---

### ขั้นตอนที่ 2: ทำไมต้องใช้ SELECTEDMEASURE()

**Step 2.1:** เหตุผลที่ 1: สามารถใช้กับทุก Measure ได้

**ปัญหา:**
- ถ้าไม่ใช้ SELECTEDMEASURE() ต้องระบุ Measure โดยตรง
- ต้องสร้าง Calculation Item แยกสำหรับแต่ละ Measure

**ตัวอย่างที่ไม่ดี:**
```dax
calculationItem 'Sales LY' = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(...))
calculationItem 'Cost LY' = CALCULATE([Total Cost], SAMEPERIODLASTYEAR(...))
calculationItem 'Profit LY' = CALCULATE([Total Profit], SAMEPERIODLASTYEAR(...))
```

**ตัวอย่างที่ดี:**
```dax
calculationItem LY = CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR(...))
```

**ข้อดี:**
- Calculation Item เดียวใช้ได้กับทุก Measure
- ลดจำนวน Calculation Items ที่ต้องสร้าง

**Step 2.2:** เหตุผลที่ 2: ง่ายต่อการบำรุงรักษา

**ปัญหา:**
- ถ้าไม่ใช้ SELECTEDMEASURE() ต้องแก้ไขทุก Calculation Item เมื่อเปลี่ยน Logic

**ตัวอย่าง:**
- ถ้าต้องการเปลี่ยนสูตร YTD ต้องแก้ไขทุก Calculation Item
- ถ้าใช้ SELECTEDMEASURE() แก้ไขที่ Calculation Item เดียว

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **SELECTEDMEASURE() คืออะไร?**
   - **คำตอบ:** Function ที่คืนค่า Measure ที่อยู่ใน Context ปัจจุบัน ไม่ต้องส่ง Parameter ใช้ใน Calculation Items เท่านั้น

2. **SELECTEDMEASURE() ใช้ที่ไหนได้บ้าง?**
   - **คำตอบ:** ใช้ใน Calculation Items เท่านั้น

3. **ทำไมต้องใช้ SELECTEDMEASURE() แทนการระบุ Measure โดยตรง?**
   - **คำตอบ:** เพราะสามารถใช้กับทุก Measure ได้, ลดจำนวน Calculation Items ที่ต้องสร้าง, และง่ายต่อการบำรุงรักษา

---

## 🎯 แบบฝึกหัดที่ 6: สร้าง Time Intelligence Calculation Group

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Time Intelligence Calculation Group พร้อม Calculation Items

---

### ขั้นตอนที่ 1: สร้าง Calculation Group

**Step 1.1:** สร้าง Calculation Group ใน Power BI Desktop (วิธีหลัก - GA)

> **ของใหม่:** ตั้งแต่ปี 2024 เป็นต้นมา สามารถสร้าง Calculation Group ได้โดยตรงใน **Model view** ของ Power BI Desktop ไม่ต้องใช้ External Tool อีกต่อไป (อ้างอิง: [Create calculation groups - Microsoft Learn](https://learn.microsoft.com/power-bi/transform-model/calculation-groups))

1. ไปที่ **Model view** ใน Power BI Desktop
2. ใน ribbon เลือกปุ่ม **Calculation group**
3. Power BI จะสร้าง Calculation Group พร้อม Calculation Item แรกให้อัตโนมัติ
4. ถ้าโมเดลยังไม่ได้เปิด **Discourage implicit measures** Power BI จะถามให้เปิด — เลือก **เปิด** (Calculation Item ทำงานกับ Explicit Measures เท่านั้น)

**Step 1.2:** ตั้งชื่อ Calculation Group:
- ตั้งชื่อเป็น: `Time Intelligence`

**Step 1.3 (ทางเลือก): สร้างผ่าน Tabular Editor**
- ถ้าต้องการสร้างผ่าน External Tool: เปิด **External Tools** > **Tabular Editor**, Right-click ที่ **Tables** > **Create New** → **Calculation Group** แล้วตั้งชื่อ `Time Intelligence`

---

### ขั้นตอนที่ 2: สร้าง Calculation Item - Current

**Step 2.1:** ใน Calculation Group "Time Intelligence":
1. Right-click
2. เลือก **Create New** → **Calculation Item**

**Step 2.2:** ตั้งชื่อ:
- พิมพ์: `Current`

**Step 2.3:** พิมพ์ Expression:

```dax
SELECTEDMEASURE()
```

**อธิบาย:**
- คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง
- ใช้เป็น Baseline

---

### ขั้นตอนที่ 3: สร้าง Calculation Item - LY

**Step 3.1:** สร้าง Calculation Item ใหม่:

**ชื่อ:** `LY`

**Expression:**
```dax
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- `SAMEPERIODLASTYEAR(...)` หาช่วงเวลาเดียวกันในปีที่แล้ว
- คำนวณ Measure สำหรับปีที่แล้ว

---

### ขั้นตอนที่ 4: สร้าง Calculation Items อื่นๆ

**Step 4.1:** สร้าง Calculation Item - MTD:

**ชื่อ:** `MTD`

**Expression:**
```dax
CALCULATE(
    SELECTEDMEASURE(),
    DATESMTD(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- `DATESMTD(...)` หาช่วง Month-to-Date (จากต้นเดือนถึงปัจจุบัน)

**Step 4.2:** สร้าง Calculation Item - QTD:

**ชื่อ:** `QTD`

**Expression:**
```dax
CALCULATE(
    SELECTEDMEASURE(),
    DATESQTD(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- `DATESQTD(...)` หาช่วง Quarter-to-Date (จากต้นไตรมาสถึงปัจจุบัน)

**Step 4.3:** สร้าง Calculation Item - YTD:

**ชื่อ:** `YTD`

**Expression:**
```dax
CALCULATE(
    SELECTEDMEASURE(),
    DATESYTD(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- `DATESYTD(...)` หาช่วง Year-to-Date (จากต้นปีถึงปัจจุบัน)

---

### ขั้นตอนที่ 5: Save และทดสอบ

**Step 5.1:** ใน Power BI Desktop:
1. Calculation Item ที่สร้างใน Model view จะถูกใช้งานได้ทันที (ไม่ต้อง Save จาก External Tool)
2. กลับไปที่ **Report view** เพื่อทดสอบ

**Step 5.2:** ทดสอบ Calculation Group:
- สร้าง Slicer จาก Calculation Group
- สร้าง Visual ที่แสดง Measure
- เลือก Calculation Item ใน Slicer
- ตรวจสอบว่าผลลัพธ์ถูกต้อง

> **Tip ของใหม่:** ใช้ **DAX Query View** ใน Power BI Desktop เพื่อทดสอบ Calculation Group ด้วย DAX Query ได้เร็วกว่าการสร้าง Visual (ดู [DAX query view - Microsoft Learn](https://learn.microsoft.com/power-bi/transform-model/dax-query-view))

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Calculation Item Current ทำอะไร?**
   - **คำตอบ:** คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง ใช้เป็น Baseline

2. **Calculation Item YTD ทำอะไร?**
   - **คำตอบ:** คำนวณ Measure จากต้นปีถึงปัจจุบัน (Year-to-Date)

---

## 🎯 แบบฝึกหัดที่ 7: สร้าง Currency Conversion Calculation Group

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Currency Conversion Calculation Group พร้อม CROSSFILTER()

---

### ขั้นตอนที่ 1: เข้าใจสถานการณ์

**Step 1.1:** ข้อมูลที่มี:

- มีตาราง `FactCurrencyRate` ที่มี `AverageRate` และ `EndOfDayRate`
- มี Relationship ระหว่าง `DimDate[DateKey]` และ `FactCurrencyRate[DateKey]`

**Step 1.2:** ความต้องการ:

- ต้องการแปลงค่าเงินเป็นสกุลเงินอื่น
- ต้องการให้สามารถเลือก Rate Type ได้ (Average หรือ End of Day)

---

### ขั้นตอนที่ 2: สร้าง Calculation Group

**Step 2.1:** ใน **Model view** ของ Power BI Desktop กดปุ่ม **Calculation group** ใน ribbon (หรือสร้างผ่าน Tabular Editor เหมือนแบบฝึกหัดที่ 6)

**Step 2.2:** สร้าง Calculation Group:
- ชื่อ: `Conversion Rate`
- ตั้ง Precedence: `1` (ทำงานก่อน Time Intelligence) — ตั้งค่าได้จาก **Properties pane** เมื่อเลือก Calculation Group ใน Model view

---

### ขั้นตอนที่ 3: สร้าง Calculation Item - No conversion

**Step 3.1:** สร้าง Calculation Item:

**ชื่อ:** `No conversion (USD)`

**Expression:**
```dax
SELECTEDMEASURE()
```

**อธิบาย:**
- ไม่แปลงค่า (ใช้ Local Currency - USD)
- คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง

---

### ขั้นตอนที่ 4: สร้าง Calculation Item - Conversion (AVG)

**Step 4.1:** สร้าง Calculation Item:

**ชื่อ:** `Conversion (AVG)`

**Expression:**
```dax
VAR _rate =
    CALCULATE(
        AVERAGE(FactCurrencyRate[AverageRate]),
        CROSSFILTER(DimDate[DateKey], FactCurrencyRate[DateKey], BOTH)
    )
RETURN
    SELECTEDMEASURE() * _rate
```

**อธิบายสูตร:**
- `VAR _rate = ...` เก็บ Exchange Rate ในตัวแปร
- `CALCULATE(..., CROSSFILTER(..., BOTH))` คำนวณ Average Rate โดยใช้ Bidirectional Filter
- `SELECTEDMEASURE() * _rate` คูณ Measure ด้วย Exchange Rate

**เหตุผลใช้ CROSSFILTER():**
- FactCurrencyRate table ไม่ได้เชื่อมโดยตรงกับ Fact Table
- ต้องใช้ Date Dimension เป็นตัวเชื่อม
- CROSSFILTER() ช่วยให้สามารถ Filter FactCurrencyRate จาก Date ใน Visual ได้

---

### ขั้นตอนที่ 5: สร้าง Calculation Item - Conversion (EOD)

**Step 5.1:** สร้าง Calculation Item:

**ชื่อ:** `Conversion (EOD)`

**Expression:**
```dax
VAR _rate =
    CALCULATE(
        AVERAGE(FactCurrencyRate[EndOfDayRate]),
        CROSSFILTER(DimDate[DateKey], FactCurrencyRate[DateKey], BOTH)
    )
RETURN
    SELECTEDMEASURE() * _rate
```

**อธิบาย:**
- คำนวณ End of Day Exchange Rate
- คูณ Measure ด้วย End of Day Rate

---

### ขั้นตอนที่ 6: ทดสอบ Calculation Group

**Step 6.1:** Save และ Refresh Model

**Step 6.2:** ทดสอบ:
- สร้าง Slicer จาก Conversion Rate Calculation Group
- สร้าง Visual ที่แสดง Measure
- เลือก Conversion Rate ใน Slicer
- ตรวจสอบว่าผลลัพธ์ถูกต้อง

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องใช้ CROSSFILTER()?**
   - **คำตอบ:** เพราะ FactCurrencyRate table ไม่ได้เชื่อมโดยตรงกับ Fact Table ต้องใช้ Date Dimension เป็นตัวเชื่อม, CROSSFILTER() ช่วยให้สามารถ Filter FactCurrencyRate จาก Date ใน Visual ได้

2. **VAR ใช้ทำอะไร?**
   - **คำตอบ:** เก็บ Exchange Rate ในตัวแปรก่อนคูณกับ Measure

---

## 🎯 แบบฝึกหัดที่ 8: Multiple Calculation Groups

### วัตถุประสงค์

เข้าใจการทำงานของ Multiple Calculation Groups และ Precedence

---

### ขั้นตอนที่ 1: เข้าใจสถานการณ์

**Step 1.1:** ข้อมูลที่มี:

- มี Calculation Group "Time Intelligence" (ไม่มี precedence)
- มี Calculation Group "Conversion Rate" (precedence: 1)

**Step 1.2:** คำถาม:

- Calculation Group ไหนจะทำงานก่อน?
- ถ้าเลือก YTD และ AVG Rate จะทำงานอย่างไร?

---

### ขั้นตอนที่ 2: เข้าใจ Precedence

**Step 2.1:** เข้าใจว่า Precedence คืออะไร:

**Precedence** = ตัวกำหนดลำดับการทำงานของ Calculation Groups

**กฎ:**
- Calculation Group ที่มี Precedence สูงกว่าจะทำงานก่อน
- Calculation Group ที่ไม่มี Precedence จะทำงานทีหลัง

**Step 2.2:** ตัวอย่าง:

- Conversion Rate: precedence = 1 (ทำงานก่อน)
- Time Intelligence: ไม่ตั้ง precedence (ทำงานทีหลัง)

**ลำดับการทำงาน:**
1. Conversion Rate (precedence: 1) - ทำงานก่อน
2. Time Intelligence (ไม่มี precedence) - ทำงานทีหลัง

---

### ขั้นตอนที่ 3: เข้าใจการทำงานของ Multiple Calculation Groups

**Step 3.1:** สถานการณ์: เลือก YTD และ AVG Rate

**ลำดับการทำงาน:**

```
Step 1: AVG Rate (Conversion Rate - precedence: 1)
  ↓
SELECTEDMEASURE() * xrate
  ↓
Step 2: YTD (Time Intelligence - ไม่มี precedence)
  ↓
CALCULATE(AVG Rate, DATESYTD(...))
```

**อธิบาย:**
- AVG Rate จะแปลงค่าเงินก่อน (SELECTEDMEASURE() * xrate)
- แล้ว YTD จะคำนวณ Year to Date ของค่าที่แปลงแล้ว
- ผลลัพธ์ = YTD ของค่าที่แปลงเป็นสกุลเงินอื่นแล้ว

---

### ขั้นตอนที่ 4: ตั้ง Precedence

**Step 4.1:** คำแนะนำ:

**ควรตั้ง Precedence:**
- Conversion Rate: precedence = 1 (ทำงานก่อน)
- Time Intelligence: ไม่ตั้ง (ทำงานทีหลัง)

**เหตุผล:**
- ควรแปลงค่าเงินก่อน
- แล้วค่อยคำนวณ Time Intelligence

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Calculation Group ไหนจะทำงานก่อน?**
   - **คำตอบ:** Conversion Rate (precedence: 1) จะทำงานก่อน เพราะมี Precedence สูงกว่า

2. **ถ้าเลือก YTD และ AVG Rate จะทำงานอย่างไร?**
   - **คำตอบ:** AVG Rate จะแปลงค่าเงินก่อน แล้ว YTD จะคำนวณ Year to Date ของค่าที่แปลงแล้ว

3. **ควรตั้ง Precedence อย่างไร?**
   - **คำตอบ:** Conversion Rate: precedence = 1 (ทำงานก่อน), Time Intelligence: ไม่ตั้ง (ทำงานทีหลัง) เพราะควรแปลงค่าเงินก่อน แล้วค่อยคำนวณ Time Intelligence

---

## 🎯 แบบฝึกหัดที่ 9: SELECTEDVALUE() Function

### วัตถุประสงค์

เข้าใจวิธีใช้ SELECTEDVALUE() เพื่อดึงค่าจาก Filter Context หรือ Slicer

---

### ขั้นตอนที่ 1: เข้าใจ SELECTEDVALUE()

**Step 1.1:** เข้าใจว่า SELECTEDVALUE() คืออะไร:

**SELECTEDVALUE()** = Function ที่คืนค่าที่ถูกเลือกไว้ในคอลัมน์จาก Filter Context หรือ Slicer

**Syntax:**
```dax
SELECTEDVALUE(<columnName>[, <alternateResult>])
```

**Parameters:**
- `columnName` - คอลัมน์ที่ต้องการดึงค่า
- `alternateResult` - ค่า Default ถ้าไม่มีการเลือก หรือมีการเลือกหลายค่า

---

### ขั้นตอนที่ 2: ตัวอย่างการใช้งาน - Measure Selector Pattern

**Step 2.1:** สร้าง Measure Selector Table:

1. ไปที่ **Table tools** → **New table**
2. พิมพ์สูตร:

```dax
Measure Selector = 
DATATABLE(
    "Measure Name", STRING,
    "Measure Sort", INTEGER,
    {
        {"Total Sales", 1},
        {"Total Sales - Bikes", 2},
        {"Total Cost", 3}
    }
)
```

**Step 2.2:** สร้าง Measure ที่ใช้ SELECTEDVALUE():

```dax
Measure to Show = 
VAR ActiveMeasurename = SELECTEDVALUE('Measure Selector'[Measure Name])
VAR Result = 
    SWITCH(
        ActiveMeasurename,
        "Total Sales", [Total Sales],
        "Total Sales - Bikes", [Total Sales - Bikes],
        "Total Cost", [Total Cost],
        BLANK()
    )
RETURN 
    Result
```

**อธิบายสูตร:**
- `SELECTEDVALUE(...)` ดึงค่าจาก Slicer
- `SWITCH(...)` เลือก Measure ตามค่าที่ได้
- ถ้าไม่มีการเลือก → คืนค่า BLANK()

---

### ขั้นตอนที่ 3: ทดสอบ SELECTEDVALUE()

**Step 3.1:** สร้าง Visual:
- สร้าง Slicer จาก `Measure Selector[Measure Name]`
- สร้าง Visual ที่แสดง `Measure to Show`

**Step 3.2:** ทดสอบ:
- เลือก Measure ที่ต้องการใน Slicer
- Visual จะแสดง Measure ที่เลือก
- ตรวจสอบว่าผลลัพธ์ถูกต้อง

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **SELECTEDVALUE() คืออะไร?**
   - **คำตอบ:** Function ที่คืนค่าที่ถูกเลือกไว้ในคอลัมน์จาก Filter Context หรือ Slicer

2. **SELECTEDVALUE() ใช้ทำอะไร?**
   - **คำตอบ:** ดึงค่าจาก Slicer, ดึงค่าจาก Filter Context, สร้าง Measure Selector Pattern

3. **ทำไมต้องมี alternateResult parameter?**
   - **คำตอบ:** เพื่อให้มีค่า Default ที่แน่นอนเมื่อไม่มีการเลือกค่า หรือมีการเลือกหลายค่า

---

## 📝 สรุป

### ✅ สิ่งที่ควรจำ

1. **Explicit Measures** ดีกว่า Implicit Measures (ควบคุม Logic ได้, Performance ดีกว่า, Reuse ได้, รองรับ Excel)
2. **Calculation Groups** ลดจำนวน Measures ที่ต้องสร้าง
3. **SELECTEDMEASURE()** ใช้เพื่อ Reference Measure ใน Context (ใช้ใน Calculation Items เท่านั้น)
4. **SELECTEDVALUE()** ใช้เพื่อดึงค่าจาก Filter Context หรือ Slicer
5. **Precedence** กำหนดลำดับการทำงานของ Calculation Groups (สูงกว่า = ทำงานก่อน)
6. **CROSSFILTER()** ใช้เพื่อ Filter ผ่าน Relationship ใน Calculation Item

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) - Code Examples เพิ่มเติม
