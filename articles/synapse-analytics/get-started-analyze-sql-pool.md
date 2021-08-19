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
ms.openlocfilehash: 56115e977603e1f2148f84569373dcf4d351e0c4
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113038207"
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
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Cliquez sur le bouton Réexécuter pour exécuter le script.
1. Ce script met moins de 60 secondes à s’exécuter. Il charge 2 millions lignes de données NYC Taxi dans une table appelée **dbo.NYCTaxiTripSmall**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Explorer les données NYC Taxi dans le pool SQL dédié

1. Dans Synapse Studio, accédez au hub **Données**.
1. Accédez à **SQLPOOL1** > **Tables**. 
3. Cliquez avec le bouton droit sur la table **dbo.NYCTaxiTripSmall**, puis sélectionnez **Nouveau script SQL** > **Sélectionner les 100 premières lignes**.
4. Patientez pendant la création et l’exécution du nouveau script SQL.
5. Notez que, en haut du script SQL, **Se connecter à** est automatiquement défini sur le pool SQL appelé **SQLPOOL1**.
6. Remplacez le texte du script SQL par ce code et exécutez-le.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Cette requête montre de quelle manière les distances totales et la distance moyenne des trajets sont liées au nombre de passagers.
1. Dans la fenêtre de résultat du script SQL, changez la **Vue** à **Graphique** pour visualiser les résultats sous forme de graphique en courbes.
    
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser des données dans un compte de stockage Azure](get-started-analyze-storage.md)
