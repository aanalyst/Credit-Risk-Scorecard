# Credit-Risk-Scorecard

| |
|---|
| **Background** |
| Australian banks and lenders are required to assess borrower creditworthiness before approving loans. Without a structured, data-driven approach, lenders expose themselves to significant credit losses — particularly from borrowers who appear low-risk but default unexpectedly. This project builds an end-to-end credit risk scorecard using **1,345,310 real loan records** from Lending Club (2007–2018), one of the largest peer-to-peer lending platforms. The dataset mirrors real-world lending patterns including borrower grades, income levels, debt obligations, and repayment outcomes. Reporting as a data analyst embedded in a credit risk function, an end-to-end analysis was conducted to identify default drivers, score borrower risk using logistic regression, and produce a points-based credit scorecard.

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
