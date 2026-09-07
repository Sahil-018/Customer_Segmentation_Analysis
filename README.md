# Customer Segmentation Project Report

## 1. Project Overview

This project performs **customer segmentation** using transactional customer data. The objective is to group customers according to their purchasing behavior and identify valuable, loyal, inactive, or low-engagement customer segments.

The notebook uses:

* Customer transaction data.
* Exploratory data analysis.
* Customer Lifetime Value calculation.
* RFM analysis.
* Feature scaling.
* K-Means clustering.
* Cluster visualization and interpretation.

The project is suitable for a data analytics or data science portfolio because it demonstrates data cleaning, feature engineering, unsupervised machine learning, and business-oriented customer analysis.

## 2. Dataset Description

The dataset contains **250,000 transaction records** and customer information for up to **50,000 customers**.

The main variables include:

| Column                | Description                                         |
| --------------------- | --------------------------------------------------- |
| Customer ID           | Unique identifier for each customer                 |
| Product Price         | Price of the purchased product                      |
| Quantity              | Number of products purchased                        |
| Total Purchase Amount | Total transaction amount                            |
| Customer Age / Age    | Customer age                                        |
| Returns               | Indicates whether the transaction involved a return |
| Churn                 | Indicates whether the customer churned              |

The dataset contains customer ages between **18 and 70 years**. The average customer age is approximately **43.94 years**. The average product price is approximately **254.66**, while the average total purchase amount is approximately **2,725.37**.

## 3. Exploratory Data Analysis

The notebook examines the structure and statistical distribution of the dataset.

Important observations include:

* The dataset contains 250,000 records.
* Product prices range from approximately 10 to 500.
* Quantity ranges from 1 to 5 items.
* Total purchase amounts range from approximately 100 to 5,350.
* The mean quantity purchased is approximately 3 items.
* Around 49.8% of the records are marked as returns.
* Approximately 19.95% of the records are marked as churned customers.
* Customer age has a median of approximately 44 years.

The dataset also contains an `Age` field that appears to represent the same information as `Customer Age`. In a production project, one of these duplicate columns should be removed to avoid redundancy.

The notebook uses descriptive statistics to understand:

* Central tendency.
* Data spread.
* Minimum and maximum values.
* Quartile ranges.
* Potential distributions and outliers.

## 4. Customer-Level Feature Engineering

Since individual transactions do not fully describe customer behavior, the notebook aggregates transaction-level information by `Customer ID`.

The following customer-level features are created:

### Number of purchases

This represents the total number of transactions made by a customer.

### Total revenue

This is the total amount spent by a customer across all transactions.

### Average purchase value

This is calculated as:

**Average Purchase Value = Total Revenue / Number of Purchases**

It measures the average value of each customer transaction.

### Customer lifespan

Customer lifespan represents the duration of customer activity, expressed in years.

### Annual purchase frequency

This estimates how often the customer purchases each year:

**Purchase Frequency = Number of Purchases / Customer Lifespan**

### Projected Customer Lifetime Value

The notebook calculates a projected Customer Lifetime Value, or CLV. The available output shows that projected CLV is based on customer revenue and purchasing frequency.

For example, the first few customer records show values such as:

| Customer ID | Purchases | Total Revenue | Average Purchase Value | Annual Frequency | Projected CLV |
| ----------: | --------: | ------------: | ---------------------: | ---------------: | ------------: |
|           1 |         1 |         3,491 |               3,491.00 |             1.00 |      2,725.37 |
|           2 |         3 |         7,988 |               2,662.67 |             1.75 |      8,176.11 |
|           3 |         8 |        22,587 |               2,823.38 |             2.94 |     21,802.97 |
|           4 |         4 |         8,715 |               2,178.75 |             1.55 |     10,901.48 |
|           5 |         8 |        12,524 |               1,565.50 |             2.53 |     21,802.97 |

Customer ID 3 has a high projected CLV because the customer has relatively high purchase frequency and total revenue.

## 5. RFM Analysis

The notebook applies the RFM framework.

RFM stands for:

* **Recency:** How recently the customer made a purchase.
* **Frequency:** How frequently the customer purchases.
* **Monetary:** How much money the customer spends.

The RFM table contains the following fields:

