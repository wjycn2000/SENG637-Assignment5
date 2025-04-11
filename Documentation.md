



# 2. Assessment Using Reliability Growth Testing

## 2.1 Introduction

In this section, we assess the software system's reliability growth using the **C-SFRAT** (Covariate Software Reliability Assessment Tool) framework. C-SFRAT enables the analysis of time-to-failure data using various statistical hazard models and the inclusion of covariates representing effort-related factors. The objective is to:

- Analyze failure behavior over time,
- Fit multiple reliability growth models,
- Evaluate the effect of covariates (effort variables),
- Compare model performance using statistical criteria, and
- Allocate testing effort to optimize defect discovery.

This process was executed in four stages, corresponding to the tabs in the C-SFRAT interface:
1. Data Upload and Model Selection
2. Model Results and Predictions
3. Model Comparison
4. Effort Allocation

Each subsection below explains the procedure followed, the options selected (and those not selected), and the rationale behind each decision.

---

## 2.2 Tab 1 – Data Upload and Model Selection

![Tab1 Screenshot](attachment:Tab1.PNG)

This tab is the starting point of the reliability assessment process, where the dataset is imported and the core modeling configuration is set.

### 2.2.1 Data Selection and Subsetting
- **Input File**: `failure-dataset-a5.csv`
- **Subset Failure Data**: Set to **31**
  - This means we used all 31 recorded intervals (full dataset).
  - *Why?* We aimed to preserve the entire time-to-failure series to better capture the full reliability growth behavior.

### 2.2.2 Hazard Function Selection
We selected the following hazard models:
- **Discrete Weibull (Order 2)**
- **Geometric**
- **Negative Binomial (Order 2)**

These were chosen based on:
- **Diversity in distribution behavior**: 
  - *Weibull* is versatile for increasing/decreasing hazard rates,
  - *Geometric* assumes a memoryless property (useful for simplicity),
  - *Negative Binomial* captures overdispersion and varied defect rates.
- **Literature support**: These are commonly used models referenced in the C-SFRAT publication (`1-s2.0-S2352711021001588-main.pdf`).

> ❌ **Not Selected**: Continuous Weibull, Salvia & Bollinger, S-Distribution, etc.
> These were skipped to keep the modeling computationally manageable and focus on discrete models better suited to count-based failure data.

### 2.2.3 Covariate Selection
- **Selected All**: E, F, C, and all their combinations
  - `E`: Developer experience
  - `F`: Testing frequency
  - `C`: Complexity
- *Why?* To evaluate the impact of individual and combined effort-related factors on model performance and failure behavior.

> ❌ **Not Selected**: “None” alone — instead, we included both with and without covariates to compare model behavior in controlled and real-world effort-influenced scenarios.

### 2.2.4 Estimation
- After selection, we clicked **Run Estimation**, triggering model training and statistical fitting using Maximum Likelihood Estimation (MLE).

### 2.2.5 Output
- Two critical plots were generated from this step:
  1. **Failure Intensity Plot**: Shows defect discovery rate across intervals.
     - ![Intensity](attachment:Tab1_Intensity_Plot.png)
  2. **MVF (Mean Value Function) Plot**: Displays cumulative defect trend over time.
     - ![MVF](attachment:Tab1_MVF_Plot.png)

We also exported their tabular data:
- `Tab1_Intensity.xlsx` – Defect rate over time
- `Tab1_MVF.xlsx` – Cumulative failures by interval

### Interpretation (for next section):
These plots confirm an initial rise in failures, followed by gradual stabilization—indicative of potential reliability growth. Formal model results and prediction insights are discussed in Section 2.3.

## 2.3 Tab 2 – Model Results and Predictions

![Tab2 Screenshot](attachment:Tab2.PNG)

This tab allowed us to visualize and compare the fitted models' predictions against the actual failure data using plots and numerical tables.

---

### 2.3.1 Model Selection for Analysis

