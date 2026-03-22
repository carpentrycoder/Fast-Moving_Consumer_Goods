# 📊 FMCG Distribution & Smart Inventory Analytics Dashboard

> A complete Power BI analytics solution for FMCG supply chain monitoring — covering distributor performance, product analysis, and AI-based inventory forecasting across 16 Indian states.

---

## 🛒 What is FMCG?

Fast-Moving Consumer Goods (FMCG) are low-cost, high-turnover products sold quickly with short shelf lives. Key examples include packaged foods, beverages, toiletries, and cleaning supplies. Defined by high volume and low margins, the sector focuses on brand recognition, frequent repeat purchases, and intense distribution networks.

### Key Concepts

| Concept | Description |
|---|---|
| **Characteristics** | High volume / low margin, short shelf life, frequent consumer replenishment |
| **Categories** | Packaged foods (snacks, cereals), beverages, cosmetics, toiletries (toothpaste, soap), medicines, office supplies |
| **Supply Chain** | Highly reliant on efficient logistics and extensive, fast distribution systems to prevent stockouts |
| **Marketing Strategy** | Heavy reliance on branding, impulse buying, and competitive pricing |
| **Key Drivers** | Consumer convenience, rising demand for healthy/organic options, increasing e-commerce adoption |

---

## 🏢 Business Context

FMCG supply chain flow:

```
Manufacturer → Distributor → Retailer → Customer → Data/BI
```

### Problems This Dashboard Solves

| # | Problem | Solution in Dashboard |
|---|---|---|
| 1 | Distributors missing sales targets | Achievement % KPI with Red/Green alerts |
| 2 | Regional stock shortages | Inventory Intelligence page with Low Stock alerts |
| 3 | Excess warehouse inventory | Overstocked Count KPI + Heatmap matrix |
| 4 | Poor demand prediction | Forecast vs Actual line chart (ML model output) |
| 5 | No visibility into distributor performance | Scatter plot — Revenue vs Margin analysis |

---

## 📁 Dataset Structure

Generated using Python (numpy + pandas) — 5 CSV files, ~77,000 total rows.

```
fmcg_output/
├── 01_products.csv          # 39 rows   — Dimension table
├── 02_distributors.csv      # 224 rows  — Dimension table
├── 03_sales.csv             # 75,000 rows — Main Fact table
├── 04_inventory.csv         # 468 rows  — Inventory Fact table
└── 05_demand_forecast.csv   # 1,404 rows — Forecast Fact table
```

### Table Schemas

**products** — `product_id` | `product_name` | `brand_name` | `category` | `sub_category` | `unit_price` | `mrp` | `is_premium`

**distributors** — `distributor_id` | `distributor_name` | `state` | `region` | `retailer_count` | `tier` | `city`

**sales** — `sale_id` | `distributor_id` | `product_id` | `sale_date` | `units_sold` | `sales_amount` | `target_amount` | `net_sales` | `gross_profit` | `achievement_pct`

**inventory** — `inventory_id` | `warehouse_id` | `warehouse_name` | `product_id` | `stock_available` | `reorder_level` | `stock_status` | `last_updated`

**demand_forecast** — `forecast_id` | `product_id` | `year` | `month` | `actual_units` | `predicted_units` | `recommended_stock` | `accuracy_pct` | `model_used`

---

## 🗺️ Star Schema Data Model

```
                    ┌─────────────────┐
                    │   products      │ ◄──────────────────┐
                    │  (product_id PK)│                    │
                    └────────┬────────┘                    │
                             │ 1                           │ 1
                    ┌────────▼────────┐          ┌─────────┴────────┐
                    │     sales       │          │  demand_forecast  │
                    │  (Fact Table ⭐) │          │  (Fact Table)     │
                    └────────┬────────┘          └──────────────────┘
                             │ *
                    ┌────────▼────────┐          ┌──────────────────┐
                    │  distributors   │          │   inventory      │
                    │(distributor_id) │          │  (Fact Table)    │
                    └─────────────────┘          └──────────────────┘
```

### Relationships

