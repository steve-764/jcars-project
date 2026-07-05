PROJECT OBJECTIVE:

Import the provided JCars Logistics dataset into your Aiven PostgreSQL database. 
Connect your Aiven database to Power BI and work on the tasks provided. 

IMPORTING DATA INTO AIVEN AND CONNECTING POWERBI TO THE DATABASE:
I have outlined how I imported my dataset into my Aiven dataset and connected powerBI to the database  in my article "How to connect to a local vs online PostgreSQL database. A quick guide" on dev.to. 

MEASURES AND CALCULATIONS: 
I had to use measures, calculations and visuals to create a dashboard that answers the following business questions:
1. What is the total number of cars sold?
2. What is the total sales revenue recorded by the company?
3. What is the total gross profit and gross profit margin?
4. Which car make generated the highest sales revenue?
5. Which car model had the highest number of units sold?
6. Which vehicle type performed best by revenue and by units sold?
7. Which branch generated the highest revenue?
8. Which region or county had the strongest sales performance?
9. Which sales representative generated the highest revenue and the highest units sold?
10. Which lead source or sales channel produced the most valuable customers?
11. What is the monthly trend of revenue, units sold, and gross profit?
12. Which payment method contributed the most to total revenue?
13. What is the payment status breakdown by revenue and number of orders?
14. Which delivery status has the highest number of orders?
15. What is the average delivery time in days by region, branch, and car make?
16. Which branch has the highest logistics cost compared to sales revenue?
17. Which car make or model gives the best profit margin?
18. Which customer type contributes the highest revenue?
19. What is the relationship between discount and sales revenue?
20. What is the relationship between customer rating and revenue or units sold?
21. Which orders or customers should management investigate further based on the
dashboard?
22. Who are the top 10 customers by sales revenue?
23. Which cars are most frequently returned or cancelled?
24. What are the best-performing cars by year of manufacture?
25. What business recommendations would you give JCars Logistics based on your
findings?

I used the following mix of measures and calculated columns to answer the business questions.

Average customer rating
average customer rating = AVERAGE('powerbi jcars'[Customer Rating])

Average  delivery time
average delivery time (days) = AVERAGE('powerbi jcars'[Delivery Time (Days)])
Delivery time
Delivery Time (Days) = 
DATEDIFF(
    'powerbi jcars'[Order Date],
    'powerbi jcars'[Delivery Date],
    DAY)

Gross profit 
gross profit = 'powerbi jcars'[total revenue] - 'powerbi jcars'[total cost]
gross profit margin = ( [total gross profit] / sum('powerbi jcars'[total revenue]))
Profit Margin % = 
DIVIDE(
    [total gross profit],
    [total gross revenue],
    0)
Returned cars
most returned car = 
VAR SummaryTable =
    ADDCOLUMNS(
        VALUES('powerbi jcars'[Car Make]),
        "Returns", [Returned Orders]
    )
VAR TopCar =
    TOPN(1, SummaryTable, [Returns], DESC)
RETURN
MAXX(TopCar, 'powerbi jcars'[Car Make]) &
" (" &
MAXX(TopCar, [Returns]) &
" Returns)"

Rating category
Rating Category = 
SWITCH(
    TRUE(),
    'powerbi jcars'[Customer Rating] < 2, "Poor",
    'powerbi jcars'[Customer Rating] < 4, "Average",
    'powerbi jcars'[Customer Rating] < 4.5, "Good",
    "Excellent"
)

return rate %
return rate % = 
DIVIDE(
    [Returned Orders],
    DISTINCTCOUNT('powerbi jcars'[Order ID]), 0)

returned orders count
returned orders count= 
CALCULATE(
    DISTINCTCOUNT('powerbi jcars'[Order ID]),
    'powerbi jcars'[Returned] = "Yes"
)

revenue discrepancy
revenue discrepancy = 'powerbi jcars'[total revenue] - 'powerbi jcars'[Revenue Recorded]

