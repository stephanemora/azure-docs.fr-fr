---
title: Interrogation de données dans la Préversion – Azure Time Series Insights | Microsoft Docs
description: Concepts de l’interrogation de données et vue d’ensemble de l’API REST HTTP dans Azure Time Series Insights Preview.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284889"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Interrogation de données dans Azure Time Series Insights (préversion)

Azure Time Series Insights permet d’interroger les données sur les événements et les métadonnées stockées dans l’environnement avec des API de surface publiques. Ces API sont également utilisées par [l’Explorateur Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Trois catégories principales d’API sont disponibles dans Time Series Insights :

* **API d’environnement** : ces API activent les requêtes sur l’environnement Time Series Insights proprement dit. Elles peuvent être utilisées pour recueillir la liste des environnements auxquels l’appelant a accès et les métadonnées de l’environnement.
* **API modèle-requête de série chronologique (TSM-Q)** : opérations CRUD (création, lecture, mise à jour et suppression) sur les métadonnées stockées dans le modèle Time Series de l’environnement. Celles-ci peuvent être utilisées pour accéder aux instances, types et hiérarchies, et les modifier.
* **API de requête de série chronologique (TSQ)** : permet la récupération des données de télémétrie ou d’événements au fur et à mesure de leur enregistrement à partir du fournisseur source et permet des calculs performants et des agrégations sur les données à l’aide de fonctions scalaires et d’agrégation avancées.

Time Series Insights utilise un langage d’expression riche basé sur des chaînes, [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax), pour exprimer des calculs.

## <a name="azure-time-series-insights-core-apis"></a>API principales d’Azure Time Series Insights

Les API principales suivantes sont prises en charge.

[![Vue d'ensemble de la requête de série chronologique](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API d’environnement

* [API Obtenir les environnements](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get) : retourne la liste des environnements auxquels l’appelant est autorisé à accéder.
* [API Obtenir la disponibilité des environnements](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) : retourne la distribution du nombre d’événements au cours du timestamp `$ts` de l’événement. Cette API permet de déterminer s’il s’est produit des événements dans l’environnement en renvoyant le nombre d’événements scindés en intervalles de temps, le cas échéant.
* [API Obtenir le schéma des événements](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema) : retourne les métadonnées du schéma des événements pour une période de recherche donnée. Cette API permet de récupérer toutes les métadonnées et propriétés disponibles dans le schéma sur une période définie.

## <a name="time-series-model-query-tsm-q-apis"></a>API modèle-requête de série chronologique (TSM-Q)

La plupart de ces API prennent en charge l’opération d’exécution par lot pour activer les opérations CRUD par lot sur plusieurs entités de modèle Time Series :

* [API Paramètres du modèle](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api) : active *OBTENIR* et *CORRIGER* sur le type par défaut et le nom du modèle de l’environnement.
* [API Types](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api) : active CRUD sur les types Time Series et les variables associées.
* [API Hiérarchies](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api) : active CRUD sur les hiérarchies Time Series et les chemins d’accès de champs associés.
* [API Instances](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api) : active CRUD sur les instances Time Series et les champs d’instance associés. En outre, l’API Instances prend en charge les opérations suivantes :
  * [Rechercher](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search) : récupère une liste partielle des résultats d’une recherche d’instances de série chronologique en fonction des attributs d’instance.
  * [Suggérer](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest) : recherche et suggère une liste partielle des résultats d’une recherche d’instances de série chronologique en fonction des attributs d’instance.

## <a name="time-series-query-tsq-apis"></a>API de requête de série chronologique (TSQ)

Ces API sont disponibles sur tous les stockages dans notre solution de stockage multicouches pris en charge dans Time Series Insights. Les paramètres URL de requête permettent de spécifier le [type de stockage](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) sur lequel la requête doit s’exécuter :

* [API Obtenir les événements](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) : permet l’interrogation et l’extraction d’événements bruts et des timestamps d’événements associés tels qu’ils sont enregistrés dans Time Series Insights à partir du fournisseur de source. Cette API permet de récupérer des événements bruts pour un ID Time Series donné et une étendue de recherche. Cette API prend en charge la pagination pour récupérer le jeu de données de réponse complet pour l’entrée sélectionnée. 

* [API Obtenir les séries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries) : Active l’interrogation et la récupération des valeurs calculées et des timestamps d’événements associés en appliquant des calculs définis par des variables sur des événements bruts. Ces variables peuvent être définies dans le modèle Time Series ou fournies inline dans la requête. Cette API prend en charge la pagination pour récupérer le jeu de données de réponse complet pour l’entrée sélectionnée. 

* [API Agréger les séries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries) : permet l’interrogation et la récupération des valeurs agrégées et des timestamps d’intervalle associés en appliquant des calculs définis par des variables sur des événements bruts. Ces variables peuvent être définies dans le modèle Time Series ou fournies inline dans la requête. Cette API prend en charge la pagination pour récupérer le jeu de données de réponse complet pour l’entrée sélectionnée. 
  
  Pour une étendue de recherche et un intervalle spécifiés, cette API renvoie une réponse agrégée par variable et par intervalle pour un ID de série chronologique. Le nombre d’intervalles dans le jeu de données de réponse est calculé en comptant les cycles d’époque (le nombre de millisecondes qui se sont écoulées depuis l’époque UNIX - 1er janvier 1970) et en divisant les cycles par la taille de l’intervalle de temps spécifiée dans la requête.

  Les timestamps retournés dans le jeu de réponses sont des limites de l’intervalle gauche, et non des événements échantillonnés à partir de l’intervalle. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les différentes variables qui peuvent être définies dans le [modèle Time Series](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- En savoir plus sur l’interrogation des données à partir de l’[Explorateur Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