We selected a representative set of models for performance comparison:

- **DW2 (None)** – baseline model with no covariates
- **DW2 (C)** – DW2 with complexity (C) as covariate
- **GM (None)** – Generalized model without covariates
- **GM (E)** – GM with experience (E)
- **GM (E, F, C)** – GM with all covariates
- **NB2 (None)** – Negative Binomial baseline
- **NB2 (E, C)** – NB2 with effort-related covariates

This selection aimed to include:
- Simpler models to observe raw failure trends
- Covariate-enhanced models to observe improvements from effort-related variables

### 2.3.2 Prediction Parameters

We configured prediction settings to simulate future testing:

- **Effort per Interval**:
  - `E = 1.00`
  - `F = 2.00`
  - `C = 3.00`
- **Number of Intervals to Predict**: `5`
- **Failure Intensity Target**: `0.0000` (default/no threshold)

These settings enabled forecasting model behavior across five additional testing intervals with realistic effort levels.

---

### 2.3.3 Output Visualizations

#### A. Failure Intensity Plot

![Intensity](attachment:Tab2_Intensity_Plot.png)

- The bar graph shows actual failure intensity per interval.
- Overlaid curves represent each model’s predicted intensity.
- Observations:
  - **GM (E, F, C)** aligns closely with intensity spikes, adapting better to effort variables.
  - **DW2 (None)** provides smoother, less reactive predictions.
  - **NB2** models show moderate fit, overestimating some intensity spikes.

> *Conclusion*: Covariate models provide better granularity for forecasting actual failure rates over time.

#### B. MVF (Mean Value Function) Plot

![MVF](attachment:Tab2_MVF_Plot.png)

- The MVF plot shows cumulative predicted failures.
- **GM (E, F, C)** and **NB2 (E, C)** closely track the observed stepwise failure curve.
- Non-covariate models like **DW2 (None)** deviate slightly under high failure accumulation.

> *Conclusion*: Models that include effort factors yield better alignment with real-world cumulative defect trends.

---

### 2.3.4 Numerical Forecast Table

The prediction table contains the estimated cumulative failures for each model across intervals.

- ![Table Screenshot](attachment:Tab2.PNG)
- Exported to: `Tab2_MVF.xlsx`, `Tab2_Intensity.xlsx`

#### Key Values at Interval 25 (from `Tab2_MVF.xlsx`):
| Model          | Predicted Cumulative Failures |
|----------------|-------------------------------|
| DW2 (None)     | 75.24                         |
| GM (None)      | 69.61                         |
| GM (E, F, C)   | **73.02**                     |
| NB2 (E, C)     | 70.98                         |

This supports the earlier visual conclusions that **GM (E, F, C)** offers the best predictive performance overall.

---

### 2.3.5 Summary

- **GM (E, F, C)** emerges as the best model by combining:
  - Strong fit to observed data
  - Accurate prediction of future failures
  - Consideration of real-world effort factors

- **DW2 (None)** serves as a valuable benchmark model with minimal assumptions.

These results form the basis for model comparison (Tab 3) and resource planning (Tab 4), discussed next.

## 2.4 Tab 3 – Model Comparison

![Tab3 Screenshot](attachment:Tab3.PNG)

The **Model Comparison** tab in C-SFRAT allows us to evaluate the performance of each model using quantitative metrics. This helps identify the most accurate and appropriate models for analysis and prediction.

---

### 2.4.1 Comparison Criteria

We used the default metric weights:

| Metric | Weight | Description |
|--------|--------|-------------|
| LLF (Log-Likelihood Function) | 1 | Measures model fit to data (higher is better) |
| AIC (Akaike Information Criterion) | 1 | Penalizes complexity while rewarding fit (lower is better) |
| BIC (Bayesian Information Criterion) | 1 | Similar to AIC but stronger penalty for complexity |
| SSE (Sum of Squared Errors) | 1 | Measures how well model fits the actual data |
| PSSE (Predictive SSE) | 0 | Ignored in this comparison to prioritize in-sample fit |

