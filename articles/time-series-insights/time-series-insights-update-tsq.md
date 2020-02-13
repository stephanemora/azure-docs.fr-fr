---
title: Interrogation de données dans la Préversion – Azure Time Series Insights | Microsoft Docs
description: Concepts de l’interrogation de données et vue d’ensemble de l’API REST HTTP dans Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 898515f49672a19ed8bf1c62439128b6727afc73
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087410"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Interrogation de données dans Azure Time Series Insights (préversion)

Azure Time Series Insights en préversion permet d’interroger les données sur les événements et les métadonnées stockées dans l’environnement avec des API de surface publiques. Ces API sont également utilisées dans [l’Explorateur Time Series Insights en préversion](./time-series-insights-update-explorer.md).

Trois catégories principales d’API sont disponibles dans Time Series Insights :

* **API d’environnement** : ces API activent les requêtes sur l’environnement Time Series Insights proprement dit. Exemples : la liste des environnements auxquels l’appelant a accès, les métadonnées de l’environnement.
* **API modèle-requête de série chronologique (TSM-Q)** : opérations CRUD (création, lecture, mise à jour et suppression) sur les métadonnées stockées dans la partie environnement du modèle de série chronologique. Exemples : instances, types et hiérarchies.
* **API de requête de série chronologique (TSQ)** : permet la récupération des données de télémétrie ou d’événements tels qu’elles sont enregistrées à partir du fournisseur source ou en réduisant les données à l’aide des fonctions scalaires et d’agrégation stockées dans une partie des variables. Ces API peuvent effectuer des opérations de transformation et de combinaison et appliquer des calculs sur des données de série chronologique.

Time Series Insights utilise un langage d’expression riche basé sur des chaînes, [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx), pour exprimer des calculs.

## <a name="azure-time-series-insights-preview-core-apis"></a>API principales Azure Time Series Insights en préversion

Les API principales suivantes sont prises en charge.

[![Vue d'ensemble de la requête de série chronologique](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API d’environnement

Les API d’environnement suivantes sont disponibles :

* [API Obtenir les environnements](/rest/api/time-series-insights/management/environments/get) : retourne la liste des environnements auxquels l’appelant est autorisé à accéder.
* [API Obtenir la disponibilité des environnements](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) : retourne la distribution du nombre d’événements au cours du timestamp `$ts` de l’événement. Cette API permet de déterminer s’il s’est produit des événements dans le timestamp en renvoyant le nombre d’événements.
* [API Obtenir le schéma des événements](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema) : retourne les métadonnées du schéma des événements pour une période de recherche donnée. Cette API permet de récupérer toutes les métadonnées et propriétés disponibles dans le schéma sur une période définie.

## <a name="time-series-model-query-tsm-q-apis"></a>API modèle-requête de série chronologique (TSM-Q)

Les API modèle-requête de série chronologique suivantes sont disponibles. La plupart de ces API prennent en charge l’opération d’exécution par lot pour activer les opérations CRUD par lot sur plusieurs entités de modèle de série chronologique :

* [API Paramètres du modèle](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api) : active *OBTENIR* et *CORRIGER* sur le type par défaut et le nom du modèle de l’environnement.
* [API Types](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api) : active CRUD sur les types Time Series et les variables associées.
* [API Hiérarchies](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api) : active CRUD sur les hiérarchies Time Series et les chemins d’accès de champs associés.
* [API Instances](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api) : active CRUD sur les instances Time Series et les champs d’instance associés. En outre, l’API Instances prend en charge les opérations suivantes :
  * [Rechercher](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search) : récupère une liste partielle des résultats d’une recherche d’instances de série chronologique en fonction des attributs d’instance.
  * [Suggérer](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest) : recherche et suggère une liste partielle des résultats d’une recherche d’instances de série chronologique en fonction des attributs d’instance.

## <a name="time-series-query-tsq-apis"></a>API de requête de série chronologique (TSQ)

Les API de requête de série chronologique suivantes sont disponibles. Ces API sont disponibles sur tous les stockages multicouches pris en charge dans Time Series Insights. Les paramètres URL de requête permettent de spécifier le [type de stockage](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) sur lequel la requête doit s’exécuter :

* [API Obtenir les événements](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) : permet l’interrogation et l’extraction de données Time Series Insights à partir d’événements au fil de leur enregistrement dans Time Series Insights à partir du fournisseur de source. Cette API permet de récupérer des événements bruts pour un ID Time Series donné et une étendue de recherche. Cette API prend en charge la pagination pour récupérer le jeu de données complet de l’entrée sélectionnée. 

* [API Obtenir les séries](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries) : permet l’interrogation et l’extraction de données Time Series Insights à partir d’événements capturés à l’aide des données enregistrées sur la connexion filaire. Les valeurs retournées dépendent des variables définies dans le modèle ou inline. Cette API prend en charge la pagination pour récupérer le jeu de données complet de l’entrée sélectionnée. Cette API permet de définir des propriétés ou des colonnes calculées.

    >[!NOTE]
    > La clause d’agrégation est ignorée même si elle est spécifiée dans un modèle ou inline.

  L’API Obtenir les séries retourne une valeur Time Series pour chaque variable de chaque intervalle. Il s’agit du format utilisé par Time Series Insights pour la sortie JSON d’une requête. Les valeurs retournées dépendent de l’ID Time Series et de l’ensemble de variables fournies.

* [API Agréger les séries](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable) : permet l’interrogation et l’extraction de données Time Series Insights à partir d’événements capturés en échantillonnant et en agrégeant les données enregistrées. Cette API prend en charge l’exécution continue en utilisant des [jetons de continuation](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage).

  L’API Agréger les séries retourne une valeur Time Series pour chaque variable de chaque intervalle. Les valeurs dépendent de l’ID Time Series et de l’ensemble de variables fournies. L’API Agréger les séries effectue une réduction en utilisant les variables stockées dans le modèle Time Series ou définies inline pour agréger ou échantillonner les données.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’entrée et le stockage](./time-series-insights-update-storage-ingress.md) dans Azure Time Series Insights (préversion).
- Consultez l'article [Azure Time Series Insights (préversion) - Modélisation de données](./time-series-insights-update-tsm.md).
- Découvrez les [meilleures pratiques pour choisir un ID Time Series](./time-series-insights-update-how-to-id.md).
