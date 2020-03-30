---
title: 'Tutoriel : Préparer les données pour entraîner un modèle prédictif en R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dans la première partie de cette série de tutoriels qui en compte trois, vous allez préparer des données à partir d’une base de données Azure SQL pour entraîner un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion).
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
ms.openlocfilehash: 505f58f13a7186948a228fefe872d74fb98eba33
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345770"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutoriel : Préparer des données pour entraîner un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion)

Dans la première partie de cette série de tutoriels qui en compte trois, vous allez importer et préparer des données à partir d’une base de données Azure SQL à l’aide de R. Plus tard dans cette série, vous allez utiliser ces données pour entraîner et déployer un modèle Machine Learning prédictif en R avec Azure SQL Database Machine Learning Services (préversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Pour cette série de tutoriels, imaginez que vous avez une entreprise de location de skis et que vous souhaitez prévoir le nombre de locations à venir à une date ultérieure. Ces informations vous aideront à préparer votre inventaire, votre personnel et vos installations.

Dans les parties 1 et 2 de cette série, vous allez développer des scripts en R dans RStudio pour préparer vos données et entraîner un modèle de Machine Learning. Ensuite, dans la troisième partie, vous allez exécuter ces scripts en R à l’intérieur d’une base de données SQL à l’aide de procédures stockées.

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Importation d’un exemple de base de données dans une base de données Azure SQL à l’aide de R
> * Chargement des données à partir de la base de données Azure SQL dans un cadre de données à l’aide de R
> * Préparation des données en R en identifiant certaines colonnes comme catégoriques

Dans la [deuxième partie](sql-database-tutorial-predictive-model-build-compare.md), vous allez apprendre à créer et à entraîner plusieurs modèles Machine Learning en R, puis à choisir le plus précis.

Dans la [troisième partie](sql-database-tutorial-predictive-model-deploy.md), vous allez apprendre à stocker le modèle dans une base de données, puis à créer des procédures stockées à partir des scripts R développés dans les première et deuxième parties. Les procédures stockées seront exécutées dans une base de données SQL pour effectuer des prédictions basées sur de nouvelles données.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/) avant de commencer.

* Serveur Azure SQL Database avec Machine Learning Services activé : dans la préversion publique, Microsoft vous intègre et active le machine learning pour votre base de données nouvelle ou existante. Suivez les étapes pour vous [inscrire à la préversion](sql-database-machine-learning-services-overview.md#signup).

* Package RevoScaleR : consultez [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) pour connaître les options qui sont nécessaires à l’installation de ce package localement.

* R IDE : ce tutoriel utilise [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Outil de requête SQL : ce tutoriel suppose que vous utilisez [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importer l’exemple de base de données

L’exemple de jeu de données utilisé dans ce tutoriel a été enregistré dans un fichier de sauvegarde de base de données **.bacpac** pour que vous puissiez le télécharger et l’utiliser.

1. Téléchargez le fichier [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Suivez les instructions dans [Importer un fichier BACPAC pour créer une base de données Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-import), en tenant compte des précisions suivantes :

   * Procédez à l’importation à partir du fichier **TutorialDB.bacpac** que vous avez téléchargé
   * Dans la préversion publique, choisissez la configuration **Gen5/vCore** pour la nouvelle base de données
   * Nommez la nouvelle base de données « TutorialDB »

## <a name="load-the-data-into-a-data-frame"></a>Charger les données dans une trame de données

Pour utiliser les données en R, vous devez charger les données à partir de la base de données Azure SQL dans un cadre de données (`rentaldata`).

Créez un fichier RScript dans RStudio et exécutez le script suivant. Remplacez **Server**, **UID** et **PWD** par vos propres informations de connexion.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

Vous devez obtenir des résultats similaires à ce qui suit :

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Préparer les données

Dans cet exemple de base de données, la majeure partie de la préparation a déjà été effectuée, mais vous allez réaliser une dernière préparation ici.
Utilisez le script R suivant pour identifier trois colonnes en tant que *catégories*, en changeant les types de données pour *factor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Vous devez obtenir des résultats similaires à ce qui suit :

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Les données sont désormais prêtes pour l’entraînement.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas l’intention de poursuivre ce didacticiel, supprimez la base de données TutorialDB de votre serveur Azure SQL Database.

Sur le portail Azure, procédez comme suit :

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources** ou **Bases de données SQL**.
1. Dans le champ **filtrer par nom...** , entrez **TutorialDB**, puis sélectionnez votre abonnement.
1. Sélectionnez votre base de données TutorialDB.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans la première partie de cette série de tutoriels, vous avez effectué les étapes suivantes :

* Importation d’un exemple de base de données dans une base de données Azure SQL à l’aide de R
* Chargement des données à partir de la base de données Azure SQL dans un cadre de données à l’aide de R
* Préparation des données en R en identifiant certaines colonnes comme catégoriques

Pour créer un modèle Machine Learning qui utilise des données à partir de la base de données TutorialDB, suivez la deuxième partie de cette série de tutoriels :

> [!div class="nextstepaction"]
> [Tutoriel : Créer un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion)](sql-database-tutorial-predictive-model-build-compare.md)
