---
title: Comment afficher et configurer des alertes pour la maintenance des circuits Azure ExpressRoute
description: Découvrez comment configurer des alertes personnalisées pour la maintenance des circuits ExpressRoute à l’aide de la page Service Health du portail Azure.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 05/10/2021
ms.author: mialdrid
ms.openlocfilehash: 93335c6f163e016c619fd4803a595ffcc3418a9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524569"
---
# <a name="how-to-view-and-configure-alerts-for-azure-expressroute-circuit-maintenance"></a>Comment afficher et configurer des alertes pour la maintenance des circuits Azure ExpressRoute

ExpressRoute utilise Azure Service Health pour vous informer de la maintenance planifiée et à venir des circuits ExpressRoute. Grâce à Service Health, vous pouvez afficher la maintenance planifiée et passée dans le portail Azure et configurer les alertes et les notifications qui répondent le mieux à vos besoins. Pour en savoir plus sur Azure Service Health, reportez-vous à l’article [Qu’est-ce qu’Azure Service Health ?](../service-health/overview.md)

> [!NOTE]
> * Au cours d’une activité de maintenance ou en cas d’événements non planifiés ayant un impact sur l’une de ces connexions, Microsoft préfère utiliser l’ajout d’un préfixe au chemin AS pour purger le trafic vers la connexion saine. Vous devez vérifier que le trafic peut être routé sur le chemin sain quand l’ajout au chemin est configuré par Microsoft et que les publications de routes nécessaires sont configurées de manière appropriée pour éviter toute interruption de service. 
> * Mettre fin à des connexions BGP ExpressRoute sur les appareils avec état peut entraîner des problèmes de basculement pendant les maintenances planifiées ou non planifiées par Microsoft ou votre fournisseur ExpressRoute. Vous devez tester votre configuration pour vérifier que votre trafic basculera correctement et, quand cela est possible, arrêter les sessions BGP sur les appareils sans état.
>

## <a name="view-planned-maintenance"></a>Afficher une maintenance planifiée

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à la page Service Health. 

    :::image type="content" source="./media/maintenance-alerts/search-service-health.png" alt-text="Capture d’écran illustrant la recherche de la page Service Health."::: 

1. Sélectionnez **Maintenance planifiée** sous *Événements actifs* sur le côté gauche de la page. Sur cette page, vous pouvez afficher des événements de maintenance individuels en filtrant sur un abonnement cible, une région Azure et un service Azure.

    :::image type="content" source="./media/maintenance-alerts/view-maintenance.png" alt-text="Capture d’écran de la page de maintenance planifiée dans Service Health." lightbox="./media/maintenance-alerts/view-maintenance-expanded.png"::: 

1. Sélectionnez **ExpressRoute** dans la liste déroulante *Services* pour afficher uniquement la maintenance relative à ExpressRoute. Sélectionnez ensuite un problème dans la liste pour afficher le résumé de l’événement. Sélectionnez l’onglet **Problèmes de mise à jour** pour plus d’informations sur une maintenance en cours.

    :::image type="content" source="./media/maintenance-alerts/summary.png" alt-text="Capture d’écran du résumé de la maintenance d’ExpressRoute." lightbox="./media/maintenance-alerts/summary-expanded.png":::

## <a name="view-past-maintenance"></a>Afficher une maintenance passée

1. Pour afficher les événements de maintenance passés, sélectionnez **Historique de l’intégrité** sous la section *Historique* sur le côté gauche de la page. 

    :::image type="content" source="./media/maintenance-alerts/health-history.png" alt-text="Capture d’écran de la sélection de la page Historique d’intégrité dans Service Health." lightbox="./media/maintenance-alerts/health-history-expanded.png"::: 

1. Sur cette page, vous pouvez afficher des événements de maintenance individuels en filtrant sur un abonnement cible et une région Azure. Pour restreindre davantage la portée des événements de l’historique d’intégrité, vous pouvez sélectionner le type d’événement d’intégrité et définir un intervalle de temps dans le passé. Pour filtrer la maintenance planifiée des circuits ExpressRoute, définissez le type d’événement d’intégrité sur **Maintenance planifiée**.

    :::image type="content" source="./media/maintenance-alerts/past-maintenance.png" alt-text="Capture d’écran de la maintenance passée sur la page Historique d’intégrité." lightbox="./media/maintenance-alerts/past-maintenance-expanded.png"::: 

## <a name="create-alerts-and-notifications-for-maintenance-events"></a>Créer des alertes et des notifications pour les événements de maintenance

1. Azure Service Health prend en charge les alertes personnalisées pour les événements de maintenance. Afin de configurer une alerte pour la maintenance des circuits ExpressRoute, accédez à **Alertes d’intégrité** sous la section *Alertes* sur le côté gauche de la page. Vous y verrez un tableau des alertes précédemment configurées.

1.  Pour créer une nouvelle alerte, sélectionnez **Ajouter une alerte d’intégrité de service** en haut de la page.

    :::image type="content" source="./media/maintenance-alerts/health-alerts.png" alt-text="Capture d’écran de la sélection de la page Alertes d’intégrité dans Service Health." lightbox="./media/maintenance-alerts/health-alerts-expanded.png"::: 

1. Sélectionnez ou entrez les informations suivantes pour créer une règle d’alerte.

    | Category | Paramètres | Valeur | 
    | --- | -------- | ----- |
    | **Condition** | Abonnement | Sélectionnez l’abonnement cible. |
    |               | Service(s) | *ExpressRoute \ Circuits ExpressRoute* |
    |               | Région(s) | Sélectionnez une région ou gardez *Mondial* pour les événements d’intégrité de toutes les régions.
    |               | Type d'événement | sélectionnez *Maintenance planifiée*. |
    | **Actions** | Nom du groupe d’actions | Le *groupe d’actions* détermine le type de notification et définit le public auquel la notification est envoyée. Pour obtenir de l’aide pour la création et la gestion du groupe d’actions, consultez [Créer et gérer des groupes d’actions](../azure-monitor/alerts/action-groups.md) dans le portail Azure. |
    | **Détails des règles d’alerte** | Nom de la règle d’alerte | Entrez un *nom* pour identifier votre règle d’alerte. |
    |                        | Description | Fournissez une description de ce que fait cette règle d’alerte. | 
    |                        | Enregistrer la règle d’alerte dans le groupe de ressources | Sélectionnez un *groupe de ressources* dans lequel créer cette règle d’alerte. |
    |                        | Activer la règle d’alerte à la création | Cochez cette case pour activer cette règle d’alerte une fois créée. |

1. Sélectionnez **Créer une règle d’alerte** pour enregistrer votre configuration.

    :::image type="content" source="./media/maintenance-alerts/create-alert-rule.png" alt-text="Capture d’écran de la page Créer une règle d’alerte."::: 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure ExpressRoute](expressroute-introduction.md), [Network Insights](../azure-monitor/insights/network-insights-overview.md) et [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Personnalisez vos métriques](expressroute-monitoring-metrics-alerts.md) et créez un [moniteur de connexion](../network-watcher/connection-monitor-overview.md)