| Feature   | Meaning                                                     |
| --------- | ----------------------------------------------------------- |
| Recency   | Number of days or time units since the most recent purchase |
| Frequency | Total number of purchases                                   |
| Monetary  | Total amount spent                                          |

Example records from the notebook include:

| Customer ID | Recency | Frequency | Monetary |
| ----------: | ------: | --------: | -------: |
|           1 |      57 |         1 |    3,491 |
|           2 |     298 |         3 |    7,988 |
|           3 |      88 |         8 |   22,587 |
|           4 |     126 |         4 |    8,715 |
|           5 |     170 |         8 |   12,524 |

A customer with low recency, high frequency, and high monetary value is generally more valuable because the customer purchased recently, purchases often, and spends a large amount.

## 6. Feature Scaling

Before clustering, the RFM features are standardized.

The notebook creates:

* `Recency_scaled`
* `Frequency_scaled`
* `Monetary_scaled`

Standardization is important because the three variables have different ranges. Without scaling, the monetary variable could dominate the distance calculations because its numerical values are much larger than the frequency values.

The standardized features use the general formula:

**z = (x - μ) / σ**

where:

* **x** is the original value.
* **μ** is the feature mean.
* **σ** is the feature standard deviation.

Example scaled values from the notebook include:

| Recency Scaled | Frequency Scaled | Monetary Scaled |
| -------------: | ---------------: | --------------: |
|      -0.826858 |        -1.827822 |       -1.494934 |
|       0.152731 |        -0.921370 |       -0.837492 |
|      -0.700853 |         1.344760 |        1.296821 |
|      -0.546395 |        -0.468144 |       -0.731207 |
|      -0.367549 |         1.344760 |       -0.174347 |

## 7. K-Means Customer Segmentation

The notebook applies the **K-Means clustering algorithm** to the scaled RFM features.

K-Means groups customers by minimizing the distance between customers and their assigned cluster center. The algorithm generally follows these steps:

1. Select the number of clusters.
2. Initialize cluster centroids.
3. Assign each customer to the closest centroid.
4. Recalculate the centroids.
5. Repeat the assignment and recalculation process until the clusters stabilize.

The output includes a `Cluster` column with cluster labels such as 0, 1, and 2.

The sample output is:

| Customer ID | Recency | Frequency | Monetary | Cluster |
| ----------: | ------: | --------: | -------: | ------: |
|           1 |      57 |         1 |    3,491 |       2 |
|           2 |     298 |         3 |    7,988 |       2 |
|           3 |      88 |         8 |   22,587 |       0 |
|           4 |     126 |         4 |    8,715 |       2 |
|           5 |     170 |         8 |   12,524 |       1 |

The cluster numbers are labels only. Cluster 0 is not automatically better than Cluster 1 or Cluster 2. The meaning of each cluster must be determined by comparing cluster-level averages.

## 8. Business Interpretation of the Segments

Based on the RFM methodology, the clusters can be interpreted as follows after calculating their average Recency, Frequency, and Monetary values.

### High-value customers

These customers usually have:

* Low recency values.
* High purchase frequency.
* High monetary value.
* High projected CLV.

Recommended actions:

* Offer loyalty rewards.
* Provide early access to new products.
* Use personalized recommendations.
* Create premium membership programs.
* Protect the relationship with high-quality customer service.

Customer ID 3 is an example of a potentially high-value customer because it has eight purchases and monetary value of 22,587.

### Regular or promising customers

These customers may have moderate frequency and monetary value. They may not yet be the most valuable customers, but they have potential for growth.

Recommended actions:

* Offer cross-selling campaigns.
* Recommend related products.
* Provide limited-time discounts.
* Encourage repeat purchases.
* Use email or notification reminders.

### Low-engagement or at-risk customers

These customers may have:

* High recency values, meaning they have not purchased recently.
* Low purchase frequency.
* Low or moderate spending.

Recommended actions:

* Send reactivation campaigns.
* Offer personalized discounts.
* Ask for feedback.
* Remind customers about abandoned or previously viewed products.
* Analyze whether service problems contributed to inactivity.

The exact business names should be assigned only after reviewing the average RFM values for every cluster.

## 9. Strengths of the Project

The project has several strong points:

