# Retail Customer Segmentation (Category-wise KMeans)

## Overview
Customer segmentation for a retail dataset. Since ~95% of customers are one-time buyers, classic RFM (especially Frequency) is less informative. Segmentation is performed per product category.

## Challenges & How I Solved Them

This dataset was **non-trivial** to work with due to several real-world issues, so the project focused not only on clustering but also on building a reliable segmentation pipeline.

### 1) Most customers are one-time buyers (~95%)
**Challenge:** Classic RFM segmentation becomes less informative because Frequency is almost always 1, which reduces the value of traditional RFM-based clustering.  
**Solution:** I shifted the focus to **category-wise behavior** and used **Monetary + Discount behavior** (and Recency mainly for profiling rather than training).

### 2) Customers can appear in multiple product categories
**Challenge:** A single customer may purchase across different categories, so assigning one global segment can be misleading.  
**Solution:** I performed **segmentation per product category**, creating an RM-style table for each category. This allows the same customer to have different segments depending on the category.

### 3) Near-uniform distributions (weak separation for some variables)
**Challenge:** Some variables (e.g., `Recency_days`, and sometimes `AvgDiscount`) were close to uniform and did not strongly separate clusters on their own.  
**Solution:** I validated feature usefulness using cluster summaries (median/IQR) and excluded weak features from training when they did not improve separation, keeping them for profiling and interpretation.
	
### 4) Outliers and skewed spending (Monetary)
**Challenge:** Spending is typically skewed with extreme values, which can dominate distance-based clustering.  
**Solution:** I used **RobustScaler** to reduce the influence of outliers (median/IQR-based scaling), ensuring KMeans distances were not dominated by a few high spenders.

### 5) Cluster label mismatch across categories (0/1/2 are not consistent)
**Challenge:** KMeans cluster IDs have no fixed meaning; label “0” in one category is not the same as label “0” in another.  
**Solution:** I mapped numeric labels to **business-friendly segment names** using cluster statistics (medians/IQR), producing consistent interpretations such as:
- **VIP Big Spenders**
- **Promo Responders**
- **Full-Price Regulars**

### 6) Merging segments back to the original transaction table
**Challenge:** After building customer-level segments per category, the final deliverable needed to be attached back to the original transaction-level dataset without mismatches.  
**Solution:** I built a mapping table and merged results using the composite key:  
`(CustomerID, ProductCategory)`  
This ensures each transaction receives the correct segment even when the same customer appears in multiple categories.

## Method
- Build customer-level RM features per category (Monetary, AvgDiscount, and Recency for profiling).
- Use RobustScaler + KMeans per category.
- Map numeric cluster labels to business-friendly segment names.
- Merge segments back to the original transactions using (CustomerID, ProductCategory).

## How to run
pip install -r requirements.txt
Open: notebooks/customer_segmentation.ipynb


