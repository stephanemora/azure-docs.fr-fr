---
title: Étendre (copier) les alertes Log Analytics au cloud Azure Government
description: Vue d’ensemble du processus de copie des alertes de Log Analytics dans le portail OMS vers Alertes Azure, avec les détails relatifs aux problèmes courants des clients.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: efd2fc9d164564126b7b641ef35bbb10c4834f49
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099326"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Étendre les alertes Log Analytics à Alertes Azure

> [!NOTE]
> Microsoft a déjà effectué la procédure décrite dans cet article pour les versions publiques d’Azure. Cela étant, elle continue de s'appliquer aux versions du gouvernement américains.  

Jusqu'à récemment, Azure Log Analytics incluait sa propre fonctionnalité d’alerte, pour vous indiquer de manière proactive les conditions en fonction des données Log Analytics. Vous gériez les règles d’alerte dans le portail Microsoft Operations Management Suite. La nouvelle expérience d’alertes intègre désormais les alertes entre les différents services dans Microsoft Azure. Elle est disponible sous forme d’**alertes** sous Azure Monitor dans le portail Azure, prend en charge les alertes des journaux d’activité, des métriques et des journaux d’activité sur Log Analytics ainsi qu’Azure Application Insights.

Les clients du cloud Azure Government qui utilisent le portail OMS peuvent [volontairement étendre leurs règles d’alerte à Azure](alerts-extend-tool.md) depuis le **1er février 2019**. À compter du **1er mars 2019**, Microsoft étendra automatiquement à Azure toutes les règles d’alerte existantes dans le portail OMS - Azure Government, sans temps d’arrêt ni interruption de vos tâches de supervision. Tout espace de travail créé le **1er mars 2019** ou après cette date dans le portail OMS du cloud Azure Government sera automatiquement étendu à Azure.

## <a name="benefits-of-extending-your-alerts"></a>Avantages de l’extension de vos alertes
La création et la gestion des alertes dans le portail Azure offrent plusieurs avantages :

- Contrairement au portail Operations Management Suite, où seules 250 alertes peuvent être créées et affichées, cette limitation ne s’applique pas aux alertes Azure.
- Les alertes Azure vous permettent de gérer, recenser et consulter tous vos types d’alertes. Auparavant, vous pouviez uniquement le faire pour les alertes Log Analytics.
- Vous pouvez restreindre l’accès des utilisateurs à la surveillance et aux alertes uniquement, à l’aide du [rôle Azure Monitor](../../azure-monitor/platform/roles-permissions-security.md).
- Dans Alertes Azure, vous pouvez utiliser des [groupes d’actions](../../azure-monitor/platform/action-groups.md). Cela vous permet d’avoir plusieurs actions pour chaque alerte. Vous pouvez envoyer des SMS ou un appel vocal, appeler un runbook Azure Automation ou un webhook, et configurer un connecteur IT Service Management (ITSM). 

## <a name="process-of-extending-your-alerts"></a>Processus d’extension de vos alertes
Le processus de déplacement des alertes de Log Analytics vers Alertes Azure n’implique pas une modification, de quelque nature que ce soit, de votre définition d’alerte, requête ou configuration. Le seul changement nécessaire est tel que, dans Azure, toutes les actions se font via un groupe d’actions. Si des groupes d’actions sont déjà associés à votre alerte, ils sont inclus lorsqu’ils sont étendus à Azure.

Lorsque vous planifiez des alertes dans un espace de travail Log Analytics afin de les étendre à Azure, elles continuent de fonctionner et n’affectent en aucun cas votre configuration. Lorsqu’elles sont planifiées, vos alertes peuvent ne pas être disponibles temporairement pour modification, mais vous pouvez entre-temps continuer à créer des alertes. Si vous tentez de modifier ou de créer des alertes à partir du portail Operations Management Suite, vous avez la possibilité de continuer à les créer à partir de votre espace de travail Log Analytics. Vous pouvez également choisir de les créer à partir d’Alertes Azure dans le portail Azure.

 ![Capture d’écran de l’option permettant de créer des alertes à partir de Log Analytics ou d’Alertes Azure](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> L’extension d’alertes de Log Analytics à Azure n’entraîne aucuns frais sur votre compte. L’utilisation d’Alertes Azure pour des alertes Log Analytics basées sur une requête n’est pas facturée dans les limites et les conditions stipulées dans la [stratégie de tarification d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Comment étendre vos alertes volontairement
Pour étendre vos alertes à Alertes Azure, vous pouvez utiliser un assistant disponible dans le portail Operations Management Suite, ou effectuer cette extension par programmation à l’aide d’une API. Pour plus d’informations, consultez [Extending alerts into Azure using Operations Management Suite portal and API](alerts-extend-tool.md) (Étendre les alertes à Azure à l’aide du portail Operations Management Suite et d’une API).

## <a name="experience-after-extending-your-alerts"></a>Expérience après l’extension de vos alertes
Une fois vos alertes étendues à Alertes Azure, elles continuent d’être disponibles pour la gestion dans le portail Operations Management Suite, exactement comme par le passé.

![Capture d’écran du portail Operations Management Suite, avec une liste d’alertes](media/alerts-extend/PostExtendList.png)

Lorsque vous tentez de modifier une alerte existante ou de créer une alerte dans le portail Operations Management Suite, vous êtes automatiquement redirigé vers Alertes Azure.  

> [!NOTE]
> Vérifiez que les autorisations affectées à des personnes qui ont besoin d’ajouter ou de modifier des alertes sont correctement attribuées dans Azure. Pour déterminer quelles autorisations vous devez accorder, consultez les [autorisations d’utilisation d’Azure Monitor et Alertes Azure](../../azure-monitor/platform/roles-permissions-security.md).  
> 

Vous pouvez continuer à créer des alertes à partir de l’[API Log Analytics](../../azure-monitor/platform/api-alerts.md) et du [modèle de ressource Log Analytics](../../azure-monitor/insights/solutions-resources-searches-alerts.md). Dans ce cas, vous devez inclure des groupes d’actions.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les outils de [lancement de l’extension des alertes de Log Analytics à Azure](alerts-extend-tool.md).
* En savoir plus sur l’[expérience Alertes Azure](../../azure-monitor/platform/alerts-overview.md).
* En savoir plus sur la création d’[alertes de journal dans Alertes Azure](alerts-unified-log.md).