top car by make and profit margin
top car by make and profit margin = 
VAR SummaryTable =
    ADDCOLUMNS(
        VALUES('powerbi jcars'[Car Make]),
        "ProfitMargin", [Profit Margin %]
    )
VAR TopCar =
    TOPN(1, SummaryTable, [ProfitMargin], DESC)
RETURN
MAXX(TopCar, 'powerbi jcars'[Car Make]) &
" (" &
FORMAT(MAXX(TopCar, [ProfitMargin]), "0.00%") &
")"

top car by vehicle year
top car by vehicle year = 
VAR TopCar =
    TOPN(
        1,
        ADDCOLUMNS(
            VALUES('powerbi jcars'[Car Make]),
            "Revenue", [total gross revenue]
        ),
        [Revenue],
        DESC
    )
RETURN
MAXX(TopCar, 'powerbi jcars'[Car Make])

top customer by revenue
top customer by revenue = 
VAR SummaryTable =
    ADDCOLUMNS(
        VALUES('powerbi jcars'[Customer Name]),
        "Revenue", [total gross revenue]
    )
VAR TopCustomerType =
    TOPN(1, SummaryTable, [total gross revenue], DESC)
RETURN
MAXX(TopCustomerType, 'powerbi jcars'[Customer Name]) &
" (" &
FORMAT(MAXX(TopCustomerType, [total gross revenue]), "#,##0.00") &
")"

top customer type and revenue
top customer type and revenue = 
VAR SummaryTable =
    ADDCOLUMNS(
        VALUES('powerbi jcars'[Customer Type]),
        "Revenue", [total gross revenue]
    )
VAR TopCustomerType =
    TOPN(1, SummaryTable, [total gross revenue], DESC)
RETURN
MAXX(TopCustomerType, 'powerbi jcars'[Customer Type]) &
" (" &
FORMAT(MAXX(TopCustomerType, [total gross revenue]), "#,##0.00") &
")"

total cars sold
total cars sold = SUM('powerbi jcars'[Units Sold])

total costs
total costs = SUM('powerbi jcars'[total cost])

total gross profit
total gross profit = SUM('powerbi jcars'[gross profit])

total gross revenue
total gross revenue = SUM('powerbi jcars'[total revenue])

total logistics cost
total logistics cost = 'powerbi jcars'[Logistics Cost] * 'powerbi jcars'[Units Sold]

total revenue discrepancy
total revenue discrepancy = [total gross revenue] - [total revenue recorded]
total revenue recorded
total revenue recorded = SUM('powerbi jcars'[Revenue Recorded])

VISUALS INCLUDED: 
I have used a combination of cards, stacked bar charts, stacked column charts, donut charts, line and stacked column charts, matrix, tables, a treemap and slicers to visualize the data and enable one to filter through the data dynamically. 

KEY INSIGHTS:
•	The average customer rating across all car makes is 3.56.
•	The most profitable car make to sell is Volkswagen cars topping the profit chart with a profit margin of 80.21% whereas Toyota cars make up a higher revenue stream.
•	Toyota cars face the most returns with a total of 24 returns across all branches.
•	There was a total of 418 cars orders where 133 orders (53.7% of orders) are fulfilled and a further 43 in transit (16.73%).
•	The data indicates a discrepancy of 576 million between the recorded revenue and the expected revenue. 
•	This could be due to improper recording of figures as denoted by numerous blank values in the revenue collected column.

KEY RECOMMENDATIONS:
The management should look into the cause for the discrepancy between the revenue collected and the revenue recorded, by investigating the customers and sales representatives who seem to under report the revenue based on the table provided that highlights this issue.

The management should consider measures to ensure critical data points such as order date, delivery date, revenue collected, unit cost and delivery fee are properly captured to eliminate null values that affect the result of business analysis.

The management should look into the reason for high return rate for certain car makes, such as Toyota, which has a high revenue but a lower profit margin due to cancellations and returns.


