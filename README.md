# End-to-End Credit Risk Optimisation: Underwriting, Affordability, and Collections
## Executive Summary
**The Business Problem:** A hypothetical consumer lending portfolio was experiencing high delinquency rates, eroding interest revenue through principal write-offs. Furthermore, the collections strategy for late-stage accounts lacked empirical validation, leading to inefficient resource allocation.

**The Solution:** This project restructures the entire credit lifecycle. By generating a synthetic dataset that mimics real-world financial behaviours, I conducted exploratory data analysis to identify primary risk drivers. I then engineered a dynamic rule-based decision engine, optimised approval score cut-offs for maximum profitability, and designed an A/B test to mathematically validate a new early-intervention collections strategy.

**Technical Environment & Methodology**
The project was developed in an Anaconda Jupyter Notebook environment running Python 3.11.4. The data pipeline relied on NumPy for vectorised mathematical operations and synthetic data generation, pandas for dataframe manipulation and P&L forecasting, seaborn and matplotlib for visual storytelling, and scipy for statistical significance testing.

## Phase 1: Synthetic Data Engineering
Because proprietary financial data is highly restricted, I built a 10,000-row synthetic dataset from scratch. Rather than using randomised noise, the script enforces realistic financial multicollinearity.

Income and Capacity: Annual income was generated using a right-skewed lognormal distribution.

Risk-Based Pricing: Interest rates were inversely correlated with credit scores.

Default Logic: A complex risk factor was engineered where high debt-to-income (DTI) ratios and maxed-out credit utilisation disproportionately pushed borrowers into default states.

## Phase 2: Exploratory Data Analysis (EDA)
To establish a baseline before writing new policies, I visualised the portfolio's structural health to prove exactly which variables were driving financial losses.

* **Portfolio Health & Baseline Risk:** The initial target distribution revealed a heavily distressed portfolio. While 45.5% of loans were 'Current' and 20.2% 'Paid Off', the combined "Bad Rate" sat at an unsustainable 33.6% (24.0% 'Default' and 9.6% 'Late (31-120)'). This established immediate mathematical proof that existing underwriting parameters were too loose.
* **Behavioural Risk by Loan Purpose:** Segmenting default rates by the stated loan purpose demonstrated clear macro-level behavioural risk. Uncategorized ("Other") and "Medical" loans drove the highest default rates, typical of unsecured, emergency-driven borrowing. Conversely, "Home Improvement" loans proved to be the safest segment, likely reflecting planned borrowing by homeowners with underlying assets.
* **Continuous Risk Drivers:** Visualising continuous financial metrics confirmed the specific parameters needed for the rule-based decision engine. The density curve for defaulted loans peaks at noticeably lower credit scores compared to good loans. Furthermore, boxplots reveal that the median Debt-to-Income (DTI) ratio is visibly higher for the default/late group, as is their median credit utilisation rate. This visually and mathematically validates that over-leveraged borrowers disproportionately drive losses.
* **Feature Correlation (Multicollinearity):** A correlation analysis validated the underlying logic of the portfolio's financial mechanics. There is a perfect inverse correlation (-1.00) between `credit_score` and `interest_rate`, confirming that risk-based pricing was strictly enforced. Additionally, strong positive correlations exist between `loan_amount` and `annual_income` (0.75), as well as `credit_score` and `annual_income` (0.65), aligning with realistic macroeconomic trends and affordability scaling.

## Phase 3: Score Cut-off Optimisation
To balance risk and reward, I simulated portfolio performance across a range of minimum credit score requirements, mapping Expected Net Profit against Approval and Default rates. The results revealed a severe systemic vulnerability in the portfolio's underlying product design.

* **The "Optimal" Threshold:** The simulation identified a credit score of 750 as the mathematically optimal cut-off.
* **Systemic Unprofitability:** Strikingly, the maximum expected portfolio profit at this optimal threshold remained deeply negative at -$3,342,696.45. The profit curve illustrates that raising the cut-off from 500 to 750 merely mitigated losses rather than generating actual profit. 
* **The Volume vs. Risk Trade-off:** At the 750 score cut-off, the approval rate plummets to a mere 2.1%, yet the default rate remains stubbornly high at 19.5%. 
* **Strategic Takeaway:** This analysis provides a critical executive insight: a single-variable strategy (simply raising the minimum credit score) cannot save this portfolio. Because even the highest-tier borrowers are generating net losses, the core underwriting parameters—specifically how credit limits are assigned relative to existing debt—are fundamentally flawed. The algorithm minimised losses by essentially halting loan originations altogether. This mandated the immediate development of the strict Affordability and DTI capping engine built in Phase 4.

## Phase 4: Affordability & Rule-Based Decision Engine
To resolve the systemic unprofitability identified in Phase 3, I engineered a dynamic, rule-based decision engine. Rather than relying solely on credit scores, this algorithm evaluates true borrower capacity by enforcing a strict 45% maximum Debt-to-Income (DTI) cap and dynamically scaling assigned credit limits to a maximum of 20% of annual income. 

* **Strategic Turnaround:** Shifting from a single-variable score cut-off to a multidimensional affordability assessment completely reversed the portfolio's financial trajectory. 
* **Approval & Exposure Control:** Out of 10,000 applications processed, the engine approved 719 loans (a 7.2% approval rate), resulting in a tightly controlled total credit exposure of $15,292,329.07.
* **Profit & Loss (P&L) Forecast:** By right-sizing loan limits to verified payment capacity, the portfolio generated an Expected Annual Interest Revenue of $1,929,198.61 against managed Expected Annual Credit Losses of $772,836.39.
* **The Bottom Line:** This strict affordability capping successfully transitioned the portfolio from a forecasted $3.34M loss into a sustainable, positive Forecasted Net Profit of $1,156,362.22.

## Phase 5: Collections Strategy A/B Testing
To optimise the back-end of the credit lifecycle, I analysed a simulated A/B test targeting the distressed "Late (31-120 days)" segment. The experiment compared standard agent outreach (Control, N=471) against an automated SMS flexible settlement offer (Treatment, N=485).

* **Performance Metrics:** The Treatment group yielded a slightly higher raw average recovery rate (25.12%) compared to the Control group (23.84%).
* **Statistical Significance:** Using a two-sample independent T-test with Welch's correction, the analysis produced a T-Statistic of 1.3647 and a P-Value of 0.1727.
* **Business Recommendation:** Because the P-value (17.27%) far exceeded the standard 5% threshold (alpha = 0.05), the difference in recovery rates was deemed statistically inconclusive. I recommended that senior stakeholders reject the new SMS strategy, protecting the business from investing resources into an operational change that the data could not confidently validate.
