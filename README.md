# **Virtual Internship Experience: Big Data Analytics - Kimia Farma**
Tool : Google Bigquery & Looker Data Studio <br>
Visualization : Looker Data Studio - [Lihat dashboard](https://lookerstudio.google.com/s/rvUSovORG5s) <br>
Dataset : [Rakamin KF Analytics](https://console.cloud.google.com/bigquery?hl=id&invt=AbtVlQ&project=rakamin-kf-analytics-454818-v3&ws=!1m5!1m4!4m3!1srakamin-kf-analytics-454818-v3!2skimia_farma!3skf_analisa)
<br>

---

## 📂 **Introduction**
Big Data Analytics Kimia Farma merupakan virtual internship experience yang difasilitasi oleh [Rakamin Academy](https://www.rakamin.com/virtual-internship-experience/kimiafarma-big-data-analytics-virtual-internship-program). Pada project ini saya berperan sebagai Data Analyst Intern yang diminta untuk menganalisis dan membuat laporan penjualan perusahaan menggunakan data-data yang telah disediakan. Dari project ini, saya juga banyak belajar tentang data data warehouse, dataleke, dan datamart. <br>
<br>

**Objectives**
- Membuat design datamart (tabel base dan tabel aggregat)
- Membuat visualisasi/dashboard laporan penjualan perusahaan
<br>

**Dataset** <br>
Dataset yang disediakan terdiri dari tabel-tabel berikut:
- Final Transaction
- Inventory
- Kantor cabang
- Product
<br>

---


## 📂 **Design Datamart**
### Tabel Base
Tabel base adalah tabel yang berisi data asli atau data mentah yang dikumpulkan dari sumbernya dan berisi informasi yang dibutuhkan untuk menjawab pertanyaan atau menyeselasikan masalah tertentu. Tabel base dalam project ini dibuat dari aggregasi data final transaction, inventory, kantor cabang, dan product. <br>

<details>
  <summary> Klik untuk melihat Query </summary>
    <br>
    
```sql
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
--- 