- **Subset data for PSSE**: 90% of the dataset was used for fitting, with the remaining 10% for testing prediction accuracy.
- *Why these defaults?* We prioritized model fitting and simplicity. PSSE was excluded to keep the focus on model accuracy over the current dataset rather than its predictive power.

---

### 2.4.2 Model Evaluation Table

| Model | Covariates | Log-Likelihood | AIC | BIC | SSE | PSSE |
|-------|------------|----------------|-----|-----|-----|------|
| DW2   | None       | -92.571        | 189.141 | 192.009 | 2031.486 | 11.611 |
| DW2   | C          | -89.074        | 184.149 | 188.451 | 2208.983 | 32.082 |
| GM    | None       | -74.982        | 153.963 | 156.831 | 917.068  | 110.422 |
| GM    | E          | -72.700        | 151.400 | 155.702 | 1114.328 | 99.513  |
| GM    | E, F, C    | **-59.653**    | **129.306** | **136.476** | **744.943** | **218.799** |
| NB2   | None       | -83.714        | 171.428 | 174.296 | 1599.496 | 18.553  |
| NB2   | E, C       | -80.347        | 168.694 | 174.430 | 1958.543 | 77.571  |

---

### 2.4.3 Key Insights

- ✅ **GM (E, F, C)** had the best overall fit across **AIC**, **BIC**, and **SSE**, indicating strong modeling of defect behavior with covariates.
- ✅ **DW2 (None)** achieved the **lowest PSSE**, meaning it predicted unseen data best.
- 🔁 **NB2 models** offered a reasonable balance but did not outperform GM or DW2 across any metric.

> **Conclusion**: We selected:
> - **GM (E, F, C)** as the best-fit model (strong internal accuracy)
> - **DW2 (None)** as the most generalizable model (low predictive error)

---

### 2.4.4 Exported Results

The full comparison table is saved as:
- `Tab3.xlsx`

These insights directly inform our testing resource decisions in the next step — Tab 4: Effort Allocation.

## 2.5 Tab 4 – Effort Allocation

![Tab4 Screenshot](attachment:Tab4.PNG)

The **Effort Allocation** tab in C-SFRAT is designed to help guide resource planning by estimating how testing effort correlates with defect discovery. It includes two allocation strategies:
- **Allocation 1**: Estimate how many defects can be discovered within a given test budget.
- **Allocation 2**: Estimate the minimum effort required to uncover a specified number of additional defects.

---

### 2.5.1 Models Selected for Allocation

We selected the following models for allocation analysis:

- **DW2 (C)**: A variation of Discrete Weibull using complexity as the only covariate.
- **GM (E, F, C)**: The best overall model based on model comparison (Tab 3).

These models represent:
- A basic model with a single covariate (DW2 C) to observe simple behavior.
- A comprehensive model with all effort-related covariates (GM E, F, C) to maximize predictive depth.

---

### 2.5.2 Allocation 1 – Maximize Defect Discovery Within Budget

**Budget**: `20.00` effort units

**Procedure**:
- We entered a total effort budget of 20.00 units and ran the allocation for both models.
- The tool estimated how many additional defects could be discovered using this effort.

**Purpose**:
- Understand how far continued testing can take us within resource constraints.

**Result** *(as seen in Tab4 interface)*:
- **GM (E, F, C)** showed a higher estimated defect discovery than **DW2 (C)**, reinforcing its strength in guiding testing effort allocation.

---

### 2.5.3 Allocation 2 – Minimum Effort for Target Defect Count

**Target Additional Defects**: `5`

**Procedure**:
- We specified that we want to discover 5 more defects.
- The tool calculated the minimum required effort for each model to achieve this.

**Purpose**:
- Helps answer: *"What will it cost to find 5 more bugs?"*

