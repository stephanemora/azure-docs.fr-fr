---
title: 'Tutoriel : Créer un modèle de clustering en R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dans la deuxième partie de ce tutoriel, vous allez créer un modèle k-moyennes en R pour effectuer un clustering en R avec Azure SQL Database Machine Learning Services (préversion).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453095"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutoriel : Créer un modèle de clustering en R avec Azure SQL Database Machine Learning Services (préversion)

Dans la deuxième partie de cette série de didacticiels qui en compte trois, vous allez créer un modèle k-moyennes dans R pour effectuer le clustering. Dans la partie suivante de cette série, vous allez déployer ce modèle dans une base de données SQL avec Azure SQL Database Machine Learning Services (préversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Définir le nombre de clusters pour un algorithme K-moyennes
> * Effectuer le clustering
> * Analyser les résultats

Dans la [première partie](sql-database-tutorial-clustering-model-prepare-data.md), vous avez appris à préparer les données d’une base de données Azure SQL afin d’effectuer un clustering.

Dans la [troisième partie](sql-database-tutorial-clustering-model-deploy.md), vous allez découvrir comment créer une procédure stockée dans une base de données Azure SQL qui vous permettra d’effectuer un clustering en R basé sur des nouvelles données.

## <a name="prerequisites"></a>Prérequis

* La deuxième partie de ce didacticiel part du principe que vous avez suivi la [**première partie**](sql-database-tutorial-clustering-model-prepare-data.md) et ses conditions préalables.

## <a name="define-the-number-of-clusters"></a>Définir le nombre de clusters

Pour regrouper vos données client en cluster, vous allez utiliser l’algorithme de clustering **K-moyennes**, l’un des moyens les plus simples et les plus connus de regrouper des données.
Pour plus d’informations sur K-moyennes, consultez [A complete guide to K-means clustering algorithm](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

L’algorithme accepte deux entrées : les données elles-mêmes et un nombre prédéfini « *k* » représentant le nombre de clusters à générer.
En sortie, vous obtenez *k* clusters et les données d’entrée sont partitionnées entre les clusters.

Pour déterminer le nombre de clusters que doit utiliser l’algorithme, utilisez un tracé de la somme des carrés intra groupe (« within groups sum of squares ») par rapport au nombre de clusters extraits. Le nombre approprié de clusters à utiliser se trouve au niveau de la courbe ou du « coude » du tracé.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Graphique coudé](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

D’après le graphique, il semble que *k = 4* serait une bonne valeur à tester. Cette valeur *k* regroupe les clients dans quatre clusters.

## <a name="perform-clustering"></a>Effectuer le clustering

Dans le script R suivant, vous allez utiliser la fonction **rxKmeans**, qui est la fonction k-moyennes du package RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analyser les résultats

Maintenant que vous avez terminé le clustering à l’aide de l’algorithme k-moyennes, l’étape suivante consiste à analyser les résultats afin de voir s’ils contiennent des informations exploitables.

L’objet **clust** contient les résultats du clustering k-moyennes.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Les moyennes des quatre clusters sont fournies à partir des variables définies dans la [première partie](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers) :

* *orderRatio* = retourne le taux de commandes (nombre total de commandes partiellement ou entièrement retournées par rapport au nombre total de commandes)
* *itemsRatio* = retourne le taux d’éléments (nombre total d’éléments retournés par rapport au nombre d’éléments achetés)
* *monetaryRatio* = retourne le taux des montants (montant monétaire total des éléments retournés par rapport au montant acheté)
* *frequency* = fréquence de retour

L’exploration de données avec K-moyennes demande souvent une analyse plus approfondie des résultats ainsi que des étapes supplémentaires pour mieux comprendre chaque cluster, mais cela peut donner de bonnes pistes.
Voici différentes interprétations possibles de ces résultats :

* Le cluster 1 (le plus grand cluster) semble correspondre à un groupe de clients inactifs (toutes les valeurs sont à zéro).
* Le cluster 3 semble être un groupe qui se distingue pour ce qui est des retours.

## <a name="clean-up-resources"></a>Nettoyer les ressources

***Si vous n’avez pas l’intention de poursuivre ce tutoriel***, supprimez la base de données tpcxbb_1gb de votre serveur Azure SQL Database.

Sur le portail Azure, procédez comme suit :

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources** ou **Bases de données SQL**.
1. Dans le champ **Filtrer par nom...** , entrez **tpcxbb_1gb**, puis sélectionnez votre abonnement.
1. Sélectionnez votre base de données **tpcxbb_1gb**.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans la deuxième partie de cette série de tutoriels, vous avez effectué les étapes suivantes :

* Définir le nombre de clusters pour un algorithme K-moyennes
* Effectuer le clustering
* Analyser les résultats

Pour déployer le modèle Machine Learning que vous avez créé, suivez la troisième partie de cette série de didacticiels :

> [!div class="nextstepaction"]
> [Tutoriel : Déployer un modèle de clustering en R avec Azure SQL Database Machine Learning Services (préversion)](sql-database-tutorial-clustering-model-deploy.md)