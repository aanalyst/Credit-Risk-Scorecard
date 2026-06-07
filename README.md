# Credit-Risk-Scorecard

| |
|---|
| **Background** |
| Australian banks and lenders are required to assess borrower creditworthiness before approving loans. Without a structured, data-driven approach, lenders expose themselves to significant credit losses; particularly from borrowers who appear low-risk but default unexpectedly. This project builds an end-to-end credit risk scorecard using **1,345,310 real loan records** from Lending Club (2007–2018), one of the largest peer-to-peer lending platforms. The dataset mirrors real-world lending patterns including borrower grades, income levels, debt obligations, and repayment outcomes. Reporting as a data analyst embedded in a credit risk function, an end-to-end analysis was conducted to identify default drivers, score borrower risk using logistic regression, and produce a points-based credit scorecard.

Dataset Source: Lending Club Loan Data (2007–2018Q4). Available on Kaggle: https://www.kaggle.com/datasets/wordsforthewise/lending-club

---

# North Star Metrics

| Metric | Value | Significance |
|---|---|---|
| **Loans Analysed** | 1,345,310 | Filtered from 2.26M to Fully Paid and Charged Off only |
| **Default Rate (Overall)** | 20% | 80/20 class split requiring balanced model design |
| **Grade A Default Rate** | 6% | Lowest risk band — near-prime borrowers |
| **Grade G Default Rate** | 50% | Highest risk band — one in two loans defaults |
| **ROC-AUC** | 0.71 | Solid performance on real-world noisy data |
| **Recall (Defaulters)** | 67% | Model catches two-thirds of actual defaults |
| **False Negatives in Low/Medium Band** | 16,560 | Missed defaulters hiding in apparently safe risk bands |
| **Scorecard Range** | 572–589 | Points scale anchored at 600 = 50:1 odds (PDO = 20) |
| **Very High Risk Default Rate** | 55% | vs 4% for Very Low — 13x separation across risk bands |

---

# Executive Summary

## Exploratory Data Analysis

**1. Grade Is the Strongest Single Predictor of Default**

![Default Rate by Grade](https://github.com/aanalyst/Credit-Risk-Scorecard/raw/main/Default%20Rate%20by%20Grade.png)

The default rate by grade chart shows a near-perfect staircase from A (6%) to G (50%). No other variable in the dataset produces this level of separation between low and high risk borrowers. Grade is Lending Club's own internal risk assessment and the data confirms it is doing its job accurately.

The implication for modelling: grade will be the dominant feature in any credit risk model trained on this dataset. However, grade and interest rate are highly correlated (lower grade = higher rate), meaning both variables are partially measuring the same underlying risk signal. This multicollinearity is noted as a known limitation.



**2. DTI Shows a Clear But Weaker Risk Gradient**

![Default Rate by DTI](https://github.com/aanalyst/Credit-Risk-Scorecard/raw/main/Default%20Rate%20by%20DTI.png)

Borrowers with a debt-to-income ratio above 40 default at 31% more than double the rate of borrowers with DTI below 10 (15%). The relationship is consistent and monotonic, confirming DTI as a meaningful predictor.

The separation is weaker than grade (31% vs 15% compared to 50% vs 6%), which tells us DTI alone is insufficient for credit decisioning. It adds signal but should not be used in isolation.

**3. FICO Score Predicts Default in the Opposite Direction**

![Default Rate by FICO Score](https://github.com/aanalyst/Credit-Risk-Scorecard/raw/main/Default%20Rate%20by%20FICO%20Score.png)

As expected, higher FICO scores correspond to lower default rates. Borrowers in the 780–850 band default at 7%; those in the 660–700 band default at 23%. The inverse relationship confirms FICO is functioning as intended as an independent creditworthiness measure.

A key observation: the lowest FICO bucket (580–620) is empty in this dataset. Lending Club applied a minimum credit score threshold at the application stage — meaning the dataset exhibits survivorship bias. The model was trained only on approved loans, not the full applicant population. This likely causes the model to underestimate risk for borderline borrowers who would have been rejected in practice.

**4. Loan Purpose Reveals Structural Risk Differences**

![Default Rate by Purpose](https://github.com/aanalyst/Credit-Risk-Scorecard/raw/main/Default%20Rate%20by%20Purpose.png)

Small business loans default at 30% — the highest of any purpose category. Wedding loans default at 12% — the lowest. The gap reflects structural differences in risk: small business borrowers face factors entirely outside their control (market conditions, competition, economic downturns) and typically borrow larger amounts. Wedding borrowers have a fixed, one-time expense with predictable repayment behaviour.

This insight supports purpose as a meaningful feature and provides a rationale for risk-based pricing differentiation by loan type.

---

## Model Performance

**Logistic Regression with Class Balancing and Feature Scaling**

| Metric | Value |
|---|---|
| ROC-AUC | 0.71 |
| Recall — Defaulters (Class 1) | 0.67 |
| Precision — Defaulters (Class 1) | 0.31 |
| Overall Accuracy | 0.64 |

Two design decisions materially improved model performance:

`class_weight='balanced'` — The dataset has an 80/20 class split. Without balancing, the model would achieve high accuracy by predicting "no default" for almost every loan. Balanced weighting forces the model to treat missed defaults as costly errors.

`StandardScaler` — Features like `loan_amnt` (range: hundreds to $40,000) and `emp_length` (range: 0–11) operate on completely different scales. Without scaling, the model assigns disproportionate weight to larger-magnitude features. Scaling lifted recall from 0.60 to 0.67 and ROC-AUC from 0.70 to 0.71.


**Why ROC-AUC of 0.71 Is Appropriate for This Dataset**

Real-world credit data is inherently noisier. A 0.71 ROC-AUC on 1.3 million real Lending Club records is consistent with published benchmarks for logistic regression on this dataset (typical range: 0.68–0.72).

---

## SQL Risk Analysis

Three queries were run against a SQLite database of model predictions to support credit decisioning:

**Query 1 — Default Rate by Risk Band**

| Risk Band | Default Rate |
|---|---|
| Very Low | 4.1% |
| Low | 10.1% |
| Medium | 20.1% |
| High | 35.4% |
| Very High | 54.9% |

Very High risk band has 54% default rate which is the highest as expected. Very low has only 4% which implies that higher risk equals higher interest rate and lower risk equals lower interest rate. The risk band could directly map to an interest rate tier.

**Query 2 — Average Default Probability by Risk Band**

| Risk Band | Avg Default Probability |
|---|---|
| Very Low | 0.1576 |
| Low | 0.3095 |
| Medium | 0.4989 |
| High | 0.6821 |
| Very High | 0.8318 |

**Query 3 — False Negatives by Risk Band**

| Risk Band | Missed Defaulters |
|---|---|
| Very Low | 1,032 |
| Low | 7,855 |
| Medium | 8,705 |
| High | 0 |
| Very High | 0 |

The High and Very High bands have zero false negatives — the model correctly flags almost all borrowers in these bands as risky. The danger zone is Low and Medium, where 16,560 actual defaulters were classified as safe. These are the borrowers who cost lenders the most money precisely because they look like normal, creditworthy customers.
