# Customer-Churn-Analysis
This analysis aims to identify the main causes of customer churn, offer strategic recommendations to enhance retention, and establish measurable KPIs to assess the effectiveness of the proposed solutions.

Dashboard - YIP Online Customer Churn Analysis
<img width="602" alt="Home" src="https://github.com/user-attachments/assets/b3d6a6e3-b8db-4610-ad95-976eba5ba566" />
<img width="599" alt="YIP 2" src="https://github.com/user-attachments/assets/d9ea8adf-07bf-474b-88b4-1fa62aca9889" />

## Key Questions
For this report, the following questions were answered;
1. How does the percentage of tickets resolved compare to customer satisfaction score?
2. Did satisfaction score change by the number of days clients subscribed to the service?
3. How did the average ticket resolution time impact customer ratings?
4. What was the customer retention rate starting from July to september?
5. How much revenue was generated in each month?
6. How long did customers spend before they churned?
7. What percntage of customers closed their subscription in each month?
8. What length of time was spent resolving the tickets of customers who remained active or churned?

## DAX for important metrics
The following data analysis expressions were used to generate results;
```dax
1. Percentage of tickets resolved = ROUND('Table 1 (Sheet1)'[Support Tickets Resolved]
/'Table 1 (Sheet1)'[Support Tickets Opened]*100, 0)

2. Months spent before churn = DATEDIFF('Table 1 (Sheet1)'[Subscription Start Date],
'Table 1 (Sheet1)'[Subscription End Date], MONTH)

3. Total_Customers = 
CALCULATE(
    DISTINCTCOUNT('Table 1 (Sheet1)'[CustomerID]), 
    'Table 1 (Sheet1)'[Subscription Start Date] <= EOMONTH(TODAY(), -1)
)

4. DaysBetween = DATEDIFF('Table 1 (Sheet1)'[Subscription Start Date], 'Table 1 (Sheet1)'[Subscription End Date], DAY)

5. Closed Subscription = 
CALCULATE(
    DISTINCTCOUNT('Table 1 (Sheet1)'[CustomerID]),
    USERELATIONSHIP('Table 1 (Sheet1)'[Subscription End Date], 'DateTable'[Date])
)

6. Opened Subscription = 
    CALCULATE(
        DISTINCTCOUNT('Table 1 (Sheet1)'[CustomerID]),
        'Table 1 (Sheet1)'[Subscription Start Date] <= MAX('Table 1 (Sheet1)'[Subscription End Date])
    )
   
7. Cumulative Opened Subscriptions = 
CALCULATE(
    SUMX(
        FILTER(
            ALL('DateTable'),
            'DateTable'[Date] <= MAX('DateTable'[Date])
        ),
        [Opened Subscription]
    )
) - 
CALCULATE(
    SUMX(
        FILTER(
            ALL('DateTable'),
            'DateTable'[Date] <= MAX('DateTable'[Date])
        ),
        [Closed Subscription]
    )
)

8. CustomersChurnedEachMonth = 
CALCULATE(
    DISTINCTCOUNT('Table 1 (Sheet1)'[CustomerID]),
    USERELATIONSHIP('Table 1 (Sheet1)'[Subscription End Date], 'DateTable'[Date])
)

9. Monthly Churn_Rate = 
VAR CustomersAtStart = 
    CALCULATE(
        [Cumulative Opened Subscriptions],
        PREVIOUSMONTH('DateTable'[Date])
    )

VAR CustChurned = 
    [Closed Subscription]

RETURN 
    DIVIDE(CustChurned, CustomersAtStart, 0) * 100

  10. Retention Rate % = 
VAR TotalCustomersAtStart = [Cumulative Opened Subscriptions]
VAR ClosedInCurrentPeriod = [Closed Subscription]
VAR RetainedCustomers = TotalCustomersAtStart - ClosedInCurrentPeriod
RETURN
    DIVIDE(RetainedCustomers, TotalCustomersAtStart, 0) * 100

