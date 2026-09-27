# Code Examples - Data Modeling Basics

## 📋 ไฟล์รวบรวม Code Examples สำหรับโมดูล Data Modeling Basics

ไฟล์นี้รวบรวมตัวอย่างโครงสร้าง Data Model และแนวทางการออกแบบ

> **หมายเหตุ**: โมดูลนี้เน้นที่การออกแบบโครงสร้าง ไม่เน้น DAX Code

---

## 1. Star Schema Structure

### ตัวอย่างโครงสร้างจาก AdventureWorksDW2025

**Fact Table: FactResellerSales**

```
FactResellerSales
├── Measures:
│   ├── SalesAmount
│   ├── OrderQuantity
│   └── UnitPrice
│
└── Dimension Keys:
    ├── ProductKey → DimProduct
    ├── OrderDateKey → DimDate
    ├── ResellerKey → DimReseller
    ├── GeographyKey → DimGeography
    ├── EmployeeKey → DimEmployee
    └── PromotionKey → DimPromotion
```

**Dimension Tables:**

```
DimProduct
├── ProductKey (Primary Key)
├── ProductName
├── ProductCategoryName
├── ProductSubcategoryName
└── ProductColor

DimDate
├── DateKey (Primary Key)
├── CalendarYear
├── CalendarQuarter
├── CalendarMonth
└── FullDateAlternateKey

DimReseller
├── ResellerKey (Primary Key)
├── ResellerName
├── BusinessType
└── ResellerType

DimGeography
├── GeographyKey (Primary Key)
├── City
├── StateProvinceName
├── CountryRegionName
└── Region

DimEmployee
├── EmployeeKey (Primary Key)
├── FirstName
├── LastName
├── Title
└── DepartmentName

DimPromotion
├── PromotionKey (Primary Key)
├── PromotionName
├── DiscountPct
└── PromotionType
```

---

## 2. Snowflake Schema (ไม่แนะนำ)

### ตัวอย่างโครงสร้าง

```
FactSales
│
├── DimProduct
│   └── DimProductCategory (เชื่อมต่อกัน)
│       └── DimProductSubcategory
│
└── DimGeography
    └── DimState
        └── DimCountry
```

**ปัญหาบน Power BI:**
- ไม่สามารถสร้าง Hierarchy โดย Hierarchy Members มาจากหลายตารางได้

**แนวทางแก้ไข:**
1. ลดรูปจาก Snowflake ไปเป็น Star Schema โดยใช้ Power Query
2. ใช้ DAX สร้าง Calculated Column

---

## 3. Conformed Dimensions

### ตัวอย่าง Date Dimension ที่ใช้ร่วมกัน

**DimDate (Conformed Dimension)**

เชื่อมโยงกับหลาย Fact Tables:

```
DimDate
│
├── FactResellerSales
│   └── OrderDateKey → DimDate[DateKey]
│
├── FactInternetSales
│   └── OrderDateKey → DimDate[DateKey]
│
└── FactSalesQuota
    └── MonthKey → DimDate[MonthKey]
```

**ประโยชน์:**
- ✅ ความสอดคล้องของข้อมูล
- ✅ ลดความซ้ำซ้อน
- ✅ ง่ายต่อการบำรุงรักษา

---

## 4. Bus Matrix

### ตัวอย่าง Bus Matrix

**ตารางแสดงความสัมพันธ์ระหว่าง Fact Tables และ Dimensions:**

| Fact Tables | Date | Product | Customer | Geography | Employee |
|-------------|------|---------|----------|-----------|----------|
| FactResellerSales | ✅ | ✅ | ❌ | ✅ | ✅ |
| FactInternetSales | ✅ | ✅ | ✅ | ✅ | ❌ |
| FactSalesQuota | ✅ | ❌ | ❌ | ✅ | ✅ |

**ประโยชน์:**
- ช่วยในการออกแบบและสื่อสารโครงสร้าง
- สนับสนุนการใช้ Conformed Dimensions
- ช่วยในการวางแผนโครงการ

---

## 5. แยกแยะ Measures และ Dimension Attributes

### ตัวอย่างคำถาม: "ยอดขาย รายไตรมาส"

**การวิเคราะห์:**
- **Measure**: ยอดขาย (Sales Amount)
- **Dimension Attribute**: รายไตรมาส (Calendar Quarter)

**โครงสร้าง:**
```
FactSales[SalesAmount] (Measure)
DimDate[CalendarQuarter] (Dimension Attribute)
```

### ตัวอย่างคำถาม: "จำนวนสินค้าคงคลัง แยกตามคลังสินค้า"

**การวิเคราะห์:**
- **Measure**: จำนวนสินค้าคงคลัง (Inventory Quantity)
- **Dimension Attribute**: รายการคลังสินค้า (Warehouse Name)

**โครงสร้าง:**
```
FactInventory[InventoryQuantity] (Measure)
DimWarehouse[WarehouseName] (Dimension Attribute)
```

---

## 📝 สรุป

### Best Practices

1. **ใช้ Star Schema** แทน Snowflake Schema
2. **ใช้ Conformed Dimensions** สำหรับ Dimensions ที่ใช้ร่วมกัน
3. **แยก Measures และ Dimension Attributes** ให้ชัดเจน
4. **ใช้ Bus Matrix** เพื่อวางแผนโครงสร้าง Model

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

