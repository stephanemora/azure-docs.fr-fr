---
title: 'Tutoriel : Déployer un modèle de clustering en R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dans le troisième volet de ce tutoriel en trois parties, vous allez déployer un modèle de clustering en R avec Azure SQL Database Machine Learning Services (préversion).
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
ms.openlocfilehash: 6f4d237d5e923aab61ae34a235d2e1f759399e6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "68640908"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutoriel : Déployer un modèle de clustering en R avec Azure SQL Database Machine Learning Services (préversion)

Dans la dernière partie de ce didacticiel qui en compte trois, vous allez déployer un modèle de clustering développé en R dans une base de données SQL en utilisant le service Machine Learning Services (préversion) d’Azure SQL Database.

Vous allez créer une procédure stockée avec un script R incorporé qui effectue un clustering. Du fait que votre modèle s’exécute dans la base de données Azure SQL, il peut facilement être entraîné sur des données stockées dans la base de données.

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une procédure stockée qui génère le modèle
> * Effectuer le clustering dans SQL Database
> * Utiliser les informations de clustering

Dans la [première partie](sql-database-tutorial-clustering-model-prepare-data.md), vous avez appris à préparer les données d’une base de données Azure SQL afin d’effectuer un clustering.

Dans la [deuxième partie](sql-database-tutorial-clustering-model-build.md), vous avez appris à créer et à effectuer l’apprentissage d’un modèle de clustering k-moyennes en R.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

* La troisième partie de cette série de tutoriels suppose que vous avez préalablement effectué dans leur intégralité la [**première partie**](sql-database-tutorial-clustering-model-prepare-data.md) et la [**deuxième partie**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Créer une procédure stockée qui génère le modèle

Exécutez le script T-SQL suivant pour créer la procédure stockée. La procédure recrée les étapes que vous avez développées dans les parties 1 et 2 de cette série de tutoriels :

* classer les clients en fonction de leur historique d’achat et de retour
* générer quatre clusters de clients à l’aide d’un algorithme K-Means

La procédure stocke les mappages de clusters clients qui en résultent dans la table de base de données **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
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
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Effectuer le clustering dans SQL Database

Maintenant que vous avez créé la procédure stockée, exécutez le script suivant pour effectuer le clustering.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Vérifiez qu’il fonctionne et que vous obtenez bien la liste des clients et leurs mappages de cluster.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Utiliser les informations de clustering

Étant donné que vous avez stocké la procédure de clustering dans la base de données, elle peut effectuer efficacement le clustering sur les données client stockées dans la même base de données. Vous pouvez exécuter la procédure chaque fois que vos données client sont mises à jour et utiliser les informations de clustering mises à jour.

Supposons que vous souhaitiez envoyer un e-mail promotionnel aux clients du cluster 3, qui sont ceux pour qui les retours sont les plus fréquents (pour lire la description des quatre clusters, lisez la [deuxième partie](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). Le code suivant sélectionne les adresses e-mail des clients du cluster 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Vous pouvez changer la valeur **r.cluster** pour retourner les adresses e-mail des clients des autres clusters.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez terminé ce tutoriel, vous pouvez supprimer la base de données tpcxbb_1gb de votre serveur Azure SQL Database.

Sur le portail Azure, procédez comme suit :

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources** ou **Bases de données SQL**.
1. Dans le champ **Filtrer par nom...** , entrez **tpcxbb_1gb**, puis sélectionnez votre abonnement.
1. Sélectionnez votre base de données **tpcxbb_1gb**.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans la troisième partie de cette série de tutoriels, vous avez effectué les étapes suivantes :

* Créer une procédure stockée qui génère le modèle
* Effectuer le clustering dans SQL Database
* Utiliser les informations de clustering

Pour en savoir plus sur l’utilisation de R dans Azure SQL Database Machine Learning Services (préversion), voir :

* [Tutoriel : Préparer des données pour entraîner un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Écrire des fonctions R avancées dans Azure SQL Database avec Machine Learning Services (préversion)](sql-database-machine-learning-services-functions.md)
* [Exploiter des données R et SQL dans Machine Learning Services d’Azure SQL Database (préversion)](sql-database-machine-learning-services-data-issues.md)
* [Ajouter un package R à Azure SQL Database Machine Learning Services (préversion)](sql-database-machine-learning-services-add-r-packages.md)
