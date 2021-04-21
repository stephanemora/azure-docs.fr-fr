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
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567551"
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
