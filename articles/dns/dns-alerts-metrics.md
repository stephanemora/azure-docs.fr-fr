---
title: Métriques et alertes - Azure DNS
description: Avec ce parcours d’apprentissage, prenez en main les métriques et alertes Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: f2eaac432673682b075763c6ce9fe9426ed77a70
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108017557"
---
# <a name="azure-dns-metrics-and-alerts"></a>Métriques et alertes Azure DNS

Azure DNS est un service d’hébergement pour les domaines DNS qui offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. Cet article aborde les métriques et les alertes du service Azure DNS.

## <a name="azure-dns-metrics"></a>Métriques Azure DNS

Azure DNS fournit des métriques vous permettant de surveiller des aspects spécifiques de vos zones DNS. Grâce aux métriques d’Azure DNS, vous pouvez configurer des alertes en fonction des conditions qui sont remplies. Les métriques fournies utilisent le [service Azure Monitor](../azure-monitor/index.yml) pour afficher les données. Pour plus d’informations sur Metrics Explorer et sur ses graphiques, consultez [Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-charts.md). 
 
Azure DNS fournit les métriques suivantes à Azure Monitor pour vos zones DNS :

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Pour plus d’informations, consultez [Définition des métriques](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones).

>[!NOTE]
> À ce stade, ces métriques sont uniquement disponibles pour les zones DNS publiques hébergées dans Azure DNS. Si vous disposez de zones privées hébergées dans Azure DNS, ces métriques ne fourniront pas de données les concernant. De plus, les métriques et les alertes sont uniquement prises en charge dans le cloud public Azure. La prise en charge dans les clouds souverains est prévue. 

L’élément plus granulaire pour lequel vous pouvez voir des métriques est une zone DNS. Actuellement, vous ne pouvez pas voir les métriques des enregistrements de ressource individuels dans une zone.

### <a name="query-volume"></a>Volume de requêtes

La métrique *Volume de requêtes* montre le nombre de requêtes DNS reçues par Azure DNS pour votre zone DNS. L’unité de mesure est `Count` et l’agrégation correspond à la `Sum` de toutes les requêtes reçues au cours d’une période.

Pour afficher cette métrique, sélectionnez l’explorateur **Métriques** à partir de la page **Surveiller** du portail Azure. Limitez la portée à votre zone DNS, puis sélectionnez **Appliquer**. Dans la liste déroulante de *Métriques*, sélectionnez `Query Volume`, puis sélectionnez `Sum` dans la liste déroulante d’*Agrégation*.