| From (Fact) | To (Dimension) | Cardinality | Filter Direction |
|---|---|---|---|
| `sales[product_id]` | `products[product_id]` | Many to One | Single |
| `sales[distributor_id]` | `distributors[distributor_id]` | Many to One | Single |
| `inventory[product_id]` | `products[product_id]` | Many to One | Single |
| `demand_forecast[product_id]` | `products[product_id]` | Many to One | Single |
| `DateTable[Date]` | `sales[sale_date]` | Many to One | Single |

---

## 📐 Dashboard Pages

### 🏠 Page 1 — Executive Overview
*CEO/Management level view*

```
┌─────────┬─────────┬─────────┬─────────┐
│Total Rev│Total Tgt│Achiev % │Units    │  ← KPI Cards
├─────────┴─────────┼─────────┴─────────┤
│  Line Chart       │   Treemap         │
│  Revenue Trend    │   Region Sales    │
├─────────┬─────────┴───────────────────┤
│Top 5    │  Donut Chart                │
│Distrib. │  Category Contribution      │
├─────────┴─────────────────────────────┤
│ Year Slicer  │  Region Slicer         │
└──────────────┴────────────────────────┘
```

**KPIs:** Total Revenue | Total Target | Achievement % | Total Units Sold

**Visuals:** Revenue Trend Line Chart | Region Sales Treemap | Top 5 Distributors Bar | Category Donut Chart

---

### 🏪 Page 2 — Distributor Performance
*Operations team view*

```
┌──────────────┬──────────────┬──────────────┐
│Dist. Above   │Achievement % │Top Performer │  ← KPI Cards
│Target        │              │(Card)        │
├──────────────┴──────┬───────┴──────────────┤
│Target vs Achievement│ Revenue Ranking      │
│(Clustered Bar)      │ (Bar Chart)          │
├─────────────────────┴──────────────────────┤
│        Scatter Plot — Revenue vs Margin    │
├────────────────────────────────────────────┤
│        Matrix Table — Distributor Details  │
├───────────────────┬────────────────────────┤
│ State Slicer      │ Tier Slicer            │
└───────────────────┴────────────────────────┘
```

**KPIs:** Distributors Above Target | Achievement % | Top Distributor Name

**Visuals:** Clustered Bar (Target vs Achievement) | Revenue Ranking Bar | Scatter Plot (Revenue vs Margin) | Matrix Table with conditional formatting

---

### 📦 Page 3 — Product & Category Analysis
*Marketing/Sales team view*

```
┌────────────┬────────────┬────────────┐
│ Total SKUs │Top Cat Rev │Total Units │  ← KPI Cards
├────────────┴──────┬─────┴────────────┤
│ Donut Chart       │ Line Chart       │
│ Category Revenue  │ Monthly Trend    │
├───────────────────┴──────────────────┤
│ Top 10 Products  │ Brand Performance │
├──────────────────┴───────────────────┤
│ Category × Region Matrix (Heatmap)   │
├──────────┬───────────┬───────────────┤
│ Category │ Brand     │ Sub Category  │  ← Slicers
└──────────┴───────────┴───────────────┘
```

**KPIs:** Total SKUs | Top Category Revenue | Total Units Sold

**Visuals:** Category Donut | Monthly Trend Multi-line | Top 10 Products Bar | Brand Comparison Bar | Category×Region Heatmap Matrix

---

### 🏭 Page 4 — Inventory Intelligence
*Supply chain/warehouse team view*

```
┌──────────┬──────────┬──────────┬──────────┐
│Total     │Low Stock │Overstck  │Forecast  │  ← KPI Cards
│Stock     │Count 🔴  │Count 🟡  │Accuracy  │
├──────────┴────┬─────┴──────────┴──────────┤
│Stock vs Reord │ Forecast vs Actual         │
│Bar Chart      │ Line Chart (AI visual) 🤖  │
├───────────────┴────────────────────────────┤
│ Low Stock Alert Table 🚨                   │
├────────────────────────┬───────────────────┤
│ Warehouse × Product    │ Gauge Chart       │
│ Matrix (Heatmap)       │ Health Score      │
├───────────────┬────────┴───────────────────┤
│Warehouse      │ Stock Status Tiles         │
│Slicer         │ 🔴 Low  🟡 Over  🟢 OK    │
└───────────────┴────────────────────────────┘
```

