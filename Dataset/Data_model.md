# 📐 Data Model Architecture — Global Electronics Retail (Power BI)

## 1. Overview

This document provides a **deep technical explanation** of the analytical data model used in the *Global Electronics Retail* Power BI solution.

The model follows a **Star Schema design**, optimized for:
- High-performance DAX evaluation
- Clear semantic layer for business users
- Scalable time intelligence
- Cross-domain analysis (Customer × Product × Store × Geography)

---

## 2. Model Design Pattern

**Schema Type:** Star Schema  
**Grain:** One row per *Order Line Item*  
**Fact Table:** FactSales  
**Dimensions:** Customers, Products, Stores, DimDate, Exchange_Rates

All dimensions are **conformed**, enabling consistent slicing across reports.

---

## 3. Fact Table (Central Transaction Table)

### FactSales

**Grain Definition:**  
> Each record represents a single product line item within an order.

| Column | Purpose |
|------|--------|
| Order Number | Business order identifier |
| Line Item | Line number within the order |
| Order Item Key | Composite key (Order Number + Line Item) |
| Order Date | Primary transaction date |
| Delivery Date | Fulfillment date |
| CustomerKey | FK → Customers |
| ProductKey | FK → Products |
| StoreKey | FK → Stores |
| CurrencyKey | FK → Exchange_Rates |
| Quantity | Units sold |
| Age at Order Date | Calculated column |
| Age Band | Derived segmentation |

### Why Line-Item Grain?
- Supports **basket analysis**
- Enables **AOV calculation**
- Preserves flexibility for product-level analytics

---

## 4. Dimension Tables

### Customers (Slowly Changing Dimension – Type 1)

| Attribute | Description |
|---------|------------|
| CustomerKey (PK) | Surrogate key |
| Name | Customer full name |
| Gender | Demographic attribute |
| City / State / Country / Continent | Geography hierarchy |
| Birthday | Used for age calculation |

**Usage Highlights**
- Customer segmentation
- Age-based analysis at transaction time
- Geographic rollups

---

### Products

| Attribute | Description |
|---------|------------|
| ProductKey (PK) | Surrogate key |
| Brand | Manufacturer / brand |
| Category | High-level grouping |
| Subcategory | Detailed grouping |

**Usage Highlights**
- Category mix analysis
- Brand growth vs profitability
- Subcategory deep dives

---

### Stores

| Attribute | Description |
|---------|------------|
| StoreKey (PK) | Store identifier |
| Country / State | Location |
| Store_Type | Online / Offline |
| Square Meters | Physical size |
| Open Date | Store launch date |

**Usage Highlights**
- Store productivity metrics
- Like-for-like analysis
- Sales & profit per SqM

---

### DimDate (Role-Playing Dimension)

| Attribute | Description |
|---------|------------|
| Date (PK) | Calendar date |
| Year / Quarter / Month | Time hierarchy |
| Day Name | Weekday |
| Week Number | ISO week |
| Is Weekend | Boolean flag |

**Roles Used**
- Order Date (Active)
- Delivery Date (Inactive via `USERELATIONSHIP`)

---

### Exchange_Rates

| Attribute | Description |
|---------|------------|
| CurrencyKey (PK) | Date + Currency |
| Currency Code | ISO currency |
| Exchange Rate | Conversion factor |

**Purpose**
- Multi-currency normalization
- Consistent financial reporting across geographies

---

## 5. Relationships

| From | To | Cardinality | Active |
|----|----|-------------|--------|
| Customers → FactSales | 1 → * | Yes |
| Products → FactSales | 1 → * | Yes |
| Stores → FactSales | 1 → * | Yes |
| DimDate → FactSales (Order Date) | 1 → * | Yes |
| DimDate → FactSales (Delivery Date) | 1 → * | No |
| Exchange_Rates → FactSales | 1 → * | Yes |

**Design Decisions**
- Single-direction filtering only
- No snowflaking
- No many-to-many relationships

---

## 6. Calculated Columns vs Measures

| Type | Usage |
|----|------|
| Calculated Columns | Row-level attributes (Age, Age Band) |
| Measures | Aggregations, KPIs, time intelligence |

**Rationale**
- Keeps model lightweight
- Improves query performance
- Avoids filter context ambiguity

---

## 7. Performance Considerations

- Minimal columns in Fact table
- Surrogate integer keys
- Avoided bi-directional relationships
- Measures preferred over calculated columns
- Date table marked as Date Table

---

## 8. Model Extensibility

Future-ready for:
- Incremental refresh
- Customer Lifetime Value (CLV)
- Cohort analysis
- Forecasting & scenario modeling

---

## 9. Summary

This data model is designed to balance:
- **Technical robustness**
- **Business interpretability**
- **Analytical scalability**

It forms a strong foundation for executive-grade decision intelligence.

