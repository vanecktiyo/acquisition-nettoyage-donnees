# Résumé — Acquisition et Nettoyage des Données
**Master 1 Data & IA — 2025-2026**
Ahmad ALMAKSOUR — Faculté de Gestion, Économie et Sciences (Université Catholique de Lille)

> **Évaluation :** Contrôle continu 50 % + Examen final 50 %

---

## 1. Pipelines de données et architecture

### Définition

Un pipeline de données est une séquence automatisée d'étapes permettant de collecter, transformer et stocker des données pour les rendre exploitables par des équipes data ou des modèles d'IA. Son flux standard est : **Source → Ingestion → Transformation → Stockage → Exploitation**.

### Les 3 garanties d'un pipeline bien conçu

- **Reproductibilité** : le code remplace le clic manuel — relancer le pipeline produit toujours le même résultat.
- **Scalabilité** : l'architecture absorbe des volumes croissants sans refonte structurelle.
- **Traçabilité** : chaque erreur est localisable grâce à la journalisation des étapes.

### Deux modes d'exécution

**Traitement par lots (Batch)**
Les données sont accumulées puis traitées ensemble, à une fréquence horaire, quotidienne ou mensuelle. Simple à mettre en œuvre et à déboguer. Cas d'usage typiques : rapports mensuels, statistiques INSEE, fichier DVF. Outils : Python + cron, Apache Airflow, Spark Batch. *Ce cours travaille principalement en mode batch, point de départ de 90 % des projets data.*

**Traitement en temps réel (Streaming)**
Chaque événement est traité dès son arrivée, avec une latence allant de la milliseconde à quelques secondes. Infrastructure plus complexe et plus coûteuse. Cas d'usage : détection de fraude bancaire, recommandations live. Outils : Apache Kafka, Spark Streaming.

---

## 2. ETL vs ELT

### ETL — Extract, Transform, Load

Dans l'ETL, la **transformation a lieu avant le chargement** dans l'entrepôt. Un serveur ETL dédié porte toute la logique de transformation ; les données arrivent donc propres dans le Data Warehouse.

