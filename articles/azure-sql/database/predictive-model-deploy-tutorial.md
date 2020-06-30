---
title: 'Tutoriel : Déployer un modèle prédictif en R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dans le troisième volet de ce tutoriel en trois parties, vous allez déployer un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion).
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
ROBOTS: NOINDEX
ms.openlocfilehash: 84c0f6564fc9ab8abc43205b58d9445cda82a2da
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253815"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutoriel : Déployer un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dans la dernière partie de ce tutoriel qui en compte trois, vous allez déployer un modèle prédictif, développé en R, dans une base de données dans Azure SQL Database avec Azure SQL Database Machine Learning Services (préversion).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Vous allez créer une procédure stockée avec un script R incorporé qui effectue des prédictions avec le modèle. Étant donné que votre modèle s’exécute dans la base de données, son apprentissage peut facilement être effectué en fonction des données stockées dans la base de données.

Dans cet article, avec les scripts en R que vous avez développés dans les parties un et deux, vous allez apprendre à :

> [!div class="checklist"]
>
> * Créer une procédure stockée qui génère le modèle Machine Learning
> * Stocker le modèle dans une table de base de données
> * Créer une procédure stockée qui effectue des prédictions à l’aide du modèle
> * Exécuter le modèle avec de nouvelles données

Dans la [première partie](predictive-model-prepare-data-tutorial.md), vous avez appris à importer un exemple de base de données, puis à préparer les données destinées à utiliser pour entraîner un modèle prédictif en R.

Dans la [deuxième partie](predictive-model-build-compare-tutorial.md), vous avez appris à créer et à entraîner plusieurs modèles Machine Learning en R, puis à choisir le plus précis.

## <a name="prerequisites"></a>Prérequis

* La troisième partie de cette série de tutoriels suppose que vous avez préalablement effectué dans leur intégralité la [**première partie**](predictive-model-prepare-data-tutorial.md) et la [**deuxième partie**](predictive-model-build-compare-tutorial.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Créer une procédure stockée qui génère le modèle

Dans la deuxième partie de cette série de tutoriels, vous avez décidé qu’un modèle d’arbre de décision (dtree) était le plus précis. Maintenant, à l’aide des scripts en R que vous avez développés, créez une procédure stockée (`generate_rental_rx_model`) qui entraîne et génère le modèle dtree avec rxDTree à partir du package RevoScaleR.

Exécutez les commandes suivantes dans Azure Data Studio ou SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Stocker le modèle dans une table de base de données

Créez une table dans la base de données TutorialDB, puis enregistrez le modèle dans la table.

1. Créez une table (`rental_rx_models`) pour stocker le modèle.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Enregistrez le modèle dans la table en tant qu’objet binaire, en utilisant le nom de modèle « rxDTree ».

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;

    DECLARE @model VARBINARY(MAX);

    EXECUTE generate_rental_rx_model @model OUTPUT;

    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );

    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Créer une procédure stockée qui effectue des prédictions

Créez une procédure stockée (`predict_rentalcount_new`) qui effectue des prédictions à l’aide du modèle entraîné et d’un jeu de nouvelles données.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Exécuter le modèle avec de nouvelles données

Vous pouvez désormais utiliser la procédure stockée `predict_rentalcount_new` pour prédire le nombre de locations à partir de nouvelles données.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Vous devez voir un résultat similaire à celui qui suit.

```results
RentalCount_Predicted
332.571428571429
```

Vous avez correctement créé, entraîné et déployé un modèle dans une base de données dans Azure SQL Database. Vous avez ensuite utilisé ce modèle dans une procédure stockée pour prédire des valeurs en fonction de nouvelles données.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin de la base de données TutorialDB, supprimez-la de votre serveur.

Sur le portail Azure, procédez comme suit :

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources** ou **Bases de données SQL**.
1. Dans le champ **filtrer par nom...** , entrez **TutorialDB**, puis sélectionnez votre abonnement.
1. Sélectionnez votre base de données TutorialDB.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans la troisième partie de cette série de tutoriels, vous avez effectué les étapes suivantes :

* Créer une procédure stockée qui génère le modèle Machine Learning
* Stocker le modèle dans une table de base de données
* Créer une procédure stockée qui effectue des prédictions à l’aide du modèle
* Exécuter le modèle avec de nouvelles données

Pour en savoir plus sur l’utilisation de R dans Azure SQL Database Machine Learning Services (préversion), voir :

* [Écrire des fonctions R avancées dans Azure SQL Database avec Machine Learning Services (préversion)](machine-learning-services-functions.md)
* [Exploiter des données R et SQL dans Machine Learning Services d’Azure SQL Database (préversion)](machine-learning-services-data-issues.md)
* [Ajouter un package R à Azure SQL Database Machine Learning Services (préversion)](machine-learning-services-add-r-packages.md)
