# 🏆 SportStats: 120 Years of Olympic History

**Coursera Data Science Capstone Final Project**

---

## 📌 1. Project Proposal & Framework

### 👤 Dataset Selection, Target Audiences & Objectives

* **Client Chosen:** Client 3 — SportsStats
* **Dataset:** 120 Years of Olympic History (`athlete_events.csv`, 271,116 records) containing physical metrics, event outcomes, and national metadata.
* **Target Audiences:**
  * **Sports Media Analysts & Journalists:** Seeking historical narrative storylines, regional achievements, and non-inflated efficiency metrics.
  * **Athletic Directors & Personal Trainers:** Seeking health, aging, and biometric benchmarks across athletic disciplines.
* **Core Objective:** Transforms raw historical records into actionable insights across demographic, geographic, and sport-specific dimensions.

---

### 💾 Data Import & Data Cleaning Pipeline

1. **Extract / CSV Ingestion:**
   * Imported 271,116 raw records into a Pandas DataFrame.
   * Validated schema, initial row counts, and data structure from `athlete_events.csv`.
2. **Clean / Nulls & Data Types:**
   * Standardized data types and imputed missing values.
   * Filtered out non-athletic competitors from historical Art Competitions.
   * Replaced 85.3% `NaN` medal entries with `'No Medal'`; cast `Year` to integer and `Age` to nullable integer (`Int64`).
3. **Transform / Feature Engineering:**
   * Engineered custom physical metrics ($\text{BMI}$, $\Delta\text{BMI}$) for trainer profiling.
   * Built event-level deduplication models ($\text{eMER}$) to enable comparative national efficiency benchmarks.
4. **Load / SQLite Export:**
   * Exported the clean relational dataset into `olympics.db`.
   * Created relational tables via Python's `sqlite3` library.

---

### 🔍 Initial Data Exploration & Key Statistics

#### Dataset Volume & Integrity Metrics

| Metric | Count |
| :--- | :--- |
| **total_records** | 271116 |
| **unique_athletes** | 135571 |
| **total_countries** | 230 |
| **total_sports** | 66 |
| **missing_age_count** | 9474 |
| **missing_height_count** | 60171 |
| **missing_weight_count** | 62875 |
| **total_medals_awarded** | 39783 |

* **Comprehensive Volume:** 271,116 total records across 135,571 unique athletes, 66 sports, and 39,783 medals awarded.
* **National Delegation Footprint:** 230 total country codes (NOCs)—exceeding the 187 modern sovereign states due to historical delegation realignments (e.g., URS vs. RUS, FRG/GDR vs. GER).
* **Disciplines & Historical Artifacts:** Includes 66 total sports/disciplines, encompassing non-traditional historical categories such as the Olympic Art Competitions (held from 1912 to 1948 across architecture, literature, music, painting, and sculpture) alongside standard Winter and Summer sports.
* **Missing Data Profile & Handling:**
  * **Missing Age:** 9,474 records (~3.5%). Low missingness; safe to impute via sport/era medians or omit for age-peak aggregations without biasing overall distributions.
  * **Missing Height:** 60,171 records (~22.2%).
  * **Missing Weight:** 62,875 records (~23.2%).
  * **Missingness Context:** Missing physical metrics are heavily concentrated in early Olympic Games (pre-1960s) and non-athletic events (Art Competitions). Biometric analyses filter for complete records to preserve statistical validity.

#### Key Exploration Highlights

1. **Era & Season Breakdown (Demographic Progression):**
   * **Early Era (1896–1919 Summer):** Female medalists averaged 29.7 years of age (youngest 15, oldest 63), but represented a tiny fraction of total medals (63 female vs. 2,161 male).
   * **Modern Era Expansion (2000–Present Summer):** Female medalists grew to 4,055 (average age 26.6), closing the historical medal gap and demonstrating major longitudinal growth across games.
2. **Discipline-Specific Baseline Highlights:**
   * **Equestrianism (Longevity Benchmark):** Showcases peak athletic longevity with average medalist ages of 34.1 (Female) and 34.2 (Male), demonstrating how equestrian sports favor experience over youthful explosive power.
   * **Gymnastics (Agility & Power-to-Weight):** Lowest average BMI recorded (19.52 Female / 22.53 Male), confirming agility prioritization.
   * **Weightlifting (Muscle Density):** Highest average BMI recorded (25.96 Female / 28.04 Male), confirming high muscle mass requirements.

---

### ❓ Initial Questions & Hypotheses

* **Q1 (Media & History):** How has national dominance evolved over time across Summer and Winter Games, and which nations consistently lead?
* **Q2 (Biometrics & Performance):** How do physical attributes (Height, Weight, BMI) and average peak ages differ across athletic disciplines?
* **Q3 (Demographics & Participation):** How has female participation grown relative to male participation across different Olympic eras?
* **H1 (Biometrics):** Power/strength sports (e.g., Weightlifting) maintain significantly higher average BMI than agility/speed sports (e.g., Gymnastics, Swimming).
* **H2 (Age Peaks):** High-impact agility/aquatic sports favor younger athletes, whereas technical precision sports (e.g., Shooting) exhibit longer career spans.
* **H3 (Dominance Shifts):** Medal efficiency shifted from early Western dominance through Cold War volume to modern hyper-specialization.

