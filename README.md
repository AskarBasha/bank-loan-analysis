# 🏦 Bank Loan Analysis | Power BI Dashboard

An end-to-end loan portfolio analysis built with SQL Server and Power BI. The report has three interactive dashboards (Summary, Overview, Details) that track lending KPIs, month-over-month trends, and the split between good and bad loans.

# Summary Dashboard

📌 Table of Contents
Problem Statement
Tools & Skills
Dataset
Data Model
Dashboards
DAX Measures
Key Insights
Project Structure
How to Use
License
Author
# 🎯 Problem Statement

The bank needs a clear view of its lending operations: how many loans are being issued, how much is funded versus recovered, and how healthy the portfolio is. This project answers that with three dashboards.

Dashboard 1: Summary

Five KPIs, each with overall, Month-to-Date (MTD) and Month-over-Month (MoM) values: Total Loan Applications, Total Funded Amount, Total Amount Received, Average Interest Rate, Average DTI
Good Loan vs Bad Loan KPIs: application %, applications, funded amount, amount received
Loan Status grid view

Dashboard 2: Overview

Monthly trend by issue date (line chart)
Regional analysis by state (filled map)
Loan term analysis (donut chart)
Employment length analysis (bar chart)
Loan purpose breakdown (bar chart)
Home ownership analysis (tree map)

Dashboard 3: Details

A consolidated loan-level grid with slicers for State, Grade and Good vs Bad Loan
# 🛠 Tools & Skills
Area	Used
SQL (MS SQL Server)	Database and table creation, SELECT, GROUP BY, ORDER BY, COUNT, DISTINCT, CAST, DECIMAL, DATENAME, DATEPART, MONTH, QUARTER, DAY, HOUR, CTEs, PARTITION
Power BI	SQL Server connection, Power Query, data cleaning and modelling, date table, time-intelligence DAX, CALCULATE, SUM/SUMX, KPI cards, charts, formatting, page navigation
# 📂 Dataset

Table: bank_loan_data (loaded from SQL Server via SSMS)

Column	Column	Column
id	address_state	application_type
emp_length	emp_title	grade
sub_grade	home_ownership	issue_date
last_credit_pull_date	last_payment_date	next_payment_date
loan_status	member_id	purpose
terms	verification_status	annual_income
dti	installment	int_rate
loan_amount	total_acc	total_payment

Good vs Bad Loan grouping: Fully Paid and Current = Good Loan; Charged Off = Bad Loan.

# 🧩 Data Model

Data Model

Date Table: CALENDAR(MIN(issue_date), MAX(issue_date)), related one-to-many to bank_loan_data
Select Measure: a disconnected table that lets the user switch the measure shown in the Overview charts
Report background colour: 
#00083D
# 📊 Dashboards
1. Summary
<img width="1327" height="740" alt="Screenshot 2026-10-24 165120" src="https://github.com/user-attachments/assets/78d6e770-e0fa-46b0-a4dc-fc3534603118" />

2. Overview
<img width="1327" height="747" alt="Screenshot 2026-10-24 165145" src="https://github.com/user-attachments/assets/d1748779-72ea-4d30-9b05-8adf58219c3c" />


3. Details
<img width="1321" height="742" alt="Screenshot 2026-10-24 165213" src="https://github.com/user-attachments/assets/79a15a84-a844-477f-9170-96a71d3f5fa4" />


# 🧮 DAX Measures

Calendar table

DAX
Date Table = CALENDAR(MIN(bank_loan_data[issue_date]), MAX(bank_loan_data[issue_date]))
Month = FORMAT('Date Table'[Date], "mmmm")

Total Loan Applications

DAX
Total Loan Applications = COUNT(bank_loan_data[id])

MTD Loan Applications = CALCULATE(TOTALMTD([Total Loan Applications], 'Date Table'[Date]))

