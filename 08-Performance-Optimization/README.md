# 08 - Performance Optimization

## 📚 เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับการ**วิเคราะห์และปรับปรุงประสิทธิภาพ**ของ Power BI Semantic Model เพื่อให้รายงานทำงานเร็วขึ้น ใช้ Memory น้อยลง และให้ประสบการณ์ที่ดีกับผู้ใช้ รวมถึงการใช้เครื่องมือวิเคราะห์ Performance (Performance Analyzer, DAX Studio, VertiPaq Analyzer) และการวัดผลก่อนและหลัง Optimization

> **หมายเหตุ:** ส่วนใหญ่สามารถทำได้ใน **Power BI Desktop** แต่ Semantic Model Metrics และการ Monitor Performance แบบ Real-time ต้องใช้ **Power BI Service**

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW2025**

---

## 📋 หัวข้อการเรียนรู้

### 1. ทำไมต้อง Optimize Performance? ⭐

เมื่อเราสร้าง Power BI Report หรือ Semantic Model ขึ้นมา เราอาจพบปัญหาต่างๆ ที่ทำให้รายงานทำงานไม่ดี เช่น Report โหลดช้ามาก ผู้ใช้ต้องรอเป็นเวลานาน หรือใช้ Memory ของเครื่องคอมพิวเตอร์มากเกินไป ซึ่งปัญหาเหล่านี้จะทำให้ผู้ใช้ไม่พึงพอใจและไม่ต้องการใช้รายงานของเรา

การ Optimize Performance คือการปรับปรุงประสิทธิภาพของ Semantic Model ให้รายงานทำงานได้เร็วขึ้น ใช้ Memory น้อยลง และให้ประสบการณ์ที่ดีกับผู้ใช้มากขึ้น 

**ปัญหาที่เรามักพบเมื่อไม่ได้ Optimize Performance:**
Report โหลดช้ามาก เพราะข้อมูลที่ต้องประมวลผลมีจำนวนมาก หรือการ Query ข้อมูลใช้เวลานาน และระบบใช้ Memory มากเกินไปจนทำให้เครื่องคอมพิวเตอร์ทำงานช้าลง

**ผลลัพธ์ที่ได้หลังจากการ Optimize Performance:**
หลังจากที่เราทำการ Optimize Performance แล้ว Report จะโหลดได้เร็วขึ้นมาก Query ข้อมูลก็ทำงานได้เร็วขึ้น และใช้ Memory น้อยลง ทำให้ผู้ใช้พึงพอใจและอยากใช้รายงานของเรามากขึ้น

---

### 2. การวิเคราะห์ Performance

#### 2.1 Performance Analyzer ใน Power BI Desktop

Performance Analyzer เป็นเครื่องมือที่อยู่ใน Power BI Desktop เอง ใช้สำหรับวิเคราะห์ว่าส่วนไหนของรายงานที่ทำให้ช้า โดยจะแสดงเวลาที่ใช้ในการทำงานของแต่ละ Visual

**วิธีใช้ Performance Analyzer:**
ก่อนอื่นเราต้องเปิด Performance Analyzer โดยไปที่เมนู **View** แล้วเลือก **Performance Analyzer** จากนั้นกด **Start Recording** เพื่อเริ่มบันทึกการทำงาน เมื่อเราทำการ Refresh Visual หรือ Report เครื่องมือจะแสดงผลลัพธ์ว่าแต่ละ Visual ใช้เวลานานเท่าไหร่ โดยแสดงข้อมูลสำคัญดังนี้

**Time** คือเวลาที่ใช้ในการทำงานทั้งหมดของ Visual นั้นๆ ซึ่งจะบอกเราว่า Visual ไหนช้าที่สุด **Visual Type** คือประเภทของ Visual เช่น Table, Chart, Matrix เป็นต้น **DAX Query** คือคำสั่ง DAX ที่ถูกใช้ในการคำนวณ และ **Details** คือรายละเอียดเพิ่มเติมเกี่ยวกับการทำงาน

**สิ่งที่เราสามารถวิเคราะห์ได้จาก Performance Analyzer:**
เครื่องมือนี้ช่วยให้เรารู้ว่า Visual ไหนช้าที่สุดในรายงานของเรา Query ไหนใช้เวลานาน และ Measures ไหนมีปัญหา ซึ่งข้อมูลเหล่านี้จะช่วยให้เรารู้ว่าควรจะ Optimize ส่วนไหนก่อน

#### 2.2 DAX Studio

DAX Studio เป็น External Tool ที่ช่วยให้เราวิเคราะห์ Performance ของ Query ใน Power BI โดยละเอียดมากกว่า Performance Analyzer ซึ่งช่วยให้เราเข้าใจว่าทำไม Query ถึงช้าและแก้ไขได้อย่างไร

**สิ่งที่ DAX Studio ใช้วิเคราะห์ได้:**
DAX Studio สามารถวิเคราะห์ **Query Performance** คือประสิทธิภาพของ Query ว่าทำงานเร็วหรือช้าแค่ไหน **Query Plan** คือแผนการทำงานของ Query ว่ามีการทำงานอย่างไรบ้าง **Storage Engine Events** คือการทำงานของ Storage Engine และ **Formula Engine Events** คือการทำงานของ Formula Engine

**วิธีใช้ DAX Studio:**
ขั้นแรกเราต้องเปิด DAX Studio โดยไปที่เมนู **External Tools** ใน Power BI Desktop แล้วเลือก **DAX Studio** จากนั้นเขียนหรือ Copy DAX Query ที่ต้องการวิเคราะห์มาใส่ แล้วกด **Run** เพื่อให้ Query ทำงาน

