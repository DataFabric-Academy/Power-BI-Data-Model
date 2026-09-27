# การสร้างและจัดการ Power BI Semantic Model

โครงสร้างหลักสูตรการเรียนรู้การสร้างและจัดการ Power BI Semantic Model (เดิมเรียกว่า Data Model) แบ่งออกเป็นโมดูลต่างๆ ดังนี้:

> **หมายเหตุ**: Microsoft ได้เปลี่ยนชื่อจาก "Dataset" หรือ "Data Model" เป็น "Semantic Model" เพื่อสะท้อนถึงความสามารถในการสร้างชั้นข้อมูลทางความหมาย (Semantic Layer) ที่ไม่เพียงแต่เก็บข้อมูล แต่ยังมี Metadata, Calculations, และ Business Logic ด้วย

## จุดเน้นของหลักสูตร

> ⭐ **จุดเน้นสำคัญ**: 
> - มุ่งเน้นที่ **Semantic Model** และ **Relationships** เป็นหลัก
> - **DAX สอนเฉพาะที่เกี่ยวข้องกับ Relationships** ไม่เน้น DAX ทั่วไป
> - **VertiPaq Engine** ถูกปูพื้นฐานตั้งแต่ต้นเพื่อให้เข้าใจการทำงานของ Storage Engine
> - เข้าใจความสำคัญของ **Low Cardinality** และ **Sorted Data** ต่อประสิทธิภาพของ Semantic Model

