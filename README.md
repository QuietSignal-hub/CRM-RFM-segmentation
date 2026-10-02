# 🎯 CRM Profiling & RFM Segmentation: Canada's Creative Economy

A Python data pipeline that turns 65,002 public arts-grant records into a CRM-ready table of 24,350 unique recipient profiles, each scored with RFM (Recency, Frequency, Monetary) and assigned to one of four audience segments. Built for an industry sponsor as a Northeastern University capstone project.

<img src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white" alt="Python"/> <img src="https://img.shields.io/badge/pandas-150458?style=flat-square&logo=pandas&logoColor=white" alt="pandas"/> <img src="https://img.shields.io/badge/RFM%20Segmentation-1C3F75?style=flat-square" alt="RFM"/> <img src="https://img.shields.io/badge/Data%20Quality-2A7FA8?style=flat-square" alt="Data Quality"/> <img src="https://img.shields.io/badge/Power%20BI-F2C811?style=flat-square" alt="Power BI"/>

> **Team project.** ALY 6980 Capstone (Spring 2026), Team 4: Shih-Na Mei, Sai Preetham Nagulapalli, Sahil Niranjan, Jayesh Patil, Vishal Rathi. Sponsor: Your Sweet Spot Life (YSSL) Inc., Saint John, New Brunswick. See [My Role](#-my-role).

---

## 📌 Problem

YSSL, a Canadian creative-industry consulting firm, is building **S.T.A.R. Toolbox**, a platform that helps creative professionals and small businesses make decisions. It needed a reliable picture of who is active in Canada's creative economy, but public data on the sector is spread across federal sources in formats that don't support analysis. The goal: one clean, documented, CRM-ready dataset that shows who the active creative organizations and individuals are, how engaged they are, and which segments to prioritize for outreach.

## 🗃️ Data

All sources are public open-government data.

| Source | Use in this project | Rows used |
|---|---|---|
| Canada Council for the Arts grants (2017–2024) | Core dataset | 65,002 grants |
| Statistics Canada CPI (Table 18-10-0005-01) | Inflation adjustment to 2024 CAD | 8 |
| Canadian Intellectual Property Office trademarks | Creative-sector IP context | 11,112 of ~2.05M |
| O\*NET 30.3 | Occupational titles, skills, work activities | 42 occupations |
| Statistics Canada business counts and financial statistics | Sector context for the dashboard | 39 / 12 |

The full profile table isn't published here because it names individual grant recipients. [`data/sample_profiles_anonymized.csv`](data/sample_profiles_anonymized.csv) contains 500 profiles (125 per segment) with names and cities removed.

## 🛠️ Approach

The pipeline is 13 numbered Jupyter notebooks:

1. **Profile each source (01–07):** shape, data types, missing values, and limitations, recorded in a [dataset inventory](docs/dataset_inventory.csv).
2. **Clean the grants (08):** standardized fields and converted every grant to 2024 dollars with CPI, so amounts are comparable across years.
3. **Build recipient profiles (09):** generated a stable ID for each recipient (a hash of normalized name + province), then collapsed 65,002 grants into 24,350 profiles with grant count, totals, average, first and last grant year, and years active.
4. **RFM scoring and segments (09):** scored Recency, Frequency, and Monetary 1–5 using percentile ranks, summed them into an RFM score (3–15), and assigned segments.
5. **Enrich (10–11):** added NAICS industry codes and O\*NET occupational data by field of practice, plus an IP activity flag (see limitations).
6. **Quality and governance (12–13):** a six-dimension data-quality scorecard and a governance package: [data dictionary](docs/data_dictionary.md), [governance plan](docs/governance_plan.md) (DAMA-DMBOK2), and [PIPEDA privacy checklist](docs/pipeda_checklist.md).

**Segment rules**

| Segment | RFM score |
|---|:---:|
| Core Champion | 13–15 |
| Established Regular | 10–12 |
| Emerging Creative | 7–9 |
| Lapsed Recipient | 3–6 |

## 📊 Results

| Segment | Recipients | Share of recipients | Share of funding (2024 CAD) | Avg. grants each |
|---|---:|---:|---:|---:|
| Core Champion | 4,517 | 18.6% | 73.7% | 7.4 |
| Established Regular | 5,549 | 22.8% | 15.4% | 2.6 |
| Emerging Creative | 8,307 | 34.1% | 8.6% | 1.3 |
| Lapsed Recipient | 5,977 | 24.5% | 2.3% | 1.0 |

![Segment analysis](images/viz_recipient_segments.png)

**Data quality scorecard:** Completeness 100 · Uniqueness 100 · Validity 100 · Consistency 100 · Accuracy 99.87 · Timeliness 54.35 (several sources are annual or single-year snapshots)

**Power BI dashboard**

![Dashboard overview](images/dashboard_overview.png)

## 💡 Key Insights

- **Funding is highly concentrated.** Core Champions are fewer than 1 in 5 recipients but received about 74% of inflation-adjusted funding, which makes them the clearest early-adopter target for S.T.A.R. Toolbox.
- **Emerging Creatives are the growth pool.** At 34% of recipients, it's the largest segment, with mostly one-time grants: a natural audience for a tool that helps creators grow.
- **Funding peaked in 2021.** Total funding rose from $255M in 2017 to $519M in 2021 (2024 CAD), then fell back to $283M by 2024.
- **Two provinces dominate.** Ontario (32.5%) and Quebec (31.6%) received nearly two-thirds of all funding.

## ⚠️ Limitations

- **The IP activity flag is a heuristic, not a trademark match.** Fuzzy matching recipient names against CIPO trademark names (notebook 10) didn't produce reliable matches, so the flag marks all organizations plus any name containing business keywords (for example "studio" or "productions"). As a result, 91% of profiles are flagged, so the flag shouldn't be used for targeting without a better matching method.
- **Most recipients are organizations** (21,683 vs. 2,667 individuals), so "creative professionals" here mostly means arts organizations.
- **IDs are name + province based,** so the same recipient listed under two provinces, or under a spelling variant, becomes two profiles.
- **O\*NET is U.S.-based** and needs a crosswalk to Canada's NOC system for production use.

## 👤 My Role

This was a five-person team project. My contributions:

- **Source profiling and cleaning (notebooks 01–08):** profiled all six public sources, built the dataset inventory, and cleaned the 65,002 grant records, including the CPI adjustment to 2024 dollars.
- **Recipient profiles and RFM segmentation (09):** designed the recipient ID, built the 24,350-profile CRM table, and developed the RFM scoring and four-segment model.
- **Data quality and governance (12–13):** built the six-dimension quality scorecard and the governance package (data dictionary, governance plan, PIPEDA checklist).
- **Power BI dashboard:** built some of the dashboard pages.

Teammates led the CIPO matching and NAICS/O\*NET enrichment (10–11). Outside this repo, in the S.T.A.R. Toolbox application, I built the source-validation layer that traces each compliance recommendation to its verified government source.

## 📁 Repository Structure

```
├── notebooks/   # 01–13: profiling, cleaning, profiles + RFM, enrichment, quality, governance
├── docs/        # data dictionary, governance plan, PIPEDA checklist, inventory, scorecard
├── images/      # charts and dashboard screenshot used in this README
├── data/        # anonymized 500-profile sample
├── requirements.txt
└── README.md
```

## ▶️ How to Run

1. Download the source files (links in the [dataset inventory](docs/dataset_inventory.csv)) into `data/raw/` with these names: `Open-Data-2017-2025.csv` (Canada Council), `statcan_cpi.csv`, `statcan_business_counts.csv`, `statcan_financial_stats.csv`, `cipo_tm_main.csv` and `cipo_tm_text.csv`, and the O\*NET folder `db_30_3_text/`.
2. Install dependencies: `pip install -r requirements.txt`
3. Run the notebooks in order (01 → 13). Processed files are written to `data/processed/`.

---

**Jayesh Patil** · [LinkedIn](https://www.linkedin.com/in/jayeshp-242e/) · [GitHub](https://github.com/QuietSignal-hub)