หลังจาก Query ทำงานเสร็จแล้ว ให้ดูที่แท็บ **Server Timings** ซึ่งจะแสดงข้อมูลสำคัญดังนี้ **Total Duration** คือเวลาทั้งหมดที่ Query ใช้ในการทำงาน **Storage Engine Query (SE Query)** คือเวลาที่ Storage Engine ใช้ในการดึงข้อมูล และ **Formula Engine Query (FE Query)** คือเวลาที่ Formula Engine ใช้ในการคำนวณผลลัพธ์

ข้อมูลเหล่านี้จะช่วยให้เราเข้าใจว่า Query ใช้เวลาอยู่ที่ส่วนไหนมากที่สุด และควรจะแก้ไขอย่างไร

#### 2.3 VertiPaq Analyzer

VertiPaq Analyzer เป็นส่วนหนึ่งของ DAX Studio ที่ช่วยให้เราวิเคราะห์โครงสร้างข้อมูลใน VertiPaq Engine ว่าข้อมูลแต่ละ Column มี Cardinality เท่าไหร่ ใช้พื้นที่มากแค่ไหน และใช้ Encoding Method อะไร

**สิ่งที่ VertiPaq Analyzer ใช้วิเคราะห์ได้:**
VertiPaq Analyzer จะแสดงข้อมูลสำคัญเกี่ยวกับแต่ละ Column ใน Table ดังนี้ **Cardinality** คือจำนวนค่าที่ไม่ซ้ำกันใน Column นั้นๆ ซึ่งสำคัญมากเพราะ Low Cardinality จะทำให้ Dictionary Encoding ทำงานได้ดีขึ้น **Size** คือขนาดของข้อมูลใน Column นั้นๆ เป็น KB หรือ MB **Encoding Method** คือวิธีที่ VertiPaq ใช้เข้ารหัสข้อมูล เช่น Dictionary, Hash, Value Encoding และ **Compression Ratio** คืออัตราการบีบอัดข้อมูลว่าบีบอัดได้มากแค่ไหน

**วิธีใช้ VertiPaq Analyzer:**
ขั้นแรกเราต้องเปิด DAX Studio ก่อน แล้วไปที่แท็บ **VertiPaq Analyzer** จากนั้นเลือก Table ที่ต้องการวิเคราะห์ เครื่องมือจะแสดงผลลัพธ์ในรูปแบบตารางที่บอกข้อมูลของแต่ละ Column ได้แก่ **Column Name** คือชื่อของ Column **Cardinality** คือจำนวนค่าที่ไม่ซ้ำกัน **Size (KB)** คือขนาดของข้อมูลเป็น KB และ **Encoding** คือวิธี Encoding ที่ใช้

ข้อมูลเหล่านี้จะช่วยให้เรารู้ว่า Column ไหนควรจะลด Cardinality หรือ Column ไหนใช้พื้นที่มากเกินไป

---

### 3. VertiPaq Engine Optimization ⭐

#### 3.1 ลด Cardinality

Cardinality คือจำนวนค่าที่ไม่ซ้ำกันใน Column นั้นๆ เช่น ถ้า Column มีค่า 1, 2, 3, 1, 2, 3 แสดงว่า Cardinality เท่ากับ 3 เพราะมีค่าไม่ซ้ำกัน 3 ค่า

**ทำไมต้องลด Cardinality:**
เมื่อ Column มี Low Cardinality (ค่าซ้ำกันมาก) VertiPaq Engine จะใช้ Dictionary Encoding ได้ดีมาก ซึ่งจะช่วยให้ประหยัด Memory และทำให้ Query ทำงานได้เร็วขึ้น เพราะ Dictionary Encoding จะสร้าง Dictionary ของค่าที่ไม่ซ้ำกันเพียงไม่กี่ค่า แล้วเก็บแค่ Reference ID แทนที่จะเก็บค่าจริงซ้ำๆ กัน

**วิธีลด Cardinality:**

**1. ใช้ Dimension Tables แทนการเก็บ Attributes ใน Fact Table**

ถ้าเราเก็บ ProductName ไว้ใน Fact Table โดยตรง เช่น FactSales มี Column ชื่อ ProductName ที่มี Cardinality เท่ากับ 1,000 (มีสินค้า 1,000 ชนิด) นี่ถือว่า Cardinality สูงเกินไป

วิธีที่ดีกว่าคือแยก ProductName ออกไปเก็บไว้ใน Dimension Table ชื่อ DimProduct แล้วใน FactSales ให้เก็บแค่ ProductKey ที่เป็น Integer และมี Cardinality ต่ำกว่า (เช่น 100) แล้วสร้าง Relationship เชื่อมระหว่าง FactSales.ProductKey กับ DimProduct.ProductKey แบบนี้จะทำให้ FactSales มี Cardinality ต่ำลง และ Performance ดีขึ้น

**2. แบ่ง DateTime ออกเป็น Date และ Time แยกกัน**

ถ้าเราเก็บ OrderDateTime แบบเต็มรูปแบบ เช่น `2024-01-15 10:23:45.123` ซึ่งมี Cardinality สูงมาก (อาจถึง 1,000,000) เพราะแต่ละวินาทีหรือมิลลิวินาทีจะถือว่าเป็นค่าที่ไม่ซ้ำกัน

วิธีที่ดีกว่าคือแบ่งออกเป็น 2 Columns คือ OrderDate ที่เก็บแค่วันที่ เช่น `2024-01-15` (Cardinality = 365 ถ้ามีข้อมูล 1 ปี) และ OrderTime ที่เก็บเวลา เช่น `10:23` (Cardinality = 1,440 ถ้าเก็บเป็นนาที) แบบนี้จะทำให้ Cardinality ลดลงมาก

**3. ใช้ Integer แทน Text**

