# 📊 TP1 - DVF Nord 2023 : Guide de Progression

## 📋 Structure du Projet (6 Sections)

---

## ✅ Section 0 : Imports et Configuration
**État** : ✔️ **COMPLÈTE** dans le notebook
- Imports de toutes les librairies essentielles
- Configuration Pandas et Matplotlib

---

## 📖 Section 1 : Chargement et Inspection (4 Questions)

### Q1 : Benchmark NumPy vs Python
```python
# Comparer vitesse de :
# - Boucle Python : [x * 2 for x in data]  
# - NumPy vectorisé : arr * 2

# Attendu : NumPy ~50-100x plus rapide
# Explication : Opérations vectorisées compilées en C
```

### Q2 : Inspection du Dataset
```python
# À afficher :
# - Dimensions (lignes, colonnes)
# - Liste des colonnes
# - Premières lignes (head)
# - Info générale (info())
```

### Q3 : Correction des Types
```python
# Rechercher :
# - date_mutation : doit être datetime (pas object)
# - Identifiants : doivent être string (pas int64)
# 
# Correction : pd.to_datetime(), astype('str')
```

### Q4 : Statistiques Descriptives
```python
# Analyser :
# - Moyenne vs Médiane de valeur_fonciere
# - Écart = distribution asymétrique ?
# - Présence de valeurs extrêmes ?
```

---

## 📈 Section 2 : Analyse Exploratoire (5 Questions)

### Q5 : Prix des Appartements
```python
# Filtrer : df[df['type_local'] == 'Appartement']
# Calculer :
# - Prix médian
# - Prix moyen
# - Nombre de transactions
```

### Q6 : Prix au m²
```python
# Créer colonne :
df['prix_m2'] = df['valeur_fonciere'] / df['surface_reelle_bati']

# Analyser statistiques de prix_m2 (pour appartements)
```

### Q7 : Comparaison par Type de Bien
```python
# df.groupby('type_local')['valeur_fonciere'].agg(['median', 'count'])
# 
# Afficher :
# - Tableau récapitulatif
# - Graphique en barres (sns.barplot ou plt.bar)
```

### Q8 : Volume de Transactions par Mois
```python
# df['date_mutation'] = pd.to_datetime(df['date_mutation'])
# df['mois'] = df['date_mutation'].dt.month
# 
# Volume par mois (graph courbe)
# Analyser : saisonnalité ?
```

### Q9 : Évolution du Prix Médian
```python
# Prix médian des appartements par mois
# Afficher : série temporelle
# Interpréter : tendance haussière/baissière ?
```

---

## 🧹 Section 3 : Diagnostic Qualité (5 Questions)

### Q10 : Taux de Complétude
```python
# Calculer taux manquantes par colonne
missing_percent = (df.isnull().sum() / len(df)) * 100

# Identifier > 30%
# Justifier : pourquoi cela gêne l'analyse ?
```

### Q11 : Stratégie de Traitement
```python
# Pour les 3 colonnes les plus incomplètes :
# - Suppression complète ?
# - Imputation (moyenne, médiane, mode) ?
# - Abandon de la variable ?

# Justifier chaque choix
```

### Q12 : Doublons
```python
# df.duplicated().sum()
# df.drop_duplicates()

# Définir clé métier unique
# Ex : (id_mutation, date_mutation, adresse)
```

### Q13 : Valeurs Aberrantes
```python
# Top 10 prix_m2 les plus élevés
# df.nlargest(10, 'prix_m2')
#
# Règle de filtrage : IQR, écart-type, percentiles ?
# Créer df_clean : version nettoyée
```

### Q14 : Profiling Automatique
```python
# from ydata_profiling import ProfileReport
# 
# profile = ProfileReport(
#     df_clean.sample(5_000, random_state=42),
#     title='DVF Nord 2023',
#     explorative=True
# )
# profile.to_notebook_iframe()

# Comparer alertes auto vs détection manuelle
```

---

## 🏗️ Section 4 : Pipeline ETL (7 Questions)

### Q15-16 : Fonctions Extract / Transform / Load
```python
def extract(path):
    """Charge fichier brut"""
    df = pd.read_csv(path, low_memory=False)
    print(f'[EXTRACT] Sorties : {len(df)} lignes')
    return df

def transform(df):
    """Applique nettoyages + transformations"""
    print(f'[TRANSFORM] Entrées : {len(df)}')
    # Toutes les corrections effectuées
    # df_transformed = ...
    print(f'[TRANSFORM] Sorties : {len(df_transformed)}')
    return df_transformed

def load(df, dest):
    """Sauvegarde en Parquet"""
    print(f'[LOAD] Entrées : {len(df)}')
    df.to_parquet(dest, compression='snappy')
    print(f'[LOAD] Fichier : {dest}')

# Exécution
df = extract(url)
df = transform(df)
load(df, 'dvf_clean.parquet')
```

