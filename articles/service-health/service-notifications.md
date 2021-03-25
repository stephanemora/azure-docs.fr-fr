---
title: Afficher les notifications Service Health dans le portail Azure
description: Afficher vos notifications Service Health dans le portail Azure. Les notifications Service Health sont publiées par l’infrastructure Azure dans le journal d’activité Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 9f9f3e7b10d9aa0014e4e00e7bfa72c9dc66e142
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588001"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Afficher les notifications Service Health dans le portail Azure

Les notifications Service Health sont publiées par l’infrastructure Azure dans le [journal d’activité Azure](../azure-monitor/essentials/platform-logs-overview.md).  Elles contiennent des informations sur les ressources de votre abonnement. Dans la mesure où le volume d’informations stockées dans le journal d’activité peut être important, il existe une interface utilisateur distincte permettant de faciliter l’affichage et la configuration d’alertes sur ces notifications. 

Selon leur classe, les notifications Service Health peuvent être fournies à titre d’information ou nécessiter une action de votre part.

Pour plus d’informations sur les différentes classes de notifications Service Health, consultez [Propriétés des notifications Service Health](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Afficher vos notifications Service Health dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Moniteur**.

    ![Capture d’écran du menu du portail Azure, avec l’option Surveiller sélectionnée](./media/service-notifications/home-monitor.png)

    Azure Monitor rassemble tous vos paramètres de surveillance et données dans une vue centralisée. Il ouvre d’abord la section **Journal d’activité** .

1. Sélectionnez **Alertes**.

    ![Capture d’écran du journal Suivi de l’activité, avec l’option Alertes sélectionnée](./media/service-notifications/service-health-summary.png)

1. Sélectionnez **+ Ajouter une alerte de journal d’activité** pour configurer une alerte qui vous permettra d’être informé des futures notifications de service. Pour plus d’informations, consultez [Créer des alertes de journal d’activité sur les notifications de service](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail les [alertes du journal d’activité](../azure-monitor/alerts/activity-log-alerts.md).
