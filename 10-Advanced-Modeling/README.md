# 11 - Advanced Modeling Patterns

## เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับเทคนิคการสร้าง Semantic Model ขั้นสูง รวมถึงการจัดการกับ Heterogeneous Granularity, Bidirectional Filters, Many-to-Many Relationships, และเทคนิคขั้นสูงอื่นๆ

> **หมายเหตุ:** เนื้อหาเกี่ยวกับ Explicit Measures และ Calculation Groups ได้ย้ายไปอยู่ใน **08-Fact-Tables-Design** แล้ว

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 📋 หัวข้อการเรียนรู้

### 1. Heterogeneous Granularity

#### 1.1 ความหมาย

**Heterogeneous Granularity** หมายถึงสถานการณ์ที่ Fact Tables ในโมเดลเดียวกันมีระดับความละเอียด (Granularity) ที่แตกต่างกัน

**ปัญหา:**
- Fact Tables ต่างกันมี Granularity ไม่เท่ากัน
- ไม่สามารถดู Measures จาก Fact Tables ต่างกันร่วมกันได้โดยตรง
- ต้องหาวิธีควบคุม Granularity เพื่อให้สามารถเปรียบเทียบข้อมูลได้

**ตัวอย่างจาก AdventureWorksDW2025:**

**FactSalesQuota:**
- **Granularity**: ระดับ Month/Quarter/Year
- **Date Key**: `MonthKey` (เชื่อมกับ DimDate ผ่าน MonthKey)
- **Measure**:
  ```dax
  Sales Quota = SUM(FactSalesQuota[SalesAmountQuota])
  ```

**FactResellerSales:**
- **Granularity**: ระดับ Transaction/Date (รายการขายแต่ละวัน)
- **Date Key**: `OrderDateKey`, `ShipDateKey`, `DueDateKey`
- **Measure**:
  ```dax
  Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
  ```

**ปัญหา:**
- FactSalesQuota มีข้อมูลระดับเดือน
- FactResellerSales มีข้อมูลระดับวัน
- ไม่สามารถเปรียบเทียบ Sales Revenue กับ Sales Quota ในระดับวันได้โดยตรง

---

#### 1.2 วิธีการจัดการ Heterogeneous Granularity

##### วิธีที่ 1: ใช้ Hierarchy ควบคุม Granularity

**แนวคิด:**
- ใช้ Date Hierarchy เพื่อควบคุมระดับความละเอียด
- เมื่อเลือกระดับ Month/Quarter/Year → ทั้งสอง Measures จะแสดงข้อมูลได้

**ตัวอย่าง Hierarchies:**
```
Calendar Year
└── Calendar Quarter
    └── Calendar Month
        └── Day
```

**การใช้งาน:**
- เมื่อเลือก **Calendar Month** → FactSalesQuota และ FactResellerSales จะแสดงข้อมูลในระดับเดือน
- เมื่อเลือก **Day** → เฉพาะ FactResellerSales จะแสดงข้อมูลได้ (FactSalesQuota ไม่มีข้อมูลระดับวัน)

**ประโยชน์:**
- ควบคุม Granularity ผ่าน Hierarchy
- ผู้ใช้เลือกระดับความละเอียดที่ต้องการได้เอง
- ไม่ต้องเขียน DAX ซับซ้อน

---

##### วิธีที่ 2: ใช้ Time Intelligence Functions

**แนวคิด:**
- ใช้ Time Intelligence Functions เพื่อ Aggregate ข้อมูลระดับ Transaction ให้เป็นระดับ Month/Quarter/Year
- ทำให้ FactResellerSales มี Granularity ที่ตรงกับ FactSalesQuota

**ตัวอย่าง:**

