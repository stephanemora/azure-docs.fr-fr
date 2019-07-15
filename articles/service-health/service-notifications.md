---
title: Afficher les notifications Service Health dans le portail Azure
description: Les notifications Service Health vous permettent de voir les messages relatifs à l’intégrité du service publiés par Microsoft Azure.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538141"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Afficher les notifications Service Health dans le portail Azure

Les notifications Service Health sont publiées par l’infrastructure Azure dans le [journal d’activité Azure](../azure-monitor/platform/activity-logs-overview.md).  Elles contiennent des informations sur les ressources de votre abonnement. Dans la mesure où le volume d’informations stockées dans le journal d’activité peut être important, il existe une interface utilisateur distincte permettant de faciliter l’affichage et la configuration d’alertes sur ces notifications. 

Selon leur classe, les notifications Service Health peuvent être fournies à titre d’information ou nécessiter une action de votre part.

Pour plus d’informations sur les différentes classes de notifications Service Health, consultez [Propriétés des notifications Service Health](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Afficher vos notifications Service Health dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Moniteur**.

    ![Capture d’écran du menu du portail Azure, avec l’option Surveiller sélectionnée](./media/service-notifications/home-monitor.png)

    Azure Monitor rassemble tous vos paramètres de surveillance et données dans une vue centralisée. Il ouvre d’abord la section **Journal d’activité** .

1. Sélectionnez **Alertes**.

    ![Capture d’écran du journal Suivi de l’activité, avec l’option Alertes sélectionnée](./media/service-notifications/service-health-summary.png)

1. Sélectionnez **+ Ajouter une alerte de journal d’activité** pour configurer une alerte qui vous permettra d’être informé des futures notifications de service. Pour plus d’informations, consultez [Créer des alertes de journal d’activité sur les notifications de service](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail les [alertes du journal d’activité](../azure-monitor/platform/activity-log-alerts.md).
