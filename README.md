# Starbucks_App_Customer_Rewards_Program_Analysis

# Introduction 
In an increasingly competitive coffee retail market, customer loyalty programs are crucial for company growth and customer retention. Understanding how customers interact with promotional offers helps businesses tailor their marketing strategy to boost engagement and increase sales. In this project I aim to explore the structure of Starbucks' customer base and identify key factors that influence engagement with different promotional offers. The goal is to provide insights that can optimize marketing strategies and enhance the effectiveness of the Starbucks Rewards Program.

# Data 
For this project, I am using data from https://www.kaggle.com/datasets/blacktile/starbucks-app-customer-reward-program-data/data

The dataset was made available by Starbucks to scholars to create innovative projects 

The dataset includes three JSON files: 

- Profile: Demographics of 17,000 customers (age, gender, income, membership date)

- Portfolio: Information about promotional offers (offer type, reward, duration, channels)

- Transcript: Customer interaction logs (transactions, offer received/viewed/completed)


## Data Cleaning and Preparation
The goal of this stage was to load the data into Pandas dataframes, clean, transform, and merge the datasets so they could be used for customer analysis, statistical inference, and machine learning models.

---

## 1. Profile Data (`profile.json`)

- Removed records with missing or undefined values.  
- After cleaning, 14,613 valid customer records remained.  

---

## 2. Portfolio Data (`portfolio.json`)

- Expanded the `channels` column (lists) into binary indicator columns for `web`, `email`, `mobile`, and `social`.  
- Added a `channel_group` column to capture unique channel combinations.  
- Created a custom offer identifier by appending `difficulty` and `duration` to the `offer_type`.  
  - Example: `bogo-5-7` for a BOGO offer with difficulty 5 and duration 7.  

---

## 3. Transcript Data (`transcript.json`)
 
- Parsed the nested `value` field to extract:  
  - `offer_id` (for received, viewed, completed events)  
  - `amount` (for transaction events)  
- Pivoted the `event` column so that each event type became its own feature (offers received, viewed, completed, transactions, spending).  

---

## 4. Merging and Feature Engineering

- Merged customer demographics (Profile) with transaction and offer data (Transcript + Portfolio) using a left join on `id`.  
- Encoded gender as a binary variable.  
- Created aggregated features per customer:  
  - Total amount spent  
  - Number of transactions  
  - Offers received, viewed, completed  
  - Offer completion rate (offers completed ÷ offers received)  

---

## 5. Summary Statistics

- **Demographics**:  
  - Median age = 55  
  - Income range: $30,000 to $120,000, median = $64,000  
- **Behavior**:  
  - Avg. spending ≈ $116.92, wide variation across customers  
  - Avg. transactions ≈ 8.36, also highly variable  
- **Engagement**:  
  - Clear funnel: offers received > viewed > completed  
  - Wide variation in offer completion rates  

---
# Data Exploration and Customer Segmentation

After preparing the datasets, I explored relationships between numerical variables and customer behaviors to better understand engagement patterns in the Starbucks Rewards Program.

---

## Pairwise Exploration

We used Seaborn’s `pairplot` to generate pairwise comparisons between variables:  
- Diagonal: Kernel Density Estimates (KDE) showing the distribution of each variable.  
- Off-diagonal: Scatterplots showing pairwise relationships.  

- Scatterplots showed high variability across relationships, confirming that no single variable explains customer behavior.  
- Customers with different ages, incomes, and transaction counts often demonstrate similar spending levels, suggesting multiple factors influence behavior.  
- KDE plots revealed multimodal distributions for age, income, and offer completion rate, suggesting distinct subgroups exist within the customer base.  

---

## K-Means Clustering

To better capture these subgroups, K-Means clustering was applied using the variables:  

- `age` – differentiates younger vs. older spending habits.  
- `income` – captures purchasing power.  
- `amount_spent` – reflects actual brand engagement.  
- `num_of_transactions` – measures frequency of engagement.  

### Cluster Selection
- The elbow method and silhouette score both suggested k = 4 as the optimal number of clusters.  
- These clusters provide distinct customer segments for targeted marketing and engagement strategies.  

---

## Hypothesis Testing: Spending vs. Offer Completion

To test whether engagement with offers is linked to spending:  

- **Null Hypothesis (H₀):** No association between amount spent and number of offers completed.  
- **Alternative Hypothesis (H₁):** There is an association between amount spent and number of offers completed.  

**Spearman’s Rank Correlation** (non-parametric, robust to skewness)  

- **Correlation coefficient:** 0.7842  
- **p-value:** 0.0000  
- **Result:** Reject H₀ → Strong evidence of a positive monotonic relationship.  

**Interpretation:** Customers who complete more offers also tend to spend more. This highlights the importance of promoting active participation in the Rewards Program as a driver of revenue.  

---

## Offer Effectiveness and Chi-Square Tests

To explore how **offer type** and **delivery channel** impact engagement.

### Tests Conducted
1. **Offer Delivery Channel vs. Offer Completion**  
   - H₀: No association between delivery channel and offer completion.  
   - H₁: Association exists.  
   - **Result:** Reject H₀. Offer completion varies significantly by channel.  

2. **Offer Type vs. Offer Completion**  
   - H₀: No association between offer type and offer completion.  
   - H₁: Association exists.  
   - **Result:** Reject H₀. Completion rates differ significantly by offer type.  

**Interpretation:** Both the type of offer and the channel used play a critical role in customer engagement. This insight can guide Starbucks toward more effective targeting strategies.  

---

# Offer Effectiveness Insights

By analyzing offer completion rates across segments:

- **Top Performing Offer Across All Clusters**: `discount-7-7`  
- **Underperforming Offer**: `discount-20-10`  
- **High-Spending Segment Success**: `bogo-10-5`  
- **Strong Performance in Moderate/Budget Segments**: `bogo-5-5`  
- **High Impact for Frequent Shoppers**: `discount-10-10`  

> *Note: Each offer was encoded with its difficulty and duration, e.g., `discount-20-10` means a discount offer with difficulty 20 and duration 10.*

### Key Findings
- **Discounts outperform BOGO offers** overall.  
- Longer durations improve success for discounts, but **very high difficulty reduces effectiveness**.  
- **Moderate-difficulty BOGO** offers work better than high-difficulty ones, while duration has little effect.  
- **Multi-channel delivery** (web, email, mobile, social) maximizes reach and engagement, confirming that channel strategy is critical.  

---

## Predictive Modeling with XGBoost

XGBoost Classifier to predict whether a customer will complete an offer.  
- Training: 70% of the dataset  
- Testing: 30% of the dataset  

### Model Performance
- High training and test accuracy
- The model favors recall (sensitivity) for class `1` (offer completion), which is acceptable since sending an unused coupon is less costly than missing a customer likely to respond.  



### Feature Importance
- Most influential predictor: `offer_complete_rate_%` → past engagement is the strongest signal of future completion.  
- Other strong predictors:  
  - Offer variations (`discount-7-7`, `discount-10-10`, `bogo-5-5`)  
  - Communication channels (`mobile`, `web`)  
- Lower importance: static demographics (`age`, `gender`, `income`), suggesting that behavior is more predictive than traits.  

---

With these insights, Starbucks can:  
- Prioritize discount offers with moderate difficulty.  
- Use a multi-channel strategy for delivery.  
- Target customers with higher past engagement for maximum impact.  
- Apply predictive modeling to tailor promotions and increase overall program effectiveness.  

