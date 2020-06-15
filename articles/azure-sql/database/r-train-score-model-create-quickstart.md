---
title: Créer et former un modèle prédictif dans R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Créez un modèle prédictif simple dans R à l’aide d’Azure SQL Database Machine Learning Services (préversion), puis prédisez un résultat à l’aide de nouvelles données.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 37cc50a31f2f31c0cb7fc49102328f2072c6570a
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324587"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Démarrage rapide : Créer et former un modèle prédictif dans R avec Azure SQL Database Machine Learning Services (préversion)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dans ce démarrage rapide, vous allez créer et entraîner un modèle prédictif avec R, l’enregistrer dans une table de votre base de données, puis utiliser ce modèle pour prédire des valeurs à partir de nouvelles données à l’aide de Machine Learning Services (avec R) dans Azure SQL Database.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Une [base de données dans Azure SQL Database](single-database-create-quickstart.md) avec une [règle de pare-feu au niveau du serveur](firewall-create-server-level-portal-quickstart.md)
- [Machine Learning Services](machine-learning-services-overview.md) avec R activé.
- [SSMS](/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio)

Cet exemple utilise un modèle de régression simple pour prédire la distance d’arrêt d’une voiture en fonction de la vitesse à l’aide du jeu de données **cars** inclus dans R.

> [!TIP]
> De nombreux jeux de données sont inclus dans le runtime R. Pour obtenir la liste de ceux qui sont installés, tapez `library(help="datasets")` à partir de l’invite de commandes R.

## <a name="create-and-train-a-predictive-model"></a>Créer et former un modèle prédictif dans R

Les données de vitesse des voitures dans le jeu de données **voitures** contient deux colonnes, toutes deux numériques : **dist** et **speed**. Les données incluent plusieurs observations d’arrêt à des vitesses différentes. À partir de ces données, vous allez créer un modèle de régression linéaire qui décrit la relation entre la vitesse de la voiture et la distance nécessaire pour qu’elle s’arrête.

Les exigences d’un modèle linéaire sont simples :
- Définissez une formule décrivant la relation entre la variable dépendante *speed* et la variable indépendante *distance*.
- Fournissez les données d’entrée à utiliser pour entraîner le modèle.

