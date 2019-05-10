---
title: Planifier la mise à l’échelle de votre environnement Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment suivre les meilleures pratiques pour planifier un environnement Azure Time Series Insights, notamment la capacité de stockage, la conservation des données, la capacité d’entrée, le monitoring et la récupération d'urgence (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: bf1f570319370fab99e2f52086bc81df259e3d35
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236511"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planifier votre environnement Azure temps série Insights GA

Cet article décrit comment planifier votre environnement de disponibilité générale (GA) Azure Time Series Insights en fonction de votre taux d’entrée attendu et vos exigences de rétention de données.

## <a name="video"></a>Vidéo

### <a name="learn-more-about-data-retention-in-azuretime-series-insights-and-how-to-plan-for-itbr"></a>En savoir plus sur la rétention des données dans AzureTime Series Insights et la planification pour celle-ci.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Bonnes pratiques

Pour commencer à utiliser Time Series Insights, il est conseillé de connaître la quantité de données à transmettre à la minute ainsi que la durée de stockage de vos données.  

Pour plus d’informations sur la capacité et la rétention des deux références Time Series Insights, consultez [Tarifs de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Tenez compte des attributs suivants pour planifier au mieux l’environnement et assurer sa réussite à long terme :

- <a href="#understand-storage-capacity">Capacité de stockage</a>
- <a href="#understand-data-retention">Période de rétention de données</a>
- <a href="#understand-ingress-capacity">Capacité d’entrée</a>
- <a href="#shape-your-events">Mise en forme de vos événements</a>
- <a href="#ensure-you-have-reference-data">Garantir que vous disposez des données de référence en place</a>

## <a name="understand-storage-capacity"></a>Comprendre la capacité de stockage

Par défaut, Azure Time Series Insights conserve les données en fonction de la quantité de stockage que vous avez configuré (unités x quantité de stockage par unité) et entrée.

## <a name="understand-data-retention"></a>Comprendre la rétention des données

Vous pouvez configurer le paramètre de **durée de rétention des données** de votre environnement Time Series Insights, en sélectionnant jusqu'à 400 jours de rétention. Time Series Insights comprend deux modes : un premier optimisé pour faire en sorte que votre environnement dispose des données les plus récentes (activé par défaut) et un second optimisé pour vérifier que les limites de rétention sont bien respectées (l’entrée est suspendue lorsque la capacité de stockage globale de l’environnement est atteinte).  Vous pouvez ajuster la rétention et basculer entre les deux modes dans la page de configuration de l’environnement dans le portail Azure.

Vous pouvez configurer une durée maximum de conservation des données de 400 jours dans votre environnement Time Series Insights.

### <a name="configure-data-retention"></a>Configurer la rétention de données

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre environnement Time Series Insights.

1. Sur la **page de l’environnement Time Series Insights**, sous le titre **Paramètres**, sélectionnez **Configurer**.

1. Dans le champ **Durée de rétention des données (en jours)**, saisissez une valeur comprise entre 1 et 400.

   [![Configurer la rétention](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> En savoir plus sur l’implémentation d’une stratégie de rétention de données approprié en examinant [comment configurer la rétention](./time-series-insights-how-to-configure-retention.md).

## <a name="understand-ingress-capacity"></a>Comprendre la capacité d’entrée

L’autre point à prendre en compte pour la planification est la capacité d’entrée, qui dépend de l’allocation par minute.

En termes de limitation, un paquet de données entré avec une taille de paquet de 32 Ko est considéré comme 32 événements, faisant chacun 1 Ko. La taille maximale autorisée de l’événement est 32 Ko ; les paquets de données d’une taille supérieure à 32 Ko sont tronqués.

Le tableau suivant récapitule la capacité d’entrée pour chaque référence SKU :

|SKU  |Nombre d’événements / mois / unité  |Taille des événements / mois / unité  |Événements compter / Minute / unité  | Taille / Minute / unité   |
|---------|---------|---------|---------|---------|
|S1     |   30 millions     |  30 Go     |  720    |  720 Ko   |
|S2     |   300 millions    |   300 Go   | 7 200   | 7 200 Ko  |

Vous pouvez augmenter la capacité d’une référence SKU S1 ou S2 à 10 unités dans un environnement unique. Vous ne pouvez pas migrer d’un environnement S1 vers un environnement S2, et inversement.

Pour la capacité d’entrée, vous devez tout d’abord déterminer le total des entrées dont vous avez besoin chaque mois. Ensuite, déterminez vos besoins par minute, car c’est là que la limitation et la latence jouent un rôle.

En cas de pic d’entrée de données d’une durée inférieure à 24 heures, Time Series Insights peut effectuer un « rattrapage » à une vitesse d’entrée égale à 2x les taux indiqués ci-dessus.

Par exemple, si vous avez une seule référence SKU S1 et des données d’entrée à un taux de 720 événements par minute, et un pic d’une durée inférieure à une heure à un taux de 1 440 événements maximum, aucune latence ne sera notable dans votre environnement. Toutefois, si vous dépassez les 1 440 événements par minute pendant plus d’une heure, vous allez probablement observer une latence des données qui sont visualisées et disponibles pour les requêtes dans votre environnement.

Vous ne savez peut-être pas à l’avance la quantité de données que vous allez transmettre en mode push. Dans ce cas, la télémétrie de données pour [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) et [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) est disponible dans votre portail Azure. Cette télémétrie peut vous aider à déterminer comment configurer votre environnement. Utilisez la page **Indicateurs de performance** dans le portail Azure de la source d’événements correspondante pour afficher sa télémétrie. Comprendre les indicateurs de performance de votre source d’événement vous permet de planifier et configurer plus efficacement votre environnement Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calculer les besoins d’entrée

- Vérifiez que votre capacité d’entrée est supérieure à votre taux moyen par minute et que votre environnement est suffisamment grand pour gérer votre entrée attendue qui équivaut à 2x votre capacité pendant moins d’une heure.

- En cas de pics d’entrée d’une durée supérieure à 1 heure, utilisez le taux de pointe comme moyenne, et provisionnez un environnement ayant la capacité de gérer ce taux.

### <a name="mitigate-throttling-and-latency"></a>Résoudre la limitation et la latence

Pour plus d’informations sur la façon d’éviter la limitation et la latence, découvrez comment [réduire la latence et la limitation](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Mettre en forme vos événements

Il est important de s’assurer de la façon de vous envoyez des événements à TSI prend en charge la taille de l’environnement que vous approvisionnez (à l’inverse, vous pouvez mapper la taille de l’environnement à TSI lit le nombre d’événements et la taille de chaque événement). De la même manière, il est important de songer aux attributs à traiter et à utiliser pour le filtrage lors de l’interrogation de vos données.

> [!TIP]
> Passez en revue le code JSON mise en forme de documentation dans [envoi d’événements](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-you-have-reference-data"></a>Assurez-vous de qu'avoir des données de référence

Un **jeu de données de référence** est une collection d’éléments qui augmente les événements à partir de votre source d’événements. Le moteur d’entrée Time Series Insights associe chaque événement de votre source d’événements à la ligne de données correspondante dans votre jeu de données de référence. Cet événement ajouté est ensuite disponible pour la requête. Cette jointure repose sur les colonnes de clé privée définies dans votre jeu de données de référence.

Notez que les données de référence ne sont pas jointes rétroactivement. Cela signifie que seules les données d’entrée actuelles et futures sont mises en correspondance et jointes à l’ensemble de données de référence, après configuration et téléchargement.  Si vous envoyez de gros volumes de données d’historique à TSI sans préalablement charger ou créer de données de référence dans TSI, il est possible que vous deviez exécuter à nouveau votre tâche (configuration pas très amusante, au demeurant).  

Pour plus d’informations sur la création, le chargement et la gestion des données de référence dans TSI, accédez à notre [documentation sur les jeux de données de référence](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Étapes suivantes

- Commencez par créer [un nouvel environnement Time Series Insights dans le portail Azure](time-series-insights-get-started.md).

- Découvrez comment [ajouter une source d’événement Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md) à Time Series Insights.

- En savoir plus sur comment [configurer une source d’événement IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