### Q17 : Batch vs Temps Réel
```python
# Batch = traitement ponctuel de tous les données à la fois
# Temps réel = traitement des données au fur et à mesure

# Caractéristiques batch :
# - Une URL = un export complet
# - Traitement une fois par an/mois
# - Latence acceptable
```

### Q18 : Cas d'Usage Temps Réel
```python
# Exemple : plateforme d'annonces immobilières
# - Nouvelles transactions temps réel
# - Mise à jour prix au m² en direct
# - Alertes sur nouvelles ventes
```

### Q19 : Avantages Parquet
```python
# Parquet > CSV car :
# - Format binaire optimisé (colonaire)
# - Compression native (snappy, gzip)
# - Lecture sélective de colonnes
# - Préservation des types
# - Typiquement 10-20x plus petit
```

### Q20 : Types après Chargement
```python
# Charger CSV et Parquet
df_csv = pd.read_csv('dvf_clean.csv')
df_pq = pd.read_parquet('dvf_clean.parquet')

# Comparer dtypes
print(df_csv.dtypes)
print(df_pq.dtypes)

# Parquet préserve mieux les types (datetime, etc.)
```

### Q21 : ETL vs ELT
```python
# ETL (Extract-Transform-Load) :
# 1. Charger données brutes
# 2. NETTOYER ici ← your workflow
# 3. Charger en base

# ELT (Extract-Load-Transform) :
# 1. Charger données brutes
# 2. Charger directement en warehouse
# 3. Nettoyer dans la warehouse SQL

# Vous avez fait ETL : transformation AVANT stockage final
```

---

## 📝 Section 5 : Synthèse Rédigée (1 Question)

### Q22 : Rapport Qualité (~150 mots)
```markdown
# Rapport Qualité - DVF Nord 2023

## 1. Complétude
- **Constat** : XXX% de données manquantes en moyenne
  Colonnes critiques : id_mutation (0%), prix (5%)
- **Recommandation** : Ignorer colonnes >30%, imputer les 5-30%

## 2. Conformité
- **Constat** : Dates mal parsées, identifiants en float
- **Recommandation** : Valider types avant analyse, utiliser schéma stricte

## 3. Unicité
- **Constat** : XXX doublons détectés
- **Recommandation** : Dédupliquer sur clé (id_mutation, code_postal, date)

## 4. Précision
- **Constat** : Valeurs aberrantes (prix_m2 > 50k€/m²)
- **Recommandation** : Filtrer percentiles 1-99%, marquer en flag

Conclusion : Dataset utilisable après nettoyage ciblé.
```

---

## 🚀 Ordre de Travail Recommandé

1. ✅ **Imports** : importer les librairies
2. 🔧 **Q1-Q4** : Charger, explorer, corriger types
3. 📊 **Q5-Q9** : Analyser prix et tendances
4. 🧹 **Q10-Q14** : Évaluer et nettoyer qualité
5. 🏗️ **Q15-Q21** : Structurer en pipeline
6. 📝 **Q22** : Rédiger synthèse

**Durée estimée** : 4-6 heures selon expérience

---

## 📌 Ressources OpenData

- **Base DVF** : https://files.data.gouv.fr/geo-dvf/
- **Documentation** : https://data.gouv.fr/datasets/demandes-de-valeurs-foncieres-dvf
- **Département 59** : https://files.data.gouv.fr/geo-dvf/latest/csv/2023/departements/59.csv.gz

---

## ✨ Astuces Pratiques

| Problème | Solution |
|----------|----------|
| Fichier .gz trop volumineux | Utiliser `compression='gzip'` dans read_csv |
| low_memory=False donne warning | C'est normal, DataFrame hétérogène |
| Date ne parse pas | `pd.to_datetime(df['date'], format='%Y-%m-%d')` |
| Prix aberrants | IQR : Q3 + 1.5*(Q3-Q1) |
| Graphiques trop petits | `plt.rcParams['figure.figsize'] = (15, 8)` |
| Affichage colonnes coupé | `pd.set_option('display.max_columns', None)` |

---

## 📞 Questions Fréquentes

**Q: Combien de lignes le dataset contient-il ?**
A: Environ 20,000-30,000 transactions pour le Nord en 2023

**Q: Dois-je télécharger le fichier ou utiliser l'URL directe ?**
A: L'URL directe suffit avec `pd.read_csv(url, compression='gzip')`

**Q: Parquet ou CSV pour le rendu ?**
A: Parquet recommandé (plus compact, types préservés)

**Q: Puis-je utiliser Google Colab ?**
A: Oui, mais attention à la limite de RAM (~13 GB)

---

**Bonne chance ! 🎓**
