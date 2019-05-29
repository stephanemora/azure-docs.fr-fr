---
title: 'Didacticiel : Déployer un modèle prédictif en R'
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
ms.date: 05/02/2019
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978701"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Didacticiel : Déployer un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion)

Dans le troisième volet de ce tutoriel en trois parties, vous allez déployer un modèle prédictif en R avec Azure SQL Database Machine Learning Services (préversion).

Vous allez créer une procédure stockée avec un script R incorporé qui effectue des prédictions avec le modèle. Du fait que votre modèle s’exécute dans la base de données Azure SQL, il peut facilement être entraîné sur des données stockées dans la base de données.

Cet article porte sur les points suivants :

> [!div class="checklist"]
> * Stockage du modèle prédictif dans une table de base de données
> * Création d’une procédure stockée qui génère le modèle
> * Création d’une procédure stockée qui effectue des prédictions avec le modèle
> * Exécution du modèle avec de nouvelles données

Dans la [première partie](sql-database-tutorial-predictive-model-prepare-data.md), vous avez appris à importer un exemple de base de données dans une base de données Azure SQL, puis à préparer les données destinées à être utilisées dans l’entraînement d’un modèle prédictif en R.

Dans la [deuxième partie](sql-database-tutorial-predictive-model-build-compare.md), vous avez appris à créer et à entraîner plusieurs modèles, puis à choisir le plus précis.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

* La troisième partie de cette série de tutoriels suppose que vous avez préalablement effectué dans leur intégralité la [**première partie**](sql-database-tutorial-predictive-model-prepare-data.md) et la [**deuxième partie**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Création d’une procédure stockée qui génère le modèle

Dans la deuxième partie de cette série de tutoriels, vous avez décidé qu’un modèle d’arbre de décision (dtree) était le plus précis. Créez à présent une procédure stockée (`generate_rental_rx_model`) qui entraîne et génère le modèle dtree avec rxDTree à partir du package RevoScaleR.

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

## <a name="execute-the-model-with-new-data"></a>Exécution du modèle avec de nouvelles données

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

Vous avez correctement créé, entraîné et déployé un modèle dans une base de données Azure SQL. Vous avez ensuite utilisé ce modèle dans une procédure stockée pour prédire des valeurs basées sur de nouvelles données.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’avez plus besoin de la base de données TutorialDB, supprimez-la de votre serveur Azure SQL Database.

Dans le portail Azure, suivez ces étapes :

1. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources** ou **Base de données SQL**.
1. Dans le champ **filtrer par nom...** , entrez **TutorialDB**, puis sélectionnez votre abonnement.
1. Sélectionnez votre base de données TutorialDB.
1. Dans la page **Vue d’ensemble**, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans la troisième partie de cette série de tutoriels, vous avez effectué les étapes suivantes :

* Stockage du modèle prédictif dans une table de base de données
* Création d’une procédure stockée qui génère le modèle
* Création d’une procédure stockée qui effectue des prédictions avec le modèle
* Exécution du modèle avec de nouvelles données

Pour en savoir plus sur l’utilisation de R dans Azure SQL Database Machine Learning Services (préversion), voir :

* [Écrire des fonctions R avancées dans Azure SQL Database avec Machine Learning Services (préversion)](sql-database-machine-learning-services-functions.md)
* [Exploiter des données R et SQL dans Azure SQL Database Machine Learning Services (préversion)](sql-database-machine-learning-services-data-issues.md)
* [Ajouter un package R à Azure SQL Database Machine Learning Services (préversion)](sql-database-machine-learning-services-add-r-packages.md)
