# Starbucks_App_Customer_Rewards_Program_Analysis

# Introduction 
In an increasingly competitive coffee retail market, customer loyalty programs are crucial for company growth and customer retention. Understanding how customers interact with promotional offers helps businesses tailor their marketing strategy to boost engagement and increase sales. In this project I aim to explore the structure of Starbucks' customer base and identify key factors that influence engagement with different promotional offers. The goal is to provide insights that can optimize marketing strategies and enhance the effectiveness of the Starbucks Rewards Program.

# Data 
For this project, I am using data from https://www.kaggle.com/datasets/blacktile/starbucks-app-customer-reward-program-data/data
The dataset was made available by Starbucks to scholars to create innovative projects 

The dataset includes three JSON files: 
• Profile: Demographics of 17,000 customers (age, gender, income, membership date)
• Portfolio: Information about promotional offers (offer type, reward, duration, channels)
• Transcript: Customer interaction logs (transactions, offer received/viewed/completed)

## Data Cleaning and Preparation
The goal of this stage was to **clean, transform, and merge the datasets** so they could be used for customer analysis, statistical inference, and machine learning models.

---

## 1. Profile Data (`profile.json`)

- Loaded into a Pandas DataFrame.  
- Removed records with **missing or undefined age/gender**.  
- Converted `became_member_on` to proper `datetime` format.  
- After cleaning, **14,613 valid customer records** remained (out of 17,000).  

---

## 2. Portfolio Data (`portfolio.json`)

- Loaded into a DataFrame.  
- Expanded the `channels` column (lists) into **binary indicator columns** for `web`, `email`, `mobile`, and `social`.  
- Added a `channel_group` column to capture unique channel combinations.  
- Created a **custom offer identifier** by appending `difficulty` and `duration` to the `offer_type`.  
  - Example: `bogo-5-7` for a BOGO offer with difficulty 5 and duration 7.  

---

## 3. Transcript Data (`transcript.json`)

- Loaded into a DataFrame.  
- Parsed the nested `value` field to extract:  
  - `offer_id` (for received, viewed, completed events)  
  - `amount` (for transaction events)  
- Pivoted the `event` column so that **each event type became its own feature** (offers received, viewed, completed, transactions, spending).  

---

## 4. Merging and Feature Engineering

- Merged customer demographics (Profile) with transaction and offer data (Transcript + Portfolio) using a **left join on `id`**.  
- Encoded gender as a binary variable.  
- Created aggregated features per customer:  
  - **Total amount spent**  
  - **Number of transactions**  
  - **Offers received, viewed, completed**  
  - **Offer completion rate** (offers completed ÷ offers received)  

---

## 5. Summary Statistics

- **Demographics**:  
  - Age range: 18 to 101, median = 55  
  - Income range: $30,000 to $120,000, median = $64,000  
- **Behavior**:  
  - Avg. spending ≈ $116.92, wide variation across customers  
  - Avg. transactions ≈ 8.36, also highly variable  
- **Engagement**:  
  - Clear funnel: offers received > viewed > completed  
  - Wide variation in offer completion rates  

---

## Notes

- Membership start date retained but not analyzed in this project.  
- Customers with extreme or missing values were dropped.  
- Distributions showed a **small group of highly engaged, high-spending customers driving averages**.  

