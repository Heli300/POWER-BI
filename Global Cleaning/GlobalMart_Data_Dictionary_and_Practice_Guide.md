# GlobalMart Sales & Business Performance Dataset
### Power BI Classroom Practice Project — Data Dictionary & Task Guide

- **File:** `GlobalMart_Sales_Dataset.csv`
- **Rows:** 8,610 transaction (order-line) records
- **Columns:** 51
- **Date Range:** January 1, 2024 – December 31, 2026
- **Countries:** India, USA, UK, Canada, Australia
- **Grain:** One row = one product line within one customer order

This single flat table is intentionally denormalized so that every major Power BI visual, DAX pattern, and Power Query cleaning technique can be practiced from one CSV — no joins required, though optional star-schema tables are suggested at the end for students who want to practice modeling.

---

## 1. Data Dictionary

### 1.1 Transaction Information

| Column | Data Type | Description |
|---|---|---|
| Transaction_ID | Text | Unique ID for each order line (primary key of the table) |
| Order_ID | Text | Order-level ID; repeats when an order has multiple product lines. A small number of fully duplicated rows exist intentionally for de-duplication practice |
| Order_Date | Date | Date the order was placed |
| Ship_Date | Date | Date the order shipped (some blanks intentionally present) |
| Delivery_Date | Date | Date the order was delivered |
| Sales_Channel | Text | Online, In-Store, Mobile App, Distributor |
| Order_Status | Text | Delivered, Shipped, Processing, Cancelled, Returned |
| Payment_Mode | Text | Credit Card, Debit Card, Net Banking, UPI, Cash on Delivery, PayPal — **contains inconsistent casing** ("Credit Card" / "Credit card" / "credit card" / "CREDIT CARD") for cleaning practice |

### 1.2 Customer Information

| Column | Data Type | Description |
|---|---|---|
| Customer_ID | Text | Unique customer identifier (customers repeat across orders — supports repeat-customer analysis) |
| Customer_Name | Text | Fictional customer name |
| Customer_Age | Whole Number | 18–70 |
| Gender | Text | Male / Female |
| Customer_Segment | Text | Consumer, Corporate, Home Office, Small Business (contains a few stray leading/trailing spaces) |
| Customer_Rating | Decimal | 1–5 star rating; some values intentionally missing |

### 1.3 Geographic Information

| Column | Data Type | Description |
|---|---|---|
| Country | Text | India, USA, UK, Canada, Australia |
| State | Text | State/province within country |
| City | Text | City (contains a few inconsistent alternate spellings, e.g. Bangalore/Bengaluru, Mumbai/Bombay) |
| Region | Text | Sales region within country (a few blanks intentionally present) |
| Postal_Code | Text | Postal/ZIP code (some missing values) |
| Latitude | Decimal | For map visuals |
| Longitude | Decimal | For map visuals |

### 1.4 Product Information

| Column | Data Type | Description |
|---|---|---|
| Product_ID | Text | Unique product identifier |
| Product_Name | Text | Product name |
| Category | Text | 7 categories (a few rows have inconsistent casing for cleaning practice) |
| Sub_Category | Text | 5–6 sub-categories per category |
| Brand | Text | Fictional brand name |
| Product_Type | Text | Standard, Premium, Economy, Limited Edition |
| Unit_Cost | Decimal (currency) | Cost per unit |
| Unit_Price | Decimal (currency) | Selling price per unit |

### 1.5 Sales & Financial Information

| Column | Data Type | Formula / Logic |
|---|---|---|
| Quantity | Whole Number | 1–20 (a few high outliers 30–60 intentionally injected) |
| Sales | Decimal (currency) | Quantity × Unit_Price |
| Discount_Percentage | Decimal (%) | 0–40% |
| Discount_Amount | Decimal (currency) | Sales × Discount_Percentage |
| Cost | Decimal (currency) | Quantity × Unit_Cost |
| Profit | Decimal (currency) | Sales − Discount_Amount − Cost (can be negative) |
| Profit_Margin | Decimal (%) | Profit ÷ Sales |
| Tax | Decimal (currency) | 8% of (Sales − Discount_Amount) |
| Shipping_Cost | Decimal (currency) | Random cost between $2–$25 |
| Total_Amount | Decimal (currency) | Sales − Discount_Amount + Tax + Shipping_Cost |

