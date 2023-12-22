# Assignment_analytical

--Monthly transactions--
#Need how much amount we have processed each month commutative and every month.
select LPAD(CAST(EXTRACT(MONTH from transaction_timestamp) as string),2,'0') as yearmonth
, billing_amount
from `test-bigquery-383510.tran_dataset.transactions`
;

--Most Popular Products/Services--
#identify the top 5 most popular products or services based on transaction counts.
SELECT 
merchant_name,
merchant_type,
count(transaction_amount) as tran_count

    FROM

    `test-bigquery-383510.tran_dataset.transactions`

    group by 1,2
    order by tran_count desc
    limit 5
    ;

--Daily Revenue Trend--
#Formulate a SQL query to visualize the daily revenue trend over time.
select datetime_trunc(transaction_timestamp, DAY) AS DAY
, transaction_amount as revenue
from
`test-bigquery-383510.tran_dataset.transactions`;

--Average Transaction Amount by Product Category--
#Formulate a SQL query to find the average transaction amount for each product category.

select
merchant_type
, round(avg(transaction_amount),2) as average_transaction_amount
from
`test-bigquery-383510.tran_dataset.transactions`
group by merchant_type
order by 2 asc
;

--Transaction Funnel Analysis--
#Create a SQL query to analyze the transaction funnel, including completed, pending, and cancelled transactions.

select *,
(case when transaction_type = 'PURCHASE' then 'Complete transactions'
when transaction_type = 'ACCOUNT_VERIFICATION' then 'Pending transactions'
when transaction_type = 'CASH_WITHDRAWAL' then 'Pending transactions'
when transaction_type = 'REFUND' then 'Cancelled transactions'
end) as transaction_funnel

from
`test-bigquery-383510.tran_dataset.transactions`;

--Monthly Retention Rate--
#Design a SQL query to calculate the Monthly Retention Rate, grouping users into monthly cohorts.

select a.user_id, a.MONTH, a.FIRST
FROM
(SELECT
  user_id
  , FORMAT_DATETIME("%b", transaction_timestamp) as MONTH
  , min(FORMAT_DATETIME("%b", transaction_timestamp)) as FIRST
  
FROM
`test-bigquery-383510.tran_dataset.transactions`
  GROUP BY user_id
  ,FORMAT_DATETIME("%b", transaction_timestamp)
)a
;

SELECT
  count(user_id),
(CASE WHEN MONTH = 'Jan' THEN 'Jan' ELSE 'MONTH' END) AS Month_1,
(CASE WHEN MONTH = 'Feb' THEN 'Feb' ELSE 'MONTH' END) AS Month_2
  from
(select a.user_id, a.MONTH, a.FIRST
FROM
(SELECT
  user_id
  , FORMAT_DATETIME("%b", transaction_timestamp) as MONTH
  , min(FORMAT_DATETIME("%b", transaction_timestamp)) as FIRST
  
FROM
`test-bigquery-383510.tran_dataset.transactions`
  GROUP BY user_id
  ,FORMAT_DATETIME("%b", transaction_timestamp)
)a
)
group by 2,3;

