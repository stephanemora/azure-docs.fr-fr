---
title: 'Tutoriel : démarrer l’analyse des données avec SQL serverless'
description: Dans ce tutoriel, vous allez découvrir comment analyser des données avec SQL à la demande en utilisant des données qui se trouvent dans des bases de données Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 9c42d1d988bc280d5e62c24f109225d91cb446ce
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893490"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analyser des données avec SQL à la demande

Dans ce tutoriel, vous allez découvrir comment analyser des données avec SQL serverless à l’aide d’un pool SQL à la demande utilisant des données qui se trouvent dans des bases de données Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>Analyser les données NYC Taxi du stockage Blob avec un pool SQL à la demande

1. Dans le hub **Données** situé sous **Liées**, cliquez avec le bouton droit sur **Stockage Blob Azure > Exemples de jeux de données > nyc_tlc_yellow**, puis sélectionnez **100 premières lignes**.
1. Un script SQL sera créé avec le code suivant :

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Cliquez sur **Exécuter**.

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analyser les données NYC Taxi dans des bases de données Spark à l’aide de SQL à la demande

Les tables des bases de données Spark sont automatiquement visibles et interrogeables par SQL à la demande.

1. Dans Synapse Studio, accédez au hub **Développer** et créez un autre script SQL.
1. Définissez **Se connecter à** sur **SQL à la demande**.
1. Collez le texte suivant dans le script et exécutez le script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Lors de la première exécution d’une requête qui utilise SQL à la demande, il faut environ 10 secondes à SQL à la demande pour collecter les ressources SQL nécessaires à l’exécution des requêtes. Les requêtes suivantes s’exécutent beaucoup plus vite.
  


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser des données dans un stockage](get-started-analyze-storage.md)