### 1.6 Time Intelligence Columns

| Column | Data Type | Description |
|---|---|---|
| Year | Whole Number | 2024, 2025, 2026 |
| Quarter | Text | Q1–Q4 |
| Month | Text | e.g. "Jan-2024" (sortable label) |
| Month_Number | Whole Number | 1–12 |
| Month_Name | Text | January–December |
| Week_Number | Whole Number | ISO week number |
| Day | Whole Number | Day of month |
| Day_Name | Text | Monday–Sunday |

### 1.7 Target / Goal Fields

| Column | Data Type | Description |
|---|---|---|
| Sales_Target | Decimal (currency) | Row-level daily-equivalent sales target (varies by Category, Region, Month) |
| Profit_Target | Decimal (currency) | Row-level profit target |
| Monthly_Target | Decimal (currency) | Aggregate target for that Category/Region/Month combination |
| Target_Achievement_Percentage | Decimal (%) | Sales ÷ Sales_Target × 100 |

---

## 2. Known Data-Quality Issues (for Power Query practice)

| Issue | Where | Approx. Volume |
|---|---|---|
| Inconsistent capitalization | Payment_Mode, Category, Sub_Category, Brand | ~2% of rows |
| Leading/trailing spaces | City, Sales_Channel, Customer_Segment | ~1% per column |
| Alternate city names | City (e.g. Bangalore/Bengaluru, Mumbai/Bombay, New York City/NYC, Chennai/Madras) | ~10% within affected cities |
| Missing values | Customer_Rating, Postal_Code, Ship_Date, Region | 1–2% per column |
| Duplicate rows | Full-row duplicates (same Transaction_ID repeated) | ~1% of rows |
| Outliers | Quantity (30–60 units), Sales (3–6x normal), Profit (large negative) | <1% of rows |

Recommended Power Query steps: `Trim`, `Clean`, `Text.Proper`/`Text.Upper` standardization, replace-value mapping for city aliases, remove duplicates, and flag/handle outliers with conditional columns.

---

## 3. Suggested Visual per Field/Analysis

| Field(s) | Suggested Visual(s) |
|---|---|
| Category / Sub_Category × Sales | Clustered/Stacked Column, Treemap, Pie/Donut |
| Sales_Channel × Sales (share of total) | 100% Stacked Bar/Column |
| Order_Date (trend) × Sales | Line Chart, Area Chart |
| Order_Date × Sales & Profit | Line and Clustered/Stacked Column Chart |
| Sales by Order_Status (cumulative flow) | Waterfall Chart |
| Order funnel (Processing → Shipped → Delivered) | Funnel Chart |
| Sales vs Profit vs Quantity | Scatter Chart (bubble size = Quantity) |
| Sales vs Discount_Percentage | Scatter Chart |
| Sales distribution | Histogram (via numeric binning) |
| Profit distribution by Category | Box Plot (custom visual) |
| Total Sales / Profit / Orders | Card, Multi-row Card |
| Sales vs Sales_Target | KPI Visual, Gauge |
| Country / State / City + Sales | Map, Filled Map |
| Year → Quarter → Month → Day | Drill-down Line/Column Chart |
| Category → Sub_Category → Product_Name | Drill-down Bar Chart, Decomposition Tree |
| Country → State → City | Drill-down Map |
| Category × Region (Sales, Profit, Qty, Margin) | Matrix |
| Product_Name × all metrics | Table |
| Sales → Region → Category → Sub_Category → Brand | Decomposition Tree |
| Segment / Gender / Region / Discount / Rating → Profit or Sales | Key Influencers |
| Free-form questions on Sales, Profit, Customers | Q&A Visual |

