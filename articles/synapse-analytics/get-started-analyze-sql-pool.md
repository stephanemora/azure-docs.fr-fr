---
title: 'Tutoriel : Démarrer l’analyse des données avec des pools SQL dédiés'
description: Dans ce tutoriel, vous allez utiliser les exemples de données NYC Taxi pour explorer les fonctionnalités d’analyse d’un pool SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: c46adf9e9f5c1b2e74c1098ebf137c4556bfc58d
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147559"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analyser des données avec des pools SQL dédiés

Azure Synapse Analytics vous offre la possibilité d’analyser les données avec un pool SQL dédié. Dans ce tutoriel, vous allez utiliser les données NYC Taxi pour explorer les fonctionnalités d’un pool SQL dédié.

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>Charger les données NYC Taxi dans SQLDB1

1. Dans Synapse Studio, accédez au hub **Développer** , puis créez un script SQL.
1. Sélectionnez le pool « SQLDB1 » (pool créé à l’[étape 1](https://docs.microsoft.com/azure/synapse-analytics/get-started-create-workspace#create-a-sql-pool) de ce tutoriel) dans la section « Connect to » du script.
1. Entrez le code suivant :
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. L’exécution de ce script prendra environ 1 minute. Il charge 2 millions lignes de données NYC Taxi dans une table appelée **dbo.Trip** .

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Explorer les données NYC Taxi dans le pool SQL dédié

1. Dans Synapse Studio, accédez au hub **Données** .
1. Accédez à **SQLDB1** > **Tables** . Vous voyez plusieurs tables chargées.
1. Cliquez avec le bouton droit sur la table **dbo.Trip** et sélectionnez **Nouveau script SQL** > **Sélectionner les 100 premières lignes** .
1. Patientez pendant la création et l’exécution du nouveau script SQL.
1. Notez que, en haut du script SQL, **Se connecter à** est automatiquement défini sur le pool SQL appelé **SQLDB1** .
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

