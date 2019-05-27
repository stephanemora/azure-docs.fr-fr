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
ms.openlocfilehash: e87a82e985ed1d1794f9da00546f167ef01e1779
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147449"
---
## <a name="business-disaster-recovery"></a>Récupération d'urgence

Cette section décrit les fonctionnalités d’Azure Time Series Insights qui empêchent les applications et services en cours d’exécution même si un incident se produit (**reprise**).

### <a name="high-availability"></a>Haute disponibilité

Comme un service Azure, Time Series Insights propose certains **haute disponibilité** fonctionnalités à l’aide de redondances au niveau régional Azure. Par exemple, Microsoft Azure prend en charge les fonctionnalités de récupération d’urgence par le biais d’Azure **disponibilité inter-régions** fonctionnalité.

Fonctionnalités de haute disponibilité supplémentaires fournies par l’intermédiaire de Azure (et est également disponible pour n’importe quelle instance de Time Series Insights) :

1. **Basculement** : Azure fournit [géo-réplication et le chargement d’équilibrage](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
1. **Restauration des données** et **restauration du stockage**: Azure fournit [plusieurs options pour préserver et récupérer les données](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
1. **Site Recovery** fonctionnalités via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Veillez à activer ces fonctionnalités Azure fournir globale, entre les régions, la haute disponibilité pour vos utilisateurs et appareils.

> [!NOTE]
> Si Azure est configuré pour activer **disponibilité inter-régions**, aucune configuration de disponibilité entre les régions supplémentaire est requise dans Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT et à Event hubs

Certains services Azure IoT incluent également des fonctionnalités de récupération d’urgence intégrées :

1. [Récupération d’urgence haute disponibilité de IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) , notamment la redondance intra-région.
1. [Stratégies de Hub d’événements](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
1. [Redondance du stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

L’intégration de Time Series Insights avec ces autres services fournit d’urgence supplémentaires des opportunités de récupération. Par exemple, les données de télémétrie envoyées à votre concentrateur d’événements peuvent être rendue persistante une sauvegarde de base de données de stockage d’objets Blob Azure.

### <a name="time-series-insights"></a>Time Series Insights

Il existe plusieurs façons de conserver vos données Time Series Insights, les applications et les services qui s’exécutent même si celles-ci sont interrompues. Vous pouvez également déterminer qu’une copie complète, en double, sauvegarde de votre environnement Azure Time Series est nécessaire :

1. Comme un Time Series Insights-spécifique **instance basculement** pour rediriger les données et le trafic vers.
1. À des fins d’audit et de données préservation.

En règle générale, la meilleure façon de dupliquer un environnement Time Series Insights consiste à créer un deuxième environnement Time Series Insights dans une région Azure de la sauvegarde. Les événements sont également envoyées à cet environnement secondaire à partir de votre source d’événement principal. Assurez-vous d’utiliser un groupe de consommateurs dédié, deuxième et suivre les instructions de récupération d’urgence métier de cette source (fournies ci-dessus).

Plus précisément, pour créer un environnement en doublon :

1. Créer un environnement dans une seconde région ([créer un nouvel environnement Time Series Insights dans le portail Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. Créez un deuxième groupe de consommateurs dédié pour votre source d’événements.
1. Se connecter à cette source d’événements dans le nouvel environnement veillant à désigner le groupe de consommateurs dédié deuxième.
1. Passez en revue le Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) et [Event hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentation.

Enfin :

* Si votre région principale est affectée par un sinistre, basculez les opérations sur l’environnement Time Series Insights de sauvegarde.
* Utilisez votre deuxième région pour sauvegarder et récupérer toutes les données de télémétrie et de requête Time Series Insights.

> [!IMPORTANT]
> * Notez qu’un délai peut se produire lors du basculement.
> * Basculement peut entraîner un pic temporaire lors du traitement du message comme opérations sont redirigées.
> * Pour plus d’informations, consultez [Réduction de la latence dans Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).