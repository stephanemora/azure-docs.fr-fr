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
ms.date: 03/24/2021
ms.openlocfilehash: a1f15330a912c8a8a93fe1f74e88ef8d117441c2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047896"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analyser des données avec des pools SQL dédiés

Dans ce tutoriel, vous allez utiliser les données NYC Taxi pour explorer les fonctionnalités d’un pool SQL dédié.

## <a name="create-a-dedicated-sql-pool"></a>Créer un pool SQL dédié

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Gérer** > **Pools SQL**.
1. Sélectionnez **Nouveau**
1. Pour **Nom du pool SQL**, sélectionnez **SQLPOOL1**
1. Pour **Niveau de performances**, choisissez **DW100C**
1. Sélectionnez **Vérifier + créer** > **Créer**. Votre pool SQL dédié sera prêt en quelques minutes. 

Votre pool SQL dédié est associé à une base de données SQL également appelée **SQLPOOL1**.
1. Accédez à **Données** > **Espace de travail**.
1. Vous devez voir une base de données nommée **SQLPOOL1**. Si vous ne la voyez pas, cliquez sur **Actualiser**.

Tant qu’il est actif, un pool SQL dédié consomme des ressources facturables. Vous pouvez suspendre le pool plus tard pour réduire les coûts.

> [!NOTE] 
> Quand vous créez un pool SQL dédié (anciennement SQL DW) dans votre espace de travail, la page de provisionnement des pools SQL s’ouvre. Le provisionnement s’effectue sur le serveur SQL logique.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Charger les données NYC Taxi dans SQLPOOL1

1. Dans Synapse Studio, accédez au hub **Développer**, cliquez sur le bouton **+** pour ajouter une nouvelle ressource, puis créez un script SQL.
1. Sélectionnez le pool « SQLPOOL1 » (pool créé à l’[étape 1](./get-started-create-workspace.md) de ce tutoriel) dans la liste déroulante « Se connecter à » au-dessus du script.
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
1. Cliquez sur le bouton Réexécuter pour exécuter le script.
1. Ce script met moins de 60 secondes à s’exécuter. Il charge 2 millions lignes de données NYC Taxi dans une table appelée **dbo.Trip**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Explorer les données NYC Taxi dans le pool SQL dédié

1. Dans Synapse Studio, accédez au hub **Données**.
1. Accédez à **SQLPOOL1** > **Tables**. 
3. Cliquez avec le bouton droit sur la table **dbo.Trip** et sélectionnez **Nouveau script SQL** > **Sélectionner les 100 premières lignes**.
4. Patientez pendant la création et l’exécution du nouveau script SQL.
5. Notez que, en haut du script SQL, **Se connecter à** est automatiquement défini sur le pool SQL appelé **SQLPOOL1**.
6. Remplacez le texte du script SQL par ce code et exécutez-le.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Cette requête montre de quelle manière les distances totales et la distance moyenne des trajets sont liées au nombre de passagers.
1. Dans la fenêtre de résultat du script SQL, changez la **Vue** à **Graphique** pour visualiser les résultats sous forme de graphique en courbes.
    
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser des données dans un compte de stockage Azure](get-started-analyze-storage.md)
