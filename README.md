# 🎮 GOTY Analysis — The Game Awards 2014–2024

> Statistical analysis of Game of the Year nominees and winners using R.  
> Logistic regression model to predict GOTY winners, validated with Leave-One-Out Cross-Validation (LOOCV).

---

## Overview

This project explores a decade of **The Game Awards** (2014–2024) nominations and results to answer one question: **what actually makes a game win GOTY?**

Using a logistic regression model trained on 62 nominees (11 winners), the analysis shows that **community engagement** (votes, popularity) is a far stronger predictor than critical acclaim. The model is evaluated rigorously with LOOCV to avoid the optimism bias of in-sample accuracy, and tested externally on **Clair Obscur: Expedition 33** (GOTY 2025).

---

## Project Structure

```
goty-analysis/
│
├── README.md                  
│
├── src/
│   └── GOTY_Analyse.Rmd
│
└── data/
    └── GOTY.csv
```

---

## Dataset Description

**Games Awards Nominees 2014–2024**  
Every GOTY nominee and winner from The Game Awards over a full decade.

| Column | Description |

| `Name` | Title of the game |
| `Developer` | Development studio |
| `Publisher` | Publishing company |
| `Genre` | Primary genre (Action-Adventure, RPG, etc.) |
| `Release.Date` | Exact release date (sourced from Metacritic) |
| `Year` | Year of The Game Awards ceremony |
| `Meta.Score` | Aggregated critic score from Metacritic (0–100) |
| `Reviews` | Number of critic reviews used for the MetaScore |
| `User.Score` | Aggregated user score from Metacritic (0–100) |
| `Votes` | Number of user ratings used for the User.Score |
| `Popularity` | Relative search interest from Google Trends (intra-year only) |
| `Most.Anticipated` | Whether the game was nominated for Most Anticipated Game |
| `Nominations` | Total number of TGA nominations received that year |
| `Wins` | GOTY winner: `1` = won, `0` = did not win |

###  Important Caveats

1. **Genre classification** is subjective for some titles and based on TGA's own categories.
2. **Release dates** are sourced from Metacritic and may reflect regional launch dates (e.g., Persona 5 shows its 2016 Japan release).
3. **PUBG** release date corresponds to its Early Access launch, as that was the relevant date for its nomination.
4. **Popularity** values are **relative within each year** — do not compare a 2015 game's popularity to a 2022 game's popularity.
5. **Most.Anticipated** is not reliable for historical analysis as the category did not exist in several earlier editions.
6. **Nominations** count varies yearly as TGA categories evolve — new ones are added and old ones are removed or merged.

---

## Methodology

### 1. Exploratory Analysis
- Genre distribution and win rates
- Publisher performance
- Descriptive statistics by winner status

### 2. Logistic Regression Model
- **Target variable**: `Wins` (binary: 0/1)
- **Features**: `Meta.Score`, `User.Score`, `Reviews`, `Nominations`, `Votes`, `Popularity`
- **Preprocessing**: centering and scaling (`caret::preProcess`)
- **Family**: `binomial` via `glm()`

### 3. Model Evaluation : Leave-One-Out Cross-Validation
Given the small dataset (n=62, only 11 positives), a standard train/test split is not appropriate. The project uses **Leave-One-Out Cross-Validation** (`boot::cv.glm`, K=n) to get an unbiased accuracy estimate.

Metrics reported:
- Accuracy (LOOCV) vs naive baseline (82.3%)
- Precision, Recall, F1-Score
- Specificity
- Confusion matrix

### 4. External Test : GOTY 2025
The trained model is applied to **Clair Obscur: Expedition 33** (actual GOTY 2025, not in the training set) using the same preprocessing pipeline, to test out-of-sample generalization.

---

##  Requirements

### R packages

```r
install.packages(c("dplyr", "ggplot2", "gridExtra", "tidyr", "caret", "boot"))
```

| Package | Version tested | Role |
|---|---|---|
| `dplyr` | ≥ 1.1.0 | Data manipulation |
| `ggplot2` | ≥ 3.4.0 | Visualizations |
| `gridExtra` | ≥ 2.3 | Multi-panel plots |
| `tidyr` | ≥ 1.3.0 | Data reshaping |
| `caret` | ≥ 6.0 | Feature preprocessing |
| `boot` | ≥ 1.3 | LOOCV via `cv.glm` |

---

##  How to Run

1. **Clone the repository**
   ```bash
   git clone https://github.com/YOUR_USERNAME/goty-analysis.git
   cd goty-analysis
   ```

2. **Open the project in RStudio**  
   Open `src/GOTY_Analyse.Rmd`

3. **Check the data path**  
   The script reads the dataset with a relative path. Make sure your working directory is set to the project root, or adjust the path in the loading chunk:
   ```r
   data <- read.csv("data/GOTY.csv", sep = ',', header = TRUE)
   ```
   In RStudio, go to **Session → Set Working Directory → To Project Directory**.

4. **Knit the document**  
   Click **Knit** (or `Ctrl+Shift+K`) to generate the HTML report.

---

## Key Findings

| Finding | Value |
|---|---|
| Games analysed | 62 (2014–2024) |
| GOTY winners | 11 (17.7%) |
| Naive baseline accuracy | 82.3% |
| Model accuracy (LOOCV) | *see output* |
| Most predictive variable | Votes (community engagement) |
| Least predictive variable | MetaScore (critic score) |
| Best genre win rate | RPG (26.7%) |
| Most awarded publisher | PlayStation (3 wins) |
| External test (GOTY 2025) | Clair Obscur correctly predicted |

---

##  License

This project is released under the [MIT License](LICENSE).  
The dataset is sourced from Kaggle and used for educational and research purposes.

---

## Acknowledgements

- Dataset originally published on [Kaggle](https://www.kaggle.com/datasets/alejandrobelda/games-awards-nominees-2014-2024?resource=download)
- [The Game Awards](https://thegameawards.com/) for the underlying nomination and winner data
- [Metacritic](https://www.metacritic.com/) for critic and user scores
- [Google Trends](https://trends.google.com/) for popularity data