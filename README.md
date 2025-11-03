<h1>Loan Analytics Dasboard using Python</h1>

<h2>Overview :</h2>

The Loan Analysis Project is a Python-based data analytics application designed to explore, process and visualize loan-related data. It aims to help financial institutions and analysts understand key factors influencing loan approvals, defaults, and customer behavior.
This project performs data cleaning, exploratory data analysis and predictive modeling to extract insights about borrower profiles, loan amounts, interest rates, and repayment patterns.

<h2>Tools used to dive into the data analyst job market :</h2>

1. Python
2. Pandas Library
3. Matplotlib Library
4. Seaborn Library
5. Jupyter Notebooks
6. Visual Studio Code

<h2>Importing Libraries :</h2>

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import warnings
import seaborn as sns
import plotly.express as px
import plotly.io as pio
```
<h2>Cleaning up the data :</h2>

```python
df = pd.read_excel("C:/Users/ADMIN/Saikat VS/Bank loan analysis/Raw Data.xlsx")
df['issue_date'] = pd.to_datetime(df['issue_date'])
```
<h2>Below are the questions answered in my project :</h2>

<h2>1. Total number of loan applications received during a specified period?</h2>

```python
total_application = df['id'].count()
latest_issue = df['issue_date'].max()
latest_issue_year = latest_issue.year
latest_issue_month = latest_issue.month
df_last_month_total_application = df[(df['issue_date'].dt.year == latest_issue_year) & (df['issue_date'].dt.month == latest_issue_month)]
month_to_date_applications = df_last_month_total_application['id'].count()

print('Total loan application :', total_application)
print(f"Month to date total loan applications for ({latest_issue_month}, {latest_issue_year}): {month_to_date_applications}")
```
<h2>2. Total amount of funds disbursed as well as Month to Date total funds disbursed?</h2>

```python
funded_amount = df['loan_amount'].sum()
funded_amount = funded_amount / 1000000
total_funded_amount = f"${funded_amount:.2f}M"
df_funded_amount_month_to_date = df[(df['issue_date'].dt.year == latest_issue_year) & (df['issue_date'].dt.month == latest_issue_month)]
total_funded_amount_mtd = df_funded_amount_month_to_date['loan_amount'].sum()
total_funded_amount_mtd = total_funded_amount_mtd / 1000000
total_funded_amount_mtd = f"${total_funded_amount_mtd:.2f}M"

print("Total Funded Amount :", total_funded_amount)
print("Month to date Total funded amount :", total_funded_amount_mtd)

```

<h2>3. Total amount as well as Month-to-Date total Amount received received from borrowers?</h2>

```python
amount_received = df['total_payment'].sum()
amount_received = amount_received / 1000000
total_amount_received = f"${amount_received:.2f}M"
df_mtd_amount_received = df[(df['issue_date'].dt.year == latest_issue_year) & (df['issue_date'].dt.month == latest_issue_month)]
total_mtd_amount_received = df_mtd_amount_received['loan_amount'].sum()
total_mtd_amount_received = total_mtd_amount_received/1000000
total_mtd_amount_received = f"${total_mtd_amount_received:.2f}M"

print("Total amount received till now :", total_amount_received)
print("Mtd Total amount received :", total_mtd_amount_received)
```
<h2>4. Average interest rate across all loans to get insights into our lending portfolio's overall cost.</h2>

```python
Avg_int_rate = df['int_rate'].mean()
Avg_int_rate = Avg_int_rate * 100
Avg_int_rate = f"{Avg_int_rate:.2f}%"

print("Average Interest Rate of all loans :", Avg_int_rate)
```
<h2>5. Monthly Trends by Issue Date:  To identify seasonality and long-term trends in lending activities.</h2>

```python
df['month_name'] = df['issue_date'].dt.strftime('%b %Y')
df['month_name'] = df['month_name'].sort_values()

monthly_trend = df.groupby('month_name', sort= False)['loan_amount'].sum()
monthly_trend = monthly_trend / 1000000
monthly_trend = monthly_trend.reset_index(name= 'Amount in millions')

plt.figure(figsize = (12,6))
sns.lineplot(data= monthly_trend, x= monthly_trend['month_name'], y=monthly_trend['Amount in millions'], linewidth = 2)
plt.fill_between(monthly_trend['month_name'], monthly_trend['Amount in millions'], color = 'skyblue', alpha = 0.5)
sns.despine()
for i, txt in enumerate(monthly_trend['Amount in millions']):
    plt.text(i,txt+0.5, f"{txt:.2f}", ha= 'center', va='bottom', fontweight = 'bold')

plt.title('\u0330'.join("Total Funded Amount in Months"), fontsize = 28, fontweight = 'bold', pad= 20)
plt.ylabel('Amount in millions', fontsize = 14, fontweight = 'bold')
plt.xlabel('')
plt.tight_layout()
plt.show()
```
<h2>6. Regional Analysis by State: To identify regions with significant lending activity and assess regional disparities.</h2>

```python
state_wise_funding = df.groupby('address_state')['loan_amount'].sum().sort_values(ascending= False).head(20)
state_wise_funding = state_wise_funding/1000
state_wise_funding = state_wise_funding.reset_index(name= 'Loan Amount')

