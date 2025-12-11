
# Nobel Prize Winners (1901–2023) — Exploratory Data Analysis (Python / Pandas / Seaborn / Matplotlib)

## Overview
The **Nobel Prize** has been among the most prestigious international awards since **1901**, honoring achievements in **Chemistry**, **Literature**, **Physics**, **Physiology or Medicine**, **Economic Sciences**, and **Peace**. In this project, we perform an **exploratory data analysis (EDA)** on prize winners from 1901 to 2023 using the dataset provided by the **Nobel Prize API** (saved as `data/nobel.csv`).

We answer several questions and provide starter code to help you explore further.

---

## Dataset
- **File:** `data/nobel.csv`
- **Rows × Columns:** ~ (depends on API snapshot) | **Columns:** 18
- **Common fields:**
  - `year`, `category`, `full_name`, `sex`, `birth_country`, `birth_city`, `birth_date`, `death_country`, `death_city`, `death_date`, etc.

> Note: Column presence can vary per API snapshot; adjust code if column names differ.

---

## Questions Answered
1. **Which sex is most common among laureates?** (`sex.value_counts()`)
2. **Which birth country appears most often among laureates?** (`birth_country.value_counts()`)
3. **How has the ratio of US‑born laureates changed by decade?** (create `US-born_winners` flag and group by decade)
4. **In which decade/category was the share of female laureates highest?** (group by `Decade, category` and find `idxmax`)
5. **Who was the first female laureate and in which category?**
6. **Which individuals/organizations won the Nobel Prize more than once?**

---

## Tech Stack
- **Python 3.x**
- **pandas**, **numpy**
- **seaborn**, **matplotlib**
- Jupyter Notebook (optional)

---

## Setup
```bash
# Optional: create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# Install dependencies
pip install pandas seaborn matplotlib numpy jupyter

# Launch notebook
jupyter notebook
```

---

## Core Analysis (Code)
```python
import pandas as pd
import seaborn as sns
import numpy as np
import matplotlib.pyplot as plt

# Load data
nobel_df = pd.read_csv('data/nobel.csv')
print(nobel_df.head())

# Most common sex and birth country
top_gender = nobel_df['sex'].value_counts().index[0]
top_country = nobel_df['birth_country'].value_counts().index[0]
print(top_gender)
print(top_country)

# US-born ratio by decade
nobel_df['US-born_winners'] = nobel_df['birth_country'] == 'United States of America'
nobel_df['Decade'] = (nobel_df['year'] // 10) * 10
US_born_winners_ratio = nobel_df.groupby('Decade', as_index=False)['US-born_winners'].mean()
max_decade_usa = US_born_winners_ratio.loc[US_born_winners_ratio['US-born_winners'].idxmax(), 'Decade']

sns.relplot(kind='line', x='Decade', y='US-born_winners', data=US_born_winners_ratio)
plt.title('Share of US-born Laureates by Decade')
plt.ylabel('Share (0–1)')
plt.show()

# Female share by decade and category
nobel_df['Female_winners'] = nobel_df['sex'] == 'Female'
female_dict = nobel_df.groupby(['Decade', 'category'], as_index=False)['Female_winners'].mean()
result_dict = female_dict.loc[[female_dict['Female_winners'].idxmax()], ['Decade', 'category']]
print(result_dict)

# First woman laureate (earliest decade among females)
female_winners = nobel_df[nobel_df['Female_winners']]
min_female_winner = female_winners[female_winners['Decade'] == female_winners['Decade'].min()]
first_woman_name = min_female_winner['full_name'].values[0]
first_woman_category = min_female_winner['category'].values[0]
print(first_woman_name)
print(first_woman_category)

# Repeat laureates (2+ awards)
counts = nobel_df['full_name'].value_counts()
repeat_list = counts[counts >= 2].index.tolist()
print(repeat_list)
```

**Example outputs** (based on a sample run):
```
Male
United States of America
    Decade    category
68    2020  Literature
First female laureate: Marie Curie, née Sklodowska
Category: Physics
Repeat winners: [
  'Comité international de la Croix Rouge (International Committee of the Red Cross)',
  'Office of the United Nations High Commissioner for Refugees (UNHCR)',
  'Linus Carl Pauling', 'John Bardeen', 'Frederick Sanger', 'Marie Curie, née Sklodowska'
]
```
> Actual values may vary depending on the API snapshot and data updates.

---

## Data Quality & Notes
- Ensure categorical fields (`sex`, `category`, `birth_country`) have consistent labels.
- Some laureates are **organizations** (e.g., ICRC, UNHCR); treat duplicates carefully.
- For decade analysis, `(year // 10) * 10` bins years to 1900s, 1910s, … 2020s.

---

## License
MIT License © 2025 Martin Karwacki
