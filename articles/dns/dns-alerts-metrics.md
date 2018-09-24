---
title: Métriques et alertes Azure DNS | Microsoft Docs
description: Découvrez les métriques et les alertes Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: victorh
ms.openlocfilehash: de29c24556522abeaff8d942edc027c7444c3ed3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965014"
---
# <a name="azure-dns-metrics-and-alerts"></a>Métriques et alertes Azure DNS
Azure DNS est un service d’hébergement pour les domaines DNS qui offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. Cet article aborde les métriques et les alertes du service Azure DNS.

## <a name="azure-dns-metrics"></a>Métriques Azure DNS

Azure DNS fournit des métriques aux clients pour qu’ils puissent surveiller certains aspects de leurs domaines DNS hébergés dans le service. De plus, avec les métriques Azure DNS, vous pouvez configurer et recevoir des alertes en fonction des conditions qui vous intéressent. Les métriques sont fournies via le [service Azure Monitor](../azure-monitor/index.yml). Azure DNS fournit les métriques suivantes via Azure Monitor pour vos zones DNS :

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Vous pouvez également voir la [définition de ces métriques](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkdnszones) dans la page de documentation d’Azure Monitor.
>[!NOTE]
> À ce stade, ces métriques sont uniquement disponibles pour les zones DNS publiques hébergées dans Azure DNS. Si vous disposez de zones privées hébergées dans Azure DNS, ces métriques ne fourniront pas de données les concernant. De plus, les métriques et les alertes sont uniquement prises en charge dans le cloud public Azure. La prise en charge dans les clouds souverains est prévue. 

Le niveau de dimension granulaire de ces métriques est Zone DNS.

### <a name="query-volume"></a>Volume de requêtes

Dans Azure DNS, la métrique *QueryVolume* (Volume de requêtes) indique le volume de requêtes DNS (trafic de requêtes) qui est reçu par Azure DNS concernant votre zone DNS. L’unité de mesure correspond à la métrique Nombre et l’agrégation correspond au total de toutes les requêtes reçues au cours d’une période. Pour afficher cette métrique, sélectionnez l’explorateur Métriques (préversion) sous l’onglet Surveiller du portail Azure. Sélectionnez votre zone DNS dans la liste déroulante Ressource, sélectionnez la métrique QueryVolume (Volume de requêtes), puis sélectionnez Somme comme type d’agrégation. La capture d'écran ci-dessous montre un exemple.  Pour plus d’informations sur Metrics Explorer et sur ses graphiques, consultez [Azure Monitor Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md).

![Volume de requêtes](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figure : Métriques QueryVolume (Volume de requêtes) dans Azure DNS*

### <a name="record-set-count"></a>Nombre de jeux d’enregistrements
La métrique *RecordSetCount* (Nombre de recordsets) indique le nombre de recordsets qui se trouvent dans Azure DNS pour votre zone DNS. Tous les recordsets définis dans votre zone sont comptabilisés. L’unité de mesure est Nombre et l’agrégation correspond au nombre total de recordsets. Pour afficher cette métrique, sélectionnez l’explorateur **Métriques (préversion)** sous l’onglet **Surveiller** du portail Azure. Sélectionnez votre zone DNS dans la liste déroulante **Ressource**, sélectionnez la métrique **RecordSetCount** (Nombre de recordsets), puis sélectionnez **Max** pour **Agrégation**. Pour plus d’informations sur Metrics Explorer et sur ses graphiques, consultez [Azure Monitor Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Nombre de jeux d’enregistrements](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figure : Métriques RecordSetCount (Nombre de recordsets) dans Azure DNS*


### <a name="record-set-capacity-utilization"></a>Utilisation de la capacité du jeu d’enregistrements
Dans Azure DNS, la métrique *RecordSetCapacityUtilization* (Utilisation de la capacité du recordset) indique le pourcentage d’utilisation de la capacité de votre recordset pour une zone DNS. Dans Azure DNS, chaque zone DNS est soumise à un nombre maximal de recordsets (voir [Limites DNS](dns-zones-records.md#limits)). Cette métrique vous permet donc de savoir si vous êtes proche de la limite autorisée. Par exemple, si vous avez configuré 500 recordsets pour votre zone DNS, et si la zone est limitée à 5 000 recordsets (limite par défaut), la métrique RecordSetCapacityUtilization (Utilisation de la capacité du recordset) affichera une valeur de 10 % (obtenue en divisant 5 000 par 500). L’unité de mesure est le **pourcentage** et **l’agrégation** est **maximale**. Pour afficher cette métrique, sélectionnez l’explorateur Métriques (préversion) sous l’onglet Surveiller du portail Azure. Sélectionnez votre zone DNS dans la liste déroulante Ressource, sélectionnez la métrique RecordSetCapacityUtilization (Utilisation de la capacité du recordset), puis sélectionnez Max pour l’agrégation. La capture d'écran ci-dessous montre un exemple. Pour plus d’informations sur Metrics Explorer et sur ses graphiques, consultez [Azure Monitor Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Nombre de jeux d’enregistrements](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figure : Métriques RecordSetCapacityUtilization (Utilisation de la capacité du recordset) dans Azure DNS*

## <a name="alerts-in-azure-dns"></a>Alertes Azure DNS
Azure Monitor permet d’être alerté lorsque des valeurs métriques sont disponibles. Les métriques DNS sont disponibles dans la nouvelle configuration des alertes. Comme décrit en détail dans la [documentation sur les alertes Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), vous pouvez sélectionner la zone DNS en tant que ressource, choisir le type de signalement des métriques et configurer la logique d’alerte et d’autres paramètres tels que la **Période** et la **Fréquence**. Vous pouvez définir un [Groupe d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) à effectuer lorsqu’une condition d’alerte est remplie. Pour plus d’informations sur la configuration des alertes dans Azure Monitor, consultez [Créer, afficher et gérer des alertes à l’aide d’Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Azure DNS](dns-overview.md)