![Volume de requêtes](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figure : Métriques QueryVolume (Volume de requêtes) dans Azure DNS*

### <a name="record-set-count"></a>Nombre de jeux d’enregistrements

La métrique *RecordSetCount* (Nombre de recordsets) indique le nombre de recordsets qui se trouvent dans Azure DNS pour votre zone DNS. Tous les recordsets définis dans votre zone sont comptabilisés. L’unité de mesure est `Count` et l’agrégation correspond au `Maximum` de tous les recordsets.

Pour afficher cette métrique, sélectionnez l’explorateur **Métriques** sous l’onglet **Surveiller** du portail Azure. Limitez la portée à votre zone DNS, puis sélectionnez **Appliquer**. Dans la liste déroulante de *Métriques*, sélectionnez `Query Volume`, puis sélectionnez `Sum` dans la liste déroulante d’*Agrégation*.

Sélectionnez votre zone DNS dans la liste déroulante **Ressource**, sélectionnez la métrique **RecordSetCount** (Nombre de recordsets), puis sélectionnez **Max** pour **Agrégation**. 

![Nombre de jeux d’enregistrements](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figure : Métriques RecordSetCount (Nombre de recordsets) dans Azure DNS*

### <a name="record-set-capacity-utilization"></a>Utilisation de la capacité du jeu d’enregistrements

La métrique *Utilisation de la capacité du jeu d’enregistrements* indique le pourcentage d’utilisation de la capacité de votre recordset pour une zone DNS. Chaque zone Azure DNS a une limite de recordsets qui définit le nombre maximal de recordsets autorisés pour la zone. Pour plus d’informations, consultez la section [Limites DNS](dns-zones-records.md#limits). L’unité de mesure est un `Percentage` et le type d’agrégation est `Maximum`.

Par exemple, si 500 recordsets sont configurés dans votre zone DNS et que la zone a une limite par défaut de 5000 recordsets, la métrique RecordSetCapacityUtilization affiche la valeur de 10 %, obtenue en divisant 500 par 5000. 

Pour afficher cette métrique, sélectionnez l’explorateur **Métriques** sous l’onglet **Surveiller** du portail Azure. Limitez la portée à votre zone DNS, puis sélectionnez **Appliquer**. Dans la liste déroulante *Métriques*, sélectionnez `Record Set Capacity Utilization`, puis sélectionnez `Sum` dans la liste déroulante *Agrégation*. 

![Capture d’écran montrant un exemple d’affichage des métriques.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figure : Métriques RecordSetCapacityUtilization (Utilisation de la capacité du recordset) dans Azure DNS*

## <a name="alerts-in-azure-dns"></a>Alertes Azure DNS

Azure Monitor dispose d’alertes que vous pouvez configurer pour chaque valeur métrique disponible. Pour plus d’informations, consultez [Alertes Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

1. Pour configurer des alertes pour les zones Azure DNS, sélectionnez **Alertes** dans la page *Surveiller* du portail Azure. Ensuite, sélectionnez **+ Nouvelle règle d’alerte**.

    :::image type="content" source="./media/dns-alerts-metrics/alert-metrics.png" alt-text="Capture d’écran du bouton Alerte sur la page Monitor.":::


1. Cliquez sur le lien **Sélectionner une ressource** dans la section Étendue pour ouvrir la page *Sélectionner une ressource*. Filtrez par **zones DNS**, puis sélectionnez la zone Azure DNS de votre choix comme ressource cible. Sélectionnez **Terminé** une fois que vous avez choisi la zone.

    :::image type="content" source="./media/dns-alerts-metrics/select-resource.png" alt-text="Capture d’écran de la page Sélectionner une ressource dans la configuration des alertes.":::

1. Ensuite, cliquez sur le lien **Ajouter une condition** dans la section Conditions pour ouvrir la page *Sélectionner un signal*. Sélectionnez l’un des trois types de signal *Métrique* pour lequel vous souhaitez configurer l’alerte.

    :::image type="content" source="./media/dns-alerts-metrics/select-signal.png" alt-text="Capture d’écran des métriques disponibles dans la page Sélectionner un signal.":::

1. Dans la page *Configurer la logique du signal*, configurez le seuil et la fréquence de l’évaluation pour la métrique sélectionnée.

    :::image type="content" source="./media/dns-alerts-metrics/configure-signal-logic.png" alt-text="Capture d’écran de la page Configurer la logique du signal.":::

1. Pour envoyer une notification ou appeler une action déclenchée par l’alerte, cliquez sur **Ajouter des groupes d’actions**. Dans la page *Ajouter des groupes d’actions*, sélectionnez **+ Créer un groupe d’actions**. Pour plus d’informations, consultez [Groupe d’actions](../azure-monitor/alerts/action-groups.md).

1. Entrez un *nom de règle d’alerte*, puis sélectionnez **Créer une règle d’alerte** pour enregistrer votre configuration.

    :::image type="content" source="./media/dns-alerts-metrics/create-alert-rule.png" alt-text="Capture d’écran de la page Créer une règle d’alerte.":::

Pour plus d’informations sur la configuration des alertes dans Azure Monitor, consultez [Créer, afficher et gérer des alertes à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-metric.md). 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure DNS](dns-overview.md)
