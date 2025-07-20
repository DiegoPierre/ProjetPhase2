# 🎬 Analyse Exploratoire des Films : Genre, Durée, Popularité
## 
Dans ce projet, je explorai les données de films issues de la base IMDB pour comprendre :
## 1 Compréhension des affaires

###  Contexte
Un nouveau studio de cinéma souhaite comprendre les facteurs qui déterminent le succès d’un film. Pour cela, il dispose de données IMDB (genres, durée, notes, etc.) et souhaite s’appuyer sur une analyse exploratoire.

###  Objectif
Analyser les données pour fournir **3 recommandations stratégiques** permettant de :

- Produire des films populaires
- Maximiser les revenus
- Mieux planifier les productions (choix du genre, durée, équipe...)

###  Questions Clés

1. Quels genres plaisent le plus au public ?
2. Quelle est la durée idéale pour un film bien noté ?
3. Existe-t-il un lien entre popularité et succès financier ?
4. Quels types de films rapportent le plus au box-office ?

###  Impact attendu

Les résultats aideront le studio à :

- Choisir des genres et formats adaptés
- Optimiser les budgets et les durées
- Réduire les risques grâce aux données

  <div style="text-align: center;">
    <img src="Images/IMDB_ERD.jpeg" width="800">
    <p style="font-style: italic; color: #7f8c8d;">
    </p>
</div>

## Données volumineuses

Certaines données ne sont pas incluses dans le dépôt GitHub car elles dépassent la limite de taille de GitHub.

Vous pouvez les télécharger ici Puis Deziper le fichier copier la base de donnees im.db dans Notebooks :

