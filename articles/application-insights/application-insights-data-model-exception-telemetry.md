---
title: 'Modèle de données de télémétrie d’Azure Application Insights : télémétrie des exceptions | Microsoft Docs'
description: Modèle de données Application Insights pour la télémétrie des exceptions
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 88e521a04d69d4ca169e33a80ac15620568c5282
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35632203"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Télémétrie des exceptions : modèle de données Application Insights

Dans [Application Insights](app-insights-overview.md), une instance d’exception représente une exception prise en charge ou non prise en charge générée pendant l’exécution de l’application surveillée.

## <a name="problem-id"></a>ID du problème

Identificateur indiquant à quel endroit du code l’exception a été levée. Utilisé pour le regroupement des exceptions. Généralement, une combinaison de type d’exception et une fonction de la pile des appels.

Longueur maximale : 1024 caractères

## <a name="severity-level"></a>Niveau de gravité

Niveau de gravité de trace. La valeur peut être `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Détails de l’exception

(À développer)

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Mesures personnalisées

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](application-insights-data-model.md).
- Découvrez comment [diagnostiquer les exceptions dans vos applications web avec Application Insights](app-insights-asp-net-exceptions.md).
- Découvrez quelles [plateformes](app-insights-platforms.md) sont prises en charge par Application Insights.
