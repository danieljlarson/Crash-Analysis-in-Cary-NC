# Traffic Crash Analysis in Cary, NC

Spatial and temporal analysis of 22,116 crash events in Cary, NC, using real municipal data to identify high-risk locations, model crash severity, and quantify when crashes are most likely to occur relative to traffic volume.

---

## Overview

Cary, NC has seen rapid population growth in recent years, bringing more vehicles, more new drivers on unfamiliar roads, and increased pedestrian activity. This project uses five years of crash records provided by the Town of Cary to answer two questions:

1. **Where** are crashes most concentrated, and how do roadway characteristics predict crash severity?
2. **When** are crashes most likely to occur at a known hotspot, after accounting for traffic volume?

Rather than treating all crashes equally, we built a custom **severity index** following North Carolina DOT (NCDOT) guidelines, weighting injuries and fatalities proportionally by the number of people involved. We also built a **binary severity classifier** (severe vs. property damage only) to cross-validate our findings across two different outcome measures.

---

## Data

- **Primary:** Town of Cary crash records via the National Incident Based Reporting System (NIBRS) — 22,116 crashes over 5 years through 2025
- **Secondary:** Hourly traffic volume counts from the Town of Cary Traffic Counts portal and NCDOT, merged by location and time to normalize crash risk by exposure

### Key Variables

| Variable | Description |
|---|---|
| `Latitude` / `Longitude` | Crash location (5 decimal point precision) |
| `Crash Date & Time` | Used to extract hour, day of week, temporal patterns |
| `Road Class` | Interstate, state secondary route, local street, private road, etc. |
| `Road Surface` | Smooth asphalt, gravel, wet, etc. |
| `Light Condition` | Daylight, dark with/without artificial lighting |
| `Weather` | Conditions at time of crash |
| `Traffic Control` | Signal present, no control, construction zone, etc. |
| `Fatalities` / `Injuries` | Used to construct severity measures |

### Severity Index Formula

Following NCDOT's Traffic Engineering Accident Analysis System (TEAAS) guidelines:

```
Severity Index = (8.4 × possible_injuries + 76.8 × fatalities) / total_people × log(total_people)
```

This adjusts proportionally for crash size — preventing large multi-vehicle crashes from artificially inflating the score.

---

## Methods

**Question 1 — Crash Severity**
- Logistic regression (binary severity: severe vs. PDO) evaluated with cross-validated accuracy and AUC
- Linear regression (continuous severity index) evaluated with 10-fold cross-validated MAE
- Predictors tested individually, additively, and with interaction terms

**Question 2 — Temporal Crash Risk**
- Spatial filtering to isolate the highest-crash-frequency hotspot (US-64 at US-1 Southbound On-Ramp)
- Traffic volume merged by hour from Town of Cary turning movement count datasets
- Natural spline regression to model expected traffic volume across the day
- Poisson regression to model crash counts, adjusting for traffic exposure
- Analysis replicated at a second location (Walnut St & Meeting St) to test generalizability
- Interactive Shiny app for exploring crash likelihood by time of day across multiple traffic surveys

---

## Key Findings

**Road class is the strongest individual predictor of severity**, outperforming surface condition, lighting, and weather across both the binary and continuous models. The full additive model achieved the best AUC (0.689) and lowest MAE (0.875).

**High accuracy is misleading.** With 86.3% of crashes being non-severe, a model guessing "non-severe" every time reaches ~86.6% accuracy — nearly identical to our best model. Confusion matrix analysis confirmed this, motivating the use of AUC and MAE as more honest evaluation metrics.

**Crash risk peaks mid-morning, not during rush hour.** After normalizing by traffic volume, crashes at the US-64/US-1 hotspot were disproportionately concentrated between 11 AM and 3 PM. Morning rush hours showed higher crash risk per 1,000 vehicles than evening rush, suggesting behavioral or environmental factors beyond volume alone.

**First contact doesn't determine possession** *(see corner kick project)* — similarly here, raw crash counts don't tell the full story without normalizing for exposure.

---

## Files

| File | Description |
|---|---|
| `CrashProject.html` | Full knitted RMarkdown report — data summary, maps, model results, visualizations |

> The interactive **Crash Likelihood Explorer Shiny app** was developed by a group member and can be accessed via the link embedded in the HTML report.

---

## Tools

R · ggplot2 · tidyverse · natural spline regression · Poisson regression · logistic regression · Shiny

---

## Authors

STOR 320.01 Group 11 — April 2025
