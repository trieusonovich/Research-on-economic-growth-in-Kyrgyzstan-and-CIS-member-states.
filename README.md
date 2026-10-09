# Project-research-on-economic-growth-in-Kyrgyzstan-and-the-CIS-member-states.

# Economic Growth Modeling in Kyrgyzstan and CIS Member States

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![statsmodels](https://img.shields.io/badge/statsmodels-ARIMA%20%7C%20ARDL-informational)
![linearmodels](https://img.shields.io/badge/linearmodels-Panel%20Data-4B8BBE)
![pandas](https://img.shields.io/badge/pandas-data%20analysis-150458?logo=pandas&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)

An econometrics project that models economic growth in Kyrgyzstan and four other CIS member states (Kazakhstan, Tajikistan, Turkmenistan, Uzbekistan) using **time series analysis (ARIMA/ARDL)**, **Granger causality testing**, and **panel regression models** (Pooled OLS, Fixed Effects, Random Effects). The workflow covers stationarity testing, VAR-based causality analysis, multivariate ARIMAX modeling, and panel model comparison for the period 1992–2023.

---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Methodology](#methodology)
- [Key Results](#key-results)
- [Panel Regression Comparison](#panel-regression-comparison)
- [Limitations](#limitations)
- [Getting Started](#getting-started)
- [Tech Stack](#tech-stack)
- [Author](#author)

---

## Overview

Panel data econometrics allows researchers to capture both individual country characteristics and their dynamics over time. This project answers a focused question: **what drives GDP growth in Kyrgyzstan and the broader CIS region — domestic structural features (fixed effects) or region-wide shocks (random effects)?**

**Objectives**

1. Analyze GDP series and build univariate ARIMA models
2. Test Granger causality between GDP and aggregate demand components
3. Build a multivariate ARIMAX/ARDL model for Kyrgyzstan
4. Compare three panel regression specifications: Pooled OLS, FE, and RE
5. Select the most appropriate model for the region

## Dataset

| Property | Value |
|---|---|
| Frequency | Annual |
| Period | 1992 – 2023 (32 years) |
| Countries | Kazakhstan, Kyrgyzstan, Tajikistan, Turkmenistan, Uzbekistan |
| Observations | 160 (5 countries × 32 years) |
| File | `Panel data for the CIS member states.xlsx` |
| Target | `GDP` — gross domestic product (constant prices) |
| Features | `final consumption expenditure` |
| | `household consumption` |
| | `government consumption` |
| | `gross capital formation` |
| | `export` |
| | `import` |

**Why these variables?**
These are the components of aggregate demand, allowing the model to explain GDP variation through the expenditure approach: Y = C + I + G + (X − M).

## Methodology

**1. Exploratory analysis**
Time series plot and histogram of Kyrgyzstan's GDP; normality test on the distribution.

**2. Stationarity testing (ADF)**
Augmented Dickey-Fuller tests on levels, first differences, and second differences for all variables to determine the order of integration.

**3. Univariate ARIMA**
ACF/PACF inspection of the differenced GDP series to identify AR and MA orders.

**4. Granger causality (VAR-based)**
Bivariate VAR models between `d_y` (GDP first difference) and each stationary transformation of the exogenous variables, followed by F-tests on lagged coefficients.

**5. Multivariate ARDL / ARIMAX**
Selected exogenous variables (household consumption, exports, imports) included based on Granger results. Model comparison via AIC and BIC.

**6. Panel regression**
Three specifications estimated and compared:
- **Pooled OLS** — no country-specific effects
- **Fixed Effects (FE)** — entity and time effects
- **Random Effects (RE)** — with Hausman test for model selection

## Key Results

### Stationarity (ADF test)

| Variable | p-value (level) | p-value (1st diff) | p-value (2nd diff) | Order |
|---|---|---|---|---|
| Final consumption (x1) | 0.9991 | 0.1489 | 0.0280 | I(2) |
| Household consumption (x2) | 0.9989 | 0.0108 | — | I(1) |
| Government consumption (x3) | 0.1045 | 0.2378 | 0.0010 | I(2) |
| Gross capital formation (x4) | 1.0000 | 0.0024 | — | I(1) |
| Export (x5) | 0.4231 | 4.77e-10 | — | I(1) |
| Import (x6) | 0.9891 | 0.6090 | 0.0084 | I(2) |

**GDP itself:** non-stationary in levels (p = 0.9999), stationary in first difference (p = 0.00073) → I(1).

### Granger causality (VAR, maxlag = 1)

| Exogenous variable | p-value (X → d_y) | p-value (d_y → X) | Interpretation |
|---|---|---|---|
| d_d_x1 (final consumption) | 0.0690 | 0.0291 | One-way: GDP → consumption |
| d_x2 (household consumption) | 0.0271 | 0.9976 | One-way: consumption → GDP |
| d_d_x3 (gov. consumption) | 0.3714 | 0.7290 | No causality |
| d_x4 (capital formation) | 0.1217 | 0.5701 | No causality |
| d_x5 (export) | 0.0006 | 0.0339 | **Two-way** |
| d_d_x6 (import) | 0.0509 | 0.0375 | **Two-way** |

**Key insight:** Household consumption and exports are the strongest drivers of GDP growth. Government consumption and capital formation show no significant causal link.

### Final ARIMAX model (Kyrgyzstan, 1994–2023)

d_y = 114.16 + 0.342·d_x5 + 0.236·d_x2 + 0.106·d_x2₋₁



| Metric | Value |
|---|---|
| R² | 0.6977 |
| Adjusted R² | 0.6753 |
| AIC | 401.66 |
| BIC | 408.66 |
| Durbin-Watson | 1.5088 |
| Residual normality (p) | 0.3441 |

Exports and household consumption are significant at the 1% level. The lagged household consumption term is significant at ~11% but retained because it improves AIC.

## Panel Regression Comparison

### Pooled OLS

| Variable | Coefficient | p-value |
|---|---|---|
| const | −2234.3 | 0.0000 |
| final consumption | 0.0010 | 0.9569 |
| household consumption | 1.2008 | 0.0000 |
| government consumption | 1.0324 | 0.0039 |
| gross capital formation | 0.0725 | 0.1968 |
| export | 1.4803 | 0.0000 |
| import | −0.6789 | 0.0000 |

R² = 0.9939; but DW = 0.35 (strong autocorrelation), White test p ≈ 0 → heteroskedasticity, residuals non-normal (p ≈ 0).

### Fixed Effects (entity + time)

| Metric | Value |
|---|---|
| Within R² | 0.9848 |
| F-test for poolability | 3.2228 (p = 0.0000) |
| F-test for differing intercepts | F(4, 150) = 14.87 (p < 0.001) |

**Final FE coefficients:**

| Variable | Coefficient | p-value |
|---|---|---|
| household consumption | 1.2329 | 0.0000 |
| government consumption | 1.0216 | 0.0017 |
| gross capital formation | −0.1237 | 0.1202 |
| export | 1.8378 | 0.0000 |
| import | −0.5646 | 0.0000 |

### Model selection

| Test | Result | Conclusion |
|---|---|---|
| F-test for poolability | p < 0.001 | FE preferred over Pooled OLS |
| Hausman test | (RE rejected) | FE preferred over RE |
| Pesaran CD (cross-sectional dependence) | p > 0.05 | No cross-sectional dependence |
| Wald test for heteroskedasticity | p < 0.001 | Heteroskedasticity present |

**Final choice: Fixed Effects model with entity and time effects.**

**Key findings:**

- **Exports** have the strongest positive effect on GDP (coefficient ≈ 1.84), confirming the export-oriented nature of Central Asian economies.
- **Household consumption** is the second-largest driver (coefficient ≈ 1.23), highlighting the importance of domestic demand.
- **Imports** have a significant negative effect (−0.56), as expected from the expenditure identity.
- **Gross capital formation** is not statistically significant in the FE specification.
- The inclusion of time effects reduces the export coefficient, showing that part of export growth is driven by region-wide external shocks rather than country-specific factors.

## Limitations

- **Short time dimension.** Only 32 annual observations per country limits the power of panel unit root and cointegration tests.
- **Mixed integration orders.** Variables are integrated of different orders (I(1) and I(2)), complicating the use of standard panel cointegration methods.
- **No cross-sectional dependence modeling.** Although the Pesaran CD test does not reject, spatial spillovers between CIS economies may still exist.
- **Non-normal residuals.** The normality assumption is violated, though the Central Limit Theorem provides some justification given n = 160.

**Possible next steps:** apply panel ARDL (PMG) for mixed integration orders, include additional macro controls (inflation, FDI, human capital), and test for structural breaks around 2008 and 2020.

## Author

Nguyen Dinh Trieu

Economics (Analytical Economics and Econometrics)

trieu31072004@gmail.com
