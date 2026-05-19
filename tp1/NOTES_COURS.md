# Acquisition et Nettoyage de Données - Notes de Cours

## 📌 Session 1 - Pipelines de Données

### Qu'est-ce qu'un pipeline de données ?

**Définition:**
Un pipeline de données est une **séquence automatisée d'étapes** permettant de :
- Collecter les données
- Transformer les données
- Stocker les données
- Rendre les données exploitables par des modèles d'IA

### 5 Étapes Principales du Pipeline

```
Source → Ingestion → Transformation → Stockage → Exploitation
```

| Étape | Description |
|-------|------------|
| **Source** | API, BDD, fichiers |
| **Ingestion** | Récupérer les données |
| **Transformation** | Nettoyer, structurer les données |
| **Stockage** | CSV, Parquet, SQL, NosQL |
| **Exploitation** | Préparer pour les modèles d'IA |

### 3 Pratiques Clés d'un Pipeline Bien Conçu

1. **Reproductibilité** 
   - La chaîne complète doit être réplicable
   - Même données → Même résultats

2. **Scalabilité**
   - Volume croissant sans problème
   - Performance stable

3. **Traçabilité**
   - Chaque étape documentée
   - Erreurs identifiables
   - Audit possible

---

## � Session 2 - Stockage des Données : Data Warehouse vs Data Lake

### 📖 Différence Littérale

**Le Data Warehouse** est une infrastructure de stockage de données hautement organisée et structurée. Avant qu'une donnée ne soit intégrée dans un Data Warehouse, elle doit d'abord être nettoyée, transformée et conforme à un schéma prédéfini. Cela signifie que la structure de chaque colonne, chaque type de données, et chaque relation entre les données a déjà été déterminée avant l'insertion. Le Data Warehouse est optimisé pour des requêtes analytiques rapides et fiables, car les données sont déjà organisées de manière parfaite. Cependant, cette structure rigide rend le Data Warehouse moins flexible : si vous avez besoin d'ajouter de nouvelles données qui ne correspondent pas au schéma existant, il faut d'abord restructurer l'ensemble du système. De plus, maintenir et exploiter un Data Warehouse est coûteux en termes d'infrastructure et de ressources informatiques. Les exemples courants incluent SQL Server, Teradata ou Snowflake.

**Le Data Lake**, en revanche, est bien plus flexible et libéral dans son approche du stockage. Il accepte des données brutes, complètement désorganisées, sous de nombreux formats différents : données structurées (tables), semi-structurées (JSON, XML) ou entièrement non-structurées (images, vidéos, logs, texte brut). Il n'y a pas de schéma prédéfini ; les données sont stockées dans leur état brut et c'est seulement lors de leur lecture et exploitation que leur structure est définie. Cette approche rend le Data Lake très économe et flexible : on peut y stocker n'importe quoi sans préparation préalable. Cependant, cette flexibilité exige une expertise technique pour exploiter correctement les données, et il existe un risque que le Data Lake devienne désorganisé et inutilisable, ce qu'on appelle un "Data Swamp" (marécage de données). Les exemples incluent Hadoop, AWS S3 ou Google Cloud Storage.

### 💡 Nouvelle Tendance : Le "Data Lakehouse"
Les entreprises modernes combinent les deux approches pour créer un "Data Lakehouse", qui offre la flexibilité d'un Data Lake tout en maintenant la structure et les performances d'un Data Warehouse. Des technologies comme Delta Lake et Apache Iceberg permettent cette hybridation.

---

## �📝 Notes Supplémentaires

*(À compléter au fur et à mesure du cours)*

