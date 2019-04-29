---
title: Éviter les interruptions de service dans les travaux Azure Stream Analytics
description: Cet article aide à rendre des travaux Stream Analytics résistants aux mises à niveau.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479454"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir la fiabilité des travaux Stream Analytics lors des mises à jour de service

Un service entièrement géré se distingue notamment par sa capacité à introduire de nouvelles fonctionnalités et améliorations de service à un rythme rapide. Par conséquent, Stream Analytics peut bénéficier d’un déploiement de mise à jour de service de manière hebdomadaire (ou plus fréquemment). Quel que soit le nombre de tests réalisés, il existe toujours un risque qu’un travail existant et en cours d’exécution cesse de fonctionner en raison de l’introduction d’un bogue. Pour les clients qui exécutent des travaux de traitement critiques en continu, ces risques doivent être limités. Pour réduire ce risque, les clients peuvent avoir recours à un mécanisme basé sur le modèle de **[région jumelée](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** d’Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Comment les régions jumelées d’Azure résolvent-elles ce problème ?

Stream Analytics garantit que les travaux dans les régions jumelées sont mis à jour dans des lots distincts. Ainsi, il existe un écart de temps suffisant entre les mises à jour pour identifier les bogues potentiels et y remédier.

_À l’exception de la région Inde Centre_ (dont la région jumelée, Inde Sud, ne prend pas en charge Stream Analytics), le déploiement d’une mise à jour sur Stream Analytics ne se produit pas en même temps dans un ensemble de régions jumelées. Les déploiements dans plusieurs régions **dans le même groupe** peuvent se produire **en même temps**.

L’article sur **[la disponibilité et les régions jumelées](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** contient les informations les plus récentes sur le jumelage des régions.

Nous conseillons aux clients de déployer des travaux identiques sur les deux régions jumelées. Outre les fonctionnalités de surveillance interne de Stream Analytics, nous recommandons aux clients de surveiller les travaux comme si **les deux** étaient des travaux de production. Si un arrêt survient suite à la mise à jour du service Stream Analytics, signalez l’incident de manière appropriée et basculez les consommateurs en aval sur la sortie de travail intègre. Le signalement à l’équipe de support permettra d’empêcher la région jumelée d’être affectée par le nouveau déploiement et de maintenir l’intégrité des travaux jumelés.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