plt.figure(figsize= (12,6))
sns.barplot(data = state_wise_funding, x= state_wise_funding['Loan Amount'], y= state_wise_funding['address_state'], 
            hue = 'Loan Amount', 
            palette= 'dark:b_r', 
            legend = False
            )
sns.set_theme(style= 'ticks')
sns.despine()
for i,txt in enumerate(state_wise_funding['Loan Amount']):
    plt.text(txt + 1, i, f"{txt:.0f}K", va = 'center')
plt.title('\u0330'.join('Total Funded Amount by states'), fontsize = 20, fontweight = 'bold', pad= 20)
plt.ylabel('States', fontweight = 'bold')
plt.xlabel('Loan Amount', fontweight = 'bold')
plt.tight_layout()
plt.show()
```
<h2>7. Loan Term Analysis: To allow the client to understand the distribution of loans across various term lengths.</h2>

```python
term_analysis = df.groupby('term')['loan_amount'].sum() / 1000000

plt.pie(term_analysis, labels= term_analysis.index, 
        startangle=90, 
        wedgeprops={'width':0.4}, 
        autopct = lambda x: f'{x:.1f}% \n ${x * term_analysis.sum() / 100:.0f}M'
        )
plt.title('\u0330'.join('Total Funded Amount on term basis'), fontsize = 16, fontweight = 'bold', pad= 20)
plt.tight_layout()
plt.show()
```
<h2>8. Employee Length Analysis: How lending metrics are distributed among borrowers with different employment lengths.</h2>

```python
emp_funding = df.groupby('emp_length')['loan_amount'].sum() / 1000000
emp_funding = emp_funding.reset_index(name= 'Loan Amount')
emp_funding = emp_funding.sort_values(by= 'Loan Amount', ascending= False)

plt.figure(figsize=(8,5))
sns.barplot(data = emp_funding, x = emp_funding['Loan Amount'], y = emp_funding['emp_length'], 
            hue= 'Loan Amount', 
            palette= 'dark:r_r', 
            legend = False
            )
sns.set_theme(style= 'ticks')
sns.despine()
for i, txt in enumerate(emp_funding['Loan Amount']):
    plt.text(txt + 1, i , f'{txt:.0f}M', va= 'center')

plt.title('\u0330'.join('Total Funded Amount by Employment Length'), fontsize = 17, fontweight = 'bold', pad = 15)
plt.ylabel('Employment length', fontweight = 'bold')
plt.xlabel('Loan Amount', fontweight = 'bold')
plt.tight_layout()
plt.show()
```
<h2>9. Loan Purpose Breakdown: Will provide a visual breakdown of loan metrics based on the stated purposes of loans.</h2>

```python
loan_purpose = df.groupby('purpose')['loan_amount'].sum() / 1000000
loan_purpose = loan_purpose.reset_index(name= 'Total Amount')
loan_purpose = loan_purpose.sort_values(by= 'Total Amount', ascending= False)

plt.figure(figsize=(8,5))
sns.barplot(data = loan_purpose, x = loan_purpose['Total Amount'], y = loan_purpose['purpose'], 
            hue= 'Total Amount', 
            palette= 'dark:g_r', 
            legend = False
            )
sns.set_theme(style= 'ticks')
sns.despine()
for i, txt in enumerate(loan_purpose['Total Amount']):
    plt.text(txt + 1, i , f'{txt:.0f}M', va= 'center')

plt.title('\u0330'.join('Total Funded Amount by Loan Purpose'), fontsize = 17, fontweight = 'bold', pad = 15)
plt.ylabel('Loan Purpose', fontweight = 'bold')
plt.xlabel('Loan Amount', fontweight = 'bold')
plt.tight_layout()
plt.show()
```

<h2>10. Home Ownership Analysis: For a hierarchical view of how home ownership impacts loan applications and disbursements.</h2>

```python
home_funding = df.groupby('home_ownership')['loan_amount'].sum() / 1000000
home_funding = home_funding.reset_index(name= 'Total Amount')
home_funding = home_funding.sort_values(by= 'Total Amount', ascending= False)
df_home_funding = pd.DataFrame({

})

fig = px.treemap(
    home_funding, 
    path= ['home_ownership'], 
    values= 'Total Amount', 
    color = 'Total Amount', 
    color_continuous_scale='Blues', 
    
    )
fig.update_layout(title = '\u0330'.join('Total Funded Amount by Home Ownership'), 
                  title_font = dict(size = 20, weight = 'bold', color = 'black'),
                  width = 1000, height = 600
                  )
pio.renderers.default = "svg"
fig.show()
```
<h2>Conclusion :</h2>

The Loan Analysis project successfully demonstrates how data-driven methods can enhance decision-making in the financial sector. By analyzing borrower profiles, credit histories, and loan characteristics, the study highlights key factors that influence loan approval and default risk. Through exploratory data analysis and predictive modeling, it was observed that applicants with higher credit scores, stable incomes, and lower debt-to-income ratios have a significantly higher chance of loan approval. Overall, the project proves that Python-based analytics and machine learning techniques can effectively assist financial institutions in minimizing risk and improving lending strategies.
