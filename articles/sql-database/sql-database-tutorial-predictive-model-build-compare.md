---
title: 'Tutoriel : Former et comparer des modèles prédictifs en R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dans la deuxième partie de cette série de didacticiels qui en compte trois parties, vous allez créer deux modèles prédictifs en R avec le moteur Machine Learning Services (préversion) d’Azure SQL Database, puis sélectionner le modèle le plus précis.
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
ms.date: 07/26/2019
ms.openlocfilehash: 52f4347fa545df88f11878709b4061662d5faffe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345752"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutoriel : Créer un modèle prédictif en R avec le moteur Machine Learning Services (préversion) d’Azure SQL Database

Dans la deuxième partie de cette série de didacticiels qui en compte trois parties, vous allez créer deux modèles prédictifs en R et sélectionner le modèle le plus précis. Dans la partie suivante de cette série, vous allez déployer ce modèle dans une base de données SQL avec Azure SQL Database Machine Learning Services (préversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Effectuer l’apprentissage de deux modèles Machine Learning
> * Effectuer des prédictions à partir des deux modèles
> * Comparer les résultats pour choisir le modèle le plus précis

Dans la [première partie](sql-database-tutorial-predictive-model-prepare-data.md), vous avez appris à importer un exemple de base de données, puis à préparer les données destinées à utiliser pour entraîner un modèle prédictif en R.

Dans la [troisième partie](sql-database-tutorial-predictive-model-deploy.md), vous allez apprendre à stocker le modèle dans une base de données, puis à créer des procédures stockées à partir des scripts R développés dans les première et deuxième parties. Les procédures stockées seront exécutées dans une base de données SQL pour effectuer des prédictions basées sur de nouvelles données.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

* La deuxième partie de ce didacticiel part du principe que vous avez suivi la [**première partie**](sql-database-tutorial-predictive-model-prepare-data.md) et ses conditions préalables.

## <a name="train-two-models"></a>Effectuer l’apprentissage de deux modèles

Pour trouver le meilleur modèle pour des données de location de skis, créez deux modèles différents (régression linéaire et arbre de décision), puis voyez lequel des deux fournit les prédictions les plus précises. Vous allez utiliser le cadre de données `rentaldata` que vous avez créé dans la première partie de cette série.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Effectuer des prédictions à partir des deux modèles

Utilisez une fonction predict pour prédire le nombre de locations à l’aide de chaque modèle formé.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Comparer les résultats

Vous voulez à présent voir lequel des modèles produit les meilleures prédictions. Un moyen simple et rapide pour ce faire consiste à utiliser une fonction de traçage de base afin de voir la différence entre les valeurs réelles de vos données d’entraînement et les valeurs prédites.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Comparaison des deux modèles](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Il semble que le modèle de type arbre de décision est le plus précis des deux.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas l’intention de poursuivre ce didacticiel, supprimez la base de données TutorialDB de votre serveur Azure SQL Database.

Sur le portail Azure, procédez comme suit :

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources** ou **Bases de données SQL**.
1. Dans le champ **filtrer par nom...** , entrez **TutorialDB**, puis sélectionnez votre abonnement.
1. Sélectionnez votre base de données TutorialDB.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans la deuxième partie de cette série de tutoriels, vous avez effectué les étapes suivantes :

* Effectuer l’apprentissage de deux modèles Machine Learning
* Effectuer des prédictions à partir des deux modèles
* Comparer les résultats pour choisir le modèle le plus précis

Pour déployer le modèle Machine Learning que vous avez créé, suivez la troisième partie de cette série de didacticiels :

> [!div class="nextstepaction"]
> [Tutoriel : Déployer un modèle prédictif en R avec le moteur Machine Learning Services (préversion) d’Azure SQL Database](sql-database-tutorial-predictive-model-deploy.md)