ถ้าเราเก็บ Status เป็น Text เช่น "Active", "Inactive", "Pending" ซึ่งเป็น Text ข้อมูลนี้จะมี Cardinality สูงและใช้พื้นที่มาก

วิธีที่ดีกว่าคือสร้าง Dimension Table ชื่อ DimStatus ที่มี StatusID เป็น Integer (1, 2, 3) และ StatusName เป็น Text แล้วใน Fact Table ให้เก็บแค่ StatusID (Integer) แบบนี้จะทำให้ Cardinality ต่ำและใช้พื้นที่น้อยลง

**การวิเคราะห์ Cardinality ด้วย VertiPaq Analyzer:**

หลังจากที่เราใช้ VertiPaq Analyzer วิเคราะห์แล้ว เราจะเห็น Cardinality ของแต่ละ Column ชัดเจน ซึ่งจะช่วยให้เรารู้ว่า Column ไหนมี High Cardinality และควรจะแก้ไขอย่างไร

**ตัวอย่างการวิเคราะห์:**
สมมติว่าเราใช้ VertiPaq Analyzer วิเคราะห์แล้วพบว่า Column ชื่อ ProductName มี Cardinality เท่ากับ 1,234 ซึ่งสูงเกินไป ขนาดข้อมูลคือ 5.2 MB และใช้ Hash Encoding อยู่

จากข้อมูลนี้เราสามารถสรุปได้ว่าควรจะเปลี่ยนจากเก็บ ProductName ใน Fact Table เป็นใช้ ProductKey + DimProduct แทน เพื่อลด Cardinality และเพิ่มประสิทธิภาพ

#### 3.2 เรียงข้อมูล (Sorting)

การเรียงข้อมูลก่อน Import เข้า Power BI Semantic Model เป็นสิ่งสำคัญมาก เพราะจะช่วยให้ VertiPaq Engine สามารถใช้ RLE Encoding ได้อย่างมีประสิทธิภาพ

**ทำไมต้องเรียงข้อมูล:**
เมื่อข้อมูลถูกเรียงลำดับแล้ว ค่าที่เหมือนกันจะอยู่ติดกันมากขึ้น ซึ่งจะทำให้ VertiPaq Engine ใช้ RLE (Run-Length Encoding) ได้ดีขึ้น RLE Encoding จะบีบอัดข้อมูลโดยการนับว่าค่าซ้ำกันกี่ครั้ง แล้วเก็บแค่ค่าพร้อมจำนวนครั้งที่ซ้ำกันแทนที่จะเก็บค่าซ้ำๆ กันหลายครั้ง ซึ่งจะช่วยประหยัด Memory และทำให้ Query ทำงานเร็วขึ้น

**วิธีเรียงข้อมูล:**

**1. เรียงใน Power Query ก่อน Import**

วิธีที่แนะนำที่สุดคือเรียงข้อมูลใน Power Query ก่อนที่จะ Import เข้า Power BI โดยเปิด Power Query Editor เลือก Table ที่ต้องการเรียง แล้วเลือก Column ที่ต้องการเรียงลำดับ เช่น ProductKey หรือ OrderDateKey จากนั้นคลิก Sort Ascending หรือ Sort Descending แล้ว Apply Changes การเรียงข้อมูลใน Power Query จะทำให้ข้อมูลถูกเรียงก่อนที่จะ Import เข้า VertiPaq Engine

**2. เรียงตาม Foreign Keys**

ควรเรียง Fact Table ตาม Foreign Keys เช่น ถ้าเราต้องการเรียง FactResellerSales ควรเรียงตาม ProductKey ซึ่งจะทำให้ ProductKey ที่เหมือนกันอยู่ติดกันมากขึ้น และ RLE Encoding จะทำงานได้ดีขึ้น

**3. เรียงตาม Date**

การเรียงตาม Date Dimension เช่น OrderDateKey ก็เป็นวิธีที่ดีเหมือนกัน เพราะจะทำให้วันที่เดียวกันอยู่ติดกัน และ RLE Encoding จะบีบอัดได้ดีขึ้น

**การวิเคราะห์ Sorting Impact ด้วย VertiPaq Analyzer:**

หลังจากที่เราเรียงข้อมูลแล้ว เราสามารถใช้ VertiPaq Analyzer เพื่อตรวจสอบ Compression Ratio ว่าดีขึ้นหรือไม่ โดยจะวัดผลการเรียงข้อมูลต่อ RLE Encoding และเปรียบเทียบ Compression Ratio ก่อนและหลัง Sorting

**ตัวอย่างการวิเคราะห์:**
สมมติว่าเราใช้ VertiPaq Analyzer ตรวจสอบ ProductKey ก่อน Sorting พบว่า Compression Ratio เท่ากับ 2.5x (หมายความว่าบีบอัดข้อมูลได้ 2.5 เท่า) แต่หลังจากที่เรียงข้อมูลแล้ว Compression Ratio เพิ่มขึ้นเป็น 5.2x ซึ่งหมายความว่าบีบอัดข้อมูลได้ดีขึ้นมาก นี้แสดงให้เห็นว่าการเรียงข้อมูลช่วยเพิ่มประสิทธิภาพของ RLE Encoding ได้จริง

#### 3.3 ลดจำนวน Columns

การมี Columns ที่ไม่จำเป็นใน Model จะทำให้ขนาดของ Model ใหญ่ขึ้น และทำให้ Query ทำงานช้าลง เพราะ VertiPaq Engine ต้องอ่าน Columns ที่ไม่จำเป็นด้วย

**หลักการ:**
เราควรลบ Columns ที่ไม่ใช้หรือไม่จำเป็นออกจาก Model เพื่อลดขนาด Model และเพิ่มประสิทธิภาพการทำงาน เมื่อ Model มีขนาดเล็กลง Query ก็จะทำงานเร็วขึ้น และใช้ Memory น้อยลง