---

## 📐 2. Technical Approach, Data Architecture & Feature Engineering

### 🗄️ Database Architecture (Star Schema Evolution)

> 📷 **[Figure 1: 5-Table Star Schema ERD Architecture — `Fact_Medals`, `Dim_Athlete`, `Dim_Games`, `Dim_NOC`, `Dim_Event`]**

* **Initial Proposal Schema (4 Tables):** The original proposal outlined a standard 4-table relational database consisting of:
  1. `Fact_Medals` (Medal achievements & athlete ties)
  2. `Dim_Athlete` (Demographics & biometrics: Age, Height, Weight, Sex)
  3. `Dim_Games` (Edition metadata: Year, Season, Host City)
  4. `Dim_NOC` (National delegation metadata & historical code mappings)
* **Schema Iteration (Adding 5th Table — `Dim_Event`):** Added `Dim_Event` as a 5th dimension table specifically to isolate unique event instances. This architectural iteration was essential to deduplicate team-sport medal inflation (e.g., preventing 12 basketball team medals from counting as 12 separate national tournament victories).
* **Data Cleaning & Standardization:** Standardized historical NOC code changes (e.g., URS $\rightarrow$ RUS, FRG/GDR $\rightarrow$ GER) to ensure longitudinal consistency across 120 years of Olympic history.

---

### 📋 Data Overview & Summary Statistics

| Metric | Count | Mean | Std | Min | 50% (Median) | Max | Skew |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Age** | 261642.0 | 25.56 | 6.39 | 10.0 | 24.0 | 97.0 | N/A |
| **Height** | 210945.0 | 175.34 | 10.52 | 127.0 | 175.0 | 226.0 | 0.02 |
| **Weight** | 208241.0 | 70.70 | 14.35 | 25.0 | 70.0 | 214.0 | 0.80 |
| **BMI** | 206853.0 | 22.78 | 2.91 | 8.36 | 22.53 | 63.90 | 1.40 |

* **Distribution & Skewness:** Height exhibits a near-perfect normal distribution (Mean = 175.3 cm, Skew = 0.02). Weight (Skew = 0.80) and BMI (Skew = 1.40) show heavy right-skewness due to heavyweight strength disciplines.
* **Extreme Ranges:** Age spans 10 to 97 years (Mean = 25.6), reflecting youth gymnasts vs. equestrian veterans. BMI spans 8.36 to 63.90 (Mean = 22.8).
* **Data Quality & Purpose:** Validated raw distributions, flagged non-athletic noise (Art Competitions), and established physical baselines prior to feature engineering.

---

### 🛠️ Custom Engineered Metrics

* **Athlete-Level Medal Efficiency Ratio (MER):**
  $$\text{MER} = \frac{\text{Total Medals}}{\text{Unique Athletes Sent}}$$
  *Purpose:* Measures raw national yield, but is susceptible to team-sport inflation (e.g., 12 individual medals for 1 basketball win).
* **Event-Level Medal Efficiency Ratio (eMER):**
  $$\text{eMER} = \frac{\text{Deduplicated Event Medals}}{\text{Unique Athletes Sent}}$$
  *Purpose:* Normalizes performance by counting 1 medal per event victory, isolating true national delegation yield.
* **BMI Deviation Score ($\Delta\text{BMI}$):**
  $$\Delta\text{BMI} = \text{Athlete BMI} - \text{Sport Baseline BMI}$$
  *Purpose:* Quantifies individual physical departures from discipline norms for coaching diagnostics and positional profiling.

---

## 📊 3. Key Findings & Proven Hypotheses

### 🏋️ Hypothesis 1: Biometric Specialization (Proven)

#### SQL Aggregation Results

| Sport | Sex | Average BMI |
| :--- | :--- | :--- |
| **Gymnastics** | F | 19.52 |
| **Gymnastics** | M | 22.53 |
| **Swimming** | F | 20.87 |
| **Swimming** | M | 22.92 |
| **Athletics** | F | 20.91 |
| **Athletics** | M | 22.69 |
| **Weightlifting** | F | 25.96 |
| **Weightlifting** | M | 28.04 |

* **Outcome:** Validated. Mainstream speed and endurance disciplines (Athletics & Swimming) establish a consistent baseline of 20.87–22.92 BMI. Power sports maintain a **5.5 to 6.4 BMI point elevation** over agility-focused disciplines.

---

### ⏱️ Hypothesis 2: Age & Athletic Peak (Proven)

#### SQL Aggregation Results

| Sport | Medals | Avg_Age | Youngest | Oldest |
| :--- | :--- | :--- | :--- | :--- |
| **Swimming** | 1637 | 20.9 | 12 | 46 |
| **Gymnastics** | 1252 | 23.4 | 10 | 45 |
| **Weightlifting** | 500 | 25.3 | 17 | 40 |
| **Shooting** | 734 | 33.4 | 16 | 72 |

