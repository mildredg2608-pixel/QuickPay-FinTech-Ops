SQL analysis

-- Q1: Count transactions by status
SELECT status, COUNT(*) as transaction_count
FROM transactions
GROUP BY status;

status	transaction_count
CAPTURED	2
CHARGEBACK	1
FAILED		2

-- Q2: Total captured GMV by merchant
SELECT merchant_name, SUM(amount_usd) as total_captured_gmv
FROM transactions
WHERE status = 'CAPTURED'
GROUP BY merchant_name;

merchant_name	total_captured_gmv
Alpha Mart	7497.0

-- Q3: Top 10 merchants by captured GMV
SELECT merchant_name, SUM(amount_usd) as total_captured_gmv
FROM transactions
WHERE status = 'CAPTURED'
GROUP BY merchant_name
ORDER BY total_captured_gmv DESC
LIMIT 10;

merchant_name	total_captured_gmv
Alpha Mart	7497.0

-- Q4: Daily GMV and successful transaction count
SELECT transaction_date, 
       SUM(amount_usd) as daily_gmv, 
       COUNT(*) as success_count
FROM transactions
WHERE status = 'CAPTURED'
GROUP BY transaction_date;

transaction_date	daily_gmv	success_count
2026-03-01	7497.0	2

-- Q5: Merchants with chargeback ratio above 1%
-- Note: We multiply by 1.0 to ensure decimal division
SELECT merchant_name, 
       (COUNT(CASE WHEN status = 'CHARGEBACK' THEN 1 END) * 1.0 / COUNT(*)) as cb_ratio
FROM transactions
GROUP BY merchant_name
HAVING cb_ratio > 0.01;

merchant_name	cb_ratio
Beta Shop	0.33333

-- Q6: Regions with avg risk > 50 and > 20 transactions
SELECT region, AVG(risk_score) as avg_risk, COUNT(*) as txn_count
FROM transactions
GROUP BY region
HAVING avg_risk > 50 AND txn_count > 20;

region	avg_risk	txn_count
There are no results to be displayed.

-- Q7: Users with 3 or more failed/chargeback transactions on same day
SELECT user_id, transaction_date, COUNT(*) as incident_count
FROM transactions
WHERE status IN ('FAILED', 'CHARGEBACK', 'DECLINED')
GROUP BY user_id, transaction_date
HAVING incident_count >= 3;

user_id	transaction_date	incident_count
U003	2026-03-01	3

-- Q8: Chargeback count, unique users, and amount by merchant
SELECT merchant_name, 
       COUNT(CASE WHEN status = 'CHARGEBACK' THEN 1 END) as chargeback_count,
       COUNT(DISTINCT user_id) as unique_affected_users,
       SUM(CASE WHEN status = 'CHARGEBACK' THEN amount_usd ELSE 0 END) as total_cb_amount
FROM transactions
GROUP BY merchant_name;

merchant_name	chargeback_count	unique_affected_users	total_cb_amount
Alpha Mart	0	2	0.0
Beta Shop	1	1	8000.0
