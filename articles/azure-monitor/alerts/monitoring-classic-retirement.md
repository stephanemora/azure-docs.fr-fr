---
title: Mise à jour de la gestion des alertes et de la supervision classiques dans Azure Monitor
description: Description de la mise hors service des services et des fonctionnalités de supervision classique, présentée précédemment dans le Portail Azure sous Alertes (classique).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: a862e54b5e1eddce5fcabf050eba1cfa1f103e30
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734755"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>La gestion des alertes et la supervision unifiées dans Azure Monitor remplace la gestion des alertes et la supervision classiques

Azure Monitor est un service unifié de une pile de surveillance unifiée qui prend en charge les métriques unifiées les alertes unifiées dans l’ensemble des ressources. Pour plus d’informations, consultez ce [billet de blog](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). La nouvelle plateforme de surveillance et de génération d’alertes Azure a été conçue pour être plus rapide, plus intelligente et extensible, permettant ainsi de suivre le rythme de l’expansion croissante du cloud computing dans le droit fil de la philosophie du cloud intelligent Microsoft.

Avec la nouvelle plateforme de surveillance et d’alerte Azure en place, les alertes classiques dans Azure Monitor sont hors service pour les utilisateurs du cloud public, même si elles resteront utilisées de manière limitée jusqu’au **31 mai 2021**. Les alertes classiques pour Azure Government Cloud et Azure China 21Vianet vont être mises hors service le **29 février 2024**.

 ![Alerte classique dans le portail Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Nous vous encourageons à l’utiliser et à recréer vos alertes sur la nouvelle plateforme.

> [!IMPORTANT]
> Les règles d'alerte classiques de journal d'activité ne seront ni déconseillées ni migrées. Toutes les règles d’alerte classiques créées sur le journal d’activité sont accessibles et utilisées en l'état dans le nouveau Azure Monitor - Alertes. Pour plus d'informations, consultez [Créer, afficher et gérer des alertes de journal d'activité avec Azure Monitor](./alerts-activity-log.md). De même, les alertes Service Health sont accessibles et peuvent être utilisées en l'état dans la nouvelle section Service Health. Pour plus d’informations, consultez [Alertes sur les notifications Service Health](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Métriques et alertes unifiées pour les ressources Azure

En mars 2018, nous avons publié la nouvelle génération d’alertes pour les ressources Azure. La plateforme de métriques et les alertes plus récentes sont plus rapides et offrent une plus grande granularité en utilisant des dimensions. Les dimensions vous permettent de scinder et de filtrer sur une combinaison de valeurs, une condition ou une opération spécifiques. Les alertes de métrique plus récentes utilisent des groupes d’actions autorisant davantage de notifications et d’actions d’automatisation. Des informations supplémentaires sont disponibles sur la [gestion des alertes de métriques à l’aide d’Azure Monitor](./alerts-metric.md).

Les métriques plus récentes pour les ressources Azure sont disponibles sous les formes suivantes :

- **Métriques standard de la plateforme Azure Monitor**, ce qui fournit des métriques préremplies populaires à partir de divers produits et services Azure. Pour plus d’informations, consultez cet article relatif aux [Métriques prises en charge sur Azure Monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) et aux [Alertes pour les métriques prises en charge sur Azure Monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Métriques personnalisées Azure Monitor**, ce qui fournit des métriques provenant de sources contrôlées par l’utilisateur, notamment l’agent de diagnostics Azure. Pour plus d’informations, consultez cet article relatif aux [métriques personnalisées dans Azure Monitor](../essentials/metrics-custom-overview.md). En utilisant des métriques personnalisées, vous pouvez également publier des métriques collectées par [l’agent Diagnostics Azure pour Windows](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) et [l’agent InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Mise hors service de la plateforme de supervision et de gestion des alertes classiques

Comme dit précédemment, la surveillance et les alertes classiques sont mis hors service pour les utilisateurs du cloud public. Cela inclut la fermeture des API liées, de l’interface du portail Azure et des services qu’il contient, même si ces ressources continueront d’être utilisées de façon limitée jusqu’au **31 mai 2021**. Les alertes classiques pour Azure Government Cloud et Azure China 21Vianet vont être mises hors service le **29 février 2024**.

Plus précisément, l’étendue de la mise hors service a trait aux métriques classiques actuellement disponibles dans la [section Alertes (classiques)](./alerts-classic.overview.md) du portail Azure, et accessibles en tant que ressources [microsoft. insights/alertrules](/rest/api/monitor/alertrules).

Cela implique :

- Le service de supervision et gestion des alertes classiques sera supprimé et ne sera plus disponible pour la création de nouvelles règles d’alerte.
- Les règles d’alerte qui existeront toujours dans Alertes (classiques) continueront de s’exécuter et de déclencher des notifications.
- La plupart des règles d’alerte classiques seront migrées. Le processus sera transparent et sans interruption de service, et les clients ne subiront aucune perte de couverture de la supervision.
- Les notifications déclenchées incluront une nouvelle structure de charges utiles. La cible devra être adaptée pour fonctionner avec la nouvelle structure.
- Certaines [règles d’alerte classiques ne pourront pas être migrées automatiquement](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) et le maintien de leur exécution nécessitera une action manuelle des utilisateurs.

> [!IMPORTANT]
> La plateforme Azure Monitor a déployé un [outil de migration volontaire](alerts-using-migration-tool.md) de ses règles d’alerte classiques vers la nouvelle plateforme. Les règles restantes seront migrées automatiquement une fois le service hors service. Les clients devront veiller à ce que la charge utile des règles d’alerte classiques qui ont recours à l’automatisation soit adaptée à la gestion de la nouvelle charge utile résultant des [métriques et alertes unifiées d’autres ressources Azure](#unified-metrics-and-alerts-for-azure-resources), après la migration des règles d’alerte classiques. Pour plus d’informations, consultez la section relative à la [préparation à la migration de règle d’alerte classique](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Tarifs des règles d’alerte migrées

Nous déployons un outil de migration pour vous aider à migrer vos [alertes classiques](./alerts-classic.overview.md) Azure Monitor vers la nouvelle expérience d’alertes. Les règles d’alerte migrées et les groupes d’actions migrés correspondants (e-mail, webhook ou LogicApp) restent gratuits. La fonctionnalité dont vous disposiez avec les alertes classiques, y compris la possibilité de modifier le seuil, le type d’agrégation et la granularité de l’agrégation, restera disponible gratuitement avec votre règle d’alerte migrée. Toutefois, si vous modifiez la règle d’alerte migrée pour utiliser l’une des nouvelles fonctionnalités de la plateforme d’alerte, les notifications ou les types d’action, les frais correspondants s’appliquent. Pour plus d’informations sur la tarification des règles d’alerte et des notifications, consultez la [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Voici des exemples de cas dans lesquels vous devrez payer pour votre règle d’alerte :

- Toute règle d’alerte (non migrée) créée au-delà des unités gratuites, sur la nouvelle plateforme Azure Monitor
- Toutes les données reçues et conservées au-delà des unités gratuites incluses par Azure Monitor
- Tous les tests web multiples exécutés par Application Insights
- Toutes les métriques personnalisées stockées au-delà des unités gratuites incluses dans Azure Monitor
- Toutes les règles d’alerte migrées qui sont modifiées pour utiliser de nouvelles fonctionnalités d’alertes de métrique comme la fréquence, les ressources/dimensions multiples, les [Seuils dynamiques](../alerts/alerts-dynamic-thresholds.md), le changement de ressource/signal, et ainsi de suite.
- Tous les groupes d’actions migrés qui sont modifiés afin d’utiliser des notifications plus récentes ou des types d’actions comme les SMS, les appels vocaux et/ou l’intégration ITSM.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir la [nouvelle instance Azure Monitor](../overview.md).
* Découvrir les nouvelles [Alertes Azure](./alerts-overview.md).