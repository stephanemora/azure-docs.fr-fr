---
title: Modèle de données de la télémétrie des exceptions d’Azure Application Insights
description: Modèle de données Application Insights pour la télémétrie des exceptions
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671934"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Télémétrie des exceptions : Modèle de données Application Insights

Dans [Application Insights](../../azure-monitor/app/app-insights-overview.md), une instance d’exception représente une exception prise en charge ou non prise en charge générée pendant l’exécution de l’application surveillée.

## <a name="problem-id"></a>ID du problème

Identificateur indiquant à quel endroit du code l’exception a été levée. Utilisé pour le regroupement des exceptions. Généralement, une combinaison de type d’exception et une fonction de la pile des appels.

Longueur maximale : 1 024 caractères

## <a name="severity-level"></a>Niveau de gravité

Niveau de gravité de trace. La valeur peut être `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Détails de l’exception

(À développer)

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Mesures personnalisées

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](data-model.md).
- Découvrez comment [diagnostiquer les exceptions dans vos applications web avec Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Découvrez quelles [plateformes](../../azure-monitor/app/platforms.md) sont prises en charge par Application Insights.
