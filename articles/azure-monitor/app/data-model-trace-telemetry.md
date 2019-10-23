---
title: 'Modèle de données de télémétrie d’Azure Application Insights : télémétrie des traces | Microsoft Docs'
description: Modèle de données Application Insights pour la télémétrie des traces
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 855a93d8a6350c625fe0820fae83644aec3459ed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678118"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Télémétrie de trace : Modèle de données Application Insights

La télémétrie des traces (dans [Application Insights](../../azure-monitor/app/app-insights-overview.md)) représente des instructions de traces de type `printf` qui font l’objet de recherches textuelles. `Log4Net`, `NLog` et les autres entrées de fichier journal de type texte sont converties en instances de ce type. La trace ne comporte pas de mesures comme l’extensibilité.

## <a name="message"></a>Message

Message de trace.

Longueur maximale : 32 768 caractères

## <a name="severity-level"></a>Niveau de gravité

Niveau de gravité de trace. La valeur peut être `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Exploration des journaux d’activité .NET dans Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Exploration du suivi des journaux d’activité Java dans Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](data-model.md).
- [Écrire des données de télémétrie de trace personnalisées](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Découvrez quelles [plateformes](../../azure-monitor/app/platforms.md) sont prises en charge par Application Insights.
