<img width="1035" alt="Screenshot 2025-04-26 at 9 23 20 PM" src="https://github.com/user-attachments/assets/710ccc36-ff09-4640-a042-189f2dd9dd03" />
# 🍫 Chocolate Sales Analysis Dashboard (Power BI)

Hi! I'm Surat Adhikari – a Data Science student with a strong interest in using data to solve real business problems. This Power BI project is a part of my learning journey, where I explore real-world sales data to create an insightful, interactive dashboard. It tracks key performance indicators like total sales, profit %, cost trends, and sales rep efficiency – all with a clean, executive-style layout.

This project helped me deepen my understanding of DAX, Power BI time intelligence, and how small visual design decisions can make dashboards more impactful. I’m sharing this project here not just to showcase what I’ve built, but to connect with those who believe in continuous learning and the power of data storytelling.

---

## 🎯 Project Goals

- Analyze monthly sales, costs, and profits for chocolate product shipments  
- Measure performance against profit targets and monthly benchmarks  
- Track shipment trends and identify underperforming segments  
- Build a dashboard that tells a story and guides decision-making  

---

## 📊 Key Dashboard Features

![Chocolate Sales Dashboard](https://github.com/surajadkhari/Chocolate_sales_analysis_PowerBI/blob/main/assets/chocolate_dashboard.png)

- ✅ Total KPIs: Sales ($34.0M), Profit ($20.5M), Cost ($13.52M), Boxes (2M), Shipments (6K)  
- 📉 MoM performance tracking with color-coded percentage changes  
- 📈 Profit % trend by month (line chart)  
- 🧠 Conditional icons to indicate profit target performance (✔, ⚠)  
- 📦 Shipment analysis: LBS% and small package volumes  
- 👥 Salesperson performance: Profit %, Total Sales, and shipment count  

---

## 📆 Custom Date Table

To support time-based metrics like MoM% and YTD, I built a dynamic Date Table using DAX:

```DAX
DateTable = 
ADDCOLUMNS(
    CALENDAR(MIN('shipment'[Date]), MAX('shipment'[Date])),
    "Year", YEAR([Date]),
    "Month", MONTH([Date]),
    "Month Name", FORMAT([Date], "MMMM"),
    "Quarter", QUARTER([Date]),
    "Day", DAY([Date]),
    "Weekday", WEEKDAY([Date]),
    "Weekday Name", FORMAT([Date], "dddd"),
    "Year-Month", FORMAT([Date], "YYYY-MM"),
    "MM-Year", FORMAT([Date], "MMM-YYYY"),
    "Start of Month", DATE(YEAR([Date]), MONTH([Date]), 1)
)
```

## 🧮 Key DAX Measures
Here are some of the measures I created using DAX:
```DAX
Total Sales = SUM(shipment[Sales])
TotalCost = SUM(shipment[Cost])
Total Profit = [Total Sales] - [TotalCost]
Total Boxes = SUM(shipment[Boxes])
Total Shipment = COUNTROWS(shipment)

Profit % = DIVIDE([Total Profit], [Total Sales], 0)
Profit Traget = 0.6
Profit Target Indicator = 
    IF([Profit %] > [Profit Traget], 2,
        IF([Profit %] > 0.9 * [Profit Traget], 1, 0)
    )

LastestDate = LASTDATE('DateTable'[Start of Month])
Total Sales Lates Month = 
    VAR a = [LastestDate]
    RETURN CALCULATE([Total Sales], DateTable[Start of Month] = a)

TotalSalesPM = CALCULATE([Total Sales], PREVIOUSMONTH(DateTable[Date]))
LastMonthProfit = CALCULATE([Total Profit], PREVIOUSMONTH(DateTable[Date]))

MoM Sales Change % = 
    VAR thisMonthSales = [Total Sales]
    VAR lastMonthSales = [TotalSalesPM]
    RETURN DIVIDE(thisMonthSales - lastMonthSales, lastMonthSales)

MoM Profit Change % = 
    DIVIDE([Total Profit] - [LastMonthProfit], [LastMonthProfit], 0)

MoM Box Changes % = 
    VAR thisMonthBox = [Total Boxes]
    VAR lastMonthBox = CALCULATE([Total Boxes], PREVIOUSMONTH(DateTable[Date]))
    RETURN DIVIDE(thisMonthBox - lastMonthBox, lastMonthBox, 0)

MoM Shipment Changes % = 
    VAR thisMonth = [Total Shipment]
    VAR lastMonth = CALCULATE([Total Shipment], PREVIOUSMONTH(DateTable[Date]))
    RETURN DIVIDE(thisMonth - lastMonth, lastMonth, 0)

Latest Month Sales Changes % = 
    VAR ld = [LastestDate]
    VAR thisMonthSales = [Total Sales Lates Month]
    VAR previousMonthSales = CALCULATE([Total Sales], DateTable[Start of Month] = EDATE(ld, -1))
    RETURN DIVIDE(thisMonthSales - previousMonthSales, previousMonthSales)

LBS Count = CALCULATE([Total Shipment], shipment[Boxes] < 50)
LBS % = DIVIDE([LBS Count], [Total Shipment])
```
