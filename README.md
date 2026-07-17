# Olist-ic Recovery: Unlocking ROI through Repeat Purchase Intelligence

In e-commerce, customer acquisition is highly expensive and resource-intensive. Maximizing Customer Lifetime Value (CLV) through retention is a far more cost-effective growth lever. Within the Olist dataset, approximately 97% of users are one-time buyers. Currently, Olist lacks a targeted retention strategy. Running untargeted, blanket marketing campaigns to encourage repeat purchases is highly inefficient, wasting significant budget on the vast majority of users who have no intention of returning. Conversely, missing the small but highly valuable pocket of loyal customers represents a massive missed revenue opportunity. Our core business goal is to identify and nurture likely repeat buyers while deprioritizing marketing spend on predicted one-time buyers.

---

## Task
* **Objective:** Binary classification of whether a customer will make a repeat purchase ($y \in \{0, 1\}$) within a defined target window of 183 days (approx. 6 months) following their initial order.
* **Data Context:** Extremely imbalanced problem with a ~3.1% positive rate.
* **Input Features ($X$):** Features derived exclusively from the **first order** (knowable ~30 days after first-order delivery to prevent target leakage): total spend, cart size (`n_items`), freight cost, payment installments, delivery speed/lateness, and review score.

---

## Key Results
* **Best Model:** Constrained Random Forest achieving a ROC-AUC of **0.56**.
* **Customer Segmentation:** 4 customer segments uncovered via K-Means; "basket builders" (rare outlier carts with higher item counts) repeat at ~2x the baseline rate.
* **Fairness Audit:** Evaluated False Negative Rate (FNR) by Brazilian state (`customer_state`); no conclusive regional disparity was found due to small-sample caveats.

---

## Data Schema (Feature Dictionary)
The engineered customer-level features used in the modeling pipeline are defined below:

| Feature | Data Type | Source Table | Description |
| :--- | :--- | :--- | :--- |
| **`customer_unique_id`** | String | `olist_customers_dataset` | Unique identifier linking individual buyers across orders (Primary Index). |
| **`total_spend`** | Float | `olist_order_items_dataset` | Cumulative sum of item prices in the customer's cart. |
| **`total_paid`** | Float | `olist_order_payments_dataset` | Total actual payment charged to the customer. |
| **`n_items`** | Integer | `olist_order_items_dataset` | Total number of physical items in the order cart. |
| **`max_installments`** | Integer | `olist_order_payments_dataset` | Maximum credit card installments selected (range: $0$ to $24$). |
| **`review_score`** | Integer | `olist_order_reviews_dataset` | Satisfaction rating from the post-purchase survey ($1$ to $5$ scale). |
| **`delivery_days`** | Float | `olist_orders_dataset` | Actual elapsed days between order and delivery timestamp. |
| **`total_freight`** | Float | `olist_order_items_dataset` | Total shipping cost of the first order. |
| **`late`** | Binary | `olist_orders_dataset` | $1$ if delivered after its estimated delivery date; $0$ otherwise. |
| **`customer_state`** | String | `olist_customers_dataset` | Geographic state of residence (used for bias and fairness audits). |
| **`target`** | Binary | Temporal join logic | $1$ if the customer placed a repeat order within 183 days; $0$ otherwise. |

---
## Repository Structure

<pre>
├── data/
│   └── README.md
├── models/
│   ├── README.md
│   ├── kmeans_model.joblib
│   ├── logreg_model.joblib
│   ├── rf_model.joblib
│   └── scaler.joblib
├── notebooks/
│   ├── README.md
│   └── aim-capstone-olist-ic-recovery_...
├── src/
│   └── README.md
├── README.md
└── requirements.txt
</pre>

How to Run
1. Clone the Repository and navigate to the project directory.

2. Set up the environment & dependencies:

    pip install -r requirements.txt

3. Download the Dataset: Download the Brazilian E-Commerce Public Dataset by Olist from Kaggle and place the raw CSV files in the same directory as the notebook.

4. Execute the Pipeline: Run the notebook sequentially from top to bottom to clean the raw transactional tables, engineer the first-order feature matrix, train the models, and run the segmentation and fairness audits.

