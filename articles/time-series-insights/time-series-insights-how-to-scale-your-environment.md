---
title: Mise à l’échelle de votre environnement - Azure Time Series Insights | Microsoft Docs
description: Découvrez comment mettre à l’échelle votre environnement Azure Time Series Insights à l’aide du Portail Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 9604f0c6eeb9d0ac4b7d07f1ab1ba03285fd60fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81640062"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Mise à l’échelle de votre environnement Time Series Insights

Cet article explique comment changer la capacité de votre environnement Time Series Insights à l’aide du [portail Azure](https://portal.azure.com). La capacité est le multiplicateur appliqué au débit d’entrée, à la capacité de stockage et au coût associé à votre référence SKU.

Vous pouvez utiliser le portail Azure pour augmenter ou diminuer la capacité d’une référence SKU tarifaire donnée.

Toutefois, vous ne pouvez pas changer la référence SKU du niveau tarifaire. Par exemple, un environnement avec une référence SKU tarifaire S1 ne peut pas être converti en environnement S2 ou inversement.

## <a name="ga-limits"></a>Limites de mise à la disposition générale

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Changer la capacité de votre environnement

1. Dans le portail Azure, recherchez et sélectionnez votre environnement Time Series Insights.

1. Dans le menu de votre environnement Time Series Insights, sélectionnez **Configuration du stockage**.

   [![Configurer votre capacité Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Réglez le curseur **Capacité** pour sélectionner la capacité qui correspond à vos besoins en matière de débit d’entrée et de stockage. Notez que le **débit d’entrée**, la **capacité de stockage** et l’**estimation des coûts** se mettent à jour de manière dynamique pour montrer l’impact du changement.

   [![Configurer votre environnement à l’aide du curseur de capacités](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Vous pouvez aussi entrer le nombre du multiplicateur de capacité dans la zone de texte à droite du curseur.

1. Sélectionnez **Enregistrer** pour mettre à l’échelle l’environnement. L’indicateur de progression s’affiche momentanément tant que la modification n’est pas validée.

1. Vérifiez que la nouvelle capacité est [suffisante pour éviter la limitation](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, voir [Présentation de la conservation des données dans Time Series Insights](time-series-insights-concepts-retention.md).

- Découvrez la [configuration de la conservation des données dans Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Découvrez la [planification de votre environnement](time-series-insights-environment-planning.md).
