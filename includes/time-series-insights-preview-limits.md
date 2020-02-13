---
title: Fichier Include
description: Fichier Include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77123187"
---
### <a name="general-availability-and-preview-comparison"></a>Comparaison entre la disponibilité générale et la version préliminaire

Le tableau suivant récapitule quelques différences clés entre les instances Azure Time Series Insights en disponibilité générale et en préversions.

| | GA | PRÉVERSION |
| --- | --- | ---|
| Citoyen de première classe | Centrage sur les événements | Centrage sur les séries chronologiques |
| Raisonnement sémantique | Bas niveau (données de référence) | Haut niveau (modèles) |
| Contextualisation des données | Niveau non-appareil | Niveau appareil et non-appareil |
| Stockage de logique de calcul | Non | Stocké dans des variables types faisant partie du modèle |
| Stockage et contrôle d’accès | Non | Activé via un modèle |
| Agrégations/échantillonnage | Non | Pondéré et l’événement et par le temps |
| Reconstruction de signal | Non | Interpolation |
| Production de séries chronologiques dérivées | Non | Oui, fusions et jointures |
| Flexibilité de la langue | Non composable | Composable |
| Langage d’expression | Chaîne de prédicat | Expressions de série chronologique (chaînes de prédicat, valeurs, expressions et fonctions) |

### <a name="property-limits"></a>Limites de propriétés

Les limites de propriétés Azure Time Series Insights ont augmenté jusqu’à 1 000 à partir d’un plafond maximal de 800 en disponibilité générale. Les propriétés d’événements fournies ont des colonnes JSON, CSV et de graphique que vous pouvez afficher dans l’[Explorateur Time Series Insights en préversion](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriétés maximales |
| --- | --- |
| PAYG en préversion | 1 000 propriétés (colonnes) |
| Disponibilité générale S1 | 600 propriétés (colonnes) |
| Disponibilité générale S2 | 800 propriétés (colonnes) |

### <a name="event-sources"></a>Sources d’événement

Au maximum deux sources d’événements par instance sont prise en charge. 

* Découvrez comment [ajouter une source Event Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurez [une source de hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Par défaut, les [environnements en préversion préliminaire prennent en charge des débits](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) d’entrée allant jusqu’à **1 mégaoctet par seconde (Mo/s) par environnement**. Les clients peuvent augmenter le débit de leurs environnements en préversion jusqu’à **16 Mo/s** si nécessaire. Une limite de **0,5 Mo/s** par partition est également fixée. 

### <a name="api-limits"></a>Limites de l’API

Les limites de l’API REST pour Time Series Insights en préversion sont spécifiées dans la [documentation de référence sur l’API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).
