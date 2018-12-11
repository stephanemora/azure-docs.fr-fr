---
title: Démarrage rapide pour utiliser Machine Learning Services (avec R) dans Azure SQL Database (préversion) | Microsoft Docs
description: Cette rubrique vous montre comment utiliser Machine Learning Services dans Azure SQL Database et comment exécuter des scripts R pour une analyse avancée à grande échelle et la possibilité de rapatrier le calcul et le traitement là où les données résident, éliminant la nécessité d’extraire les données sur le réseau.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 11/30/2018
ms.openlocfilehash: fc5398b4ffb0b9310b6ab13561830d8d3db7a611
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725741"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>Démarrage rapide : Utiliser Machine Learning Services (avec R) dans Azure SQL Database (préversion)

Cet article explique comment vous pouvez utiliser la préversion publique de Machine Learning Services (avec R) dans Azure SQL Database. Il vous guide dans les principes fondamentaux du déplacement de données entre une base de données SQL et R. Il explique également comment encapsuler du code R bien formé dans la procédure stockée [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) pour générer, entraîner et utiliser des modèles d’apprentissage automatique dans une base de données SQL.

L’apprentissage automatique dans SQL Database est utilisé pour exécuter des fonctions et du code R. Le code est totalement disponible pour les données relationnelles sous forme de procédures stockées, de script T-SQL contenant des instructions R ou de code R contenant du T-SQL. Utilisez la puissance des packages R d’entreprise pour une analyse avancée à grande échelle et la possibilité de rapatrier le calcul et le traitement là où les données résident, éliminant la nécessité d’extraire les données sur le réseau.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-up-for-the-preview"></a>S’inscrire à la préversion

La préversion publique de Machine Learning Services (avec R) dans SQL Database n’est pas activée par défaut. Envoyez un e-mail à Microsoft sur [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) pour vous inscrire à la préversion publique.

Une fois que vous êtes inscrit dans le programme, Microsoft vous intègre à la préversion publique et migre votre base de données existante ou en crée une nouvelle sur un service R.

Actuellement, Machine Learning Services (avec R) dans SQL Database est uniquement disponible dans le modèle d’achat vCore des niveaux de service **Usage général** et **Critique pour l’entreprise** pour les bases de données individuelles et en pool. Dans cette préversion publique initiale, le niveau de service **Hyperscale** et **Managed Instance** ne sont pas pris en charge. N’utilisez pas Machine Learning Services avec R pour des charges de travail de production avec la préversion publique.

Une fois Machine Learning Services (avec R) activé pour votre base de données SQL, revenez à cette page pour savoir comment exécuter des scripts R dans le contexte d’une procédure stockée.

Actuellement, R est le seul langage pris en charge. Il n’existe aucune prise en charge de Python pour l’instant.

## <a name="prerequisites"></a>Prérequis

Pour exécuter l’exemple de code dans ces exercices, vous devez tout d’abord disposer d’une base de données SQL avec Machine Learning Services (avec R) activé. Pendant la préversion publique, Microsoft vous intégrera et activera l’apprentissage automatique sur votre base de données nouvelle ou existante, comme décrit ci-dessus.

Vous pouvez vous connecter à la base de données SQL et exécuter les scripts R dans tous les outils de gestion ou de requête de base de données, tant qu’ils peuvent se connecter à une base de données SQL et exécuter une requête T-SQL ou une procédure stockée. Ce guide de démarrage rapide utilise [SQL Server Management Studio](sql-database-connect-query-ssms.md).

