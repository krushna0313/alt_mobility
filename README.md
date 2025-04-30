# alt_mobility
**Customer Retention & Sales Analysis**

**Project Overview**
This Power BI dashboard provides insights into customer retention behavior and sales trends by analyzing historical order and payment data. The objective is to identify repeat customers, measure retention rate, and observe payment method preferences to support data-driven decision-making.

**Dataset Description**
Two tables were used in this project:

1. customer_orders
columns names: order_id, customer_id, order_date, order_status

2. payments
columns names: order_id, payment_method, payment_status
These tables were merged using NATURALINNERJOIN in DAX to create a unified final_table for analysis.

**Data Preparation**

FinalTable = 
SELECTCOLUMNS (
    NATURALINNERJOIN(customer_orders, payments),
    "order_id", customer_orders[order_id],
    "customer_id", customer_orders[customer_id],
    "order_date", customer_orders[order_date],
    "order_status", customer_orders[order_status],
    "payment_status", payments[payment_status],
    "payment_method", payments[payment_method]
)

Created custom calculated columns and measures:

1. RepeatCustomer: Identifies whether a customer made more than one purchase.
2. CohortMonth: Captures the month of the customer’s first order.
3. Retention Rate: Percentage of repeat customers.

Key DAX Measures & Columns
RepeatCustomer = 
VAR currentCustomer = FinalTable[customer_id]
VAR currentOrderDate = FinalTable[order_date]
RETURN
    IF(
        CALCULATE(
            COUNTROWS(FinalTable),
            FILTER(FinalTable,
                FinalTable[customer_id] = currentCustomer &&
                FinalTable[order_date] > currentOrderDate
            )
        ) > 0, "Yes", "No"
    )
1. 
CohortMonth = 
CALCULATE(
    MIN(FinalTable[order_date]),
    ALLEXCEPT(FinalTable, FinalTable[customer_id])
)

2. 
TotalOrders = COUNTROWS(FinalTable)

3. 
RepeatOrders = 
CALCULATE(
    COUNTROWS(FinalTable),
    FinalTable[RepeatCustomer] = "Yes"
)

4. 
RetentionRate = 
DIVIDE([RepeatOrders], [TotalOrders], 0)


**Visualizations Included**

| Visualization Type       | Columns Used                                       | Purpose                                                        |
|--------------------------|----------------------------------------------------|----------------------------------------------------------------|
| **Card**                 | Total Orders, Repeat Orders, Retention Rate        | Show KPI metrics                                               |
| **Matrix/Table**         | CohortMonth vs. Retained Customers                 | Tabular view of retention by month                             |
| **Stacked Column Chart** | Axis: CohortMonth, Legend: RepeatCustomer          | Shows repeat vs. one-time buyers by month                      |
| **Line Chart**           | X: CohortMonth, Y: Number of Repeat Customers      | Track retention trend over time                                |
| **Pie Chart**            | Legend: payment_method, Values: Count of Payments  | Understand preferred payment methods                           |
| **Slicers**              | order_status, payment_status                       | Filter all visuals based on order or payment status            |


**Key Insights**
1. Retention Rate: 37% of customers are repeat buyers.
2. Customer Loyalty: Higher retention observed in earlier cohorts.
3. Trend: Repeat customer count is declining over the years, suggesting potential engagement issues.
4. Payment Methods: Credit Card and PayPal are the most preferred options.
5. Time-based Analysis: Retention varies month by month; recent months show lower repeat activity.


**Conclusion**
This dashboard effectively highlights customer buying behavior and retention trends. It helps stakeholders understand where to focus marketing or loyalty programs and how payment preferences vary across customers.