**KPIs:** Total Stock | Low Stock Count | Overstocked Count | Forecast Accuracy %

**Visuals:** Stock vs Reorder Bar | Forecast vs Actual Line | Low Stock Alert Table | Warehouse Heatmap Matrix | Inventory Health Gauge

---

## 🧠 Key DAX Measures

```dax
-- Core KPIs
Total Revenue       = SUM(sales[sales_amount])
Total Target        = SUM(sales[target_amount])
Total Units Sold    = SUM(sales[units_sold])
Total Gross Profit  = SUM(sales[gross_profit])

-- Achievement
Achievement %       = DIVIDE(SUM(sales[sales_amount]), SUM(sales[target_amount]), 0) * 100

Distributors Above Target =
CALCULATE(DISTINCTCOUNT(sales[distributor_id]), sales[achievement_pct] >= 100)

-- Time Intelligence (requires DateTable)
Revenue Growth MoM =
VAR CurrentMonth = [Total Revenue]
VAR PrevMonth = CALCULATE([Total Revenue], DATEADD(DateTable[Date], -1, MONTH))
RETURN DIVIDE(CurrentMonth - PrevMonth, PrevMonth, 0) * 100

Revenue YTD = CALCULATE([Total Revenue], DATESYTD(DateTable[Date]))

-- Ranking
Revenue Rank =
RANKX(ALL(distributors[distributor_name]), [Total Revenue], , DESC, DENSE)

-- Inventory
Low Stock Count =
CALCULATE(COUNTROWS(inventory), inventory[stock_status] = "Low Stock")

Inventory Health % =
DIVIDE(
  CALCULATE(COUNTROWS(inventory), inventory[stock_status] = "Healthy"),
  COUNTROWS(inventory), 0
) * 100

-- Forecasting
Avg Forecast Accuracy = AVERAGE(demand_forecast[accuracy_pct])

Forecast Variance =
SUM(demand_forecast[predicted_units]) - SUM(demand_forecast[actual_units])
```

---

## ⚙️ Tech Stack

| Tool | Purpose |
|---|---|
| **Python** (numpy, pandas) | Synthetic dataset generation |
| **Power BI Desktop** | Data modeling + dashboard |
| **DAX** | Business calculations + KPIs |
| **Star Schema** | Optimized data model |
| **ML Forecasting** | ARIMA / Random Forest / Linear Regression (simulated) |

---

## 🚀 How to Run

**Step 1 — Generate Dataset**
```bash
python3 fmcg_generator_v2.py
# Output: ./fmcg_output/ folder with 5 CSV files
```

**Step 2 — Import in Power BI (this order)**
```
01_products.csv       → Dimension
02_distributors.csv   → Dimension
03_sales.csv          → Fact (main)
04_inventory.csv      → Fact
05_demand_forecast.csv → Fact
```

**Step 3 — Create Date Table**
```dax
DateTable = CALENDAR(DATE(2022,1,1), DATE(2024,12,31))
```

**Step 4 — Set Relationships in Model View**
```
sales[product_id]           → products[product_id]
sales[distributor_id]       → distributors[distributor_id]
inventory[product_id]       → products[product_id]
demand_forecast[product_id] → products[product_id]
DateTable[Date]             → sales[sale_date]
```

**Step 5 — Build 4 Dashboard Pages** *(follow guide above)*

---

## 📊 Dataset Stats

| Metric | Value |
|---|---|
| Total Rows | ~77,000 |
| Sales Transactions | 75,000 |
| Date Range | Jan 2022 — Dec 2024 |
| Indian States | 16 |
| Regions | 5 (North, South, East, West, Central) |
| FMCG Brands | 37 |
| Product Categories | 5 |
| Warehouses | 12 |
| Distributors | 224 |
| Seasonal Patterns | Built-in (Dec/Nov peak, Jul low) |

---

*Built as a portfolio project simulating a real FMCG analytics implementation across India.*
