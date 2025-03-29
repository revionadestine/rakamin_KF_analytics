/*
Author: Reviona Destine
Date: 29/03/2025
Tool used: Google Bigquery & Looker Data Studio 
*/

/*
--------------------------
Bigquery Syntax
--------------------------
*/

CREATE TABLE `kimia_farma.analisa_percobaan` AS
SELECT
  ft.transaction_id,
  ft.date,
  kc.branch_id,
  kc.branch_name,
  kc.kota,
  kc.provinsi,
  kc.rating AS rating_cabang,
  ft.customer_name,
  p.product_id,
  p.product_name,
  p.price AS actual_price,
  ft.discount_percentage,
CASE
  WHEN ft.price <= 50000 THEN 10
  WHEN ft.price > 50000 AND ft.price <= 100000 THEN 15
  WHEN ft.price > 100000 AND ft.price <= 300000 THEN 20
  WHEN ft.price > 300000 AND ft.price <= 500000 THEN 25
  ELSE 30
END AS percentage_gross_laba,

-- rumus net sales adalah harga x diskon
ft.price * (1- ft.discount_percentage/100) AS nett_sales,
-- rumus nett profit adalah harga x diskon x gross laba
ft.price * (1- ft.discount_percentage/100) *
CASE
  WHEN ft.price <= 50000 THEN 0.10
  WHEN ft.price > 50000 AND ft.price <= 100000 THEN 0.15
  WHEN ft.price > 100000 AND ft.price <= 300000 THEN 0.20
  WHEN ft.price > 300000 AND ft.price <= 500000 THEN 0.25
  ELSE 0.30
END AS nett_profit,

ft.rating AS rating_transaction

FROM `rakamin-kf-analytics-454818-v3.kimia_farma.kf_final_transaction` AS ft
JOIN `rakamin-kf-analytics-454818-v3.kimia_farma.kf_kantor_cabang` AS kc ON kc.branch_id = ft.branch_id
JOIN `rakamin-kf-analytics-454818-v3.kimia_farma.kf_product` AS p ON p.product_id = ft.product_id;