- **Adapté à** : données structurées, schéma stable, volumes maîtrisés, conformité RGPD avec minimisation à la source, systèmes legacy on-premise.
- **Limite principale** : le serveur ETL devient un goulot d'étranglement dès que les volumes augmentent. Le re-traitement nécessite un rejeu complet.
- **Outils typiques** : Talend, Informatica PowerCenter, Stambia.
- **Schéma** : *schema-on-write* (types et structure fixés à l'écriture).

**Exemple Python minimal (3 fonctions)**
```python
def extract(path):   # E — Lecture depuis la source brute
    return pd.read_csv(path)

def transform(df):   # T — Nettoyage et typage
    df = df.dropna(subset=['price', 'number_of_reviews'])
    df['price'] = df['price'].str.replace('$', '').astype(float)
    return df[df['price'].between(10, 1000)]

def load(df, dest):  # L — Stockage dans l'entrepôt
    df.to_parquet(dest, compression='snappy')
```

### ELT — Extract, Load, Transform

Dans l'ELT, les **données brutes sont chargées en premier** dans un Data Lake ou un stockage cloud, puis transformées à la demande sans re-collecter la source.

- **Adapté à** : architecture cloud moderne (AWS, GCP, Azure), données variées, grands volumes, schéma susceptible d'évoluer, exploration fréquente.
- **Avantage clé** : toute la puissance de calcul du cloud (BigQuery, Snowflake) est disponible pour la transformation ; si le besoin métier change, on re-transforme sans re-collecter.
- **Outils typiques** : dbt, Airbyte / Fivetran, Apache Spark, BigQuery / Snowflake, Databricks.
- **Schéma** : *schema-on-read* (flexible à la lecture).

### Tableau comparatif ETL / ELT

| Critère | ETL | ELT |
|---|---|---|
| Ordre | Extract → Transform → Load | Extract → Load → Transform |
| Lieu de transformation | Serveur ETL dédié | Dans le Data Warehouse / Lake |
| Schéma | Schema-on-write (fixe) | Schema-on-read (flexible) |
| Infrastructure | On-premise ou hybride | Cloud-native principalement |
| Volumétrie | Petits à moyens volumes | Grands volumes, Big Data |
| Flexibilité | Faible — refonte coûteuse | Élevée — re-transformation possible |
| Re-traitement | Rejeu complet nécessaire | Sans re-collecter la source |

---

## 3. Les 5V du Big Data

Ces cinq dimensions guident les choix d'architecture, d'outils et de format de stockage pour tout projet data.

| V | Question clé | Description |
|---|---|---|
| **Volume** | Combien ? | Quantité de données générées et stockées |
| **Vélocité** | À quelle vitesse ? | Vitesse de génération et de traitement |
| **Variété** | Sous quelle forme ? | Structurées, semi-structurées, non-structurées, images |
| **Véracité** | Peut-on faire confiance ? | Qualité, fiabilité et exactitude des données |
| **Valeur** | À quoi ça sert ? | Utilité métier et ROI de l'exploitation |

**Application concrète — Données DVF Paris 2023 :**
Les Demandes de Valeurs Foncières (DVF) constituent le registre officiel de toutes les transactions immobilières en France. Volume : ~3 millions de transactions par an, ~70 000 lignes pour Paris 2023. Vélocité : mise à jour annuelle — un traitement batch annuel suffit. Variété : données entièrement structurées (numériques, textuelles, dates). Véracité : source notariale officielle, très fiable malgré quelques erreurs de saisie. Valeur : analyse des marchés, détection d'inégalités territoriales, modèles d'estimation immobilière.

---

## 4. Sources de données

### Open Data et bases de données

L'**Open Data** regroupe des jeux de données librement accessibles, citables et reproductibles. Sources principales : data.gouv.fr (DVF, INSEE, Santé, Éducation), Eurostat (statistiques européennes harmonisées), Kaggle / UCI ML Repository (datasets académiques). Limite : mises à jour parfois lentes, qualité variable selon la source.

Les **bases de données** se déclinent en trois familles :
- **SQL** (PostgreSQL, MySQL, SQLite) : schéma rigide, transactions ACID.
- **NoSQL** (MongoDB, Redis, Cassandra, Neo4j) : flexibilité structurelle, modèles variés (documents, clé-valeur, colonnes, graphes).
- **Data Warehouses** (Snowflake, BigQuery, Redshift) : optimisés pour les lectures analytiques à grande échelle.

### API REST

Mode d'accès préférentiel en data engineering. Le client Python émet une requête GET vers le serveur API qui retourne une réponse JSON (code 200 OK). Avantages par rapport au fichier plat : données toujours à jour, pas de fichier local à gérer, reproductibilité, documentation standardisée, gestion des droits intégrée (token, OAuth).

### Web Scraping

Extraction du contenu HTML d'une page web via son DOM. Outils : BeautifulSoup (pages statiques), Scrapy (crawler multi-pages), Selenium / Playwright (sites JavaScript). Cas d'usage : prix en ligne, offres d'emploi, actualités.

**Contraintes légales et éthiques incontournables :**
- **robots.txt** : toujours vérifier avant de scraper.
- **CGU** : les conditions générales peuvent interdire la collecte automatisée.
- **RGPD** (directive CNIL 2020) : les données personnelles sont protégées même si elles sont publiquement accessibles.
- **Rate limiting** : respecter un délai entre les requêtes pour ne pas surcharger le serveur.

> *L'API reste toujours préférable au scraping quand elle existe. Le scraping est un dernier recours, pas un premier choix.*

---

## 5. Formats de fichiers

### CSV — Le plus universel, le plus limité

Format texte à valeurs séparées. Lisible par tous les outils (Excel, R, Pandas, SQL), facile à inspecter, aucune dépendance. Idéal pour les petits datasets et le partage ponctuel.

Limites : pas de types intégrés (inférence obligatoire), séparateurs variables, lent sur de gros volumes (scan ligne par ligne), taille non compressée (5 à 10× plus lourd que Parquet), encodages incohérents possibles, aucun schéma.

**→ Utiliser quand** : dataset < 100 Mo, interopérabilité maximale, partage ponctuel.

### JSON et XML

**JSON** est le format natif des API REST. Semi-structuré, il supporte les objets imbriqués. Verbeux (clés répétées pour chaque ligne). Lu avec `pd.read_json()` ou `pd.json_normalize()`.

**XML** est le standard d'échange historique (services publics, banques). Validable via schéma XSD, robuste mais très verbeux (les balises doublent la taille des données). Progressivement remplacé par JSON dans les nouveaux projets.

**→ Utiliser JSON quand** : réponse d'API, données semi-structurées, configuration.
**→ Utiliser XML quand** : interfaces avec des systèmes legacy bancaires ou gouvernementaux.

### Apache Parquet — Le format de référence en production

Format de stockage en colonnes (vs lignes pour CSV). Ses atouts principaux :
- **Lectures sélectives** : on ne lit que les colonnes nécessaires, sans charger toute la table.
- **Compression intégrée** (Snappy, ZSTD) : 5 à 20× plus petit qu'un CSV équivalent.
- **Schéma intégré** : les types sont conservés, pas de ré-inférence à la lecture.
- **Prédicats pushdown** : filtrage directement sur le disque sans chargement en mémoire.
- **Interopérabilité** : Pandas, Spark, DuckDB, BigQuery, Snowflake lisent tous Parquet.

| Format | Taille | Lecture | Écriture |
|---|---|---|---|
| CSV | 350 Mo | 12,4 s | 8,2 s |
| CSV + gzip | 48 Mo | 14,1 s | 19,5 s |
| Parquet (Snappy) | 22 Mo | 0,8 s | 2,1 s |
| Parquet (Zstd) | 14 Mo | 0,9 s | 2,8 s |

**→ Utiliser quand** : pipeline de production, dataset > 100 Mo, analytique performante.

---

## 6. Qualité des données et stack Python

### Les 5 dimensions de la qualité

Un modèle d'IA entraîné sur des données corrompues produit des prédictions corrompues. La qualité des données est la première responsabilité du data engineer.

- **Complétude** : toutes les valeurs attendues sont présentes.
- **Conformité** : les valeurs respectent le format et le type attendus.
- **Cohérence** : pas de contradictions entre colonnes ou entre sources.
- **Unicité** : pas de doublons, chaque entité est représentée une seule fois.
- **Précision** : les valeurs reflètent fidèlement la réalité.

### Valeurs manquantes

Stratégie selon le taux de manquants :
- **< 5 %** → supprimer les lignes ou imputer par médiane / mode.
- **5 à 30 %** → imputer intelligemment selon le contexte métier.
- **> 30 %** → remettre en question la colonne, la supprimer ou recollecte.

> *Règle importante : avant d'imputer, se demander pourquoi la valeur manque. Une surface manquante sur un garage n'a pas le même sens que sur un appartement.*

### Doublons et valeurs aberrantes

Les doublons se détectent avec `df.duplicated()` sur l'ensemble du DataFrame ou sur une clé métier. Les valeurs aberrantes sont identifiées par la méthode IQR (Q1 − 1,5×IQR et Q3 + 1,5×IQR). Avant de supprimer une anomalie, vérifier le contexte métier : certaines valeurs extrêmes sont légitimes.

### La stack Python data science

| Bibliothèque | Rôle |
|---|---|
| **NumPy** | Calcul numérique, tableaux N-dimensionnels, opérations vectorisées en C compilé |
| **Pandas** | Manipulation de DataFrames, ETL léger, équivalent SQL (groupby, merge, pivot) |
| **Matplotlib / Seaborn** | Visualisation, graphiques statistiques |
| **Requests / BeautifulSoup** | Appels API HTTP, web scraping |

**Pourquoi NumPy est rapide** : tableaux homogènes (stockage contigu en mémoire), opérations exécutées en C compilé, vectorisation (une seule instruction CPU pour tout un tableau). En pratique, NumPy est utilisé indirectement via Pandas — d'où l'importance d'éviter les boucles Python sur les DataFrames.

**Opérations Pandas essentielles** : `read_csv` / `read_parquet` / `to_parquet`, `shape` / `dtypes` / `head` / `describe`, `dropna` / `fillna` / `drop_duplicates`, filtrage booléen, `groupby` / `agg`, `merge` / `concat`.

**ydata-profiling** : outil industriel qui génère automatiquement un rapport HTML interactif (distribution de chaque colonne, matrice de corrélation, heatmap des manquants) en 3 lignes de code. Sur les grands datasets, travailler sur `df.sample(10_000)` ou utiliser `ProfileReport(df, minimal=True)`.

---

## 7. Architecture data moderne — Vue d'ensemble

Dans une architecture réelle, tous les concepts s'articulent ainsi :

```
Sources          Ingestion           Stockage brut      Transformation    Consommation
─────────────   ─────────────────   ────────────────   ───────────────   ─────────────
Open Data    → Batch (cron)      → Data Lake (S3)  → Python/Pandas  → Dashboards
API REST     → Streaming (Kafka) → Format Parquet  → dbt (SQL)      → ML / IA
Scraping     → Airbyte / NiFi   → Données brutes  → Spark          → Rapports
Données admin
```

Le choix ETL ou ELT dépend uniquement de si la transformation précède ou suit le chargement dans le stockage brut.

---

## 8. Bonnes pratiques du data engineer

1. **Versionner le code** : tout script de traitement va dans Git. Le notebook qui tourne une fois puis disparaît n'existe pas.
2. **Documenter les transformations** : chaque étape de nettoyage doit être justifiée dans le code.
3. **Ne jamais modifier la source** : les données brutes sont sacrées. Toujours travailler sur une copie ou dans une couche transformation.
4. **Automatiser les contrôles** : les tests de qualité (Great Expectations) s'exécutent à chaque ingestion, pas seulement lors du développement.
5. **Tracer les métadonnées** : conserver date de collecte, source, version et nombre de lignes — indispensable pour auditer et déboguer.
6. **Choisir le bon format dès le départ** : passer de CSV à Parquet après coup sur 10 To coûte cher. La décision d'architecture se prend en amont.

---

## Récapitulatif des concepts clés

| Thème | L'essentiel à retenir |
|---|---|
| Pipeline | Séquence automatisée Source → Ingestion → Transformation → Stockage → Exploitation |
| Batch vs Streaming | Batch = périodique et simple ; Streaming = temps réel et complexe |
| ETL | Transformation AVANT chargement — on-premise, schéma stable |
| ELT | Chargement PUIS transformation — cloud, grands volumes, schéma flexible |
| 5V | Volume, Vélocité, Variété, Véracité, Valeur |
| CSV | Universel mais lent, sans types ni schéma |
| JSON/XML | Échanges API et systèmes legacy |
| Parquet | Colonnaire, compressé, typé — format pivot de la production |
| Qualité | 5 dimensions : Complétude, Conformité, Cohérence, Unicité, Précision |
| Stack Python | NumPy → Pandas → Matplotlib/Seaborn → Requests/BeautifulSoup |

---

*Contact enseignant : ahmad.almosaalmaksour@lacatholique.fr*