**วิธีลดจำนวน Columns:**

ขั้นแรกเราควรตรวจสอบ Columns ที่ไม่ใช้ด้วย VertiPaq Analyzer เพื่อดูว่า Column ไหนใช้ Space มากแต่ไม่ได้ใช้ประโยชน์ จากนั้นเราสามารถลบ Columns เหล่านี้ออกได้ใน Power Query โดยการ Remove Columns หรือถ้า Column นั้นยังต้องเก็บไว้เพื่อใช้กับ Relationship เราสามารถซ่อนได้ใน Model View โดยการ Hide in Report View

**การวิเคราะห์ Model Size:**

เราสามารถใช้ VertiPaq Analyzer เพื่อตรวจสอบขนาดของแต่ละ Table และ Column ได้ ซึ่งจะช่วยให้เรารู้ว่า Table ไหนหรือ Column ไหนใช้ Space มากที่สุด จากนั้นเราสามารถวางแผนการลด Model Size ได้ เช่น ลบ Columns ที่ไม่ใช้ หรือพิจารณาใช้ Incremental Refresh สำหรับ Large Tables

---

### 4. Relationships Optimization

> **หมายเหตุ:** Best Practices สำหรับ Relationships มีอยู่ใน **09-Best-Practices** โมดูลนี้เน้นที่การ **วิเคราะห์และวัดผล** Performance Impact

#### 4.1 การวิเคราะห์ Relationships Impact

การตั้งค่า Relationships ระหว่าง Tables มีผลต่อ Performance มาก โดยเฉพาะทิศทางของ Relationship (Single Direction vs Both Direction) และประเภทของ Relationship (One-to-Many vs Many-to-Many)

**ใช้ DAX Studio เพื่อวิเคราะห์:**

เราสามารถใช้ DAX Studio เพื่อวิเคราะห์ Query Performance ว่าเมื่อใช้ Single Direction Relationship กับ Both Direction Relationship จะแตกต่างกันอย่างไร โดยจะวัดผล Filter Propagation Time ว่าการส่ง Filter ผ่าน Relationship ใช้เวลานานแค่ไหน และเปรียบเทียบ Performance ระหว่าง Relationship Types ต่างๆ เพื่อดูว่าประเภทไหนให้ Performance ที่ดีที่สุด

**ตัวอย่างการวิเคราะห์:**

สมมติว่าเรามี Relationship ระหว่าง DimProduct กับ FactResellerSales ถ้าเราตั้งเป็น Single Direction Relationship และ Run Query ดูผลลัพธ์ Query Duration จะใช้เวลาประมาณ 150ms แต่ถ้าเราเปลี่ยนเป็น Both Direction Relationship (เพื่อให้ Filter ทำงานทั้งสองทิศทาง) และ Run Query เดิม Query Duration จะเพิ่มขึ้นเป็น 320ms ซึ่งช้ากว่าเดิมเกือบ 2 เท่า นี้แสดงให้เห็นว่าการใช้ Both Direction Relationship มีผลกระทบต่อ Performance มาก ดังนั้นเราควรใช้เท่าที่จำเป็นจริงๆ เท่านั้น

#### 4.2 การวิเคราะห์ Many-to-Many Relationships Impact

Many-to-Many Relationships เป็น Relationship ที่ซับซ้อนและมีผลต่อ Performance มากกว่าปกติ เพราะต้องใช้ Bridge Table หรือ Materialized Relationship ซึ่งจะเพิ่มความซับซ้อนในการ Query

**ใช้ DAX Studio เพื่อวิเคราะห์:**

เราสามารถใช้ DAX Studio เพื่อวิเคราะห์ Query Performance ของ Many-to-Many Relationships เปรียบเทียบกับ Bridge Table Pattern และวัดผล Performance Impact ว่าแตกต่างกันอย่างไร โดยปกติแล้ว Many-to-Many Relationships จะช้ากว่า One-to-Many Relationships เพราะต้องทำการ Join ข้อมูลหลายขั้นตอน

**ตัวอย่างการวิเคราะห์:**

ถ้าเราใช้ Many-to-Many Relationship ระหว่าง DimProduct กับ DimCustomer เพื่อดูว่าลูกค้าซื้อสินค้าอะไรบ้าง Query Duration อาจใช้เวลาประมาณ 500ms แต่ถ้าเราเปลี่ยนมาใช้ Bridge Table Pattern แทน Query Duration อาจลดลงเหลือประมาณ 250ms ซึ่งเร็วกว่าเดิม 2 เท่า นี้แสดงให้เห็นว่าการใช้ Bridge Table Pattern ให้ Performance ที่ดีกว่า Many-to-Many Relationships

#### 4.3 การตรวจสอบ Relationships ที่ไม่ใช้งาน

ใน Power BI เราสามารถสร้าง Inactive Relationships ได้หลายอัน แต่ถ้า Relationships เหล่านี้ไม่ได้ใช้งานจริง ก็จะทำให้ Model ซับซ้อนขึ้นโดยไม่จำเป็น และอาจมีผลกระทบต่อ Performance จากการตรวจสอบ Relationships ที่ไม่ใช้งาน

**ใช้ DAX Studio หรือ Model View เพื่อ:**

เราสามารถใช้ DAX Studio หรือ Model View เพื่อระบุ Inactive Relationships ที่ไม่ได้ใช้งาน และวิเคราะห์ Impact ของการลบ Relationships เหล่านี้ออกจาก Model การลบ Relationships ที่ไม่ใช้จะช่วยให้ Model เรียบง่ายขึ้นและลดความซับซ้อน

---

### 5. DAX Optimization

