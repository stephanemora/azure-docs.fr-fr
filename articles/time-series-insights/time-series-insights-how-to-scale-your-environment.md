---
title: Mise à l’échelle de votre environnement Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment mettre à l’échelle votre environnement Azure Time Series Insights. Utilisez le portail Azure pour augmenter ou réduire la capacité d’une référence SKU tarifaire.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: ce29336c6e5dc187eb2ef713cd5dfeafdc8a003f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165649"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Mise à l’échelle de votre environnement Time Series Insights

Cet article décrit comment changer la capacité de votre environnement Time Series Insights à l’aide du portail Azure. La capacité est le multiplicateur appliqué au débit d’entrée, à la capacité de stockage et au coût associé à votre référence SKU.

Vous pouvez utiliser le portail Azure pour augmenter ou diminuer la capacité d’une référence SKU tarifaire donnée.

Toutefois, vous ne pouvez pas changer la référence SKU du niveau tarifaire. Par exemple, un environnement avec une référence SKU tarifaire S1 ne peut pas être converti en environnement S2 ou inversement.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Capacités et débits d’entrée de la référence S1

| Capacité de la référence S1 | Débit d’entrée | Capacité de stockage maximale
| --- | --- | --- |
| 1 | 1 Go (1 million d’événements) | 30 Go (30 millions d’événements) par mois |
| 10 | 10 Go (10 millions d’événements) | 300 Go (300 millions d’événements) par mois |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Capacités et débits d’entrée de la référence S2

| Capacité de la référence S2 | Débit d’entrée | Capacité de stockage maximale
| --- | --- | --- |
| 1 | 10 Go (10 millions d’événements) | 300 Go (300 millions d’événements) par mois |
| 10 | 100 Go (100 millions d’événements) | 3 To (3 milliards d’événements) par mois |

Les capacités sont mises à l’échelle de façon linéaire. Par conséquent, une référence SKU S1 avec la capacité 2 prend en charge 2 Go (2 millions) d’événements par débit d’entrée par jour et 60 Go (60 millions d’événements) par mois.

## <a name="change-the-capacity-of-your-environment"></a>Changer la capacité de votre environnement

1. Dans le portail Azure, recherchez et sélectionnez votre environnement Time Series Insights.

1. Dans le menu de votre environnement Time Series Insights, sélectionnez **Configurer**.

   [![configure.png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Réglez le curseur **Capacité** pour sélectionner la capacité qui correspond à vos besoins en matière de débit d’entrée et de stockage. Notez que le **débit d’entrée**, la **capacité de stockage** et l’**estimation des coûts** se mettent à jour de manière dynamique pour montrer l’impact du changement.

   [![Curseur](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Vous pouvez aussi entrer le nombre du multiplicateur de capacité dans la zone de texte à droite du curseur.

1. Sélectionnez **Enregistrer** pour mettre à l’échelle l’environnement. L’indicateur de progression s’affiche momentanément tant que la modification n’est pas validée.

## <a name="next-steps"></a>Étapes suivantes

- Vérifiez que la nouvelle capacité est [suffisante pour éviter la limitation](time-series-insights-diagnose-and-solve-problems.md).