**Base Measure (FactResellerSales):**
```dax
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

**Measure ที่ Aggregate เป็นระดับเดือน:**
```dax
Reseller Sales Revenue (Monthly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day]),
    VALUES('Date'[MonthKey])
)
```

**Measure ที่ Aggregate เป็นระดับไตรมาส:**
```dax
Reseller Sales Revenue (Quarterly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day], 'Date'[MonthKey]),
    VALUES('Date'[CalendarQuarter])
)
```

**Measure ที่ Aggregate เป็นระดับปี:**
```dax
Reseller Sales Revenue (Yearly) = 
CALCULATE(
    [Reseller Sales Revenue],
    ALL('Date'[Day], 'Date'[MonthKey], 'Date'[CalendarQuarter]),
    VALUES('Date'[CalendarYear])
)
```

**อธิบาย:**
- ใช้ `ALL()` เพื่อลบ Filter Context ในระดับที่ต่ำกว่า
- ใช้ `VALUES()` เพื่อกำหนด Filter Context ในระดับที่ต้องการ

**ประโยชน์:**
- สามารถควบคุม Granularity ได้อย่างชัดเจน
- สร้าง Measures ที่มี Granularity ตรงกับ FactSalesQuota

---

##### วิธีที่ 3: ใช้ Conformed Date Dimension

**แนวคิด:**
- สร้าง Conformed Date Dimension ที่รองรับทั้ง Granularity ระดับวันและระดับเดือน
- Fact Tables ทั้งสองเชื่อมกับ Date Dimension เดียวกัน แต่ผ่าน Date Keys ที่ต่างกัน

**โครงสร้าง Relationships:**

```
DimDate (Conformed Dimension)
│
├── FactSalesQuota
│   └── MonthKey → DimDate[MonthKey] (Active)
│
└── FactResellerSales
    └── OrderDateKey → DimDate[DateKey] (Active)
```

**ตัวอย่าง Measures:**

```dax
// FactSalesQuota - ระดับเดือน
Sales Quota = SUM(FactSalesQuota[SalesAmountQuota])