#### 5.1 ใช้ Measures แทน Calculated Columns

เมื่อเราต้องการคำนวณค่าอะไรสักอย่าง เรามีตัวเลือกอยู่ 2 ทาง คือใช้ Calculated Columns หรือใช้ Measures แต่สำหรับ Performance แล้ว Measures จะดีกว่า Calculated Columns มาก

**ทำไมต้องใช้ Measures แทน Calculated Columns:**

Measures เป็นการคำนวณแบบ Dynamic ซึ่งหมายความว่าจะคำนวณเฉพาะเมื่อมีการใช้งานจริงเท่านั้น เช่น เมื่อผู้ใช้เลือก Filter หรือดู Visual จึงจะคำนวณขึ้นมา ทำให้ไม่ต้องใช้ Memory เก็บค่าที่คำนวณไว้ล่วงหน้า

ในทางกลับกัน Calculated Columns เป็นการคำนวณแบบ Static ซึ่งหมายความว่าจะคำนวณค่าทุกแถวทันทีที่ Import ข้อมูล และเก็บค่าที่คำนวณไว้ใน Model ตลอดเวลา ทำให้ใช้ Memory มากและเพิ่มขนาดของ Model

**ตัวอย่างความแตกต่าง:**

ถ้าเราสร้าง Calculated Column ชื่อ TotalAmount ที่คำนวณ `[Quantity] * [UnitPrice]` Power BI จะคำนวณค่าสำหรับทุกแถวในตารางทันที และเก็บค่าเหล่านี้ไว้ใน Model เช่น ถ้ามี 1 ล้านแถว ก็จะคำนวณ 1 ล้านค่าและเก็บไว้ ซึ่งจะทำให้ Model มีขนาดใหญ่ขึ้นมาก

แต่ถ้าเราใช้ Measure แทน เช่น `Total Sales = SUMX(FactSales, [Quantity] * [UnitPrice])` Measure นี้จะไม่คำนวณล่วงหน้า แต่จะคำนวณเฉพาะเมื่อมีการใช้งานจริงเท่านั้น ซึ่งจะช่วยประหยัด Memory และลดขนาดของ Model

**การวิเคราะห์ Measures vs Calculated Columns Impact:**

เราสามารถใช้ VertiPaq Analyzer เพื่อเปรียบเทียบ Model Size ระหว่างการใช้ Calculated Columns กับ Measures ได้ ซึ่งจะเห็นความแตกต่างชัดเจน

**ตัวอย่างการวิเคราะห์:**

สมมติว่าเราใช้ Calculated Column Approach สำหรับคำนวณ TotalAmount Model จะมีขนาดประมาณ 250 MB และ Query Duration จะใช้เวลาประมาณ 180ms แต่ถ้าเราเปลี่ยนมาใช้ Measure Approach แทน Model จะมีขนาดลดลงเหลือประมาณ 180 MB (เล็กลง 70 MB) และ Query Duration ก็ลดลงเหลือประมาณ 150ms (เร็วขึ้น 30ms) ซึ่งแสดงให้เห็นว่าใช้ Measures ได้ Performance ที่ดีกว่า

#### 5.2 หลีกเลี่ยง DAX ที่ซับซ้อนเกินไป

เมื่อเราเขียน DAX เราควรพยายามเขียนให้เรียบง่ายและอ่านง่าย เพราะ DAX ที่ซับซ้อนเกินไปจะทำให้ Query ทำงานช้าลง และยากต่อการดูแลรักษา

**หลักการ:**
เราควรใช้ DAX ที่เรียบง่ายที่สุดที่สามารถตอบโจทย์ได้ และหลีกเลี่ยง Nested Functions ที่ซับซ้อนเกินไป เพราะการมี Nested Functions หลายชั้นจะทำให้ Formula Engine ต้องประมวลผลซับซ้อนขึ้น

**ตัวอย่างความแตกต่าง:**

ถ้าเราเขียน DAX ที่ซับซ้อนมาก เช่น มี CALCULATE ที่ซ้อน SUMX ที่ซ้อน FILTER ที่ซ้อน RELATEDTABLE และมีหลายชั้น Query จะทำงานช้ามาก เพราะ Formula Engine ต้องประมวลผลหลายขั้นตอน

แต่ถ้าเราเขียน DAX ที่เรียบง่าย เช่น `Simple Measure = SUM(FactSales[SalesAmount])` Query จะทำงานเร็วมาก เพราะ Formula Engine ไม่ต้องประมวลผลซับซ้อน

#### 5.3 ใช้ Variables ใน DAX

Variables ใน DAX ช่วยให้เราสามารถคำนวณค่าที่ใช้ซ้ำได้ครั้งเดียว แล้วเก็บไว้ในตัวแปร ซึ่งจะช่วยเพิ่ม Performance และทำให้โค้ดอ่านง่ายขึ้น

**ทำไมต้องใช้ Variables:**

Variables จะช่วยให้เราคำนวณค่าครั้งเดียวแล้วเก็บไว้ ซึ่งจะเร็วกว่าการคำนวณซ้ำหลายครั้ง และทำให้อ่านโค้ดง่ายขึ้น เพราะเราสามารถตั้งชื่อตัวแปรให้สื่อความหมายได้

**ตัวอย่างการใช้ Variables:**

สมมติว่าเราต้องการคำนวณ Profit ที่เท่ากับ TotalSales ลบ TotalCost ถ้าเราไม่ใช้ Variables เราอาจต้องเขียน SUM(FactSales[SalesAmount]) และ SUM(FactSales[CostAmount]) หลายครั้ง ซึ่งจะทำให้ Query ช้าลง

