# A/B Testing: Evaluating a New Search Ranking Algorithm for an Online Travel Agency

## Project Overview

A leading online travel agency developed a new search ranking algorithm designed to improve booking conversion rates. Before rolling it out to all users, the Product team needed statistical evidence that the change delivers a **real improvement in conversions** without **negatively impacting booking speed**.

This project analyzes the A/B test results and provides a data-driven recommendation on whether to proceed with a full rollout.

---

## Business Context

When users search for hotels or flights, the order in which results appear matters. A better ranking means users find what they want faster, leading to more bookings. But changing the ranking is risky — if the new order confuses users, bookings could drop or take longer.

**The experiment:** Users were randomly split 50/50 into two groups:
- **Control** → existing search ranking (business as usual)
- **Variant** → new search ranking algorithm

**Rollout criteria (both must be met):**
1. **Primary metric (Conversion Rate):** Must show a statistically significant **increase**
2. **Guardrail metric (Time to Booking):** Must either show **no significant change** or a **decrease** (faster booking)

**Confidence Level:** 90% (α = 0.1)

---

## Datasets

| File | Description |
|------|-------------|
| `sessions_data.csv` | Session-level data: session ID, user ID, timestamps, booking time, time to booking |
| `users_data.csv` | User-level experiment assignment: user ID and experiment group (control/variant) |

### Schema

**sessions_data.csv**

| Column | Type | Description |
|--------|------|-------------|
| `session_id` | string | Unique session identifier |
| `user_id` | string | Unique user identifier (missing for non-logged-in users) |
| `session_start_timestamp` | string | When the session started |
| `booking_timestamp` | string | When a booking was made (missing if no booking) |
| `time_to_booking` | float | Minutes from session start to booking (missing if no booking) |

**users_data.csv**

| Column | Type | Description |
|--------|------|-------------|
| `user_id` | string | Unique user identifier |
| `experiment_group` | string | `control` or `variant` |

---

## Methodology

### Step 1: Data Preparation
- Loaded both datasets and merged them using a **LEFT JOIN** on `user_id` to preserve all sessions (including non-logged-in users)
- Created a binary `conversion` column: `1` if booking occurred, `0` otherwise

### Step 2: Sanity Check — Sample Ratio Mismatch (SRM)
- Verified the 50/50 user split using a **Chi-squared goodness-of-fit test**
- Ensures the experiment randomization worked correctly before trusting any results

### Step 3: Primary Metric Analysis — Conversion Rate
- Compared conversion rates between control and variant using a **Proportions Z-test** (appropriate for binary outcomes)
- Calculated effect size as relative change: `variant_mean / control_mean - 1`

### Step 4: Guardrail Metric Analysis — Time to Booking
- Compared average booking time between groups using an **Independent Samples T-test** (appropriate for continuous outcomes)
- Only included sessions where a booking actually occurred
- Calculated effect size using the same relative change formula

### Step 5: Decision
- Applied the predefined rollout criteria to make a go/no-go recommendation

---

## Results

### Sanity Check (SRM)
| Metric | Value |
|--------|-------|
| Chi-squared p-value | 0.8453 |
| Result | ✅ No mismatch — experiment setup is valid |

### Primary Metric — Conversion Rate
| Metric | Control | Variant |
|--------|---------|---------|
| Conversion Rate | 15.92% | 18.19% |
| **Effect Size** | **+14.22%** | |
| **p-value** | **0.0002** | |
| Result | ✅ Statistically significant positive effect |

### Guardrail Metric — Time to Booking
| Metric | Control | Variant |
|--------|---------|---------|
| Avg Time (minutes) | 15.01 | 14.89 |
| **Effect Size** | **-0.79%** | |
| **p-value** | **0.5365** | |
| Result | ✅ No significant negative effect |

### Final Decision

| Criterion | Status |
|-----------|--------|
| Conversion significantly increased | ✅ Yes |
| Time to booking not significantly worse | ✅ Yes |
| **Recommendation** | **🟢 FULL ROLLOUT** |

> The new search ranking algorithm delivers **14.22% more bookings** with no negative impact on booking speed. The improvement is statistically significant at the 90% confidence level with a p-value of 0.0002.

---

## Key Takeaways

1. **Always run a sanity check first** — Sample Ratio Mismatch can invalidate your entire experiment
2. **Choose the right statistical test** — Z-test for proportions (binary), T-test for continuous metrics
3. **Statistical significance ≠ practical significance** — you need both p-value (is it real?) and effect size (is it big enough to matter?)
4. **Guardrail metrics prevent unintended harm** — improving one metric shouldn't come at the cost of worsening another
5. **A/B testing is a decision-making framework** — the goal isn't just to find differences, but to make confident business decisions

---

## Tech Stack

- **Python 3.x**
- **Pandas** — data manipulation and merging
- **SciPy** — Chi-squared test, proportions z-test
- **Pingouin** — independent samples t-test
- **Statsmodels** — proportions z-test

---

## Project Structure

```
ab-testing-search-ranking/
│
├── data/
│   ├── sessions_data.csv
│   └── users_data.csv
│
├── notebooks/
│   └── ab_test_analysis.ipynb
│
├── README.md
└── requirements.txt
```

---

## How to Run

```bash
# Clone the repository
git clone https://github.com/chigozie22/ab-testing-search-ranking.git
cd ab-testing-search-ranking

# Install dependencies
pip install -r requirements.txt

# Open the notebook
jupyter notebook notebooks/ab_test_analysis.ipynb
```

---

## Author

**Chigozie Chinonso**
- [LinkedIn](https://linkedin.com/in/chigozie-johnclinton-chinonso)
- [GitHub](https://github.com/chigozie22)