---

## 4. Suggested DAX Measures

Create these as new measures (not pre-calculated columns) so students practice DAX:

```
Total Sales = SUM(Sales[Sales])
Total Cost = SUM(Sales[Cost])
Total Profit = SUM(Sales[Profit])
Total Quantity = SUM(Sales[Quantity])
Total Orders = DISTINCTCOUNT(Sales[Order_ID])
Total Customers = DISTINCTCOUNT(Sales[Customer_ID])
Average Sales = AVERAGE(Sales[Sales])
Average Order Value = DIVIDE([Total Sales], [Total Orders])
Average Profit = AVERAGE(Sales[Profit])
Profit Margin % = DIVIDE([Total Profit], [Total Sales])
Sales Target Total = SUM(Sales[Sales_Target])
Sales Target Achievement % = DIVIDE([Total Sales], [Sales Target Total])

Sales PY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))
Sales Growth % = DIVIDE([Total Sales] - [Sales PY], [Sales PY])
Profit PY = CALCULATE([Total Profit], SAMEPERIODLASTYEAR('Date'[Date]))
Profit Growth % = DIVIDE([Total Profit] - [Profit PY], [Profit PY])

Top Product = TOPN(1, VALUES(Sales[Product_Name]), [Total Sales])   -- use with a Table/Rank visual
Repeat Customers =
VAR CustOrderCounts = SUMMARIZE(Sales, Sales[Customer_ID], "Orders", DISTINCTCOUNT(Sales[Order_ID]))
RETURN COUNTROWS(FILTER(CustOrderCounts, [Orders] > 1))

Rank by Sales = RANKX(ALL(Sales[Product_Name]), [Total Sales])
```

A `Date` table (marked as a date table, related on `Order_Date`) is recommended for `SAMEPERIODLASTYEAR`, `DATEADD`, and other time-intelligence functions to work correctly.

---

## 5. Suggested Table Structure (Star Schema)

The dataset works perfectly as a single flat table for most visuals. For students who also want to practice data modeling, it can be split into:

| Table | Key Columns | Relationship |
|---|---|---|
| **Fact_Sales** | Transaction_ID, Order_ID, Order_Date, Customer_ID, Product_ID, Quantity, Sales, Discount_Amount, Cost, Profit, Tax, Shipping_Cost, Total_Amount, Sales_Target | Central fact table |
| **Dim_Customer** | Customer_ID, Customer_Name, Age, Gender, Segment | 1-to-many → Fact_Sales[Customer_ID] |
| **Dim_Product** | Product_ID, Product_Name, Category, Sub_Category, Brand, Product_Type, Unit_Cost, Unit_Price | 1-to-many → Fact_Sales[Product_ID] |
| **Dim_Geography** | Country, State, City, Region, Postal_Code, Latitude, Longitude | 1-to-many → Fact_Sales (via City or a generated Geo_ID) |
| **Dim_Date** | Date, Year, Quarter, Month, Month_Name, Week_Number, Day, Day_Name | 1-to-many → Fact_Sales[Order_Date] |

---

## 6. Practice Questions / Tasks (35 Tasks)

### Basic Charts (1–11)
1. Build a **Clustered Column Chart** of Total Sales by Category.
2. Build a **Stacked Column Chart** of Total Sales by Category, stacked by Sales_Channel.
3. Convert the chart above into a **100% Stacked Column Chart** to show channel share per category.
4. Build a **Clustered Bar Chart** of Total Profit by Region.
5. Build a **Stacked Bar Chart** of Quantity by Sub_Category, stacked by Customer_Segment.
6. Convert it into a **100% Stacked Bar Chart**.
7. Build a **Line Chart** of Total Sales by Month across all three years.
8. Build an **Area Chart** of Total Profit by Month.
9. Build a **Stacked Area Chart** of Sales by Month, stacked by Category.
10. Combine Total Sales (columns) and Profit_Margin % (line) in a **Line and Clustered Column Chart**.
11. Combine Sales_Channel Sales (stacked columns) with Order count (line) in a **Line and Stacked Column Chart**.

