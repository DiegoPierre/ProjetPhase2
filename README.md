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


sns.set(style="whitegrid")  # Style avec fond blanc et grille discrète
plt.rcParams["figure.figsize"] = (10, 6)  # Taille standard des graphiques

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



plt.figure(figsize=(8,5))
sns.histplot(filtered_movies['runtime_minutes'], bins=30, kde=True)
plt.title("Distribution de la durée des films")
plt.xlabel("Durée (minutes)")
plt.ylabel("Nombre de films")
plt.show()


 <div style="text-align: center;">
    <img src="Images/Screenshot 2025-07-19 104151.png" width="800">
    <p style="font-style: italic; color: #7f8c8d;">
    </p>
</div>
