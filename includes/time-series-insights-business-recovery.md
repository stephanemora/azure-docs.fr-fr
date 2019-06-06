---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431040"
---
## <a name="business-disaster-recovery"></a>Récupération d'urgence

Cette section décrit les fonctionnalités d’Azure Time Series Insights qui empêchent les applications et services en cours d’exécution, même si un incident se produit (connu sous le nom *reprise*).

### <a name="high-availability"></a>Haute disponibilité

Comme un service Azure, Time Series Insights propose certains *haute disponibilité* fonctionnalités à l’aide de redondances au niveau régional Azure. Par exemple, Azure prend en charge les fonctionnalités de récupération d’urgence par le biais d’Azure *disponibilité inter-régions* fonctionnalité.

Les fonctionnalités de haute disponibilité supplémentaires fournies par l’intermédiaire de Azure (et est également disponible pour n’importe quelle instance de Time Series Insights) comprennent :

- **Basculement** : Azure fournit [géo-réplication et l’équilibrage de charge](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Restauration des données** et **restauration du stockage**: Azure fournit [plusieurs options pour préserver et récupérer les données](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Récupération de site**: Azure fournit des fonctionnalités de récupération de site via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Veillez à qu'activer les fonctionnalités importantes de Azure fournir une haute disponibilité globale, entre les régions pour vos utilisateurs et appareils.

> [!NOTE]
> Si Azure est configuré pour activer la disponibilité de plusieurs régions, aucune configuration de disponibilité entre les régions supplémentaire est requise dans Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT et à event hubs

Certains services Azure IoT incluent également des fonctionnalités de récupération d’urgence intégrées :

- [Récupération d’urgence haute disponibilité de IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), y compris la redondance intra-région
- [Stratégies de concentrateurs d’événements](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redondance de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

L’intégration de Time Series Insights avec les autres services fournit d’urgence supplémentaires des opportunités de récupération. Par exemple, les données de télémétrie envoyées à votre concentrateur d’événements peuvent être rendue persistante une sauvegarde de base de données de stockage Blob Azure.

### <a name="time-series-insights"></a>Time Series Insights

Il existe plusieurs façons de conserver vos données Time Series Insights, les applications et les services en cours d’exécution, même si celles-ci sont interrompues. 

Toutefois, vous pouvez déterminer qu’une copie de sauvegarde complète de votre environnement Azure Time Series est également la nécessaire, aux fins suivantes :

- Comme un *instance basculement* spécifiquement pour Time Series Insights rediriger les données et le trafic vers
- Pour conserver les données et les informations d’audit

En règle générale, la meilleure façon de dupliquer un environnement Time Series Insights consiste à créer un deuxième environnement Time Series Insights dans une région Azure de la sauvegarde. Les événements sont également envoyées à cet environnement secondaire à partir de votre source d’événement principal. Assurez-vous que vous utilisez un groupe de consommateurs dédié deuxième. Suivez les instructions de récupération d’urgence de cette source business, comme décrit précédemment.

Pour créer un environnement en doublon :

1. Créez un environnement dans la deuxième région. Pour plus d’informations, consultez [créer un nouvel environnement Time Series Insights dans le portail Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Créez un deuxième groupe de consommateurs dédié pour votre source d’événements.
1. Connectez cette source d’événements au nouvel environnement. Assurez-vous que vous désignez le groupe de consommateurs dédié deuxième.
1. Passez en revue le Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) et [Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentation.

Si un événement se produit :

1. Si votre région principale est affectée par un sinistre, basculez les opérations sur l’environnement Time Series Insights de sauvegarde.
1. Utilisez votre deuxième région pour sauvegarder et récupérer toutes les données de télémétrie et de requête Time Series Insights.

> [!IMPORTANT]
> Si un basculement se produit :
> 
> * Un délai peut également se produire.
> * Un pic momentané du traitement des messages peut se produire, comme les opérations sont redirigées.
> 
> Pour plus d’informations, consultez [Réduction de la latence dans Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

