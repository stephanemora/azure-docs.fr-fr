---
title: Interrogation de données Azure Time Series Insights en préversion | Microsoft Docs
description: Interrogation de données Azure Time Series Insights en préversion.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: bbf682df2df7a8cdc9fedb36aa4244fc5c0e9488
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243999"
---
# <a name="data-querying"></a>Interrogation de données

Azure Time Series Insights en préversion permet d’interroger les données sur les événements et les métadonnées stockées dans l’environnement avec des API de surface publiques. Ces API sont également utilisées dans [l’Explorateur Time Series Insights en préversion](./time-series-insights-update-explorer.md).

Trois catégories principales d’API sont disponibles dans Time Series Insights :

* **API d’environnement** : requêtes sur l’environnement Time Series Insights proprement dit. Exemples : la liste des environnements auxquels l’appelant a accès, les métadonnées de l’environnement.

* **API modèle-requête de série chronologique (TSM-Q)** : opérations de création, de lecture, de mise à jour et de suppression sur les métadonnées stockées dans la partie environnement du modèle de série chronologique. Exemples : instances, types et hiérarchies.

* **API de requête de série chronologique (TSQ)** : récupération des données d’événements au fil de leur enregistrement à partir du fournisseur de source. Ces API peuvent effectuer des opérations de transformation, de combinaison et de calculs sur des données de série chronologique.

Le [langage Expression de série chronologique (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) constitue une quatrième catégorie puissante. Il utilise des modèles de série chronologique pour permettre la composition de calculs avancés.

## <a name="azure-time-series-insights-preview-core-apis"></a>API principales Azure Time Series Insights en préversion

Les API principales suivantes sont prises en charge.

[![Vue d’ensemble de la requête de série heure](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API d’environnement

Les API d’environnement suivantes sont disponibles :

* [API Obtenir l’environnement](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api) : retourne la liste des environnements auxquels l’appelant est autorisé à accéder.
* [API Obtenir la disponibilité de l’environnement](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api) : retourne la distribution du nombre d’événements au cours du timestamp `$ts` de l’événement. Cette API permet de déterminer s’il s’est produit des événements dans le timestamp en renvoyant le nombre d’événements.
* [API Obtenir le schéma des événements](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api) : retourne les métadonnées du schéma des événements pour une période de recherche donnée. Cette API permet de récupérer toutes les métadonnées et propriétés disponibles dans le schéma sur une période définie.

## <a name="time-series-model-query-tsm-q-apis"></a>API modèle-requête de série chronologique (TSM-Q)

Les API modèle-requête de série chronologique suivantes sont disponibles :

* [API Paramètres du modèle](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api) : permet d’obtenir et de corriger le type par défaut et le nom du modèle de l’environnement.
* [API Types](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api) : active CRUD sur les types Time Series et les variables associées.
* [API Hiérarchies](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api) : active CRUD sur les hiérarchies Time Series et les chemins d’accès de champs associés.
* [API Instances](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api) : active CRUD sur les instances Time Series et les champs d’instance associés.

## <a name="time-series-query-tsq-apis"></a>API de requête de série chronologique (TSQ)

Les API de requête de série chronologique suivantes sont disponibles :

* [API Obtenir les événements](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api) : permet l’interrogation et l’extraction de données Time Series Insights à partir d’événements au fil de leur enregistrement dans Time Series Insights à partir du fournisseur de source.

* [API Obtenir les séries](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api) : permet l’interrogation et l’extraction de données Time Series Insights à partir d’événements capturés à l’aide des données enregistrées sur la connexion filaire. Les valeurs retournées dépendent des variables définies dans le modèle ou inline.

    >[!NOTE]
    > La clause d’agrégation est ignorée même si elle est spécifiée dans un modèle ou inline.

  L’API Obtenir les séries retourne une valeur Time Series pour chaque variable de chaque intervalle. Il s’agit du format utilisé par Time Series Insights pour la sortie JSON d’une requête. Les valeurs retournées dépendent de l’ID Time Series et de l’ensemble de variables fournies.

* [API Agréger les séries](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api) : permet l’interrogation et l’extraction de données Time Series Insights à partir d’événements capturés en échantillonnant et en agrégeant les données enregistrées.

  L’API Agréger les séries retourne une valeur Time Series pour chaque variable de chaque intervalle. Les valeurs dépendent de l’ID Time Series et de l’ensemble de variables fournies. L’API Agréger les séries effectue une réduction en utilisant les variables stockées dans le modèle Time Series ou définies inline pour agréger ou échantillonner les données.

  Types d’agrégations pris en charge : `Min`, `Max`, `Sum`, `Count` et `Average`.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [entrée et stockage](./time-series-insights-update-storage-ingress.md) dans la version préliminaire d’Azure temps série Insights.

- Lire la version préliminaire de temps série Insights [modélisation des données](./time-series-insights-update-tsm.md) article.

- Découvrir [meilleures pratiques lors du choix d’un ID de série de temps](./time-series-insights-update-how-to-id.md).
