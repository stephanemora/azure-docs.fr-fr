---
title: 'Tutoriel : Démarrer l’analyse des données avec SQL à la demande'
description: Dans ce tutoriel, vous allez découvrir comment analyser des données avec SQL à la demande en utilisant des données qui se trouvent dans des bases de données Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093492"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analyser des données avec SQL à la demande

Dans ce tutoriel, vous allez découvrir comment analyser des données avec SQL à la demande en utilisant des données qui se trouvent dans des bases de données Spark. 

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
> [Analyser avec Spark](get-started-analyze-spark.md)
