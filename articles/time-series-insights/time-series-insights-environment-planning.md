---
title: Planifier la mise à l’échelle de votre environnement Azure Time Series Insights | Microsoft Docs
description: Cet article décrit comment suivre les meilleures pratiques lorsque vous planifiez un environnement Azure Time Series Insights. Zones couvertes incluent la capacité de stockage, la rétention des données, capacité d’entrée, la surveillance et business la continuité des activités et récupération d’urgence (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9f9b7fe7aafa927f023e1d4e30e079a26f7fab
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431026"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planifier votre environnement Azure temps série Insights GA

Cet article décrit comment planifier votre environnement de disponibilité générale (GA) Azure Time Series Insights en fonction de votre taux d’entrée attendu et vos exigences de rétention de données.

## <a name="video"></a>Vidéo

**Regardez cette vidéo pour en savoir plus sur la conservation des données dans Azure Time Series Insights et comment il planifier**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Bonnes pratiques

Pour vous familiariser avec Time Series Insights, il est préférable si vous connaissez la quantité de données à transmettre à la minute et la durée pendant laquelle vous avez besoin stocker vos données.  

Pour plus d’informations sur la capacité et la rétention des deux références Time Series Insights, consultez [Tarifs de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Pour mieux planifier votre environnement Time Series Insights pour la réussite à long terme, prenez en compte les attributs suivants :

- <a href="#storage-capacity">Capacité de stockage</a>
- <a href="#data-retention">Période de rétention de données</a>
- <a href="#ingress-capacity">Capacité d’entrée</a>
- <a href="#shape-your-events">Mise en forme de vos événements</a>
- <a href="#ensure-that-you-have-reference-data">S’assurer que les données de référence en place</a>

## <a name="storage-capacity"></a>Capacité de stockage

Par défaut, Time Series Insights conserve les données selon la quantité de stockage que vous approvisionnez (unités &#215; la quantité de stockage par unité) et d’entrée.

## <a name="data-retention"></a>Conservation des données

Vous pouvez modifier le **durée de conservation des données** définition dans votre environnement Time Series Insights. Vous pouvez activer jusqu'à 400 jours de rétention. 

Time Series Insights a deux modes. Un seul mode optimise pour vérifier que votre environnement dispose de données les plus récentes. Ce mode est activé, par défaut. 

L’autre mode est configuré pour vous assurer que les limites de rétention sont satisfaites. Dans le deuxième mode, l’entrée est suspendue si la capacité de stockage globale de l’environnement est remplie. 

Vous pouvez ajuster la rétention et basculer entre les deux modes, sur la page de configuration de l’environnement dans le portail Azure.

Vous pouvez configurer une durée maximum de conservation des données de 400 jours dans votre environnement Time Series Insights.

### <a name="configure-data-retention"></a>Configurer la rétention de données

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre environnement Time Series Insights.

1. Dans le **environnement Time Series Insights** volet, sous **paramètres**, sélectionnez **configurer**.

1. Dans le **durée de rétention de données (en jours)** , entrez une valeur comprise entre 1 et 400.

   [![Configurer la rétention](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Pour en savoir plus sur la façon d’implémenter une stratégie de rétention de données approprié, consultez [comment configurer la rétention](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacité d’entrée

La deuxième zone de se concentrer sur pour la planification de votre environnement Time Series Insights est la capacité d’entrée. Capacité d’entrée est un dérivé de l’allocation par minute.

En termes de limitation, un paquet de données entré qui a une taille de paquet de 32 Ko est considéré comme 32 événements, chaque taille de 1 Ko. La taille maximale autorisée de l’événement est 32 Ko. Paquets de données supérieure à 32 Ko sont tronqués.

Le tableau suivant récapitule la capacité d’entrée par unité pour chaque référence Time Series Insights :

|SKU  |Nombre d’événements par mois  |Taille d’événement par mois  |Nombre d’événements par minute  |Taille d’événement par minute  |
|---------|---------|---------|---------|---------|
|S1     |   30 millions     |  30 Go     |  720    |  720 Ko   |
|S2     |   300 millions    |   300 Go   | 7 200   | 7 200 Ko  |

Vous pouvez augmenter la capacité d’une référence SKU S1 ou S2 à 10 unités dans un environnement unique. Vous ne pouvez pas migrer à partir d’un environnement S1 à S2. Vous ne pouvez pas migrer à partir d’un environnement S2 à un S1.

Pour la capacité d’entrée, d’abord déterminer le total des entrées dont vous avez besoin chaque mois. Ensuite, déterminez quels sont vos besoins votre par minute. 

La limitation et la latence jouent un rôle dans la capacité de la minute. Si vous avez un pic dans votre entrée de données qui a une durée inférieure à 24 heures, Time Series Insights peut « rattraper » à un taux d’entrée de deux fois les taux indiqués dans le tableau précédent.

Par exemple, si vous avez une seule référence SKU S1, vos données d’entrée à un taux de 720 événements par minute, et le débit de données de pics de moins d’une heure à une fréquence inférieure ou égale à 1 440 événements, n’est aucune latence notable dans votre environnement. Toutefois, si vous dépassez 1 440 événements par minute pour plus d’une heure, cela signifie probablement que sera connaître une latence dans les données qui sont visualisées et disponibles pour la requête dans votre environnement.

Vous ne savez pas à l’avance la quantité de données que vous vous attendez à transmettre. Dans ce cas, vous pouvez trouver des données de télémétrie de données pour [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) et [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) dans votre abonnement au portail Azure. Les données de télémétrie peuvent vous aider à déterminer comment configurer votre environnement. Utilisez le **métriques** volet dans le portail Azure pour la source d’événements respectifs afficher sa télémétrie. Comprendre les indicateurs de performance de votre source d’événement vous permet de planifier et configurer plus efficacement votre environnement Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calculer les besoins d’entrée

Pour calculer vos besoins d’entrée :

- Vérifiez que votre capacité d’entrée est au-dessus de votre taux moyen de la minute et que votre environnement est suffisamment grand pour gérer votre entrée attendue qui équivaut à deux fois votre capacité pendant moins d’une heure.

- Si les pics d’entrée se produisent et dernière pendant plus de 1 heure, utilisez le taux de pointe comme moyenne. Configurer un environnement avec la capacité de gérer le taux de pointe.

### <a name="mitigate-throttling-and-latency"></a>Résoudre la limitation et la latence

Pour plus d’informations sur la manière d’éviter la limitation et la latence, consultez [Résoudre la latence et la limitation](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Mettre en forme vos événements

Il est important de vérifier que la façon de que vous envoyer des événements à Time Series Insights prend en charge la taille de l’environnement que vous approvisionnez. (À l’inverse, vous pouvez mapper les la taille de l’environnement à combien d’événements Time Series Insights lit et la taille de chaque événement.) Il est également important de réfléchir sur les attributs que vous pouvez utiliser pour segmenter et filtrer par lorsque vous interrogez vos données.

> [!TIP]
> Passez en revue le code JSON mise en forme de documentation dans [envoi d’événements](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Assurez-vous d’avoir des données de référence

Un *jeu de données de référence* est une collection d’éléments qui augmente les événements à partir de votre source d’événements. Le moteur d’entrée Time Series Insights associe chaque événement à partir de votre source d’événements avec la ligne de données correspondante dans votre jeu de données de référence. L’événement augmentée est alors disponible pour la requête. La jointure est basée sur le **clé primaire** colonnes qui sont définies dans votre jeu de données de référence.

> [!NOTE]
> Données de référence ne sont pas jointes rétroactivement. Uniquement les données d’entrée actuelles et futures sont mis en correspondance et jointes au dataset de référence après avoir configuré et téléchargé. Si vous envisagez d’envoyer une grande quantité de données d’historique à Time Series Insights et ne le premier chargement ou créer des données de référence dans Time Series Insights, vous devrez peut-être rétablir votre travail (indicateur : pas très amusante).  

Pour en savoir plus sur la façon de créer, charger et gérer vos données de référence dans Time Series Insights, consultez notre [documentation du jeu de données de référence](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Étapes suivantes

- Commencez par créer [un nouvel environnement Time Series Insights dans le portail Azure](time-series-insights-get-started.md).

- Découvrez comment [ajouter une source d’événement Event Hubs](time-series-insights-how-to-add-an-event-source-eventhub.md) à Time Series Insights.

- En savoir plus sur comment [configurer une source d’événement IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