* **Outcome:** Validated. Proves a **12.5-year gap** between early-peak aquatic sports (Swimming at 20.9 yrs) and extended-longevity precision disciplines (Shooting at 33.4 yrs, spanning up to age 72).

---

### 🌍 Hypothesis 3: Geographic Dominance Shifts (Proven)

> 📷 **[Figure 2: Evolution of eMER National Efficiency Across Eras (Early Western Volume $\rightarrow$ Cold War Expansion $\rightarrow$ Modern Global Hyper-Specialization)]**

* **Outcome:** Validated. Top-tier efficiency evolved from Early Western dominance through Cold War volume dominance (where high efficiency required massive delegations) to modern global hyper-specialization.
* **Modern Leaders:** Smaller, focused delegations like **Ethiopia**, **Jamaica**, and **Georgia** lead the world in yield per athlete sent.

---

### 📈 Regressions & Demographic Correlations

> 📷 **[Figure 3: OLS Linear Regression of Female Olympic Participation Growth (+0.365%/year, R² = 0.896)]**

* **Female Participation Growth (OLS Fit):** Linear regression demonstrates steady historical growth ($+0.365\%$/year, $R^2 = 0.896$), projecting $43.0\%$ baseline representation by 2028. Recent proactive IOC policies have further accelerated growth past historical linear baselines.

> 📷 **[Figure 4: Pearson Correlation Matrix — Physical Biometrics & Age Independence]**

* **Physical Correlations (Pearson Matrix):**
  * **Weight vs. BMI ($r = 0.831$)** dominates **Height vs. BMI ($r = 0.337$)**, proving BMI variance is driven by muscle density rather than height.
  * **Age Independence ($r \approx 0.14\text{--}0.22$):** Confirms body composition is discipline-selected rather than age-dependent.

---

## 💡 4. Insights Discovered & Lessons Learned

> 📷 **[Figure 5: Raw Athlete-Level MER vs. Deduplicated Event-Level eMER Comparison]**

* **Metric Normalization (MER vs. eMER):** Raw MER is distorted by large team-sport rosters (USA, Russia, Germany). Deduplicated `eMER` isolates hyper-specialized, high-yield delegations such as Cyprus (1.581), Iceland (1.580), and Moldova (1.551).

> 📷 **[Figure 6: Historical Biometric Drift — Weightlifting vs. Gymnastics & Swimming over 120 Years]**

* **Biometric Specialization & Physical Outlier Profiling:** Historical biometric analysis reveals that Weightlifting steadily expanded its body mass baseline over time, while Gymnastics and Swimming experienced a parallel drop toward leaner modern baselines and Athletics remained remarkably stable. Heavyweight combat and strength disciplines exhibit extreme individual positive deviations up to $+35 \text{ kg/m}^2$ above sport norms.

---

## 🏁 5. Conclusion

* **Hypothesis 1 (Biometric Specialization — Proven):** Physical profiles are strictly discipline-bound. Power-based Weightlifting maintains a 5.5 to 6.4 BMI point elevation over speed and agility disciplines like Gymnastics and Swimming.
* **Hypothesis 2 (Age & Athletic Peak — Proven):** SQL analysis reveals a 12.5-year gap in average medalist age between early-peak aquatic sports and technical precision disciplines.
* **Hypothesis 3 (Geographic Dominance Shifts — Proven):** Top-tier Olympic efficiency shifted from Cold War era volume dominance to modern global hyper-specialization. Today, smaller, focused delegations like Ethiopia, Jamaica, and Georgia lead the world in yield per athlete sent.
* **Feature Engineering & Metric Normalization:** Raw athlete-level MER is distorted by team-sport size. Deduplicated event-level MER (`eMER`) correctly isolates true national efficiency, while Delta BMI serves as an effective diagnostic tool for evaluating physical outliers.

---

## 🎯 6. Actionable Recommendations & Next Steps

### 👟 For Athletic Directors & Personal Trainers / Coaches

* **Target Outlier Diagnostics ($\Delta\text{BMI}$):** Compare client physical builds against sport-specific baseline averages to evaluate readiness for weight-class or leverage-dependent sports.
* **Focus Specialization Strategy:** Allocate delegation resources into high-yield, sport-specific niches with high `eMER` athlete efficiency.
* **Track Era Benchmarks:** Recognize that physical requirements evolve over time.

### 📰 For Sports Media Analysts & Journalists

* **Adopt Event-Level MER ($\text{eMER}$):** Shift from raw medal counts to deduplicated event-level efficiency to accurately report on high-performing smaller nations (e.g., Georgia, Moldova, Iceland).
* **Contextualize Biometric Evolution & Eras:** Feature narrative storylines around physical evolution, such as the widening physical gap between Weightlifting and Gymnastics, and reference age-peak gaps when evaluating athletic longevity.

---