> **Data Source หลักของหลักสูตร**: **AdventureWorksDW2025** — ดูวิธีเชื่อมต่อและติดตั้งได้ที่ [02-Data-Sources](./02-Data-Sources/) หรือดาวน์โหลด [AdventureWorksDW2025.bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2025.bak) พร้อมวิธี restore จาก [AdventureWorks sample databases - Microsoft Learn](https://learn.microsoft.com/sql/samples/adventureworks-install-configure)

## เวลาเรียนรวม 12 ชั่วโมง ⏱️ (สอนด้วย Power BI Desktop)

> **เงื่อนไขหลักสูตร:** ใช้เวลาสอนรวม**ไม่เกิน 12 ชั่วโมง** สอนบน **Power BI Desktop** เป็นหลัก — งานที่เคยต้องใช้ External Tool ปัจจุบันทำใน Desktop ได้เอง (Model view, DAX Query View, TMDL View) ส่วน External Tools (DAX Studio / Tabular Editor) ใช้เป็นเครื่องมือเสริม

| โมดูล | เวลาเรียน |
|-------|-----------|
| 01-Introduction & VertiPaq Engine (รวม Desktop Views + External Tools) | 1.5 ชม. |
| 02-Data-Sources | 1.0 ชม. |
| 03-Data-Modeling-Basics | 0.5 ชม. |
| 04-Relationships ⭐ หัวใจของหลักสูตร | 2.0 ชม. |
| 05-Dimension-Table-Design | 1.0 ชม. |
| 06-Date-Dimensions-Relationships | 1.5 ชม. |
| 07-Fact-Tables-Design (Calculation Groups) | 1.5 ชม. |
| 08-Performance-Optimization | 1.0 ชม. |
| 09-Best-Practices | 0.5 ชม. |
| 10-Advanced-Modeling | 1.0 ชม. |
| 11-Case-Studies | 0.5 ชม. |
| **รวม** | **12.0 ชั่วโมง** |

> **โมดูล 12 (Security-RLS) และ 13 (Incremental Refresh-Partitioning)** เป็น **ส่วนขยายสำหรับศึกษาเพิ่มเติมนอกเวลาเรียน** (self-study) ไม่นับรวมใน 12 ชั่วโมง

## ของใหม่ที่นำมาอัปเดตหลักสูตร (Power BI Desktop 2025–2026) 🆕

อ้างอิงจาก [Microsoft Learn](https://learn.microsoft.com/power-bi/fundamentals/desktop-latest-update-archive) และ [Power BI Blog](https://powerbi.microsoft.com/blog/):

| ฟีเจอร์ใหม่ | สถานะ | ใช้ในโมดูล |
|------------|-------|-----------|
| สร้าง **Calculation Groups** ใน Model view ของ Desktop (ไม่ต้องใช้ Tabular Editor) | GA | 07 |
| **TMDL View** — แก้ model metadata ด้วยโค้ด รวมถึง `IsAvailableInMDX` | GA (ก.ย. 2025) | 01, 05 |
| **DAX Query View** — เขียน/ทดสอบ DAX Query ใน Desktop + Quick Queries | GA | 01, 07 |
| **Direct Lake ใน Power BI Desktop** — live edit จาก Desktop + ผสม Direct Lake/Import ได้ใน model เดียว | GA (ก.ย. 2025) | 02 |
| **Enhanced DAX Time Intelligence** — Custom Calendar (ปีงบ/4-5-4), `TOTALWTD`, `PREVIOUSWEEK` | Preview (ก.ย. 2025) | 06 |
| **DAX User-Defined Functions** — สร้างฟังก์ชัน DAX ใช้ซ้ำได้ | Preview (มี.ค. 2026) | อ่านเพิ่มเติม |

## โครงสร้างหลักสูตร

### 01-Introduction & VertiPaq Engine ⭐ **ปูพื้นฐานสำคัญ**

**Part 1: Introduction**
- บทนำเกี่ยวกับ Power BI Semantic Model
- ความแตกต่างระหว่าง Data Model และ Semantic Model
- Semantic Model Architecture
- Power BI Process (Formula Engine, Storage Engine)
- Relationships เป็นหัวใจของ Semantic Model
- ภาพรวมของหลักสูตร

**Part 2: VertiPaq Engine**
- VertiPaq Engine และ Columnar Storage Architecture
- Encoding Methods (Value Encoding, Dictionary Encoding, RLE)
- **Low Cardinality และผลกระทบต่อ Performance**
- **Sorted Data เพื่อสนับสนุน Encoding**
- Compression และ Dictionary
- Best Practices สำหรับออกแบบ Semantic Model

**Part 3: External Tools**
- Power BI Desktop Views (Model View, DAX Query View, TMDL View)
- **DAX Studio และ VertiPaq Analyzer** ⭐ - วิเคราะห์ Cardinality และ Compression
- **Tabular Editor** ⭐ - จัดการ Semantic Model ได้เร็วกว่า Power BI Desktop
- เครื่องมืออื่นๆ (ALM Toolkit, Best Practice Analyzer)

### 02-Data-Sources
- ประเภทของแหล่งข้อมูล
- การเชื่อมต่อแหล่งข้อมูล
- Import Mode, DirectQuery Mode, และ Dual Mode
- Composite Models
- Query Folding และ Performance
- การทำ Data Transformation
- **การเตรียมข้อมูลเพื่อลด Cardinality และเพิ่ม Sorting**

### 03-Data-Modeling-Basics
- แนวคิดพื้นฐานการสร้าง Data Model
- Star Schema และ Snowflake Schema
- Fact Tables และ Dimension Tables
- **Low Cardinality Columns สำหรับ Foreign Keys**
- **Sorted Data เพื่อสนับสนุน Encoding**
- การจัดการและจัดระเบียบตาราง
- การออกแบบ Schema เพื่อใช้ประโยชน์จาก VertiPaq Engine

### 04-Relationships ⭐ **หัวใจของหลักสูตร**

**Part 1: พื้นฐาน Relationships**
- ความสัมพันธ์ระหว่างตาราง (Relationships)
- การสร้าง Relationships (Auto-Detection, Manual)
- Cardinality (One-to-Many, Many-to-Many, One-to-One)
- Cross Filter Direction (Single, Both)
- Active และ Inactive Relationships
- Relationship Properties
- VertiPaq Engine และ Relationships

**Part 2: DAX สำหรับ Relationships** (รวมในโมดูลนี้)
- RELATED() และ RELATEDTABLE() - ดึงข้อมูลจาก Related Tables
- CALCULATE() และ Relationship Filters - ควบคุม Filter Context
- USERELATIONSHIP() - ใช้ Inactive Relationships
- CROSSFILTER() - ควบคุม Cross Filter Direction แบบ Dynamic
- TREATAS() - สร้าง Virtual Relationships
- ALLRELATED() และ ALLSELECTED() - ลบ Filter จาก Related Tables
- SELECTEDVALUE() - Measure Selector Pattern
- Filter Context Propagation ผ่าน Relationships

### 05-Dimension-Table-Design
- Surrogate Key, Business Key, Dimension Attributes
- Calculated Columns สำหรับ Dimension Tables (RELATED(), Calculated Tables)
- Attribute Hierarchies (Natural, Non-Natural, Parent-Child)
- PATH(), PATHITEM(), PATHLENGTH() Functions
- Date Dimension (หลายปฏิทิน, Mark as Date Table)
- Time Dimension
- Role-Playing Dimension
- Slow Changing Dimension (SCD Type 1, Type 2)
- IsAvailableInMDX
- การซ่อน Attributes

### 06-Date-Dimensions-Relationships
- Conformed Date Dimension Pattern
- Date Dimension และ Relationships
- Auto Date/Time และการปิด
- Multiple Date Relationships (Role-Playing)
- USERELATIONSHIP() กับ Date Relationships
- Time Intelligence ผ่าน Relationships
- Dummy Table Pattern สำหรับรวม Measures

### 07-Fact-Tables-Design
- Explicit Measures vs Implicit Measures
- Don't Summarize
- การสร้าง Explicit Measures
- Calculation Groups
- SELECTEDMEASURE(), SELECTEDVALUE()
- Time Intelligence Calculation Groups
- Currency Conversion Calculation Groups
- Multiple Calculation Groups และ Precedence

### 08-Performance-Optimization
- การวิเคราะห์ Performance
- Semantic Model Metrics
- VertiPaq Engine Optimization
- การเพิ่มประสิทธิภาพของ Relationships
- Large Models Management
- Best Practices สำหรับ Performance
- การใช้ VertiPaq Analyzer และ DAX Studio

### 09-Best-Practices
- แนวทางปฏิบัติที่ดีสำหรับ Semantic Model
- Naming Conventions
- การจัดการ Errors
- การทำ Documentation
- Relationships Best Practices

### 10-Advanced-Modeling
- Bidirectional Filters และผลกระทบต่อ Performance
- Many-to-Many Relationships
- Bridge Tables
- Composite Models
- Aggregation Tables
- Heterogeneous Granularity

### 11-Case-Studies
- โครงการตัวอย่างจริง
- การแก้ปัญหาในสถานการณ์ต่างๆ
- Best Practices จากกรณีศึกษา
- การประยุกต์ใช้ VertiPaq Engine Knowledge

### 12-Security-RLS (ศึกษาเพิ่มเติมนอกเวลาเรียน)
- Row-Level Security (RLS) แบบ Static และ Dynamic
- Object-Level Security (OLS)
- Table-Level Security
- การจัดการ Security Roles
- การใช้ DAX สำหรับ Security Filters
- Security Best Practices

### 13-Incremental-Refresh-Partitioning (ศึกษาเพิ่มเติมนอกเวลาเรียน)
- Incremental Refresh Policy
- การจัดการ Partitions
- Retention Policies
- Hybrid Tables
- การ Optimize Large Models
- Sorted Partitions เพื่อเพิ่มประสิทธิภาพ


## วิธีการใช้งาน

1. เริ่มจากโมดูล **01-Introduction & VertiPaq Engine** เพื่อปูพื้นฐานที่สำคัญ (รวม VertiPaq Engine และ External Tools)
2. เรียนรู้ตามลำดับโมดูลที่แนะนำ
3. **เน้นที่โมดูล 04-Relationships** ซึ่งเป็นหัวใจของหลักสูตร
4. ฝึกปฏิบัติตาม CODE-EXAMPLES.md และ EXERCISES.md ในแต่ละโมดูล
5. ทำ Case Studies ในโมดูล 11

## หลักการสำคัญ

### Low Cardinality
- คอลัมน์ที่มีค่า unique น้อย (Low Cardinality) จะทำงานได้ดีกับ VertiPaq Engine
- Foreign Keys ควรมี Low Cardinality
- Dimension Tables ช่วยลด Cardinality ใน Fact Tables

### Sorted Data
- ข้อมูลที่เรียงลำดับ (Sorted) จะช่วยเพิ่มประสิทธิภาพ RLE Encoding
- ควรเรียงข้อมูลใน Fact Table ตามคอลัมน์ที่สำคัญ
- Sorted Data ช่วยเพิ่มประสิทธิภาพการ Join และ Relationships

### Relationships Focus
- Relationships เป็นหัวใจของ Semantic Model
- DAX ที่สอนในหลักสูตรเน้นเฉพาะที่เกี่ยวข้องกับ Relationships
- การออกแบบ Relationships ที่ดีจะส่งผลต่อ Performance มาก