// FactResellerSales - ระดับวัน (แต่สามารถ Aggregate เป็นเดือนได้)
Reseller Sales Revenue = SUM(FactResellerSales[SalesAmount])
```

**การใช้งาน:**
- เมื่อเลือก **Month** ใน Date Hierarchy → ทั้งสอง Measures จะแสดงข้อมูลได้
- Date Hierarchy จะควบคุม Granularity อัตโนมัติ

**ประโยชน์:**
- ใช้ Conformed Date Dimension ร่วมกัน
- ความสอดคล้องของข้อมูล
- ง่ายต่อการบำรุงรักษา

---

##### วิธีที่ 4: Daily Allocation ด้วย CROSSFILTER(None) + TREATAS ⭐ (ตัวอย่างจริงจาก AdventureWorksDW2025)

สามวิธีข้างบนดูปัญหาจากฝั่งยอดขาย แต่งานจริงมีอีกทางเลือก: **จัดสรร (allocate) quota รายไตรมาสลงเป็นรายวัน** เพื่อเทียบกับยอดขายรายวันได้เลย จากข้อมูลจริงของ AW:

**ลักษณะข้อมูลจริงของ FactSalesQuota:**
- Grain = **รายพนักงาน ต่อ ไตรมาส** (10–17 แถวต่อไตรมาส ตามขนาดทีมขาย)
- วันที่ anchor ของแต่ละไตรมาส**ไม่ตรง boundary** เสมอไป เช่น ปี 2011: 31 มี.ค. / 30 มิ.ย. / 29 ก.ย. / 29 ธ.ค. — ดังนั้นห้ามคำนวณจากวันที่ ต้อง map ด้วยคู่ `(CalendarYear, CalendarQuarter)` ของตารางโควตาเอง
- **ไตรมาส 2/2011 ไม่มีแถวโควตาเลย** และแถวที่ควรเป็น Q2 ถูกบันทึกเป็น 1 ก.ค. tag Q3 (data quality จริงที่ใช้คุยในคลาสได้ — ตัวอย่างถือว่าแก้ที่ต้นทางแล้ว)

**แนวคิดของ Measure:**

```
โควตาต่อวัน = โควตาของไตรมาสที่วันนั้นสังกัด ÷ จำนวนวันของไตรมาสนั้น
```

1. `CROSSFILTER(FactSalesQuota[DateKey], DimDate[DateKey], None)` — **ปิด relationship ทางวันที่ชั่วคราว** ไม่ให้ filter "วัน" กลั่นแถวโควตาจนหมด
2. `TREATAS({(ปี, ไตรมาส)}, FactSalesQuota[CalendarYear], FactSalesQuota[CalendarQuarter])` — ทาบกลับด้วยมิติไตรมาส (virtual relationship)
3. หารเท่า ๆ ตามจำนวนวันของไตรมาส แล้ว `SUMX` รวม

**ตัวเลขจริงที่ได้:**

| ระดับ | `Sales Quota Amount` (ธรรมดา) | `(Daily Alloc)` |
|---|---|---|
| ปี 2011 | 25,982,000 | 25,982,000 ✓ (เท่ากัน — แค่กระจายลงวัน) |
| วันที่ 30 มิ.ย. 2011 | 4,750,000 (โผล่เฉพาะวัน anchor) | 52,198 = 4.75M ÷ 91 วัน |
| วันที่อื่น ๆ ของ Q2 | (blank) | 52,198 ทุกวัน |

**ประโยชน์:**
- เทียบ actual (รายวัน) กับ quota (รายไตรมาส) ได้ใน visual เดียว ทุกระดับ
- ใช้เทคนิคของคอร์สครบใน measure เดียว: `CROSSFILTER(None)` + `TREATAS`
- จุดสอน data quality: ตรวจวัน anchor และช่วงที่ขาดข้อมูลของตาราง quota ก่อนเขียน measure เสมอ

**👉 ดูโค้ดเต็ม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

#### 1.3 Best Practices สำหรับ Heterogeneous Granularity

1. **ใช้ Conformed Date Dimension**
   - ใช้ Date Dimension เดียวกันสำหรับทุก Fact Table
   - สร้าง Relationships ที่เหมาะสมตาม Granularity

2. **สร้าง Hierarchy ให้เหมาะสม**
   - สร้าง Date Hierarchy ที่รองรับหลายระดับ
   - เช่น Year > Quarter > Month > Day

3. **ใช้ Time Intelligence Functions อย่างระมัดระวัง**
   - ใช้เมื่อจำเป็นต้อง Aggregate ข้อมูล
   - พิจารณา Performance Impact

4. **อธิบาย Granularity ให้ชัดเจน**
   - ตั้งชื่อ Measures ให้ระบุ Granularity ชัดเจน
   - เช่น `Sales Revenue (Monthly)`, `Sales Revenue (Daily)`

5. **พิจารณา Performance**
   - การ Aggregate ข้อมูลอาจส่งผลต่อ Performance
   - ใช้ VertiPaq Engine Optimization เพื่อเพิ่มประสิทธิภาพ

6. **ตรวจ Grain และวันที่ Anchor ของ Fact ที่หยาบกว่าก่อนเขียน Measure**
   - ดูว่าตาราง quota/budget มี grain ระดับใด (เดือน/ไตรมาส) และวันที่ anchor ตรง boundary หรือไม่
   - ถ้า anchor ไม่ตรง ให้ map ด้วยคอลัมน์ปี/ไตรมาสของตารางเอง (`TREATAS`) อย่าคำนวณจากวันที่
   - ตรวจช่วงเวลาที่ขาดข้อมูล (เช่น ไตรมาสที่ไม่มีแถว) ก่อนเผชิญหน้าผู้เรียน/ผู้ใช้

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 2. Bidirectional Filters

**Bidirectional Filters** คือการตั้งค่า Cross-Filter Direction เป็น "Both" เพื่อให้ Filter Context สามารถส่งผ่าน Relationship ได้ทั้งสองทิศทาง

**สถานการณ์ที่เหมาะสม:**
- เมื่อต้องการให้การกรองส่งผลทั้งสองทิศทาง
- เช่น กรอง Product → Filter Sales ทั้ง Product และ Customer

**ข้อควรระวัง:**
- อาจทำให้เกิด Circular Dependencies
- Performance อาจลดลง
- ควรใช้ด้วยความระมัดระวัง

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 3. Many-to-Many Relationships

**Many-to-Many Relationships** คือความสัมพันธ์ที่ทั้งสองคอลัมน์มีค่าซ้ำกัน

**ปัญหา:**
- Power BI ไม่รองรับ Many-to-Many Relationships โดยตรง
- อาจทำให้ผลลัพธ์ไม่ถูกต้อง

**แนวทางแก้ไข:**
- ใช้ Bridge Tables
- ใช้ Conformed Dimensions
- หลีกเลี่ยง Many-to-Many Relationships ถ้าเป็นไปได้

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 4. Bridge Tables

**Bridge Tables** คือตารางที่ใช้เชื่อมระหว่าง Fact Tables และ Dimension Tables ที่มีความสัมพันธ์แบบ Many-to-Many

**ตัวอย่าง:**
- Product Fact Table → Bridge Table → Category Dimension Table
- Student Fact Table → Bridge Table → Course Dimension Table

**ประโยชน์:**
- แก้ปัญหา Many-to-Many Relationships
- โครงสร้าง Model ชัดเจนขึ้น

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 5. Composite Models

**Composite Models** คือ Model ที่รวมหลายประเภทของ Data Connection (Import + DirectQuery)

**สถานการณ์ที่เหมาะสม:**
- ต้องการรวมข้อมูลจากหลายแหล่ง
- ข้อมูลบางส่วนต้อง Real-time (DirectQuery)
- ข้อมูลบางส่วนสามารถ Import ได้

**ข้อควรระวัง:**
- Performance อาจลดลง
- ต้องจัดการ Relationships อย่างระมัดระวัง

---

### 6. Aggregation Tables

**Aggregation Tables** คือตารางที่เก็บข้อมูลที่ Aggregate ไว้ล่วงหน้าเพื่อเพิ่มประสิทธิภาพ

**ตัวอย่าง:**
- ตาราง `Sales Daily` (Fact Table) → ตาราง `Sales Monthly` (Aggregation Table)
- Query ระดับเดือนจะใช้ Aggregation Table แทน Fact Table

**ประโยชน์:**
- Performance ดีขึ้นมาก
- ลดขนาด Query ที่ต้องประมวลผล

**ข้อควรระวัง:**
- ต้องบำรุงรักษา Aggregation Tables
- อาจทำให้ Model ซับซ้อนขึ้น

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ เข้าใจปัญหา Heterogeneous Granularity
- ✅ จัดการกับ Fact Tables ที่มี Granularity แตกต่างกันได้
- ✅ ใช้ Hierarchy เพื่อควบคุม Granularity ได้
- ✅ ใช้ Time Intelligence Functions เพื่อ Aggregate ข้อมูลได้
- ✅ เข้าใจ Bidirectional Filters และผลกระทบต่อ Performance
- ✅ เข้าใจ Many-to-Many Relationships และ Bridge Tables
- ✅ เข้าใจ Composite Models และ Aggregation Tables

---

## 📚 เอกสารที่เกี่ยวข้อง

- **03-Data-Modeling-Basics**: พื้นฐาน Dimensional Model และ Conformed Dimensions
- **04-Relationships**: พื้นฐาน Relationships และ Cross-Filter Direction
- **06-Date-Dimensions-Relationships**: Conformed Date Dimension และ Multiple Date Relationships
- **07-Fact-Tables-Design**: Explicit Measures และ Calculation Groups
- **[CODE-EXAMPLES.md](./CODE-EXAMPLES.md)** - ตัวอย่างโค้ด DAX
- **[EXERCISES.md](./EXERCISES.md)** - แบบฝึกหัด

---

## 📝 สรุป

### Best Practices

1. **จัดการ Heterogeneous Granularity**
   - ใช้ Conformed Date Dimension
   - สร้าง Hierarchy ให้เหมาะสม
   - ใช้ Time Intelligence Functions อย่างระมัดระวัง

2. **ใช้ Bidirectional Filters เมื่อจำเป็นเท่านั้น**
   - หลีกเลี่ยง Circular Dependencies
   - พิจารณา Performance Impact

3. **หลีกเลี่ยง Many-to-Many Relationships**
   - ใช้ Bridge Tables แทน
   - ใช้ Conformed Dimensions

4. **พิจารณา Composite Models และ Aggregation Tables**
   - ใช้เมื่อจำเป็นต้องเพิ่มประสิทธิภาพ
   - บำรุงรักษาอย่างสม่ำเสมอ

---

### ไฟล์ตัวอย่างที่แนะนำ

**หมายเหตุ:** ตัวอย่างในโมดูลนี้ใช้ AdventureWorksDW2025 เป็น Data Source โดยเฉพาะ FactSalesQuota และ FactResellerSales สำหรับ Heterogeneous Granularity

---

**🎉 ขอแสดงความยินดี! คุณได้เรียนจบโมดูล Advanced Modeling Patterns แล้ว!**

**ขั้นตอนต่อไป:**
- ฝึกปฏิบัติตาม [EXERCISES.md](./EXERCISES.md)
- ดูตัวอย่างโค้ดใน [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)
- เรียนโมดูล 08-Performance-Optimization (Advanced Patterns มักเรียนหลัง Performance Optimization)
