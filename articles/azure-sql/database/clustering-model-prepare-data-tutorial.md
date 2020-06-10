---
title: 'Tutoriel : Préparer des données pour effectuer un clustering en R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dans la première partie de ce tutoriel, vous allez préparer les données d’une base de données Azure SQL pour effectuer un clustering en R avec Azure SQL Database Machine Learning Services (préversion).
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
ms.openlocfilehash: c06e1b13f87972cbcd50e888edf55158b77881d8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84024096"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutoriel : Préparer des données pour effectuer un clustering en R avec Azure SQL Database Machine Learning Services (préversion)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dans la première partie de cette série de tutoriels qui en compte trois, vous allez importer et préparer les données à partir d’une base de données Azure SQL à l’aide de R. Plus tard dans cette série, vous allez utiliser ces données pour entraîner et déployer un modèle de clustering en R avec Azure SQL Database Machine Learning Services (préversion).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Le *clustering* permet d’organiser les données dans des groupes dont les membres sont similaires.
Vous allez utiliser l’algorithme **K-moyennes** pour effectuer le clustering des clients dans un jeu de données d’achats et de retours de produits. Lorsque vous effectuez le clustering de clients, vous pouvez concentrer plus efficacement vos efforts marketing en ciblant des groupes spécifiques.
Le clustering K-myennes est un algorithme *d’apprentissage non supervisé* qui recherche des modèles dans les données en fonction de similarités.

Dans les parties 1 et 2 de cette série, vous allez développer des scripts en R dans RStudio pour préparer vos données et entraîner un modèle de Machine Learning. Ensuite, dans la troisième partie, vous allez exécuter ces scripts en R à l’intérieur d’une base de données SQL à l’aide de procédures stockées.

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
>
> * Importer un exemple de base de données dans Azure SQL Database
> * Séparer les clients selon différentes dimensions à l’aide de R
> * Chargement des données à partir de la base de données Azure SQL dans un cadre de données à l’aide de R

Dans la [deuxième partie](clustering-model-build-tutorial.md), vous allez voir comment créer et entraîner un modèle de clustering k-moyennes en R.

Dans la [troisième partie](clustering-model-deploy-tutorial.md), vous allez découvrir comment créer une procédure stockée dans une base de données Azure SQL qui vous permettra d’effectuer un clustering en R basé sur des nouvelles données.

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/) avant de commencer.

* [Azure SQL Database avec Machine Learning Services (avec R)](machine-learning-services-overview.md) activé.

* Package RevoScaleR : consultez [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) pour connaître les options qui sont nécessaires à l’installation de ce package localement.

* R IDE : ce tutoriel utilise [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Outil de requête SQL : ce tutoriel suppose que vous utilisez [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importer l’exemple de base de données

L’exemple de jeu de données utilisé dans ce tutoriel a été enregistré dans un fichier de sauvegarde de base de données **.bacpac** pour que vous puissiez le télécharger et l’utiliser. Ce jeu de données est dérivé du jeu de données [TPCX-BB](http://www.tpc.org/tpcx-bb/default.asp) fourni par le [TPC (Transaction Processing Performance Council)](http://www.tpc.org/default.asp).

1. Téléchargez le fichier [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Suivez les instructions dans [Importer un fichier BACPAC pour créer une base de données Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-import), en tenant compte des précisions suivantes :

   * Importez le fichier **tpcxbb_1gb.bacpac** que vous avez téléchargé.
   * Dans la préversion publique, choisissez la configuration **Gen5/vCore** pour la nouvelle base de données
   * Nommez la nouvelle base de données « tpcxbb_1gb ».

## <a name="separate-customers"></a>Séparer des clients

Créez un fichier RScript dans RStudio et exécutez le script suivant.
Dans la requête SQL, vous séparez les clients selon les dimensions suivantes :

* **orderRatio** = retourne le taux de commandes (nombre total de commandes partiellement ou entièrement retournées par rapport au nombre total de commandes)
* **itemsRatio** = retourne le taux d’éléments (nombre total d’éléments retournés par rapport au nombre d’éléments achetés)
* **monetaryRatio** = retourne le taux des montants (montant monétaire total des éléments retournés par rapport au montant acheté)
* **frequency** = fréquence de retour

Dans la fonction **paste**, remplacez **Server**, **UID** et **PWD** par vos propres informations de connexion.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE
            WHEN (returns_count IS NULL)
            THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
"
```

## <a name="load-the-data-into-a-data-frame"></a>Charger les données dans une trame de données

Maintenant, utilisez le script suivant pour retourner les résultats de la requête vers une trame de données R à l’aide de la fonction **rxSqlServerData**.
Dans le cadre de ce processus, vous allez définir le type des colonnes sélectionnées (avec colClasses) pour garantir que les types soient correctement transférés vers R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Vous devez obtenir des résultats similaires à ce qui suit :

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

***Si vous ne poursuivez pas ce tutoriel***, supprimez la base de données tpcxbb_1gb de votre serveur.

Sur le portail Azure, procédez comme suit :

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources** ou **Bases de données SQL**.
1. Dans le champ **Filtrer par nom...** , entrez **tpcxbb_1gb**, puis sélectionnez votre abonnement.
1. Sélectionnez votre base de données **tpcxbb_1gb**.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans la première partie de cette série de tutoriels, vous avez effectué les étapes suivantes :

* Importation d’un exemple de base de données dans une base de données Azure SQL
* Séparer les clients selon différentes dimensions à l’aide de R
* Chargement des données à partir de la base de données Azure SQL dans un cadre de données à l’aide de R

Pour créer un modèle Machine Learning qui utilise ces données clients, suivez la deuxième partie de ce tutoriel :

> [!div class="nextstepaction"]
> [Tutoriel : Créer un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion)](clustering-model-build-tutorial.md)
