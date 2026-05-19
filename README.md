# Acquisition et Nettoyage de Données — M1 Data & IA

**Université Catholique de Lille**  
**Auteur :** Vaneck Duramel DAGAR TIYO  
**Binôme :** Jean-Daniel KITIHOUN (TP2, TP3, TP4, TP5)

Série de 5 travaux pratiques couvrant l'ensemble du cycle de vie de la donnée : acquisition, transformation, enrichissement par APIs et scraping, puis nettoyage avancé.

---

## Contenu

| Dossier | Sujet | Compétences |
|---------|-------|-------------|
| [`tp1/`](tp1/) | **Pipeline ETL sur les DVF (Demandes de Valeurs Foncières) du Nord 2023** | NumPy, Pandas, analyse exploratoire, diagnostic qualité, pipeline Extract/Transform/Load, format Parquet |
| [`tp2/`](tp2/) | **Pandas & NumPy avancés** | Opérations vectorisées, performances, agrégations avancées, visualisation |
| [`tp3/`](tp3/) | **APIs REST — client robuste, Pydantic et géocodage** | Requêtes HTTP, Pydantic, géocodage, visualisation géospatiale (Folium) |
| [`tp4/`](tp4/) | **Web Scraping — BeautifulSoup, Scrapy et Playwright** | Scraping statique et dynamique, enrichissement de dataset |
| [`tp5/`](tp5/) | **Nettoyage approfondi — Titanic** | Imputation, encodage, détection d'outliers, pipeline scikit-learn |

---

## Dataset principal

Les TP1 à TP4 utilisent les **DVF (Demandes de Valeurs Foncières)** du département du Nord (59) — 2023.  
Source officielle : [data.gouv.fr](https://files.data.gouv.fr/geo-dvf/latest/csv/2023/departements/59.csv.gz)

> Les fichiers de données (`.csv`, `.parquet`) ne sont pas versionnés (voir `.gitignore`).  
> Pour reproduire les notebooks, télécharger le fichier source et l'enregistrer dans `tp1/59.csv`.

---

## Structure

```
.
├── tp1/   # Pipeline ETL DVF (individuel)
├── tp2/   # Pandas & NumPy avancés (binôme)
├── tp3/   # APIs REST & géocodage (binôme)
├── tp4/   # Web scraping (binôme)
├── tp5/   # Nettoyage approfondi Titanic (binôme)
└── README.md
```

---

## Environnement

```bash
pip install numpy pandas matplotlib seaborn pyarrow requests pydantic folium beautifulsoup4 scikit-learn
```
