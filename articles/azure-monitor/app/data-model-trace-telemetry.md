---
title: 'Modèle de données de télémétrie d’Azure Application Insights : télémétrie des traces | Microsoft Docs'
description: Modèle de données Application Insights pour la télémétrie des traces
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: a80c96891f3d91a920519db2915932742bd84d72
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002295"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Télémétrie de trace : Modèle de données Application Insights

La télémétrie des traces (dans [Application Insights](../../application-insights/app-insights-overview.md)) représente des instructions de traces de type `printf` qui font l’objet de recherches textuelles. `Log4Net`, `NLog` et les autres entrées de fichier journal de type texte sont converties en instances de ce type. La trace ne comporte pas de mesures comme l’extensibilité.

## <a name="message"></a>Message

Message de trace.

Longueur maximale : 32 768 caractères

## <a name="severity-level"></a>Niveau de gravité

Niveau de gravité de trace. La valeur peut être `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Exploration des journaux .NET dans Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Exploration du suivi des journaux Java dans Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](data-model.md).
- [Écrire des données de télémétrie de trace personnalisées](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Découvrez quelles [plateformes](../../azure-monitor/app/platforms.md) sont prises en charge par Application Insights.