👉 [Télécharger les données sur Google Drive Cliquer Sur Data](https://drive.google.com/drive/folders/1b0ARNFQVrvjQDW_qD9HMm8M_nbPl6Vxl?usp=drive_link)

## 2. Compréhension des données

Dans cette section, j'analyse la structure de la base de données **`im.db`** afin d’identifier les tables disponibles et déterminer lesquelles seront utiles pour répondre à nos **questions commerciales**.

La base contient plusieurs tables issues d'IMDB, représentant des informations sur les films, les notes du public, les équipes de production, etc.



###  Tables présentes dans la base de données `im.db`

| Nom de la table     | Description                                                                 |
|-------------------|-----------------------------------------------------------------------------|
| **movie_basics**   | Informations générales sur les films (titre, année, durée, genres, etc.)   |
| **movie_ratings**  | Notes moyennes et nombre de votes pour chaque film                         |
| **persons**        | Détails sur les personnes (nom, année de naissance, profession)            |
| **principals**     | Liste des principaux intervenants dans chaque film                         |
| **directors**      | Liens entre films et réalisateurs                                          |
| **writers**        | Liens entre films et scénaristes                                           |
| **movie_akas**     | Titres alternatifs des films (par région/langue)                           |
| **known_for**      | Films pour lesquels une personne est la plus connue                        |


### Objectif de l’analyse

Pour explorer les facteurs de succès d’un film, je vais principalement utiliser :

- **`movie_basics`** => titre, année, genre, durée
- **`movie_ratings`** => note moyenne (`averagerating`) et nombre de votes (`numvotes`)
- **`principals`** + **`persons`** => identification des principaux intervenants (acteurs, réalisateurs, scénaristes)

Les autres tables comme **`directors`**, **`writers`**, **`known_for`**, **`movie_akas`** pourront **enrichir** l’analyse, mais elles ne sont **pas centrales** pour les premières analyses.



###  Tables clés retenues pour l’analyse

1 **`movie_basics`** : informations générales sur les films.  
2 **`movie_ratings`** : popularité et notes des films.  
3 **`principals`** + **`persons`** : identification des équipes principales (cast & crew).



###  Stratégie de jointure prévue

- **`movie_basics`** sera la **table centrale** via la clé `movie_id`.
- **`movie_ratings`** sera directement reliée via `movie_id`.
- **`principals`** permettra de relier les films aux personnes (via `person_id`).
- **`persons`** donnera les détails sur les intervenants (nom, profession, etc.).
- Les tables **`directors`** et **`writers`** peuvent être utilisées pour des analyses spécifiques supplémentaires (ex : impact des réalisateurs sur les notes).


## Importation des librairies et configuration

Je commence par importer les principales librairies nécessaires à l'analyse :

- **pandas** : pour la manipulation et l'analyse des données tabulaires  
- **sqlite3** : pour se connecter à la base de données SQLite `im.db`  
- **matplotlib.pyplot** et **seaborn** : pour la visualisation des données  
- **numpy** : pour les calculs numériques et statistiques

Ensuite, nous configurons l'apparence des graphiques avec `seaborn` et `matplotlib` pour garantir des visualisations claires et cohérentes :
---

```python
sns.set(style="whitegrid")  # Style avec fond blanc et grille discrète
plt.rcParams["figure.figsize"] = (10, 6)  # Taille standard des graphiques
```

## 4. Analysis et Recommendations

###  Visualisations

- **Histogramme des notes**  
  Pour visualiser la distribution des notes moyennes des films.

- **Barplot des genres les mieux notés**  
  Permet d'identifier quels genres obtiennent les meilleures critiques du public.

- **Scatterplots** :
  - **Durée vs Note moyenne** : Analyse de la relation entre la longueur d’un film et sa popularité.
  - **Popularité vs Revenus** : Si les données du box-office sont disponibles, cette visualisation explore si les films populaires rapportent plus.



###  Analyses statistiques

- **Corrélations** :
  - **Durée vs Note moyenne** : Test de corrélation pour voir s’il existe un lien entre la longueur d’un film et son score IMDb.
  - **Popularité (nombre de votes) vs Note moyenne** : Vérification d’un éventuel biais de popularité.

- **Régression linéaire** :
  - **Durée => Note moyenne** : Modèle pour prédire la note moyenne en fonction de la durée d’un film.

- **ANOVA** :
  - Test d’analyse de variance pour voir si les différences de notes moyennes entre genres sont statistiquement significatives.


## Visualisation de la distribution des durées des films

Ce graphique représente un histogramme de la durée des films (`runtime_minutes`) avec une estimation de la densité (courbe KDE) superposée.  

- L’histogramme montre la répartition du nombre de films par intervalles de durée.  
- La courbe KDE permet d’avoir une estimation lisse de la densité des durées, mettant en évidence les tendances et les pics.

Cette visualisation aide à comprendre la distribution des durées et à détecter d’éventuelles valeurs atypiques ou modes dans les données.
-----

```python
plt.figure(figsize=(8,5))
sns.histplot(filtered_movies['runtime_minutes'], bins=30, kde=True)
plt.title("Distribution de la durée des films")
plt.xlabel("Durée (minutes)")
plt.ylabel("Nombre de films")
plt.show()
```

 <div style="text-align: center;">
    <img src="Images/Screenshot 2025-07-19 104151.png" width="800">
    <p style="font-style: italic; color: #7f8c8d;">
    </p>
</div>

## Distribution des notes arrondies (PMF)

- La colonne `averagerating` est arrondie à l'entier le plus proche (`rounded_rating`).  
- La fonction de masse de probabilité (PMF) est calculée en comptant la fréquence relative de chaque note arrondie.  
- Le graphique en barres représente la probabilité d'obtenir chaque note entière.  

Cette visualisation permet d’analyser la répartition des notes de manière discrète, facilitant la compréhension des tendances de notation.


```python
filtered_movies['rounded_rating'] = filtered_movies['averagerating'].round()
pmf = filtered_movies['rounded_rating'].value_counts(normalize=True).sort_index()
pmf.plot(kind='bar', color='skyblue')
plt.title("PMF - Répartition des notes")
plt.xlabel("Note arrondie")
plt.ylabel("Probabilité")
plt.show()

```
 <div style="text-align: center;">
    <img src="Images/Screenshot 2025-07-19 104048.png" width="800">
    <p style="font-style: italic; color: #7f8c8d;">
    </p>
</div>

## Distribution des notes moyennes (IMDb)

Ce graphique utilise un **KDE plot** (Kernel Density Estimation) pour visualiser la distribution continue des notes moyennes (`averagerating`) des films.

- La courbe lisse montre comment les notes sont réparties dans l’ensemble des films filtrés.  
- L’aire sous la courbe est remplie pour faciliter la lecture visuelle de la densité.  
- Cette représentation est utile pour identifier les concentrations de films autour de certaines notes, ainsi que pour détecter des asymétries ou anomalies.

Cela donne une vue plus fine et continue de la répartition des notes qu’un simple histogramme.

```python
plt.figure(figsize=(8,5))
sns.kdeplot(filtered_movies['averagerating'], fill=True, color='purple')
plt.title("Distribution des notes moyennes (IMDb)")
plt.xlabel("Note moyenne")
plt.ylabel("Densité")
plt.show()
```
 <div style="text-align: center;">
    <img src="Images/Screenshot 2025-07-19 104222.png" width="800">
    <p style="font-style: italic; color: #7f8c8d;">
    </p>
</div>

## Simulation du Théorème Central Limite (TCL)

Cette expérience illustre le **Théorème Central Limite (TCL)** à partir des notes moyennes des films.

### Étapes de la simulation :

- 1000 échantillons de taille 30 sont prélevés aléatoirement avec remise depuis la colonne `averagerating`.  
- Pour chaque échantillon, la moyenne est calculée et ajoutée à une liste.  
- Un histogramme des 1000 moyennes obtenues est ensuite tracé.

### Interprétation :

Même si la distribution initiale des notes (`averagerating`) n’est pas parfaitement normale,  
la distribution des **moyennes d’échantillons** tend à suivre une distribution normale.

Cela confirme empiriquement le **Théorème Central Limite**, qui garantit la normalité des moyennes d’échantillons pour des tailles d’échantillons suffisamment grandes (ici, n=30).

```python
means = []
for i in range(1000):
    sample = filtered_movies['averagerating'].sample(30, replace=True)
    means.append(sample.mean())

plt.hist(means, bins=30, color='green', alpha=0.7)
plt.title("Théorème Central Limite - Moyennes d'échantillons")
plt.xlabel("Moyenne des notes")
plt.ylabel("Fréquence")
plt.show()
```
<div style="text-align: center;">
    <img src="Images/Screenshot 2025-07-19 104251.png" width="800">
    <p style="font-style: italic; color: #7f8c8d;">
    </p>
</div>
## Matrice de corrélation entre les variables principales

Cette visualisation montre la corrélation entre trois variables clés du dataset :

- `averagerating` : note moyenne des films  
- `runtime_minutes` : durée des films en minutes  
- `numvotes` : nombre total de votes  

### Interprétation :

- La matrice de corrélation est affichée sous forme de **heatmap** avec des annotations des coefficients de corrélation.  
- Les valeurs varient entre **-1** (corrélation négative parfaite) et **+1** (corrélation positive parfaite).  
- Une valeur proche de **0** indique l’absence de corrélation linéaire.

Cette analyse permet d’identifier les relations linéaires éventuelles entre les variables, ce qui peut guider les choix d’analyses statistiques ou de modélisation.

```python
corr = movies[['averagerating', 'runtime_minutes', 'numvotes']].corr()
sns.heatmap(corr, annot=True, cmap="Blues")
plt.title("Corrélation entre les variables principales")
plt.show()
```
<div style="text-align: center;">
    <img src="Images/Screenshot 2025-07-19 104324.png" width="800">
    <p style="font-style: italic; color: #7f8c8d;">
    </p>
</div>

## Régression Linéaire : Durée des films vs Note moyenne

Un modèle de **régression linéaire** est construit pour analyser la relation entre :

- **Variable indépendante (X)** : `runtime_minutes` (durée du film en minutes)  
- **Variable dépendante (y)** : `averagerating` (note moyenne IMDb)

### Étapes :

1. Suppression des films dont la durée est manquante avec `dropna`.  
2. Ajout d’une constante (intercept) avec `sm.add_constant(X)` pour modéliser l’ordonnée à l’origine.  
3. Ajustement d’un modèle de régression linéaire avec `statsmodels.OLS`.  
4. Affichage des résultats avec `model.summary()`.

### Résultats principaux :

- **Coef. (runtime_minutes)** : indique l’effet d’une minute supplémentaire sur la note moyenne (positif, négatif ou nul).  
- **P-value** : permet de tester si cet effet est statistiquement significatif.  
- **R-squared** : mesure la proportion de la variance expliquée par le modèle (plus il est proche de 1, mieux c’est).

### Interprétation :

- Si la pente est significative et non nulle, la durée du film influence les notes moyennes.  
- Si la p-value est > 0.05, la relation n’est **pas significative** statistiquement.


# Conclusion et Prochaines Étapes

## Synthèse des Résultats Clés

- Certains **genres** se distinguent par leur combinaison de **popularité** (nombre de votes, box-office) et de **qualité perçue** (note moyenne).
- Une **durée optimale** de film autour de **90 à 120 minutes** semble associée à de meilleures performances critiques et commerciales.
- Les **acteurs et réalisateurs récurrents** dans les films à succès peuvent être identifiés pour orienter les castings futurs.
- La corrélation entre durée, note et popularité reste modérée mais significative pour certains segments.

## Limites de l’Analyse

- Les données IMDB et box-office utilisées couvrent principalement des films passés :  
  **les tendances récentes ou émergentes peuvent ne pas être capturées**.
- Certains films ont des **données manquantes** (durée, genres, recettes brutes), ce qui peut biaiser les résultats.
- L’analyse ne tient pas compte de facteurs qualitatifs importants : **scénario, innovation, contexte de sortie, plateformes de streaming, etc.**

## Pistes pour des Analyses Futures

1. **Utiliser l'API TMDB ou OMDB**  
   - Pour récupérer des données plus récentes ou plus détaillées (budget, casting complet, synopsis, etc.).

2. **Inclure des données financières externes**  
   - Budget de production  
   - ROI (retour sur investissement)  
   - Données par région ou par plateforme de diffusion (Netflix, Prime, etc.)

3. **Analyser les tendances récentes du marché**  
   - Évolution des genres populaires après 2023  
   - Impact des plateformes de streaming sur la durée optimale ou les formats préférés (séries vs films courts)

4. **Étendre l’analyse à l’international**  
   - Comparer les préférences entre les marchés (Amérique, Europe, Asie, etc.)

## Prochaine Étape

- Intégrer ces analyses dans un tableau de bord interactif (ex : **Streamlit, Dash**)  
- Automatiser la collecte de données pour suivre l’évolution des tendances en temps réel  
- Affiner les recommandations commerciales avec des données complémentaires
