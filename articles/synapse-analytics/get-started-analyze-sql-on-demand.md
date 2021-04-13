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
ms.openlocfilehash: 0ef76be2c083d6185b8b919d174de28aa3b65446
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293768"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analyser des données avec un pool SQL serverless

Dans ce tutoriel, vous allez apprendre à analyser des données avec un pool SQL serverless. 

## <a name="the-built-in-serverless-sql-pool"></a>Pool SQL serverless intégré

Les pools SQL serverless vous permettent d’utiliser SQL sans avoir de capacité de réserve. La facturation du pool SQL serverless est basée sur la quantité de données traitées pour exécuter la requête, et non sur le nombre de nœuds utilisés pour exécuter la requête.

Chaque espace de travail est fourni avec un pool SQL serverless préconfiguré, dit **Intégré**. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analyser les données NYC Taxi du stockage Blob avec un pool SQL serverless

Dans cette section, vous allez utiliser un pool SQL serverless pour analyser les données NYC Taxi sur un compte Stockage Blob Azure.

1. Dans Synapse Studio, accédez au hub **Développer**
1. Un nouveau script SQL est alors créé.
1. Collez le code suivant dans le script.

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

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser des données avec un pool Spark serverless](get-started-analyze-spark.md)