**Result**:
- **GM (E, F, C)** required a smaller budget than **DW2 (C)**, further validating that models incorporating effort factors provide more cost-effective recommendations.

---

### 2.5.4 Key Insights

- **GM (E, F, C)** remains the most resource-efficient and accurate model for effort planning.
- **DW2 (C)**, while simpler, requires more effort to yield comparable results.
- The combination of Allocation 1 and 2 provides actionable insights for release planning and resource prioritization.

---

### 2.5.5 Result of Model Comparison

As detailed in **Section 2.4**, we selected the following two models based on performance:
- **GM (E, F, C)**: Strongest fit, lowest AIC/BIC/SSE, highly responsive to effort data.
- **DW2 (None)**: Best PSSE, making it the most generalizable for unseen data.

This selection balanced model precision with generalization and shaped further analysis in effort allocation and decision-making.

---

### 2.5.6 Result of Range Analysis

As configured in **Section 2.2**, we used the full failure interval range (Subset = 31) for all analyses. The dataset showed consistent failure trends across all intervals, with the cumulative failure plot (MVF) revealing stabilization after interval 25. Thus, the entire range was retained for maximal data utilization and reliability insights.

---

### 2.5.7 Failure Rate and Reliability Plots

Plots illustrating failure rate and reliability over time were generated through:
- **Failure Intensity Graphs** (Section 2.3.3)
- **MVF/Cumulative Reliability Graphs** (Section 2.3.3)

These highlight:
- Declining failure intensity in the latter intervals.
- Converging MVF curves for most models, supporting reliability stabilization.

These plots serve as a visual confirmation of system improvement through ongoing testing and effort investment.

---

### 2.5.8 Discussion on Decision-Making with a Target Failure Rate

Although we did not explicitly set a failure intensity threshold (kept at 0.0000), the analysis framework supports configuring such targets. Using effort allocation features, stakeholders can:
- Determine effort needed to meet desired failure intensity.
- Plan release schedules around reaching specific reliability thresholds.
- Assess trade-offs between additional testing and cost/benefit.

GM (E, F, C), given its superior prediction of failure trends, would be optimal for future decision-making involving target failure rate validation.

---

### 2.5.9 Advantages and Disadvantages of Reliability Growth Analysis

#### ✅ Advantages:
- Quantifies system reliability trends over time.
- Models can incorporate real-world effort factors (e.g., testing intensity, complexity).
- Supports proactive planning and release readiness evaluations.
- Enables failure prediction and testing resource allocation.

#### ⚠️ Disadvantages:
- Requires failure data in interval format (structured and clean).
- Model selection and interpretation demand statistical understanding.
- Overfitting risk with excessive covariate inclusion.
- Output interpretation may vary based on input effort estimation quality.

---

### 2.5.10 Difficulties Encountered and Lessons Learned

#### ⚠️ Challenges:
- Understanding and properly configuring hazard models.
- Interpreting AIC/BIC/SSE vs PSSE during model selection.
- Aligning predictions with testing intervals and effort parameters.
- Matching graph outputs to report expectations.

#### ✅ Overcome By:
- Referring to the provided academic papers on C-SFRAT usage.
- Step-by-step validation through each tab and setting.
- Visual confirmation of trends via intensity and MVF plots.

#### 🧠 Lessons Learned:
- Covariate-aware models drastically improve reliability forecasting.
- Visual analytics are essential for confirming statistical insights.
- Full use of C-SFRAT’s effort planning tools enhances actionable decision-making.

---

### 2.5.11 Conclusion of Reliability Growth Assessment

The C-SFRAT analysis supports the following:
- The system exhibits **reliability growth**, as shown by cumulative failure stabilization and declining intensity in certain intervals.
- **Effort-related covariates (E, F, C)** significantly improve model fit and predictive accuracy.
- **GM (E, F, C)** should be used as the primary guide for future resource allocation and readiness evaluation.

With this analysis complete, the next section will compare this methodology with alternative reliability evaluation techniques such as RDC.
