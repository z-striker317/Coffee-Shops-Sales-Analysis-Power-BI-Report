# Coffee Shop Sales Analysis

This project showcases a Power BI dashboard designed to visualize and analyze sales data for a coffee shop. The dataset was compiled by merging sales data from three different coffee shop outlets, resulting in a comprehensive dataset of approximately 150,000 rows. The dashboard provides insights into total sales, orders, quantity sold, daily trends, weekday vs. weekend performance, sales by location, and product-specific sales.

## Dashboard Preview

This image provides an overview of the main dashboard, displaying key performance indicators and various charts for sales analysis.

![Dashboard Preview](Dashboard%20Preview.png)

## Calendar Chart Tooltip

This image demonstrates the interactive tooltip functionality when hovering over a specific date on the calendar, providing detailed sales metrics for that day.

![Calendar Chart Tooltip](Calendar%20Chart%20Tool%20Tip.png)

## Day & Hour Chart Tooltip

This image illustrates the tooltip displayed when interacting with the Day & Hour chart, showing specific sales data for a selected day and time.

![Day & Hour Chart Tooltip](Day%20&%20Hour%20Chart%20Tool%20Tip.png)

## DAX Queries

Below are some of the key DAX queries used in this project to create calculated columns and measures for the analysis.

### Date / Calendar Table
[cite_start]A new date table was created to facilitate time-based analysis[cite: 2].

* **Date Table Creation**:
    ```dax
    Date Table = CALENDAR(MIN(Transactions[transaction_date]), MAX(Transactions[transaction_date]))
    ```
    [cite_start][cite: 4]
* [cite_start]**Month (Abbreviated)**: Creates a column with the first three letters of the month name[cite: 5].
    ```dax
    Month = FORMAT('Date Table'[Date], "mmm")
    ```
    [cite_start][cite: 6]
* [cite_start]**Month Number**: Adds a column for the month's number[cite: 7].
    ```dax
    Month Number = MONTH('Date Table'[Date])
    ```
    [cite_start][cite: 8]
* [cite_start]**Month Year**: Creates a column combining the abbreviated month and the year[cite: 9].
    ```dax
    Month Year = FORMAT('Date Table'[Date], "mmm yyyy")
    ```
    [cite_start][cite: 10]

### Transactions Table Measures
[cite_start]These measures were created to analyze sales performance in the transactions table[cite: 11].

* [cite_start]**Sales Column**: A new column to calculate the total sales for each transaction[cite: 12].
    ```dax
    Sales = Transactions[unit_price] * Transactions[transaction_qty]
    ```
    [cite_start][cite: 13]
* [cite_start]**Total Sales Measure**: A new measure to calculate the sum of all sales[cite: 14].
    ```dax
    Total Sales = SUM(Transactions[Sales])
    ```
    [cite_start][cite: 15]
* [cite_start]**Current Month (CM) Sales Measure**: A measure to calculate sales for the current month[cite: 16].
    ```dax
    CM Sales = 
    VAR selected_month = SELECTEDVALUE('Date Table'[Month])
    RETURN
    TOTALMTD(CALCULATE([Total Sales],'Date Table'[Month] = selected_month),'Date Table'[Date])
    ```
    [cite_start][cite: 17, 18, 19]
* [cite_start]**Previous Month (PM) Sales Measure**: A measure to calculate sales for the previous month[cite: 20].
    ```dax
    PM Sales = CALCULATE([CM Sales], DATEADD('Date Table'[Date], -1, MONTH))
    ```
    [cite_start][cite: 21]
* [cite_start]**Month-over-Month (MoM) Growth Measure**: Calculates the month-over-month growth percentage and the absolute difference in sales[cite: 22].
    ```dax
    MoM Growth & Diff Sales = 
    VAR month_diff = [CM Sales] - [PM Sales]
    VAR mom = ([CM Sales] - [PM Sales])/[PM Sales]
    VAR _sign = IF(month_diff > 0, "+", "")
    VAR _sign_trend = IF(month_diff >0, "▲", "▼")
    RETURN
    _sign_trend & " " & _sign & FORMAT(mom, "#0.0%" & " | " & _sign & FORMAT(month_diff / 1000, "0.0K")) & " " & "vs LM"
    ```
    [cite_start][cite: 23, 24, 25, 26, 27, 28, 29]
