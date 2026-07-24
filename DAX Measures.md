# DAX Measures Documentation

This document contains all DAX measures used in the **Rios Analytics Dashboard**.  
Measures are grouped by analytical module: Sales, Payments, Logistics, and Feedback.

---

## 📊 Sales Measures

### **Total Sales**
Total_Sales :=
SUM ( 'Order_items'[Sales] )

### AVG Check

AVG_Check :=
DIVIDE ( [Total_Sales], DISTINCTCOUNT ( Orders[order_id] ) )

### Active Customers

Active_Customers :=
DISTINCTCOUNT ( Customers[customer_unique_id] )

## 💳 Payments Measures
### Installment Payment Count

Installment_Payment_Count :=
CALCULATE (
    COUNTROWS ( 'Order_payments' ),
    'Order_payments'[payment_installments] > 1,
    'Order_payments'[payment_type] <> "voucher"
)


### Non‑Installment Payment Count

NonInstallment_Payment_Count :=
CALCULATE (
    COUNTROWS ( 'Order_payments' ),
    'Order_payments'[payment_installments] = 1
)


### Installment Payment Value

Installment_Payment_Value :=
CALCULATE (
    SUM ( 'Order_payments'[payment_value] ),
    'Order_payments'[payment_installments] > 1,
    'Order_payments'[payment_type] <> "voucher"
)

### Non‑Installment Payment Value

NonInstallment_Payment_Value :=
CALCULATE (
    SUM ( 'Order_payments'[payment_value] ),
    'Order_payments'[payment_installments] = 1
)

### Total Payment Count

Total_Payment_Count :=
COUNTROWS ( 'Order_payments' )

### Total Payment Value

Total_Payment_Value :=
SUM ( 'Order_payments'[payment_value] )

## 🚚 Logistics Measures
### AVG Freight Value

AVG_Freight_Value :=
AVERAGE ( 'Order_items'[freight_value] )

### On‑Time Delivery Rate

OnTime_Delivery_Rate :=
DIVIDE (
    CALCULATE (
        COUNTROWS ( Orders ),
        Orders[order_delivered_customer_date] <= Orders[order_estimated_delivery_date]
    ),
    COUNTROWS ( Orders )
)

### AVG Processing Time (days)

AVG_Processing_Time :=
AVERAGEX (
    Orders,
    DATEDIFF (
        Orders[order_purchase_timestamp],
        Orders[order_approved_at],
        DAY
    )
)

### AVG Full Cycle Time (days)

AVG_Full_Cycle_Time :=
AVERAGEX (
    Orders,
    DATEDIFF (
        Orders[order_purchase_timestamp],
        Orders[order_delivered_customer_date],
        DAY
    )
)

## ⭐ Feedback Measures
### Review Comment Count

Review_Comment_Count :=
CALCULATE (
    COUNTROWS ( 'Order_reviews' ),
    NOT ( ISBLANK ( 'Order_reviews'[review_comment_message] ) )
)

### Average Review Response Time (days)

Average_Review_Response_Time :=
AVERAGEX (
    FILTER (
        'Order_reviews',
        NOT ISBLANK ( 'Order_reviews'[review_answer_timestamp] )
            && NOT ISBLANK ( 'Order_reviews'[review_creation_date] )
    ),
    DATEDIFF (
        'Order_reviews'[review_creation_date],
        'Order_reviews'[review_answer_timestamp],
        DAY
    )
)

### Average Review Score

Average_Review_Score :=
AVERAGE ( 'Order_reviews'[review_score] )

### Review Rating Category (calculated column)

Review_Rating :=
SWITCH (
    TRUE(),
    'Order_reviews'[review_score] >= 4, "Good",
    'Order_reviews'[review_score] = 3, "Neutral",
    'Order_reviews'[review_score] < 3, "Bad"
)

## 📌 Notes
 - ll measures follow star‑schema best practices.

 - Installment logic excludes vouchers by business rule.

 - Measures are optimized for cross‑filtering using the data model shown in the project PDF.

Author: Denys Bidiuk