แต่ถ้าเราใช้ Variables เช่น `VAR TotalSales = SUM(FactSales[SalesAmount])` และ `VAR TotalCost = SUM(FactSales[CostAmount])` แล้วค่อย RETURN `TotalSales - TotalCost` แบบนี้จะทำให้ Power BI คำนวณ TotalSales และ TotalCost แค่ครั้งเดียว แล้วใช้ค่าที่คำนวณแล้วในการหาผลต่าง ซึ่งจะเร็วกว่าการคำนวณซ้ำๆ

---

### 6. Query Performance Optimization

#### 6.1 ใช้ Filter ที่เหมาะสม

เมื่อเราใช้ Filter ใน DAX Query เราควรใช้ Filter ที่ Column ที่มี Low Cardinality เพราะจะทำให้ Query ทำงานเร็วขึ้น

**หลักการ:**
Filter ที่ Column ที่มี Low Cardinality จะทำงานเร็ว เพราะ Dictionary Encoding จะช่วยให้การค้นหาข้อมูลทำได้เร็วขึ้น แต่ Filter ที่ Column ที่มี High Cardinality จะทำงานช้า เพราะต้อง Scan ข้อมูลหลายค่า

**ตัวอย่างความแตกต่าง:**

ถ้าเราใช้ Filter ที่ DimProduct โดย Filter ที่ Category เช่น `FILTER(DimProduct, [Category] = "Electronics")` จะทำงานเร็วมาก เพราะ Category มี Cardinality ต่ำ (เช่น มีแค่ 5-10 หมวดหมู่) และ Dictionary Encoding จะช่วยให้ค้นหาได้เร็ว

แต่ถ้าเราใช้ Filter ที่ FactSales โดย Filter ที่ TransactionID เช่น `FILTER(FactSales, [TransactionID] = 12345)` จะทำงานช้ามาก เพราะ TransactionID มี Cardinality สูงมาก (อาจมีหลายล้านค่า) และต้อง Scan ข้อมูลหลายค่า

#### 6.2 หลีกเลี่ยง ALL() ที่ไม่จำเป็น

ALL() ใน DAX เป็นฟังก์ชันที่ใช้ลบ Filters ทั้งหมด ซึ่งจะทำให้ต้อง Scan ทั้งตาราง จึงทำให้ Query ทำงานช้าลง

**หลักการ:**
เราควรใช้ ALL() เท่าที่จำเป็นจริงๆ เท่านั้น เพราะ ALL() จะทำให้ต้อง Scan ทั้งตารางทั้งหมด ซึ่งจะช้ามากถ้าตารางมีข้อมูลจำนวนมาก และควรพิจารณาใช้ ALLSELECTED() หรือ ALLEXCEPT() แทนถ้าเป็นไปได้

#### 6.3 ใช้ CALCULATE() อย่างถูกต้อง

CALCULATE() เป็นฟังก์ชันที่สำคัญมากใน DAX เพราะใช้สำหรับเปลี่ยน Filter Context แต่ถ้าเราใช้ไม่ถูกต้อง ก็จะทำให้ Query ทำงานช้าลงได้

**หลักการ:**
เราควรใช้ CALCULATE() เมื่อจำเป็นจริงๆ และระบุ Filter อย่างชัดเจน เพื่อให้ Formula Engine ประมวลผลได้อย่างมีประสิทธิภาพ และควรหลีกเลี่ยงการซ้อน CALCULATE() หลายชั้นโดยไม่จำเป็น

---

### 7. Model Size Optimization

> **หมายเหตุ:** เทคนิคพื้นฐานในการลด Model Size มีอยู่ใน **09-Best-Practices** โมดูลนี้เน้นที่การ **วิเคราะห์และวัดผล** Model Size

#### 7.1 การวิเคราะห์ Model Size

การวิเคราะห์ Model Size ช่วยให้เรารู้ว่า Table ไหนหรือ Column ไหนใช้ Space มากที่สุด ซึ่งจะช่วยให้เราวางแผนการลด Model Size ได้อย่างมีประสิทธิภาพ

**ใช้ VertiPaq Analyzer เพื่อ:**

เราสามารถใช้ VertiPaq Analyzer เพื่อตรวจสอบขนาดของแต่ละ Table และระบุ Tables ที่ใช้ Space มาก จากนั้นเราสามารถวางแผนการลด Model Size ได้ เช่น ลบ Columns ที่ไม่ใช้ หรือพิจารณาใช้ Incremental Refresh สำหรับ Large Tables

**ตัวอย่างการวิเคราะห์:**

สมมติว่าเราใช้ VertiPaq Analyzer ตรวจสอบแล้วพบว่า FactResellerSales ใช้ Space 125 MB FactInternetSales ใช้ Space 98 MB และ DimProduct ใช้ Space 2.5 MB 

จากข้อมูลนี้เราสามารถสรุปได้ว่า Fact Tables ใช้ Space มากที่สุด ซึ่งเป็นเรื่องปกติเพราะ Fact Tables มักจะมีข้อมูลจำนวนมาก ถ้า Fact Tables มีขนาดใหญ่มาก เราควรพิจารณาใช้ Incremental Refresh เพื่อลดขนาดและเพิ่มประสิทธิภาพการ Refresh

#### 7.2 การวัดผลหลัง Optimization

หลังจากที่เราทำ Optimization แล้ว เราควรวัดผลอีกครั้งเพื่อดูว่าการ Optimization มีผลต่อ Model Size อย่างไร

**ใช้ VertiPaq Analyzer เพื่อ:**

เราสามารถใช้ VertiPaq Analyzer เพื่อเปรียบเทียบ Model Size ก่อนและหลัง Optimization วัดผลการลด Columns หรือ Rows และติดตาม Model Growth ว่าเพิ่มขึ้นมากแค่ไหนเมื่อเวลาผ่านไป

**ตัวอย่างการวัดผล:**

