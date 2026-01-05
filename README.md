# Reproducible Pairs-Trading Research Pipeline

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Platform](https://img.shields.io/badge/Platform-Google%20Colab%20%7C%20Drive-orange)
![Status](https://img.shields.io/badge/Status-Completed-success)
![License](https://img.shields.io/badge/License-MIT-green)

## 📖 Overview

This repository hosts a robust, fault-tolerant backtesting pipeline designed for statistical arbitrage (Pairs Trading) in a mixed ETF-Equity universe.

Unlike typical backtests that prioritize "finding alpha" at all costs, this project prioritizes **research hygiene**, **auditability**, and **reproducibility**. The system is engineered to handle real-world constraints (free data quality, execution delays) and prevent common pitfalls like lookahead bias, survivorship bias, and overfitting.

📄 **Read the full Research Paper:** [A Reproducible Pairs-Trading Backtest (PDF)](./paper/Le_Reproducible_Pairs_Trading.pdf)

---

## 🚀 Key Engineering Features

This pipeline transforms a fragile research notebook into a production-grade research system:

* **🛡️ Strict Signal-Execution Separation:** Implements a rigorous data contract. Research logic uses `log-prices` for signal generation, while the execution engine uses a separate `OPEN/CLOSE` layer with explicit `event_flags` (splits/dividends). This prevents "future peaking" via vendor-adjusted prices.
* **💾 Atomic Writes & Drive-First Persistence:** Designed for ephemeral environments (Google Colab). The system uses atomic write protocols (`.tmp` -> rename) and checkpointing/resume logic to ensure data integrity during long-running simulations (10+ hours).
* **🔄 Run Isolation:** Every experiment generates a unique `RUN_DIR` with a full artifact bundle (logs, config snapshots, parquet streams), ensuring that results are always traceable to a specific codebase version.

## 🔬 Methodology & Bias Control

* **Universe:** Mixed US Universe (Sector ETFs, Major ETFs, Large-cap Equities).
* **Walk-Forward Validation:**
    * Formation Window: 12 Months
    * Trading Window: 6 Months
    * Step Size: 1 Month
* **🚫 Survivorship Bias Control:** Implements **Per-Window Eligibility Gating**. Instead of filtering for assets that exist "today" (global lookahead), the system computes data coverage *within* each formation window. Assets are only traded if they were valid *at that time*.
* **📉 Overfitting Quantification:** Uses **Combinatorial Symmetric Cross-Validation (CSCV)** to estimate the Probability of Backtest Overfitting (PBO).

---

## 📊 Key Results (2011–2025)

The strategy exhibits strong regime dependence, validating the need for rigorous sub-period analysis rather than single full-sample metrics.

| Metric | Value (Post-2020 Regime) | Full Sample (2011-2025) |
| :--- | :--- | :--- |
| **Annualized Sharpe** | **1.07** | 0.41 |
| **CAGR** | **9.68%** | 2.96% |
| **PBO (Overfitting Risk)** | - | **~0.28** (Moderate) |

### Recent Calendar Performance
* **2023 Return:** +18.33%
* **2024 Return:** +15.29%

> *Note: Detailed performance attribution, regime analysis, and PBO charts are available in the [Research Paper](./paper/Le_Reproducible_Pairs_Trading.pdf).*

---

## 📂 Project Structure

```text
reproducible-pairs-trading/
├── paper/
│   └── Le_Reproducible_Pairs_Trading.pdf   # Full Research Report
├── notebooks/
│   └── main_pipeline.ipynb                 # The core monolithic notebook (Modules 1-11)
├── src/                                    # (Optional) Modularized python scripts
├── requirements.txt                        # Python dependencies
├── README.md                               # This file
└── .gitignore