Pour l’exercice [Ajouter un package](#add-package), vous devrez également installer [R](https://www.r-project.org/) et [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) sur votre ordinateur local.

Ce guide de démarrage rapide nécessite également la configuration d’une règle de pare-feu au niveau du serveur. Pour accéder au guide de démarrage rapide expliquant la marche à suivre, consultez [Créer une règle de pare-feu au niveau du serveur](sql-database-get-started-portal-firewall.md).

## <a name="different-from-sql-server"></a>Différences par rapport à SQL Server

Le fonctionnement de Machine Learning Services (avec R) dans Azure SQL Database est similaire à celui de [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Toutefois, il existe certaines différences entre ces deux éléments :

- R uniquement. Il n’existe actuellement aucune prise en charge de Python.
- Inutile de configurer `external scripts enabled` via `sp_configure`.
- Les packages doivent être installés via **sqlmlutils**.
- Il n’existe aucune gouvernance distincte des ressources externes. Les ressources R représentent un certain pourcentage des ressources SQL, en fonction du niveau.

## <a name="verify-r-exists"></a>Vérifiez que R existe

Vous pouvez vérifier que Machine Learning Services (avec R) est activé pour votre base de données SQL. Pour ce faire, procédez comme suit.

1. Ouvrez SQL Server Management Studio, puis connectez-vous à votre base de données SQL.

1. Exécutez le code ci-dessous. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    Si tout se passe bien, vous devriez voir un message de résultat similaire à celui-ci.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. Si vous rencontrez des erreurs, cela peut être dû au fait que la préversion publique de Machine Learning Services (avec R) n’est pas activée pour votre base de données SQL. Découvrez comment vous inscrire à la préversion publique ci-dessus.

## <a name="grant-permissions"></a>Accorder des autorisations

Si vous êtes administrateur, vous pouvez exécuter automatiquement le code externe. Toute autre personne doit recevoir une autorisation.

Remplacez `<username>` par une connexion d’utilisateur de base de données valide avant d’exécuter la commande.

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>Interaction R de base

Il existe deux façons d’exécuter du code R dans SQL Database :

+ Ajouter un script R en tant qu’argument de la procédure stockée système [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).
+ À partir d’un [client R à distance](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client), connectez-vous à votre base de données SQL et exécutez du code en utilisant la base de données SQL en tant que contexte de calcul.

L’exercice suivant se concentre sur le premier modèle d’interaction : comment passer du code R dans une procédure stockée.

1. Exécuter un script simple pour voir comment un script R s’exécute dans votre base de données SQL.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. En supposant que vous avez tout configuré comme il faut, le résultat correct est calculé et la fonction `print` R le renvoie dans la fenêtre **Messages**.

    **Résultats**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    Même si les messages **stdout** sont utiles lorsque vous testez votre code, le plus souvent, il est plus judicieux que les résultats soient renvoyés dans un format tabulaire, afin que vous puissiez les utiliser dans une application ou les écrire dans une table. Consultez la section sur les entrées et sorties ci-dessous pour plus d’informations.

N’oubliez pas que tout le contenu de l’argument `@script` doit être du code R valide.

## <a name="inputs-and-outputs"></a>Entrées et sorties

Par défaut, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) accepte un seul jeu de données d’entrée, que vous fournissez en général sous la forme d’une requête SQL valide. D’autres types d’entrées peuvent être passés en tant que variables SQL.

La procédure stockée retourne une trame de données R unique en tant que sortie, mais vous pouvez également générer des valeurs scalaires et des modèles sous forme de variables. Vous pouvez par exemple générer un modèle entraîné en tant que variable binaire et le passer dans une instruction T-SQL INSERT pour écrire ce modèle dans une table. Vous pouvez également générer des tracés (au format binaire) ou des valeurs scalaires (les valeurs individuelles, telles que la date et l’heure, la durée d’entraînement du modèle, etc.).

Pour l’instant, examinons simplement la valeur par défaut des variables d’entrée et de sortie de sp_execute_external_script : `InputDataSet` et `OutputDataSet`.

1. Créez une petite table de données de test en exécutant l’instruction T-SQL suivante :

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    Lorsque la table est créée, utilisez l’instruction suivante pour interroger la table :
  
    ```sql
    SELECT * FROM RTestData
    ```

    **Résultats**

    ![Contenu de la table RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

2. Vous pouvez vous servir des données de la table comme entrée pour votre script R. Exécutez l’instruction ci-dessous. Elle récupère les données de la table, effectue un aller-retour dans le runtime R et renvoie les valeurs avec le nom de colonne *NewColName*.

    Les données retournées par la requête sont transmises au runtime R, qui les renvoie à la base de données SQL sous forme d’une trame de données. La clause WITH RESULT SETS définit le schéma de la table de données retournées pour la base de données SQL.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **Résultats**

    ![Sortie du script R qui renvoie les données d’une table](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. Nous allons modifier le nom des variables d’entrée ou de sortie. Le script ci-dessus utilisait les noms de variables d’entrée et de sortie par défaut _InputDataSet_ et _OutputDataSet_. Pour définir les données d’entrée associées avec _InputDatSet_, utilisez la variable *@input_data_1*.

    Dans ce script, les noms des variables d’entrée et de sortie de la procédure stockée ont été remplacés par *SQL_out* et *SQL_in* :

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Notez que R respecte la casse, par conséquent, la casse des variables d’entrée et de sortie dans `@input_data_1_name` et `@output_data_1_name` doit correspondre à celle du code R dans `@script`. 

    Notez également que l’ordre des paramètres est important. Vous devez d’abord spécifier les paramètres requis *@input_data_1* et *@output_data_1* pour pouvoir utiliser les paramètres facultatifs *@input_data_1_name* et *@output_data_1_name*.

    Un seul jeu de données d’entrée peut être passé en tant que paramètre, et un seul jeu de données peut être renvoyé. Toutefois, vous pouvez appeler d’autres jeux de données dans votre code R et des sorties d’autres types peuvent être renvoyées en plus du jeu de données. Vous pouvez également ajouter le mot clé OUTPUT à n’importe quel paramètre pour qu’il soit retourné avec les résultats. 

    L’instruction `WITH RESULT SETS` définit le schéma des données utilisées dans la base de données SQL. Vous devez fournir des types de données compatibles avec SQL pour chaque colonne renvoyée à partir de R. Vous pouvez utiliser la définition de schéma pour fournir de nouveaux noms de colonnes, car il n’est pas nécessaire d’utiliser les noms de colonnes de la trame de données R.

4. Vous pouvez également générer des valeurs en utilisant le script R et laisser la chaîne de requête d’entrée dans _@input_data_1_ vide.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **Résultats**

    ![Résultats de la requête en utilisant @script comme entrée](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Vérifier la version de R

Si vous souhaitez savoir quelle version de R est installée dans votre base de données SQL, procédez comme suit :

1. Exécutez le script ci-dessous sur votre base de données SQL.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. La fonction `print` R renvoie la version dans la fenêtre **Messages**. Dans l’exemple ci-dessous, vous pouvez constater que dans ce cas, c’est la version R 3.4.4 qui est installée dans SQL Database.

    **Résultats**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>Répertorier les packages R

Microsoft fournit un nombre de packages R préinstallés avec Machine Learning Services dans votre base de données SQL. Pour afficher la liste des packages R installés, y compris les versions, les dépendances, les licences et les informations de chemin d’accès aux bibliothèques, suivez les étapes ci-dessous. Pour ajouter des packages supplémentaires, consultez la section [Ajouter un package](#add-package).

1. Exécutez le script ci-dessous sur votre base de données SQL.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. La sortie provient de `installed.packages()` dans R et elle est retournée sous forme de jeu de résultats.

    **Résultats**

    ![Packages installés dans R](./media/sql-database-connect-query-r/r-installed-packages.png)


## <a name="create-a-predictive-model"></a>Créer un modèle prédictif

Vous pouvez entraîner un modèle avec R et l’enregistrer dans une table de votre base de données SQL. Dans cet exercice, vous allez entraîner un modèle de régression simple prédisant la distance d’arrêt d’une voiture en fonction de sa vitesse. Vous utiliserez le jeu de données `cars` inclus avec R, car il est petit et facile à comprendre.

1. Tout d’abord, créez une table pour enregistrer les données d’entraînement.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    De nombreux jeux de données, petits et grands, sont inclus avec le runtime R. Pour obtenir une liste des jeux de données installés avec R, tapez `library(help="datasets")` à partir d’une invite de commandes R.

2. Créez un modèle de régression. Les données de vitesse de la voiture forment deux colonnes, toutes deux numériques, `dist` et `speed`. Plusieurs observations sont associées à certaines vitesses. À partir de ces données, vous allez créer un modèle de régression linéaire qui décrit une relation entre la vitesse de la voiture et la distance nécessaire pour qu’elle s’arrête.

    Les exigences d’un modèle linéaire sont simples :

    - Définissez une formule décrivant la relation entre la variable dépendante `speed` et la variable indépendante `distance`.

    - Fournissez les données d’entrée à utiliser pour entraîner le modèle.

    > [!TIP]
    > Si vous avez besoin de vous rafraîchir la mémoire, nous vous recommandons de consulter ce didacticiel sur les modèles linéaires. Il décrit le processus d’ajustement d’un modèle avec rxLinMod : [Fitting Linear Models (Ajustement des modèles linéaires)](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

    Pour générer le modèle, vous devez définir la formule dans le code R et passer les données comme paramètre d’entrée.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
        EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
            trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @output_data_1_name = N'trained_model'
        WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

    Le premier argument de rxLinMod est le paramètre *formula*, qui définit la distance comme étant dépendante de la vitesse. Les données d’entrée sont stockées dans la variable `CarsData`, qui est renseignée par la requête SQL. Si vous n’attribuez pas de nom spécifique à vos données d’entrée, le nom de variable par défaut est _InputDataSet_.

2. Ensuite, créez une table où vous stockerez le modèle afin de l’entraîner à nouveau ou l’utiliser pour la prédiction. La sortie d’un package R qui crée un modèle est généralement un **objet binaire**. Par conséquent, la table doit contenir une colonne du type **VARBINARY(max)**.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. Pour enregistrer le modèle, exécutez l’instruction Transact-SQL suivante pour appeler la procédure stockée, générer le modèle et l’enregistrer dans une table.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    Notez que si vous exécutez ce code une deuxième fois, vous obtenez cette erreur :

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    Pour éviter cette erreur, vous pouvez mettre à jour le nom de chaque nouveau modèle. Vous pouvez par exemple utiliser un nom plus descriptif et y inclure le type de modèle, le jour de sa création, etc.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. En règle générale, la sortie de R de la procédure stockée [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) est limité à une seule trame de données.

    Toutefois, vous pouvez retourner des sorties d’autres types, telles que des valeurs scalaires, en plus de la trame de données.

    Par exemple, supposons que vous souhaitiez entraîner un modèle, mais afficher immédiatement une table de coefficients à partir de ce dernier. Vous pouvez créer la table de coefficients comme jeu de résultats principal, et retourner la sortie du modèle entraîné dans une variable SQL. Vous pouvez immédiatement réutiliser le modèle en appelant la variable, ou vous pouvez enregistrer le modèle dans une table, comme indiqué ici.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **Résultats**

    ![Modèle entraîné avec sortie supplémentaire](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>Prédire

Utilisez le modèle créé dans la section précédente pour attribuer des scores de prédiction à partir de nouvelles données. Pour effectuer ce _scoring_ avec de nouvelles données, récupérez l’un des modèles entraînés dans la table, puis appelez un nouveau jeu de données sur lequel baser vos prédictions. Le scoring est un terme parfois utilisé dans le domaine de la science des données pour désigner la génération de prédictions, de probabilités ou d’autres valeurs en s’appuyant sur de nouvelles données chargées dans un modèle entraîné.

1. Tout d’abord, créez une table avec de nouvelles données de vitesse. Avez-vous remarqué que les données d’entraînement d’origine s’arrêtaient à une vitesse de 25 miles/h ? C’est parce que les données d’origine étaient basées sur une expérience réalisée en 1920 ! Vous seriez peut-être curieux de savoir combien de temps une voiture des années 20 mettrait à s’arrêter, en supposant qu’elle puisse atteindre une vitesse de 60 voire 100 miles à l’heure ? Pour répondre à cette question, vous devez fournir des nouvelles valeurs de vitesse.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    Dans cet exemple, étant donné que votre modèle est basé sur l’algorithme **rxLinMod** fourni dans le package **RevoScaleR**, vous devez appeler la fonction [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict), plutôt que la fonction `predict` R générique.

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
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
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    Le script ci-dessus effectue les actions suivantes :

    + Utilisez une instruction SELECT pour récupérer un modèle dans la table et le passer en tant que paramètre d’entrée.

    + Après avoir récupéré le modèle dans la table, appelez la fonction `unserialize` sur le modèle.

        > [!TIP] 
        > Intéressez-vous également aux nouvelles [fonctions de sérialisation](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) fournies par RevoScaleR, qui prennent en charge le scoring en temps réel.
    + Appliquez la fonction `rxPredict` avec les arguments appropriés au modèle de fonction, et fournissez les nouvelles données d’entrée.

    + Dans l’exemple, la fonction `str` est ajoutée au cours de la phase de test, afin de vérifier le schéma de données retourné à partir de R. Vous pourrez supprimer l’instruction ultérieurement.

    + Les noms de colonne utilisés dans le script R ne sont pas nécessairement passés à la sortie de la procédure stockée. Ici, nous avons utilisé la clause WITH RESULTS pour définir des nouveaux noms de colonnes.

    **Résultats**

    ![Jeu de résultats pour la prédiction de la distance d’arrêt](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

    Il est également possible d’utiliser [PREDICT in Transact-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) pour générer une valeur prédite ou un score basé sur un modèle stocké.

<a name="add-package"></a>

## <a name="add-a-package"></a>Ajouter un package

Si vous devez utiliser un package qui n’est pas déjà installé dans votre base de données SQL, vous pouvez l’installer à l’aide de [sqlmlutils](https://github.com/Microsoft/sqlmlutils). Pour installer le package, suivez les étapes ci-dessous.

1. Téléchargez le dernier fichier zip **sqlmlutils** à partir de [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) sur votre ordinateur local. Vous n’avez pas besoin de décompresser le fichier.

1. Si R n’est pas installé, téléchargez-le sur [www.r-project.org](https://www.r-project.org/) et installez-le sur votre ordinateur local. R est disponible pour Windows, MacOS et Linux. Dans cet exemple, nous utilisons Windows.

1. Tout d’abord, installez le package **RODBCext**, prérequis pour **sqlmlutils**. **RODBCext** installe également la dépendance du package **RODBC**. Ouvrez une **invite de commandes** et exécutez la commande suivante :

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    Si l’erreur suivante apparaît : « R n’est pas reconnu comme une commande interne ou externe, un programme exécutable ou un fichier de commandes », cela signifie probablement que le chemin du fichier R.exe n’est pas inclus dans votre variable d’environnement **PATH** sur Windows. Vous pouvez ajouter le répertoire à la variable d’environnement ou accéder au répertoire à partir de l’invite de commandes (par exemple `cd C:\Program Files\R\R-3.5.1\bin`) avant d’exécuter la commande.

1. Utilisez la commande **R CMD installer** pour installer **sqlmlutils**. Spécifiez le chemin d’accès au répertoire dans lequel vous avez téléchargé le fichier zip et le nom de ce dernier. Par exemple : 

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    Le résultat devrait être similaire à ce qui suit :

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. Dans cet exemple, vous allez utiliser RStudio Desktop comme IDE. Vous pouvez utiliser un autre IDE si vous préférez. Téléchargez et installez RStudio Desktop à partir de [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/), si RStudio n’est pas encore installé.

1. Ouvrez **RStudio** et créez un nouveau fichier **Script R**. Utilisez le code R suivant pour installer un package à l’aide de sqlmlutils. Dans l’exemple ci-dessous, vous allez installer le package [glue](https://cran.r-project.org/web/packages/glue/), qui peut mettre en forme et interpoler une chaîne.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > La valeur **scope** (Étendue) peut être définie sur **PUBLIC** ou **PRIVATE**. Une étendue publique est utile si l’administrateur de base de données doit installer des packages que tous les utilisateurs peuvent utiliser. Avec une étendue privée, le package est uniquement disponible pour l’utilisateur qui l’installe. Si vous ne spécifiez pas l’étendue, elle est définie sur **PRIVATE** par défaut.

1. Maintenant, vérifiez que le package **glue** a été installé.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **Résultats**

    ![Contenu de la table RTestData](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. Une fois le package installé, vous pouvez l’utiliser dans votre script R via **sp_execute_external_script**. Ouvrez **SQL Server Management Studio**, puis connectez-vous à votre base de données SQL. Exécutez le script qui suit :

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    Vous verrez le résultat suivant dans l’onglet Messages.

    **Résultats**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. Si vous souhaitez supprimer le package, exécutez le script R suivant dans **RStudio** sur votre ordinateur local.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> Une autre façon d’installer des packages R sur votre base de données SQL consiste à les charger à partir du flux d’octets via [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Machine Learning Services, consultez les articles ci-dessous sur les Services de Machine Learning SQL Server. Bien que ces articles concernent SQL Server, la plupart des informations sont également applicables à Machine Learning Services (avec R) dans Azure SQL Database.

- [Services de Machine Learning SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Didacticiel : Apprendre l’analytique en base de données avec R dans SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Procédure pas à pas de bout en bout en science des données pour R et SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Didacticiel : Utiliser les fonctions R RevoScaleR avec des données SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