สมมติว่า Model ของเรามีขนาด 250 MB ก่อน Optimization หลังจากที่เราลบ Columns ที่ไม่ใช้ออกแล้ว Model ลดลงเหลือ 220 MB (ลดลง 30 MB) และหลังจากที่เราตั้งค่า Incremental Refresh แล้ว Model ลดลงเหลือ 180 MB (ลดลงอีก 40 MB)

จากข้อมูลนี้เราสามารถสรุปได้ว่าการ Optimization ช่วยลด Model Size ได้จริง และควรจะติดตาม Model Growth อย่างต่อเนื่องเพื่อให้แน่ใจว่า Model ไม่ใหญ่เกินไป

---

### 8. Large Models Management

#### 8.1 Incremental Refresh

Incremental Refresh เป็นเทคนิคที่ช่วยให้เรา Refresh เฉพาะข้อมูลใหม่เท่านั้น แทนที่จะ Refresh ข้อมูลทั้งหมด ซึ่งจะทำให้การ Refresh เร็วขึ้นมาก

**ทำไมต้องใช้ Incremental Refresh:**

ถ้าเรามี Fact Table ที่มีข้อมูลจำนวนมาก เช่น มีข้อมูลหลายปี และข้อมูลเพิ่มขึ้นทุกวัน การ Refresh ข้อมูลทั้งหมดทุกครั้งจะใช้เวลานานมาก แต่ถ้าเราใช้ Incremental Refresh เราจะ Refresh เฉพาะข้อมูลใหม่ (เช่น ข้อมูลของเดือนล่าสุด) และเก็บข้อมูลเก่าไว้ ซึ่งจะทำให้การ Refresh เร็วขึ้นมาก

**วิธีใช้ Incremental Refresh:**

เราต้องตั้งค่า Incremental Refresh Policy ใน Power BI Desktop โดยกำหนด RangeStart และ RangeEnd เพื่อบอกว่าต้องการเก็บข้อมูลตั้งแต่เมื่อไหร่ถึงเมื่อไหร่ และควรเรียนรู้เพิ่มเติมใน Module 13 สำหรับรายละเอียดเพิ่มเติม

#### 8.2 Aggregation Tables

Aggregation Tables เป็นเทคนิคที่ช่วยให้เรา Pre-aggregate ข้อมูลไว้ล่วงหน้า เพื่อให้ Query ทำงานเร็วขึ้นเมื่อผู้ใช้ต้องการดูข้อมูลแบบสรุป

**ทำไมต้องใช้ Aggregation Tables:**

ถ้าเรามี Fact Table ที่มีข้อมูลจำนวนมาก และผู้ใช้ต้องการดูข้อมูลแบบสรุป เช่น ยอดขายต่อเดือน หรือยอดขายต่อปี การ Query ข้อมูลทั้งหมดเพื่อคำนวณสรุปจะใช้เวลานานมาก แต่ถ้าเราใช้ Aggregation Tables เราจะสร้าง Table ที่สรุปข้อมูลไว้ล่วงหน้าแล้ว (เช่น สรุปยอดขายต่อเดือน) และเมื่อผู้ใช้ Query ข้อมูลสรุป Query จะใช้ข้อมูลจาก Aggregation Table แทน ซึ่งจะเร็วกว่ามาก

**วิธีใช้ Aggregation Tables:**

เราต้องสร้าง Aggregation Table ที่มีข้อมูลสรุปตามที่ต้องการ แล้วใช้ Aggregation กับ Summaries ใน Power BI Desktop เพื่อให้ Query Engine ใช้ Aggregation Table เมื่อเป็นไปได้

---

### 9. การวัดผลและติดตาม Performance

#### 9.1 การวัดผลก่อนและหลัง Optimization

การวัดผลก่อนและหลัง Optimization เป็นสิ่งสำคัญมาก เพราะจะช่วยให้เรารู้ว่าการ Optimization มีผลอย่างไร และควรจะ Optimize ต่อหรือไม่

**ขั้นตอนการวัดผล:**

ขั้นแรกเราต้องวัดผล Baseline (ก่อน Optimization) โดยบันทึกข้อมูลสำคัญ เช่น Model Size, Query Duration, และ Memory Usage จากนั้นเราจึงทำ Optimization เช่น ลด Cardinality, เรียงข้อมูล, หรือลบ Columns ที่ไม่ใช้

หลังจากทำ Optimization แล้ว เราต้องวัดผลอีกครั้ง (หลัง Optimization) โดยใช้วิธีเดียวกับ Baseline เพื่อเปรียบเทียบผลลัพธ์ และบันทึกผลไว้เพื่ออ้างอิงในอนาคต

**ตัวอย่างการวัดผล:**

สมมติว่า Baseline ของเรา Model Size เท่ากับ 250 MB Query Duration เท่ากับ 500ms และ Memory Usage เท่ากับ 1.2 GB

หลังจากทำ Optimization แล้ว Model Size ลดลงเหลือ 180 MB (ลดลง 28%) Query Duration ลดลงเหลือ 250ms (ลดลง 50%) และ Memory Usage ลดลงเหลือ 0.9 GB (ลดลง 25%)

จากข้อมูลนี้เราสามารถสรุปได้ว่าการ Optimization มีผลดีมาก และช่วยเพิ่มประสิทธิภาพได้อย่างชัดเจน

#### 9.2 การติดตาม Performance อย่างต่อเนื่อง

หลังจากที่เราทำ Optimization แล้ว เราควรติดตาม Performance อย่างต่อเนื่องเพื่อให้แน่ใจว่า Performance ยังดีอยู่ และ Model ไม่ใหญ่เกินไปเมื่อเวลาผ่านไป

**สิ่งที่ควรติดตาม:**

