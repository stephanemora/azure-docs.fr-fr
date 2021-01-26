---
title: 'Tutoriel : Bien démarrer avec l’analyse des données à l’aide d’un pool SQL serverless'
description: Dans ce tutoriel, vous allez voir comment analyser des données avec un pool SQL serverless, en utilisant des données situées dans des bases de données Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209404"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Analyser des données avec un pool SQL serverless dans Azure Synapse Analytics

Dans ce tutoriel, vous allez voir comment analyser des données avec un pool SQL serverless, en utilisant des données situées dans des bases de données Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analyser les données NYC Taxi du stockage Blob avec un pool SQL serverless

1. Dans le hub **Données** situé sous **Liées**, cliquez avec le bouton droit sur **Stockage Blob Azure > Exemples de jeux de données > nyc_tlc_yellow**, **Nouveau script SQL**, puis sélectionnez **Sélectionner les 100 premières lignes**
1. Un script SQL sera créé avec le code suivant :

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Cliquez sur **Exécuter**.

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>Analyser les données NYC Taxi des bases de données Spark avec un pool SQL serverless

Les tables des bases de données Spark sont automatiquement visibles et interrogeables par le pool SQL serverless.

1. Dans Synapse Studio, accédez au hub **Développer** et créez un autre script SQL.
1. Configurez **Se connecter à** sur le pool SQL serverless **Intégré**.
1. Collez le texte suivant dans le script et exécutez le script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Lors de la première exécution d’une requête qui utilise un pool SQL serverless, il faut environ 10 secondes au pool pour collecter les ressources SQL nécessaires à l’exécution des requêtes. Les requêtes suivantes s’exécutent beaucoup plus vite.
  


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser des données dans un stockage](get-started-analyze-storage.md)
