---
title: 'Tutoriel : Démarrage de l’analyse des données avec un pool SQL serverless'
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
ms.openlocfilehash: 7c228bfe5897b45e6345234f2ed8e0f5cfbec73a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312788"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analyser des données avec un pool SQL serverless

Dans ce tutoriel, vous allez apprendre à analyser des données avec un pool SQL serverless. 

## <a name="the-built-in-serverless-sql-pool"></a>Pool SQL serverless intégré

Les pools SQL serverless vous permettent d’utiliser SQL sans avoir de capacité de réserve. La facturation du pool SQL serverless est basée sur la quantité de données traitées pour exécuter la requête, et non sur le nombre de nœuds utilisés pour exécuter la requête.

Chaque espace de travail est fourni avec un pool SQL serverless préconfiguré, dit **Intégré**. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analyser les données de NYC Taxi avec un pool SQL serverless


1. Dans Synapse Studio, accédez au hub **Développer**
1. Un nouveau script SQL est alors créé.
1. Collez le code suivant dans le script.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Cliquez sur **Exécuter**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser des données avec un pool Spark serverless](get-started-analyze-spark.md)
