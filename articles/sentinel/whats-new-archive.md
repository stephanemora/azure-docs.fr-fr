---
title: Archives pour les nouveautés d’Azure Sentinel
description: Description des nouveautés et modifications apportées à Azure Sentinel jusqu’à il y a six mois.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: 1e629b52b7904d2f671c9d472916ba0a2185a986
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104042"
---
# <a name="archive-for-whats-new-in-azure-sentinel"></a>Archives pour les nouveautés d’Azure Sentinel

La page principale des notes de publication [Nouveautés d’Azure Sentinel](whats-new.md) a trait aux mises à jour des six derniers mois, tandis que celle-ci a trait à des mises à jour plus anciennes.

Pour plus d’informations sur les fonctionnalités précédentes fournies, consultez nos [blogs Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Les fonctionnalités indiquées sont disponibles en préversion. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.


> [!TIP]
> Nos équipes de chasse des menaces Microsoft mettent à la disposition de la [communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) des requêtes, playbooks, workbooks et notebooks, notamment des [requêtes de chasse](https://github.com/Azure/Azure-Sentinel) spécifiques que vos équipes peuvent adapter et utiliser.
>
> Vous pouvez également contribuer ! Rejoignez-nous dans la [communauté GitHub des chasseurs de menaces Azure Sentinel](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="november-2020"></a>Novembre 2020

- [Superviser l’intégrité de vos playbooks dans Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Connecteur Microsoft 365 Defender (préversion publique)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Superviser l’intégrité de vos playbooks dans Azure Sentinel

Les playbooks Azure Sentinel sont basés sur des workflows intégrés à [Azure Logic Apps](../logic-apps/index.yml), service cloud qui vous permet de planifier, d’automatiser et d’orchestrer des tâches, des processus métier et des workflows. Les playbooks peuvent être appelés automatiquement lors de la création d’un incident ou lors du tri et de l’utilisation d’incidents. 

Pour fournir des insights sur l’intégrité, les performances et l’utilisation de vos playbooks, nous avons ajouté un [workbook](../azure-monitor/visualize/workbooks-overview.md) nommé **Supervision de l’intégrité des playbooks**. 

Utilisez le workbook **Supervision de l’intégrité des playbooks** pour superviser l’intégrité de vos playbooks ou rechercher des anomalies dans le nombre d’exécutions réussies ou ayant échoué. 

Le workbook **Supervision de l’intégrité des playbooks** est maintenant disponible dans la galerie des modèles Azure Sentinel :

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Exemple du workbook Supervision de l’intégrité des playbooks":::

Pour plus d'informations, consultez les pages suivantes :

- [Documentation Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Documentation Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Connecteur Microsoft 365 Defender (préversion publique)
 
Le connecteur Microsoft 365 Defender pour Azure Sentinel vous permet d’envoyer en streaming des journaux de chasse avancée (un type de données d’événement brutes) de Microsoft 365 Defender vers Azure Sentinel. 

Avec l’intégration de [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) dans le cadre de la sécurité [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection), vous pouvez désormais collecter vos événements de chasse avancée Microsoft Defender for Endpoint à l’aide du connecteur Microsoft 365 Defender et les envoyer en streaming directement dans de nouvelles tables dédiées de votre espace de travail Azure Sentinel. 

Les tables Azure Sentinel sont générées sur le même schéma que celui utilisé dans le portail Microsoft 365 Defender et vous fournissent un accès complet à l’ensemble des journaux de chasse avancée. 

Pour plus d’informations, consultez [Connecter des données de Microsoft 365 Defender à Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender était anciennement connu sous le nom de Protection Microsoft contre les menaces ou MTP. Microsoft Defender for Endpoint était précédemment appelé Microsoft Defender Advanced Threat Protection, ou MDATP.
> 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Intégrer Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenir une visibilité des alertes](quickstart-get-visibility.md)
