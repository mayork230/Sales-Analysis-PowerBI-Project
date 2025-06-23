# Sales Data Analysis Power BI

---

![Firefly_Sales Store 923186](https://github.com/user-attachments/assets/405852ed-ac75-45c6-8524-421160949d82)

---

## Table of Contents
- [Introduction](#Introduction)
- [Project Overview](#Project-Overview)
- [Tools and Skills](#Tools-and-Skills)
- [Data Cleaning and Transformation](#Data-Cleaning-and-Transformation)
- [Data Exploration and Insights](#Data-Exploration-and-Insights)
- [Recommendations](#Recommendations)
- [Conclusion](#Conclusion)

---

## Introduction
Every sales transaction is more than a line item it’s a clue about what customers value, how supply chains respond, and where tomorrow’s growth might lie. By modeling five years of U.S. orders (2021-2025, 9 988 rows × 20 columns) in Power BI and layering in a dedicated calendar table, explicit DAX measures, and drill-through paths we turned a raw CSV into an interactive narrative that business leaders can act on in minutes.

---

## Project Overview
|**Aspect**|**Details**|
|--------------------|---------------------------|
| **Business Goal** | Reveal where revenue, profit, and order volume are growing or shrinking so managers can target high-value segments, fix unprofitable pockets, and forecast demand. |
| **Time Frame** | FY 2021 – FY 2025 _(note: no orders yet in 2025)_ |
| **Grain** | One record per product line on an order _(“wide” Super-Store style)_ |
| **Key Dimensions**|	Date ➡ Year/Month/Day, Ship Mode, Customer Segment, Region > State > City, Category > Sub-category|
| **Key Facts** |Sales, Quantity, Discount, Profit|

---
## Tools and Skills

- Power BI Desktop – data modeling, visuals, drill-through, bookmarks
- DAX – explicit measures, time-intelligence (SAMEPERIODLASTYEAR,DATEADD YoY %)
- Power Query (M) – data typing, null handling, calendar table generation
- Storytelling – layering insights from national to SKU level

---

## Data Cleaning & Transformation
1. Loaded the main fact table (20 columns) → renamed fields, enforced types.
2. Created a Calendar table with Date, Year, Month, Month Name, Weekday, linked 1-* to Calender Date and :many to Ship  on the main table.
3. Removed noise:
   - Dropped blank state rows (< 0.05 %).
   - Converted discounts to decimal (0 – 1).
     
---

### Dax Measures
Core metrics
```markdown
Total Sales      = SUM ( 'Sales'[Sales] )
Total Profit     = SUM ( 'Sales'[Profit] )
Total Orders     = DISTINCTCOUNT ( 'Sales'[Order ID] )
Profit Margin %  = DIVIDE ( [Total Profit], [Total Sales], 0 )

-- Time intelligence
PY Sales         = CALCULATE ( [Total Sales], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )
PY Profit        = CALCULATE ( [Total Profit], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )
PY Orders        = CALCULATE ( [Total Orders], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )

YoY Sales %      = DIVIDE ( [Total Sales] - [PY Sales], [PY Sales], 0 )
YoY Profit %     = DIVIDE ( [Total Profit] - [PY Profit], [PY Profit], 0 )
YoY Orders %     = DIVIDE ( [Total Orders] - [PY Orders], [PY Orders], 0 )

Indicator Color Sales = 
var current_sales = [Total Sales]
var previous_year = if(not ISBLANK(SELECTEDVALUE(Calender[Year])),SELECTEDVALUE(Calender[Year])-1,BLANK())
var previous_year_value = if(NOT ISBLANK(previous_year),CALCULATE([Total Sales],YEAR('Calender'[Date])=previous_year),BLANK())

var previous_sales = [Previous Year Sales]
var selectedYear = SELECTEDVALUE(Calender[Year])
var diff = current_sales-[Previous Year Sales]
var yoychange = if(and(not ISBLANK(current_sales),not ISBLANK(previous_year_value)), DIVIDE(current_sales-previous_year_value,previous_year_value),BLANK())
RETURN IF(current_sales-[Previous Year Sales]<0,"Red","Blue")

```

---

## Data Exploration and Insights
Across the full period, the business generated $2.30 M in revenue, $286 k in profit, and 9 998 orders, averaging a 12.46 % margin. Yet the trajectory was anything but flat.

In 2021 we opened the narrative with $484 k in sales and a modest 10.2 % margin. Customers were still discovering the brand, and the bulk of shipments moved by economical Standard Class.

2022 looked like bad news at first glance—revenue slipped 2.9 % to $470 k—but a deeper read shows a healthy pivot: order volume grew 5.4 % and margin jumped to 13.1 %. The culprit behind the topline dip was conservative corporate procurement during a brief inflation scare, yet the margin lift proved we could protect profitability by tightening discounts.

The plot twist arrived in 2023. Demand roared back: sales soared 29.5 % to $609 k and orders leapt past 2 500. We saw two forces at play: (1) a phone-upgrade cycle in Technology that pushed big-ticket items, and (2) an uptick in Same-Day shipping, satisfying impatient consumers but nudging freight costs upward.

2024 extended the rally to $733 k (+20.3 %), but the margin eased down to 12.7 %. Investigation showed that Same-Day volume tripled since 2022 and heavy couponing on Accessories trimmed the profit slice. Essentially, we bought growth with speed and discounts.

2025 begins with a blank slate—no orders yet—setting the stage for a margin-focused campaign.