PMTD Loan Applications =
CALCULATE([Total Loan Applications], DATESMTD(DATEADD('Date Table'[Date], -1, MONTH)))

MoM Loan Applications =
([MTD Loan Applications] - [PMTD Loan Applications]) / [PMTD Loan Applications]

Total Funded Amount

DAX
Total Funded Amount = SUM(bank_loan_data[loan_amount])
MTD Funded Amount = CALCULATE(TOTALMTD([Total Funded Amount], 'Date Table'[Date]))
PMTD Funded Amount =
CALCULATE([Total Funded Amount], DATESMTD(DATEADD('Date Table'[Date], -1, MONTH)))
MoM Funded Amount = ([MTD Funded Amount] - [PMTD Funded Amount]) / [PMTD Funded Amount]

Total Amount Received

DAX
Total Amount Received = SUM(bank_loan_data[total_payment])
MTD Amount Received = CALCULATE(TOTALMTD([Total Amount Received], 'Date Table'[Date]))
PMTD Amount Received =
CALCULATE([Total Amount Received], DATESMTD(DATEADD('Date Table'[Date], -1, MONTH)))
MoM Amount Received = ([MTD Amount Received] - [PMTD Amount Received]) / [PMTD Amount Received]

Average Interest Rate

DAX
Average Interest Rate = AVERAGE(bank_loan_data[int_rate])
MTD Avg Int Rate = CALCULATE(TOTALMTD([Average Interest Rate], 'Date Table'[Date]))
PMTD Avg Int Rate =
CALCULATE([Average Interest Rate], DATESMTD(DATEADD('Date Table'[Date], -1, MONTH)))
MoM Avg Int Rate = ([MTD Avg Int Rate] - [PMTD Avg Int Rate]) / [PMTD Avg Int Rate]

Average DTI

DAX
Avg DTI = AVERAGE(bank_loan_data[dti])
MTD Avg DTI = CALCULATE(TOTALMTD([Avg DTI], 'Date Table'[Date]))
PMTD Avg DTI = CALCULATE([Avg DTI], DATESMTD(DATEADD('Date Table'[Date], -1, MONTH)))
MoM Avg DTI = ([MTD Avg DTI] - [PMTD Avg DTI]) / [PMTD Avg DTI]

Good vs Bad Loan

DAX
Good Loan Applications = CALCULATE([Total Loan Applications], bank_loan_data[Good vs Bad Loan] = "Good Loan")
Good Loan % = [Good Loan Applications] / [Total Loan Applications]
Good Loan Funded Amount = CALCULATE([Total Funded Amount], bank_loan_data[Good vs Bad Loan] = "Good Loan")
Good Loan Amount Received = CALCULATE([Total Amount Received], bank_loan_data[Good vs Bad Loan] = "Good Loan")

Bad Loan Applications = CALCULATE([Total Loan Applications], bank_loan_data[Good vs Bad Loan] = "Bad Loan")
Bad Loan % = [Bad Loan Applications] / [Total Loan Applications]
Bad Loan Funded Amount = CALCULATE([Total Funded Amount], bank_loan_data[Good vs Bad Loan] = "Bad Loan")
Bad Loan Amount Received = CALCULATE([Total Amount Received], bank_loan_data[Good vs Bad Loan] = "Bad Loan")
# 💡 Key Insights
38.6K loan applications, $435.8M funded and $473.1M received
86.2% of loans are good (33.2K); 13.8% are bad (5.3K)
Bad loans were funded with $65.5M but only $37.3M was received
Charged-off loans carry a higher average interest rate (13.88%) than fully paid loans (11.64%)
Monthly applications grew from 2.3K (Jan) to 4.3K (Dec)
Debt consolidation is the top purpose (about 18K applications)
73.2% of loans have a 36-month term
Borrowers with 10+ years of employment apply the most; California leads by state

# 👤 Author

Askar Basha R 
Data Analytics | Power BI • SQL • Tableau • Python • Excel
