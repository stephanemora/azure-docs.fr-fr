---
title: Fichier Include
description: Fichier Include
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: e3e7044148e262e6977ae3be96f7cb61218114a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388746"
---
## <a name="business-disaster-recovery"></a>Récupération d'urgence

Cette section décrit les fonctionnalités d’Azure Time Series Insights qui empêchent les applications et services en cours d’exécution même si un incident se produit. Ce concept est appelé reprise.

### <a name="high-availability"></a>Haute disponibilité

Comme un service Azure, Time Series Insights propose certaines fonctionnalités de haute disponibilité à l’aide de redondances au niveau de la région Azure. Par exemple, Microsoft Azure prend en charge les fonctionnalités de récupération d’urgence via la fonctionnalité de disponibilité inter-régions d’Azure.

Les fonctionnalités de haute disponibilité supplémentaires fournies via Azure et qui sont également disponibles pour n’importe quelle instance de Time Series Insights, incluez :

* **Basculement** : Azure fournit [géo-réplication et l’équilibrage de charge](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
* **Restauration des données** et **restauration du stockage**: Azure fournit [plusieurs options pour préserver et récupérer les données](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
* **Récupération de site**: Les fonctionnalités qui sont disponibles via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Veillez à activer ces fonctionnalités Azure fournir une haute disponibilité globale, entre les régions pour vos utilisateurs et appareils.

> [!NOTE]
> Si Azure est configuré pour activer la disponibilité de plusieurs régions, aucune configuration de disponibilité entre les régions supplémentaire n’est requise dans Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT et à event hubs

Certains services Azure IoT incluent également des fonctionnalités de récupération d’urgence intégrées :

* [Azure IoT Hub haute disponibilité sinistre](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), qui inclut la redondance intra-région.
* [Stratégies d’Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
* [Redondance du stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

L’intégration de Time Series Insights avec ces autres services fournit d’urgence supplémentaires des opportunités de récupération. Par exemple, les données de télémétrie envoyées à votre concentrateur d’événements peuvent être rendue persistante une sauvegarde de base de données de stockage Blob Azure.

### <a name="time-series-insights"></a>Time Series Insights

Il existe plusieurs façons de conserver vos données Time Series Insights, les applications et les services qui s’exécutent même si celles-ci sont interrompues. Vous pouvez également déterminer qu’une copie de sauvegarde complète de votre environnement Azure Time Series est nécessaire :

* Comme pour rediriger les données et le trafic vers une instance de basculement Time Series Insights spécifique.
* À des fins d’audit et de données préservation.

En règle générale, la meilleure façon de dupliquer un environnement Time Series Insights consiste à créer un deuxième environnement Time Series Insights dans une région Azure de la sauvegarde. Les événements sont également envoyées à cet environnement secondaire à partir de votre source d’événement principal. Assurez-vous d’utiliser un groupe de consommateurs dédié ensuite et à suivre business disaster recovery guidelines de cette source fournis précédemment.

Plus précisément, pour créer un environnement en doublon :

1. Créez un environnement dans la deuxième région. Pour obtenir des instructions, consultez [créer un nouvel environnement Time Series Insights dans le portail Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Créez un deuxième groupe de consommateurs dédié pour votre source d’événements.
1. Connectez cette source d’événements au nouvel environnement. Veillez à désigner le deuxième groupe de consommateurs dédié.
1. Passez en revue le Time Series Insights [IoT hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) et [concentrateur d’événements](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentation.

Enfin :

1. Si votre région principale est affectée par un sinistre, basculez les opérations sur l’environnement Time Series Insights de sauvegarde.
1. Utilisez votre deuxième région pour sauvegarder et récupérer toutes les données de télémétrie et de requête Time Series Insights.

> [!IMPORTANT]
> * Notez qu’un délai peut se produire lors du basculement.
> * Basculement peut entraîner un pic temporaire lors du traitement du message comme opérations sont redirigées.
> * Pour plus d’informations, consultez [Réduction de la latence dans Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).
