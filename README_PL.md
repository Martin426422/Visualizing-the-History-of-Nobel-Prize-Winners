
# Laureaci Nagrody Nobla (1901–2023) — Eksploracyjna Analiza Danych (Python / Pandas / Seaborn / Matplotlib)

## Przegląd
**Nagroda Nobla** należy do najbardziej prestiżowych wyróżnień od **1901 roku**, przyznawanych w kategoriach: **chemia**, **literatura**, **fizyka**, **fizjologia lub medycyna**, **ekonomia** oraz **pokój**. W projekcie wykonujemy **EDA** laureatów z lat 1901–2023, korzystając z datasetu pochodzącego z **Nobel Prize API** (zapisany jako `data/nobel.csv`).

Odpowiadamy na kilka pytań i dostarczamy kod startowy do dalszych eksploracji.

---

## Dane
- **Plik:** `data/nobel.csv`
- **Wymiary:** zależne od snapshotu API | **Kolumny:** 18
- **Typowe pola:**
  - `year`, `category`, `full_name`, `sex`, `birth_country`, `birth_city`, `birth_date`, `death_country`, `death_city`, `death_date`, itp.

> Uwaga: Zawartość kolumn może się różnić między snapshotami API; dopasuj kod do rzeczywistych nazw kolumn.

---

## Pytania
1. **Jaka płeć dominuje wśród laureatów?** (`sex.value_counts()`)
2. **Który kraj urodzenia pojawia się najczęściej?** (`birth_country.value_counts()`)
3. **Jak zmienia się udział laureatów urodzonych w USA w dekadach?** (flaga `US-born_winners` + grupowanie po dekadach)
4. **W której dekadzie/kategorii udział kobiet był najwyższy?** (grupowanie `Decade, category` + `idxmax`)
5. **Kim była pierwsza laureatka i w jakiej kategorii?**
6. **Kto zdobył Nobla więcej niż raz (osoby/organizacje)?**

---

## Technologia
- **Python 3.x**
- **pandas**, **numpy**
- **seaborn**, **matplotlib**
- Jupyter Notebook (opcjonalnie)

---

## Uruchomienie
```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install pandas seaborn matplotlib numpy jupyter
jupyter notebook
```

---

## Kod analizy
```python
import pandas as pd
import seaborn as sns
import numpy as np
import matplotlib.pyplot as plt

nobel_df = pd.read_csv('data/nobel.csv')
print(nobel_df.head())

# Najczęstsza płeć i kraj urodzenia
top_gender = nobel_df['sex'].value_counts().index[0]
top_country = nobel_df['birth_country'].value_counts().index[0]
print(top_gender)
print(top_country)

# Udział laureatów urodzonych w USA w dekadach
nobel_df['US-born_winners'] = nobel_df['birth_country'] == 'United States of America'
nobel_df['Decade'] = (nobel_df['year'] // 10) * 10
US_born_winners_ratio = nobel_df.groupby('Decade', as_index=False)['US-born_winners'].mean()
max_decade_usa = US_born_winners_ratio.loc[US_born_winners_ratio['US-born_winners'].idxmax(), 'Decade']

sns.relplot(kind='line', x='Decade', y='US-born_winners', data=US_born_winners_ratio)
plt.title('Udział laureatów urodzonych w USA wg dekady')
plt.ylabel('Udział (0–1)')
plt.show()

# Udział kobiet wg dekady i kategorii
nobel_df['Female_winners'] = nobel_df['sex'] == 'Female'
female_dict = nobel_df.groupby(['Decade', 'category'], as_index=False)['Female_winners'].mean()
result_dict = female_dict.loc[[female_dict['Female_winners'].idxmax()], ['Decade', 'category']]
print(result_dict)

# Pierwsza laureatka
female_winners = nobel_df[nobel_df['Female_winners']]
min_female_winner = female_winners[female_winners['Decade'] == female_winners['Decade'].min()]
first_woman_name = min_female_winner['full_name'].values[0]
first_woman_category = min_female_winner['category'].values[0]
print(first_woman_name)
print(first_woman_category)

# Laureaci wielokrotni (2+ nagrody)
counts = nobel_df['full_name'].value_counts()
repeat_list = counts[counts >= 2].index.tolist()
print(repeat_list)
```

**Przykładowe wyniki** (z uruchomienia):
```
Male
United States of America
    Decade    category
68    2020  Literature
Pierwsza laureatka: Marie Curie, née Sklodowska
Kategoria: Physics
Laureaci wielokrotni: [
  'Comité international de la Croix Rouge (International Committee of the Red Cross)',
  'Office of the United Nations High Commissioner for Refugees (UNHCR)',
  'Linus Carl Pauling', 'John Bardeen', 'Frederick Sanger', 'Marie Curie, née Sklodowska'
]
```
> Rzeczywiste wyniki mogą różnić się w zależności od snapshotu API i aktualizacji danych.

---

## Jakość danych i uwagi
- Upewnij się, że etykiety kategorii (`sex`, `category`, `birth_country`) są spójne.
- Część laureatów to **organizacje** (np. ICRC, UNHCR); traktuj duplikaty ostrożnie.
- Dla dekad używamy binowania `(year // 10) * 10` (1900s, 1910s, … 2020s).

---

## Licencja
MIT License © 2025 Martin Karwacki
