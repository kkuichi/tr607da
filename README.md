# Detekcia minoritných porúch v elektroenergetických systémoch

Bakalárska práca — Technická univerzita v Košiciach   
Autor: Tymofii Kuichi  
Vedúci práce: doc. Ing. Martin Sarnovský, PhD.

---

## Popis projektu

Projekt implementuje hierarchický ML pipeline pre automatizovanú detekciu minoritných typov porúch na odberných miestach elektroenergetickej siete. Pipeline pozostáva z troch sekvenčných modelov XGBoost optimalizovaných pomocou nástroja Optuna:

- **Model 1** — binárna detekcia prítomnosti poruchy
- **Model 2** — identifikácia minoritného typu poruchy
- **Model 3** — multi-label klasifikácia konkrétneho typu poruchy

---

## Štruktúra repozitára

| Súbor | Popis |
|---|---|
| `README.md` | Popis projektu a inštrukcie |
| `requirements.txt` | Zoznam knižníc |
| `data` | Vstupnе datasetн |
| `models/model1.pkl` | Natrénovaný Model 1 |
| `models/model2.pkl` | Natrénovaný Model 2 |
| `models/model3_models.pkl` | Natrénované modely Model 3 |
| `model1.ipynb` | Trénovanie Modelu 1 |
| `model2.ipynb` | Trénovanie Modelu 2 |
| `model3.ipynb` | Trénovanie Modelu 3 |
| `pipeline.ipynb` | Spustenie celého pipeline |
| `xai.ipynb` | SHAP analýza a interpretovateľnosť modelov |

---

## Požiadavky

Python 3.12

### Inštalácia knižníc

```bash
pip install -r requirements.txt
```

### Zoznam knižníc

| Knižnica | Verzia |
|---|---|
| xgboost | 3.2.0 |
| optuna | 4.8.0 |
| scikit-learn | 1.5.1 |
| joblib | 1.4.2 |
| numpy | 1.26.4 |
| pandas | 2.2.2 |
| shap | 0.51.0 |
| matplotlib | 3.9.2 |

---

## Poriadok spustenia


### Krok 1 — Príprava

Uistite sa že súbor `data/final_typy.csv` je umiestnený v priečinku `data/` a priečinok `models/` obsahuje natrénované modely.


### Krok 2 — Spustenie pipeline a analýzy

Natrénované modely sú už uložené v priečinku `models/` — trénovanie nie je potrebné. Stačí spustiť:

pipeline.ipynb  →  výsledky celého hierarchického pipeline
xai.ipynb       →  SHAP analýza interpretovateľnosti modelov

### Krok 3 — Opätovné trénovanie (voliteľné)

Ak chcete modely natrénovať znova, spustite v ľubovoľnom poradí:
model1.ipynb  →  uloží models/model1.pkl
model2.ipynb  →  uloží models/model2.pkl
model3.ipynb  →  uloží models/model3_models.pkl

> **Poznámka:** Trénovanie každého modelu trvá niekoľko minút z dôvodu optimalizácie hyperparametrov pomocou Optuna (150–300 pokusov).
> > **Poznámka:** Pri opätovnom trénovaní môžu výsledky mierne odlišovať od výsledkov uvedených v bakalárskej práci. Je to spôsobené pravdepodobnostným charakterom algoritmu TPE v nástroji Optuna, a to aj napriek fixovanému parametru `seed=42`. Pre reprodukciu pôvodných výsledkov použite uložené modely zo súborov `.pkl`.

### Krok 4 — Spustenie pipeline a analýzy

Po natrénovaní všetkých modelov spustite:

pipeline.ipynb  →  výsledky celého hierarchického pipeline
xai.ipynb       →  SHAP grafy interpretovateľnosti modelov

---

## Dáta

Vstupný súbor `data/final_typy.csv` obsahuje agregované 6-hodinové bloky meraní napätia a prúdu z odberných miest elektroenergetickej siete. Dataset obsahuje celkovo 208 358 záznamov zo 106 odberných miest (veľkosť súboru: 144 MB).

Priečinok `data/` obsahuje aj pomocné súbory použité pri predspracovaní dát:
- `Zoznam_evidovaných_po...` — zoznam evidovaných porúch merania
- `typy_fixed.xlsx` — opravený zoznam typov porúch

---

## Výsledky

Navrhnutý hierarchický pipeline bol hodnotený na testovacej množine obsahujúcej 28 odberných miest. Systém pozostáva z troch postupných modelov kde každý spracúva výstup predchádzajúceho:

- **Model 1** detekuje prítomnosť poruchy v 6-hodinovom bloku
- **Model 2** určuje či ide o minoritný typ poruchy
- **Model 3** klasifikuje konkrétny typ minoritnej poruchy (fault_type_7, fault_type_8, fault_type_9, fault_type_13)