### Part-to-Whole Charts (12–16)
12. Build a **Pie Chart** of Sales share by Sales_Channel.
13. Build a **Donut Chart** of Order count share by Order_Status.
14. Build a **Treemap** of Sales by Category → Sub_Category.
15. Build a **Funnel Chart** of Order_Status stages (Processing → Shipped → Delivered).
16. Build a **Waterfall Chart** showing month-over-month change in Total Profit.

### Relationship & Distribution Charts (17–20)
17. Build a **Scatter Chart** of Sales (x) vs Profit (y), colored by Category.
18. Add Quantity as bubble size to create a **Bubble-style Scatter** of Sales vs Discount_Percentage.
19. Build a **Histogram** of Sales values (bin the Sales field) to see the transaction-value distribution.
20. Using a **Box Plot** custom visual, compare Profit distribution across Categories.
21. Build a scatter of Customer_Age vs Sales and a separate one of Customer_Rating vs Sales.

### KPI & Summary Visuals (22–25)
22. Add **Cards** for Total Sales, Total Profit, Total Orders, Total Customers.
23. Build a **Multi-row Card** showing Average Order Value, Profit Margin %, and Sales Growth % together.
24. Build a **KPI Visual** comparing monthly Sales against Sales_Target.
25. Build a **Gauge** showing Target_Achievement_Percentage against a 100% goal.

### Geographic Visuals (26–27)
26. Build a **Map** visual (bubble size = Sales) using Latitude/Longitude.
27. Build a **Filled Map** of Total Sales by Country and, separately, by State.

### Hierarchical & Table/Matrix (28–32)
28. Build a **Drill-down Column Chart** on the Date hierarchy: Year → Quarter → Month → Day.
29. Build a **Drill-down Bar Chart** on the Product hierarchy: Category → Sub_Category → Product_Name.
30. Build a **Drill-down Map** on Country → State → City.
31. Build a **Table** listing Product_Name, Category, Sales, Profit, Profit_Margin, sorted by Sales descending.
32. Build a **Matrix** of Category (rows) × Region (columns) showing Sales, Profit, Quantity, and Profit Margin.

### Advanced Features (33–46)
33. Add a **Category slicer** (dropdown) and a **Date slicer** (between) to the report page.
34. Add a **Relative Date Slicer** to show "Last 3 months" of activity.
35. Add a **Numeric Range Slicer** on Discount_Percentage.
36. Set up a **Drillthrough** page: right-click a Product to jump to a Product Detail page filtered to that item.
37. Add a **Tooltip page** that shows Profit and Quantity when hovering over a Sales bar.
38. Apply **Conditional Formatting** (color scale) to Profit Margin in the Matrix from Task 32.
39. Build a **Top N** filter to show the Top 10 Products by Sales.
40. Add a **Ranking** visual/measure (RANKX) for Customers by Total Sales.
41. Turn the Category column chart into **Small Multiples** split by Region.
42. Create two **Bookmarks** (e.g., "Sales View" and "Profit View") and add **Buttons** to toggle between them.
43. Build a **Decomposition Tree** starting at Sales, exploring Region → Category → Sub_Category → Brand.
44. Run **Key Influencers** on Profit, testing Customer_Segment, Gender, Region, Category, Discount_Percentage, Customer_Rating.
45. Add a **Q&A Visual** and test natural-language questions like "total sales by category" or "average profit margin by region".
46. As a capstone, combine at least 6 of the visuals above into a single **Executive Sales & Profitability Dashboard** page with slicers for Year, Category, and Region.

---



