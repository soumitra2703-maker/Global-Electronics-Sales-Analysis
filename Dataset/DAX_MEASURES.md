# 🧮 DAX Measures Reference  
**Global Electronics Retail — Power BI**

This document lists **all DAX measures used in the model**, exactly as defined.  
Measures are grouped only for readability; formulas are unchanged.

---

## 1. Core Financial Measures

### 1. Total Sales
```DAX
Total Sales :=
SUMX(
    FactSales,
    FactSales[Quantity] * RELATED(Products[Unit Price USD])
)