* It uses a large dataset with 250,000 records.
* It performs customer-level aggregation instead of clustering raw transactions.
* It includes Customer Lifetime Value analysis.
* It applies the established RFM framework.
* It scales variables before applying K-Means.
* It uses unsupervised learning for business segmentation.
* It connects technical analysis with marketing decisions.
* It is relevant to customer analytics, e-commerce, CRM, and retail businesses.

## 10. Areas for Improvement

To make the notebook stronger for GitHub and portfolio presentation, consider adding the following improvements.

### Explain the source dataset

Add information about:

* Dataset name.
* Dataset source.
* Business context.
* Data collection period.
* Meaning of every column.
* Whether the data is real or synthetic.

### Add data-quality checks

Include checks for:

* Missing values.
* Duplicate records.
* Invalid customer IDs.
* Negative transaction values.
* Incorrect age values.
* Outliers.
* Inconsistent return values.

### Remove duplicate columns

`Customer Age` and `Age` appear to contain the same values. Keep only one of them unless both have different business meanings.

### Validate the CLV formula

The notebook should clearly document the exact CLV formula. A common approach is:

**CLV = Average Purchase Value × Purchase Frequency × Customer Lifespan**

If profit margins or retention rates are available, a more advanced formula could include them.

### Select the optimal number of clusters

The notebook should demonstrate why the selected number of clusters was used. Useful techniques include:

* Elbow method.
* Silhouette score.
* Calinski–Harabasz index.
* Davies–Bouldin index.

### Add cluster profiles

Create a summary table containing:

* Number of customers in each cluster.
* Average recency.
* Average frequency.
* Average monetary value.
* Average CLV.
* Percentage of churned customers.
* Percentage of returned transactions.

This would make the business interpretation more reliable.

### Improve visualizations

Recommended charts include:

* Distribution of Recency, Frequency, and Monetary values.
* Elbow curve.
* Silhouette score comparison.
* Cluster-size bar chart.
* RFM scatter plot.
* CLV by cluster.
* Churn rate by cluster.
* Return rate by cluster.
* Customer age distribution by cluster.

### Explain cluster labels

Instead of showing only numerical labels such as 0, 1, and 2, rename the segments after analysis:

* High-value loyal customers.
* Regular customers.
* At-risk customers.

The names should be based on the actual cluster statistics.

## 11. Suggested GitHub README Description

You can use the following text in your GitHub repository:

> ### Customer Segmentation Using RFM Analysis and K-Means
>
> This project analyzes customer transaction data to identify meaningful customer segments based on purchasing behavior. The analysis uses Recency, Frequency, and Monetary value features to measure customer engagement and value.
>
> The project includes exploratory data analysis, customer-level aggregation, Customer Lifetime Value calculation, RFM analysis, feature scaling, and K-Means clustering. Customers are grouped into behavioral segments that can support targeted marketing, customer retention, loyalty programs, and reactivation campaigns.
>
> The dataset contains 250,000 transaction records and customer attributes such as product price, quantity, total purchase amount, age, returns, and churn. The final output assigns each customer to a cluster based on their standardized RFM characteristics.
>
> ### Technologies Used
>
> * Python
> * Pandas
> * NumPy
> * Matplotlib
> * Seaborn
> * Scikit-learn
> * Jupyter Notebook
>
> ### Key Techniques
>
> * Exploratory data analysis.
> * Feature engineering.
> * Customer Lifetime Value calculation.
> * RFM analysis.
> * StandardScaler preprocessing.
> * K-Means clustering.
> * Customer segment interpretation.
>
> ### Business Applications
>
> The resulting customer segments can be used to identify high-value customers, target inactive customers, improve retention, personalize marketing campaigns, and design customer loyalty programs.

## 12. Final Assessment

The notebook presents a complete introductory customer segmentation workflow. It begins with transaction-level data, creates customer-level behavioral features, calculates CLV, applies RFM analysis, standardizes the features, and uses K-Means to create customer segments.

For a stronger GitHub project, the most important next step is to add a clear cluster-profile table and explain the exact business meaning of every cluster. The project already demonstrates the core skills expected in an entry-level data analyst or junior data scientist portfolio: Python, data preprocessing, feature engineering, statistical exploration, machine learning, and business interpretation.
