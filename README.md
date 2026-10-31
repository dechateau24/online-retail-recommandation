# Recommandation de produits : Online Retail II

Projet de data mining (INSEEC Paris), mission C : *que proposer à un client parmi les produits qu'il n'a pas encore achetés ?*

## Contexte

Un site de vente en ligne britannique veut afficher à ses clients des produits qu'ils sont susceptibles d'acheter. On se place du côté du responsable produit : à partir de l'historique d'achats, quels cinq ou dix produits proposer à un client donné, en excluant ceux qu'il a déjà pris ?

## Données

Jeu de données [Online Retail II](https://archive.ics.uci.edu/dataset/502/online+retail+ii) (UCI Machine Learning Repository, licence CC BY 4.0) : 1 067 371 lignes de transactions, de décembre 2009 à décembre 2011, 5 942 clients, 5 305 produits, 43 pays.

Le fichier n'est pas fourni dans ce dépôt. Pour le récupérer, téléchargez `online_retail_II.xlsx` depuis la page UCI et placez-le à côté du notebook. Le chargement direct par `ucimlrepo` ne fonctionne pas pour ce jeu de données.

## Démarche

### 1. Nettoyage

| Étape | Lignes retirées |
|---|---|
| Annulations (factures commençant par C), mises de côté | 19 494 |
| Quantité ou prix non positifs | 6 207 |
| Codes qui ne sont pas des produits (port, frais bancaires, tests...) | 4 793 |
| Doublons exacts | 33 663 |

Les lignes sans identifiant client (22,6 % après nettoyage) sont exclues, puisqu'on ne recommande rien à un client anonyme.

### 2. Périmètre

- Pays : Royaume-Uni
- Période : année 2010
- Produits : les 1 000 présents dans le plus de factures. Ils couvrent 76 % des lignes, et 3 498 clients sur 3 817 en ont acheté au moins 5.

### 3. Représentation et méthodes

Matrice client × produit en 0/1 (1 si le client a acheté le produit). Densité de 4 % environ. Trois méthodes comparées :

- **Produits populaires** (référence) : on recommande les produits les plus achetés que le client n'a pas.
- **Similarité cosinus entre produits** : on additionne les similarités entre chaque produit candidat et ceux déjà achetés par le client.
- **Similarité cosinus entre clients** : on regarde ce qu'ont acheté les 30 clients les plus proches.

### 4. Évaluation

Découpage dans le temps : apprentissage avant le 1er novembre 2010, test en novembre-décembre. On évalue sur les 1 252 clients qui avaient au moins 5 produits en apprentissage et qui ont acheté au moins un produit nouveau pendant le test. Mesures : précision@k, rappel@k et part de clients pour lesquels au moins une recommandation est bonne.

## Résultats

| k | Méthode | Précision@k | Rappel@k | Au moins 1 bon |
|---|---|---|---|---|
| 5 | Populaires | 0,04 | 0,01 | 18 % |
| 5 | Cosinus produits | 0,08 | 0,03 | 28 % |
| 5 | Cosinus clients | 0,09 | 0,04 | 30 % |
| 10 | Populaires | 0,04 | 0,03 | 30 % |
| 10 | Cosinus produits | 0,07 | 0,05 | 39 % |
| 10 | Cosinus clients | 0,07 | 0,06 | 43 % |
| 20 | Populaires | 0,04 | 0,05 | 43 % |
| 20 | Cosinus produits | 0,06 | 0,09 | 53 % |
| 20 | Cosinus clients | 0,06 | 0,10 | 56 % |

Les deux méthodes par similarité font environ deux fois mieux que la référence à k = 5. Entre elles, l'écart est trop faible pour conclure sur 1 252 clients. Les valeurs absolues restent basses : on cherche une dizaine de produits nouveaux parmi près de 900 candidats.

## Limites

- Une seule période de test, qui inclut Noël.
- Évaluation limitée aux clients britanniques actifs fin 2010.
- Les achats sans identifiant client ne sont pas couverts.
- Les produits hors du top 1 000, ou apparus après octobre 2010, ne peuvent pas être recommandés.
- On mesure des achats ultérieurs, pas l'effet réel d'une recommandation affichée. Seul un test en conditions réelles pourrait le dire.

## Décisions possibles

Afficher cinq recommandations par similarité entre produits sur la page panier, pour les clients ayant au moins cinq produits achetés, et tester pendant un mois contre un affichage des produits populaires en suivant le taux de clic et le panier moyen.

## Utilisation

1. Ouvrir le notebook dans Google Colab ou Jupyter.
2. Placer `online_retail_II.xlsx` à côté.
3. Exécuter les cellules dans l'ordre. La lecture du fichier Excel prend plusieurs minutes.

Bibliothèques : `pandas`, `numpy`, `matplotlib`, `scikit-learn`, `openpyxl`.

## Auteurs

Groupe mission C, INSEEC Paris : *(noms des membres)*
