---
title: Ingérer des exemples de données dans l’Explorateur de données Azure
description: Découvrez comment ingérer (charger) des exemples de données météorologiques dans l’Explorateur de données Azure.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 7fdd32f9263b4d1694a0516a98b681ba8744ab6b
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394576"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Ingérer des exemples de données dans l’Explorateur de données Azure

Cet article vous montre comment ingérer (charger) des exemples de données dans une base de données de l’Explorateur de données Azure. Il existe [plusieurs façons d’ingérer des données](ingest-data-overview.md) ; cet article se concentre sur une approche de base qui convient à des fins de test.

> [!NOTE]
> Vous avez déjà ces données si vous avez terminé [Démarrage rapide : Ingérer des données à l’aide de la bibliothèque Python de l’Explorateur de données Azure](python-ingest-data.md).

## <a name="prerequisites"></a>Prérequis

[Un cluster et une base de données de test](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Réception de données

L’exemple de jeu de données **StormEvents** contient des données météorologiques des [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Dans la partie supérieure gauche de l’application, sélectionnez **Ajouter un cluster**.

1. Dans la boîte de dialogue **Ajouter un cluster**, entrez l’URL de votre cluster sous la forme `https://<ClusterName>.<Region>.kusto.windows.net/`, puis sélectionnez **Ajouter**.

1. Collez la commande suivante, puis sélectionnez **Exécuter**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Une fois l’ingestion terminée, collez la requête suivante, sélectionnez la requête dans la fenêtre, puis **Exécuter**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    La requête retourne les résultats suivants à partir des exemples de données ingérés.

    ![Résultats de la requête](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes](write-queries.md)

[Ingestion des données de l’Explorateur de données Azure](ingest-data-overview.md)