เราควรติดตาม Model Size Growth ว่าขยายตัวเพิ่มขึ้นมากแค่ไหนเมื่อเวลาผ่านไป Query Performance Trends ว่า Query ยังทำงานเร็วอยู่หรือไม่ Memory Usage Patterns ว่าใช้ Memory เพิ่มขึ้นหรือไม่ และ User Feedback ว่าผู้ใช้ยังพอใจกับ Performance อยู่หรือไม่

**ใช้ Power BI Service เพื่อ:**

ถ้าเรามี Power BI Premium เราสามารถใช้ Power BI Service เพื่อ Monitor Semantic Model Metrics แบบ Real-time Track Query Performance และ Analyze Usage Patterns เพื่อเข้าใจว่า Report ไหนถูกใช้งานมากที่สุด และ Query ไหนใช้เวลานานที่สุด

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ วิเคราะห์ Performance ของ Semantic Model ได้
- ✅ ใช้เครื่องมือวิเคราะห์ Performance ได้ (Performance Analyzer, DAX Studio, VertiPaq Analyzer)
- ✅ วิเคราะห์และวัดผล VertiPaq Engine Optimization ได้
- ✅ วิเคราะห์และวัดผล Relationships Performance Impact ได้
- ✅ วิเคราะห์และ Optimize DAX Query Performance ได้
- ✅ วิเคราะห์และ Optimize Query Performance ได้
- ✅ วิเคราะห์และวัดผล Model Size Optimization ได้
- ✅ วัดผลและติดตาม Performance อย่างต่อเนื่องได้


---

## 📝 สรุป Performance Optimization

### 🎯 Key Points

1. **เครื่องมือวิเคราะห์ Performance:**
   - Performance Analyzer (Power BI Desktop) - ใช้วิเคราะห์ Visual Performance
   - DAX Studio (Query Performance, Query Plan) - ใช้วิเคราะห์ Query Performance โดยละเอียด
   - VertiPaq Analyzer (Cardinality, Size, Encoding) - ใช้วิเคราะห์โครงสร้างข้อมูลใน VertiPaq Engine

2. **การวิเคราะห์ VertiPaq Engine:**
   - วิเคราะห์ Cardinality ด้วย VertiPaq Analyzer เพื่อระบุ Columns ที่มี High Cardinality
   - วัดผล Sorting Impact ต่อ Compression Ratio เพื่อดูว่าการเรียงข้อมูลช่วยเพิ่มประสิทธิภาพหรือไม่
   - วิเคราะห์ Model Size เพื่อระบุ Tables และ Columns ที่ใช้ Space มาก

3. **การวิเคราะห์ Relationships Performance:**
   - วิเคราะห์ Single vs Both Direction Impact เพื่อดูว่าทิศทางของ Relationship มีผลต่อ Performance อย่างไร
   - วิเคราะห์ Many-to-Many Performance Impact เพื่อเปรียบเทียบกับ Bridge Table Pattern
   - ตรวจสอบ Relationships ที่ไม่ใช้งานเพื่อลดความซับซ้อนของ Model

4. **DAX Query Optimization:**
   - วิเคราะห์ Measures vs Calculated Columns Impact เพื่อเลือกวิธีการคำนวณที่เหมาะสม
   - ใช้ Variables เพื่อเพิ่ม Performance และทำให้โค้ดอ่านง่ายขึ้น
   - หลีกเลี่ยง DAX ที่ซับซ้อนเกินไปเพื่อเพิ่มประสิทธิภาพ

5. **Query Performance Optimization:**
   - ใช้ Filter ที่ Column ที่มี Low Cardinality เพื่อเพิ่มประสิทธิภาพ
   - หลีกเลี่ยง ALL() ที่ไม่จำเป็นเพื่อลดการ Scan ทั้งตาราง
   - ใช้ CALCULATE() อย่างถูกต้องเพื่อเพิ่มประสิทธิภาพ

6. **การวัดผลและติดตาม:**
   - วัดผลก่อนและหลัง Optimization เพื่อดูผลลัพธ์ของการ Optimization
   - ติดตาม Performance อย่างต่อเนื่องเพื่อให้แน่ใจว่า Performance ยังดีอยู่
   - ใช้ Power BI Service Metrics (ถ้ามี Premium) เพื่อ Monitor Performance แบบ Real-time


---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](../README.md) - โครงสร้างหลักสูตร
- **01-Introduction & VertiPaq Engine** - เข้าใจ VertiPaq Engine
- **04-Relationships** - Relationships Fundamentals
- **09-Best-Practices** - Best Practices สำหรับการสร้างและจัดการ Semantic Model (Naming, Organization, Documentation)
- **13-Incremental-Refresh-Partitioning** - Large Models Management
- [Microsoft Learn: Power BI Performance](https://learn.microsoft.com/power-bi/)
- [sqlbi.com: Performance](https://www.sqlbi.com/)

---

## 💡 Tips สำหรับการสอบ

1. **จำ Performance Tools**: Performance Analyzer, DAX Studio, VertiPaq Analyzer - จำชื่อเครื่องมือและหน้าที่ของแต่ละตัว
2. **เข้าใจการวิเคราะห์ Performance**: ใช้เครื่องมือวิเคราะห์เพื่อระบุปัญหาและวัดผลการ Optimization
3. **เข้าใจการวัดผล**: วัดผลก่อนและหลัง Optimization เพื่อดูผลลัพธ์
4. **เข้าใจ Trade-offs**: Measures vs Calculated Columns, Single vs Both Direction - รู้ว่าแต่ละวิธีมีข้อดีข้อเสียอย่างไร
5. **เข้าใจ Best Practices**: Naming, Organization, Documentation มีอยู่ใน 09-Best-Practices