> [!TIP]
> Si vous avez besoin de rafraîchir vos connaissances sur les modèles linéaires, consultez le tutoriel suivant, qui décrit le processus d’ajustement des modèles linéaire à l’aide de rxLInMod : [Ajustement des modèles linéaires](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

Dans les étapes suivantes, vous allez configurer les données d’entraînement, créer un modèle de régression, puis l’entraîner avec les données d’entraînement et l’enregistrer dans une table SQL.

1. Ouvrez **SQL Server Management Studio** et connectez-vous à votre base de données.

   Si vous avez besoin d’aide pour la connexion, consultez [Démarrage rapide : Utiliser SQL Server Management Studio pour se connecter à une base de données dans Azure SQL Database et l’interroger](connect-query-ssms.md).

1. Créez la table **CarSpeed** pour enregistrer les données d’apprentissage.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Créez un modèle de régression avec `rxLinMod`. 

   Pour créer le modèle, vous devez définir la formule dans le code R et transmettre les données d’apprentissage **CarSpeed** en tant que paramètre d’entrée.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Le premier argument de rxLinMod est le paramètre *formula*, qui définit la distance comme étant dépendante de la vitesse. Les données d’entrée sont stockées dans la variable `CarsData`, qui est renseignée par la requête SQL.

1. Créez une table où vous stockerez le modèle afin de pouvoir l’utiliser ultérieurement pour la prédiction. 

   La sortie d’un package R qui crée un modèle est généralement un **objet binaire**, de sorte que la table doit avoir une colonne de type **VARBINARY(max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Appelez maintenant appeler la procédure stockée, créez le modèle et enregistrez-le dans une table.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Notez que si vous exécutez ce code une deuxième fois, vous obtenez cette erreur :

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Pour éviter cette erreur, vous pouvez mettre à jour le nom de chaque nouveau modèle. Vous pouvez par exemple utiliser un nom plus descriptif et y inclure le type de modèle, le jour de sa création, etc.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Afficher la table de coefficients

En règle générale, la sortie de R de la procédure stockée [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) est limité à une seule trame de données. Toutefois, vous pouvez retourner des sorties d’autres types, telles que des valeurs scalaires, en plus de la trame de données.

Par exemple, supposons que vous souhaitiez former un modèle, mais afficher immédiatement la table de coefficients à partir de ce dernier. Pour ce faire, créez la table de coefficients en tant que jeu de résultats principal, et retournez la sortie du modèle entraîné dans une variable SQL. Vous pouvez immédiatement réutiliser le modèle en appelant la variable, ou vous pouvez enregistrer le modèle dans une table, comme indiqué ici.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Résultats**

![Modèle entraîné avec sortie supplémentaire](./media/r-train-score-model-create-quickstart/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Noter les nouvelles données à l’aide du modèle formé

La *notation* est un terme utilisé dans la science des données pour signifier la génération de prédictions, de probabilités ou d’autres valeurs en fonction de nouvelles données introduites dans un modèle formé. Vous utiliserez le modèle que vous avez créé dans la section précédente pour noter les prédictions en fonction des nouvelles données.

Avez-vous remarqué que les données d’entraînement d’origine s’arrêtaient à une vitesse de 25 miles/h ? C’est parce que les données d’origine étaient basées sur une expérience réalisée en 1920 ! Vous seriez peut-être curieux de savoir combien de temps une voiture des années 20 mettrait à s’arrêter si elle pouvait atteindre une vitesse de 60, voire 100 miles à l’heure ? Pour répondre à cette question, vous pouvez fournir des nouvelles valeurs de vitesse pour votre modèle.

1. Créez une table avec de nouvelles données de vitesse.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Prédisez la distance d’arrêt à partir de ces nouvelles valeurs de vitesse.

   Étant donné que votre modèle est basé sur l’algorithme **rxLinMod** fourni dans le package **RevoScaleR**, vous devez appeler la fonction [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) plutôt que la fonction `predict` R générique.

   Cet exemple de script :
   - utilise une instruction SELECT pour récupérer un modèle unique dans la table ;
   - transmet une valeur en tant que paramètre d’entrée ;
   - appelle la fonction `unserialize` sur le modèle ;
   - applique la fonction `rxPredict` avec les arguments appropriés au modèle ;
   - fournit les nouvelles données d’entrée.

   > [!TIP]
   > Pour le scoring en temps réel, consultez les [Fonctions de sérialisation](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) fournies par RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Résultats**

   ![Jeu de résultats pour la prédiction de la distance d’arrêt](./media/r-train-score-model-create-quickstart/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> Dans cet exemple de script, la fonction `str` est ajoutée au cours de la phase de test, afin de vérifier le schéma de données retourné à partir de R. Vous pourrez supprimer l’instruction ultérieurement.
>
> Les noms de colonne utilisés dans le script R ne sont pas nécessairement transmis à la sortie de la procédure stockée. Ici, la clause WITH RESULTS définit de nouveaux noms de colonnes.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Machine Learning Services avec R dans Azure SQL Database (préversion), consultez les articles suivants.

- [Machine Learning Services avec R dans Azure SQL Database (préversion)](machine-learning-services-overview.md)
- [Créer et exécuter des scripts R simples dans Azure SQL Database Machine Learning Services (préversion)](r-script-create-quickstart.md)
- [Écrire des fonctions R avancées dans Azure SQL Database avec Machine Learning Services (préversion)](machine-learning-services-functions.md)
- [Exploiter des données R et SQL dans Machine Learning Services d’Azure SQL Database (préversion)](machine-learning-services-data-issues.md)
