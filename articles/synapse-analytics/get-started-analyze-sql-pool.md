---
title: 'Tutoriel : Démarrer l’analyse des données avec un pool SQL'
description: Dans ce tutoriel, vous allez utiliser les exemples de données NYC Taxi pour explorer les fonctionnalités d’analyse d’un pool SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325217"
---
# <a name="analyze-data-with-sql-pools"></a>Analyser des données avec des pools SQL

Azure Synapse Analytics vous offre la possibilité d’analyser les données avec un pool SQL. Dans ce tutoriel, vous allez utiliser les exemples de données NYC Taxi pour explorer les fonctionnalités d’analyse d’un pool SQL.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Charger les données de l’exemple NYC Taxi dans la base de données SQLDB1

1. Dans Synapse Studio, sur le menu bleu tout en haut, sélectionnez l’icône de point d’interrogation ( **?** ).
1. Sélectionnez **Démarrer** > **Hub Démarrer**.
1. Dans la carte **Exemple de données de requête**, sélectionnez le pool SQL nommé **SQLDB1**.
1. Sélectionnez **Données de requête**. Une notification de chargement de l’exemple de données s’affiche brièvement. La barre d’état de couleur bleu clair située en haut de Synapse Studio indique que les données sont en cours de chargement dans SQLDB1.
1. Quand la barre d’état devient verte, fermez-la.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explorer les données NYC Taxi dans le pool SQL

1. Dans Synapse Studio, accédez au hub **Données**.
1. Accédez à **SQLDB1** > **Tables**. Vous voyez plusieurs tables chargées.
1. Cliquez avec le bouton droit sur la table **dbo.Trip** et sélectionnez **Nouveau script SQL** > **Sélectionner les 100 premières lignes**.
1. Patientez pendant la création et l’exécution du nouveau script SQL.
1. Notez que, en haut du script SQL, **Se connecter à** est automatiquement défini sur le pool SQL appelé **SQLDB1**.
1. Remplacez le texte du script SQL par ce code et exécutez-le.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Cette requête montre de quelle manière les distances totales et la distance moyenne des trajets sont liées au nombre de passagers.
1. Dans la fenêtre de résultat du script SQL, changez la **Vue** à **Graphique** pour visualiser les résultats sous forme de graphique en courbes.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser avec Spark](get-started-analyze-spark.md)

