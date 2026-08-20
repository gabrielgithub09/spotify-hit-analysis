# Spotify Top 100 Songs (2010-2019) : Analyse pour un label musical

Projet personnel, mené de ma propre initiative par intérêt pour l'analyse de données appliquée à la musique : SQL, Python, HTML/CSS/JavaScript pour le dashboard interactif. Une reconstruction Power BI est en cours (voir `docs/powerbi_guide.md`).

**Question business :** quelles caractéristiques rendent une chanson populaire ?

**Dashboard interactif en ligne :** [gabrielgithub09.github.io/spotify-hit-analysis/dashboard/spotify_dashboard.html](https://gabrielgithub09.github.io/spotify-hit-analysis/dashboard/spotify_dashboard.html)

## Structure du repo

```
data/
  spotify_raw.xlsx          fichier source (Kaggle, enrichi)
  spotify_clean.csv         dataset nettoyé (1000 lignes x 23 colonnes)
  spotify.db                base SQLite, schéma en étoile
  query_results/            résultats des 11 requêtes SQL + matrice de corrélation

data_v2/
  spotify_extended_clean.csv     dataset étendu nettoyé (89 740 titres uniques, hits + flops, popularité 0-100)
  spotify_extended.db            base SQLite correspondante
  extended_analysis_results.json résultats de la régression/classification (section 7 du rapport)
  key_mode_analysis.json         résultats de l'analyse tonalité/mode (annexe)
  query_results/                 résultats des 5 requêtes SQL étendues
  track_genre_long.csv           table longue titre <-> micro-genre (avant dédoublonnage)

python/
  01_clean_data.py            nettoyage & feature engineering (dataset original)
  02_build_db.py              construction du schéma en étoile SQLite (dataset original)
  03_run_queries.py           exécution + export des requêtes SQL (dataset original)
  04_analysis.py               corrélations + régression linéaire (dataset original)
  05_charts.py                 graphiques statiques (matplotlib) pour le rapport
  06_clean_extended.py         nettoyage du dataset étendu (89 740 titres)
  07_range_restriction_test.py test de l'hypothèse de restriction de gamme + classification hit/non-hit
  08_build_extended_db.py      base SQLite + requêtes sur le dataset étendu
  09_key_mode_analysis.py      analyse complémentaire tonalité/mode
  10_hit_classifier_chart.py   graphique statique du classifieur hit/non-hit pour le rapport

sql/
  analysis_queries.sql          11 requêtes sur le dataset original (CTE, window functions, jointures)
  extended_analysis_queries.sql 5 requêtes sur le dataset étendu (RANK, agrégations, dénormalisation justifiée)

dashboard/
  spotify_dashboard.html    dashboard interactif autonome (ouvrir dans un navigateur, ou en ligne via GitHub Pages)

docs/
  powerbi_guide.md          guide pas-à-pas pour reconstruire le rapport dans Power BI Desktop
  Spotify_Project_Report.docx   rapport complet (méthodologie, insights, glossaire, section 7 = extension)
  assets/, assets_v2/       graphiques utilisés dans le rapport
```

## Reproduire l'analyse

```bash
cd python
# Dataset original (1000 titres, Top 100 2010-2019)
python3 01_clean_data.py    # -> data/spotify_clean.csv
python3 02_build_db.py      # -> data/spotify.db
python3 03_run_queries.py   # -> data/query_results/*.csv
python3 04_analysis.py      # -> corrélations + régression
python3 05_charts.py        # -> docs/assets/*.png

# Dataset étendu (89 740 titres, hits + flops), test de robustesse
python3 06_clean_extended.py            # -> data_v2/spotify_extended_clean.csv
python3 07_range_restriction_test.py    # -> data_v2/extended_analysis_results.json
python3 08_build_extended_db.py         # -> data_v2/spotify_extended.db + requêtes SQL
python3 09_key_mode_analysis.py         # -> data_v2/key_mode_analysis.json
python3 10_hit_classifier_chart.py      # -> docs/assets_v2/chart_hit_classifier.png
```

Le dashboard s'ouvre directement (`dashboard/spotify_dashboard.html`), aucune installation nécessaire.

## Résultat principal

Les caractéristiques audio seules expliquent très peu la popularité d'un titre (R² = 0.06 sur le Top 100, 1000 titres). Testé à nouveau sur un second dataset indépendant de 89 740 titres (hits et flops confondus) : le R² reste tout aussi faible (0.028) ; la conclusion est confirmée, pas infirmée par la taille de l'échantillon. En revanche, ce second dataset permet de calculer un taux de conversion en hit par genre, impossible à obtenir sans données incluant des flops (hip-hop/r&b : 10.5% ; electronic/dance, le genre le plus volumineux : seulement 3.5%). Détails dans `docs/Spotify_Project_Report.docx`, section 7.

Sources des données :
- [Kaggle : Top Spotify songs from 2010-2019 by year](https://www.kaggle.com/datasets/leonardopena/top-spotify-songs-from-20102019-by-year/data)
- [Kaggle : Spotify Tracks Dataset (maharshipandya, 114k titres)](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset)
