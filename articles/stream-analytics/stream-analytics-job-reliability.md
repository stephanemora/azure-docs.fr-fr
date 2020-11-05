---
title: Éviter les interruptions de service dans les travaux Azure Stream Analytics
description: Cet article aide à rendre des travaux Stream Analytics résistants aux mises à niveau.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 889e298f64689748340713de6318f8ffcd181001
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123844"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir la fiabilité des travaux Stream Analytics lors des mises à jour de service

Un service entièrement géré se distingue notamment par sa capacité à introduire de nouvelles fonctionnalités et améliorations de service à un rythme rapide. Par conséquent, Stream Analytics peut bénéficier d’un déploiement de mise à jour de service de manière hebdomadaire (ou plus fréquemment). Quel que soit le nombre de tests réalisés, il existe toujours un risque qu’un travail existant et en cours d’exécution cesse de fonctionner en raison de l’introduction d’un bogue. Si vous exécutez des tâches stratégiques, ces risques doivent être écartés. Vous pouvez réduire ce risque en suivant le modèle de **[région jumelée](../best-practices-availability-paired-regions.md)** d’Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Comment les régions jumelées d’Azure résolvent-elles ce problème ?

Stream Analytics garantit que les travaux dans les régions jumelées sont mis à jour dans des lots distincts. Ainsi, il existe un intervalle de temps suffisant entre les mises à jour pour identifier les problèmes potentiels et y remédier.

_À l’exception de la région Inde Centre_ (dont la région jumelée, Inde Sud, ne prend pas en charge Stream Analytics), le déploiement d’une mise à jour sur Stream Analytics ne se produit pas en même temps dans un ensemble de régions jumelées. Les déploiements dans plusieurs régions **dans le même groupe** peuvent se produire **en même temps**.

L’article sur **[la disponibilité et les régions jumelées](../best-practices-availability-paired-regions.md)** contient les informations les plus récentes sur le jumelage des régions.

Nous conseillons de déployer des tâches identiques sur les deux régions jumelées. Vous devez ensuite [superviser ces tâches](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) pour être averti quand un événement inattendu se produit. Si une de ces tâches se termine dans un [État d’échec](./job-states.md) après une mise à jour du service Stream Analytics, vous pouvez contacter le support technique pour vous aider à identifier la cause racine. Vous devez également basculer les consommateurs en aval vers la sortie de tâche saine.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion de Stream Analytics](/rest/api/streamanalytics/)