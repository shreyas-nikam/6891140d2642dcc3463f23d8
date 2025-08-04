
# IRRBB Model Validation Simulator - Jupyter Notebook Specification

## 1. Notebook Overview

This Jupyter Notebook provides a simulated environment for validating an Interest Rate Risk in the Banking Book (IRRBB) model. It allows users to load, inspect, and validate a pre-existing IRRBB model, assess its assumptions, and analyze its performance under various scenarios.

**Learning Goals:**

*   Understand the key components of an IRRBB model (cash-flow engine, EVE/NII valuation, scenario generator).
*   Learn how to perform quantitative validation tests on an IRRBB model.
*   Understand the importance of documenting and reviewing model assumptions.
*   Learn how to build an automated monitoring dashboard to track key metrics.
*   Understand the key components of regulator ready validation & governance report.

**Expected Outcomes:**

*   Ability to load and inspect a pre-existing IRRBB model.
*   Ability to perform quantitative validation tests, including baseline PV reconciliation, unit tests, and historical back-testing.
*   Ability to compile and review an assumption register.
*   Ability to populate the enterprise Model Inventory and schedule annual/quarterly reviews.
*   Ability to produce a regulator-ready validation & governance report.
*   Ability to build an automated monitoring dashboard that tracks key metrics against risk limits.

## 2. Mathematical and Theoretical Foundations

This section will outline the relevant mathematical and theoretical concepts underpinning IRRBB model validation.

**2.1. Economic Value of Equity (EVE)**

EVE represents the difference between the present value of assets and the present value of liabilities.  Mathematically, it can be expressed as:

$$EVE = PV_{Assets} - PV_{Liabilities}$$

where:
*   $PV_{Assets}$ is the present value of all assets.
*   $PV_{Liabilities}$ is the present value of all liabilities.

The sensitivity of EVE to changes in interest rates is a key IRRBB metric.

**2.2. Net Interest Income (NII)**

NII is the difference between interest income and interest expense over a specific period.

$$NII = Interest\ Income - Interest\ Expense$$

IRRBB models forecast NII under different interest rate scenarios.

**2.3. Present Value Calculation**

The present value (PV) of a future cash flow is calculated using a discount rate.

$$PV = \sum_{t=1}^{n} \frac{CF_t}{(1 + r_t)^t}$$

where:
*   $CF_t$ is the cash flow in period $t$.
*   $r_t$ is the discount rate for period $t$.
*   $n$ is the number of periods.

Discount rates are typically derived from yield curves.  Different curves (e.g., OIS) may be used for different instruments or currencies.

**2.4. Rate Shock Scenarios**

Rate shock scenarios involve applying instantaneous changes (shocks) to the yield curve and observing the impact on EVE and NII.  For example, a +200bp parallel shift in the yield curve.

**2.5. Backtesting**

Backtesting involves comparing model outputs (e.g., ΔNII, ΔEVE) to realized values over a historical period.  Regression analysis can be used to assess the model's accuracy.  The core equation validated is whether modeled changes accurately reflect actual changes:

$$\Delta NII_{Model} \approx \Delta NII_{Actual}$$
$$\Delta EVE_{Model} \approx \Delta EVE_{Actual}$$

Where accuracy is determined using regression metrics like $R^2$.

**2.6. IRRBB Risk Limits**

IRRBB risk limits are established to constrain the bank's exposure to interest rate risk.  These limits are typically expressed as a percentage of Tier 1 capital.  For example:

$$|\Delta EVE| \leq 15\% \ Tier\ 1\ Capital$$

## 3. Code Requirements

This section details the code requirements for the Jupyter Notebook.

**3.1. Expected Libraries:**

*   `pandas`: For data manipulation and analysis (loading datasets, creating tables, etc.).
*   `numpy`: For numerical calculations and array operations.
*   `scipy`:  For statistical analysis (regression, etc.).
*   `matplotlib` and `seaborn`: For data visualization (creating charts and plots).
*   `yaml`: For reading risk limit configurations.
*   `pickle`: For loading the cash-flow engine, EVE/NII valuation, and scenario generator.
*   `docx`: For ALCO report template.
*   `json`: For Model Inventory
*   `pytest`: For runnning validation test suits

**3.2. Input/Output Expectations:**

*   **Input:**
    *   Uploaded Part 1 models (`models/part1/*.pkl`, `configs/*.yaml`).
    *   `gl_balance_sheet.xlsx`: For book-equity & PV reconciliation.
    *   `historical_rates_nii.csv`: For back-testing.
    *   `risk_limits.yaml`: For formal limits & triggers.
    *   `model_inventory_template.json`: For governance metadata.
    *   `alco_report_template.docx`: Formatting for quarterly governance packs.
*   **Output:**
    *   `tests/irrbb_validation_suite.py`: Includes unit, integration, back-test cases.
    *   `outputs/part2/validation_results.csv`: Stores numeric test outcomes & pass/fail flags.
    *   `02_IRRBB_Monitoring_Dashboard.ipynb`: Generates V-3, V-4, V-5; reads `risk_limits.yaml`.
    *   `outputs/part2/ALCO_Q{YYYYQ}.pdf`: Auto-exported from notebook using `nbconvert`.
    *   `governance/model_inventory_filled.json`: Populated template with metadata & sign-offs.
    *   `governance/IRRBB_Model_Validation_Memo.docx`: Summarises scope, tests, results, outstanding issues.

**3.3. Algorithms and Functions:**

The notebook will require implementation of the following algorithms and functions:

*   **Loading the IRRBB Model:** A function to load the persisted cash-flow engine, EVE/NII valuation, and scenario generator from the provided files.
*   **PV Reconciliation:** A function to reconcile the model's baseline PV with book equity values by major balance sheet line.
*   **Instrument Unit Tests:** A function to perform unit tests on sample instruments by manually calculating the impact of rate shocks and comparing the results to the model's output.
*   **Historical Backtesting:** A function to perform historical backtesting of ΔNII and ΔEVE sensitivities. The function should calculate the historical sensitivities using the model and compare them to the realized values.
*   **Assumption Register Compilation:** A function to compile and review the model's assumptions, including discount curves, betas, and prepayment rates.
*   **Risk Limit Simulation:** A function to simulate the impact of different risk limit configurations on key metrics.
*   **Model Inventory Population:** A function to populate the enterprise Model Inventory with relevant model metadata.
*   **ALCO Report Generation:** A function to generate a quarterly ALCO report.

**3.4. Visualization Requirements:**

The notebook will generate the following visualizations:

*   **V-1: Bar chart** – baseline PV vs book equity variance by major balance-sheet line.
*   **V-2: Waterfall chart** – manual vs model ΔPV for the sampled validation instruments (unit tests).
*   **V-3: Scatter/regression plot** – historical ΔNII (model) vs realized NII to illustrate back-test fit.
*   **V-4: Control-limit line chart** – rolling quarterly ΔEVE (% Tier 1) with breach markers at ±15%.
*   **V-5: Heat-map dashboard** – traffic-light view of key metrics (ΔEVE, ΔNII-Y1, model health score) for ALCO.

## 4. Additional Notes or Instructions

*   The notebook assumes that the user has a basic understanding of IRRBB concepts and model validation techniques.
*   The notebook is designed to be modular, allowing users to easily customize and extend the validation tests.
*   All file paths are relative to the notebook's location.
*   The validation process will follow a versioning rule. The model `.version` is incremented (e.g., `1.0.0` → `1.1.0`) after all validation fixes, and Part 2 artifacts will reference this new version.
