---
title: Présentation du journal d’activité Azure
description: Découvrez le journal d’activités Azure et comment vous pouvez l’utiliser pour comprendre les événements qui se produisent dans votre abonnement Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fa1737a8627fe9561a2a84e7f0ef69aefb6deb14
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170616"
---
# <a name="overview-of-azure-activity-log"></a>Présentation du journal d’activité Azure

Le **journal d’activité Azure** apporte des insights sur les événements liés aux abonnements qui se sont produits dans Azure. Cela inclut une plage de données, à partir de données opérationnelles d’Azure Resource Manager pour les mises à jour des événements de l’état d’intégrité du service. Le journal d’activité était précédemment appelé _journal d’audit_ ou _journal des opérations_, car la catégorie Administratif regroupe les événements de plan de contrôle pour vos abonnements. 

Avec le journal d’activité, répondez aux questions _quoi_, _qui_ et _quand_ concernant toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur les ressources dans votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. 

Le journal d’activité n’inclut pas d’opérations de lecture (GET) ni d’opérations sur les ressources basées sur le modèle Classic/RDFE.

## <a name="comparison-to-diagnostic-logs"></a>Comparaison avec les journaux de diagnostic
Il y a un seul journal d’activité par abonnement Azure. Il fournit des données sur les opérations effectuées sur une ressource à partir de l’extérieur (le « plan de contrôle »). Les [journaux de diagnostic](diagnostic-logs-overview.md), qui sont générés par une ressource, contiennent des informations sur le fonctionnement de cette ressource (le « plan de données »). Vous devez activer les paramètres de diagnostic pour chaque ressource.

![Comparaison des journaux d’activité avec les journaux de diagnostic](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Le journal d’activité Azure est principalement utilisé pour les activités qui se produisent dans Azure Resource Manager. Il ne suit pas les ressources à l’aide du modèle Classic/RDFE. Certains types de ressources Classic ont un fournisseur de ressources proxy dans Azure Resource Manager (par exemple, Microsoft.ClassicCompute). Si vous interagissez avec un type de ressource Classic par le biais d’Azure Resource Manager à l’aide de ces fournisseurs de ressources proxy, les opérations s’affichent dans le journal d’activité. Si vous interagissez avec un type de ressource Classic en dehors de proxys Azure Resource Manager, vos actions sont uniquement enregistrées dans le journal des opérations. Le journal des opérations est accessible dans une section distincte du portail.

## <a name="activity-log-retention"></a>Conservation du journal d’activité
Une fois créées, les entrées du journal d’activité ne sont pas modifiées ou supprimées par le système. En outre, vous ne pouvez pas les modifier dans l’interface ou par programmation. Les événements du journal d’activité sont conservés pendant 90 jours. Pour stocker ces données plus longtemps, [collectez-les dans Azure Monitor](activity-log-collect.md) ou [exportez-les vers le stockage ou vers Event Hubs](activity-log-export.md).

## <a name="view-the-activity-log"></a>Afficher le journal d’activité
Affichez le journal d’activité de toutes les ressources à partir du menu **Moniteur** dans le portail Azure. Affichez le journal d’activité d’une ressource particulière à partir de l’option **Journal d’activité** disponible dans le menu de cette ressource. Vous pouvez également récupérer les enregistrements du journal d’activité avec PowerShell, l’interface CLI ou l’API REST.  Consultez [Afficher et récupérer les événements du journal d’activité Azure](activity-log-view.md).

![Afficher le journal d’activité](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Collecter le journal d’activité dans Azure Monitor
Collectez le journal d’activité dans un espace de travail Log Analytics dans Azure Monitor si vous souhaitez l’analyser avec d’autres données de supervision et conserver les données pendant plus de 90 jours. Consultez [Collecter et analyser les journaux d’activité Azure dans l’espace de travail Log Analytics dans Azure Monitor](activity-log-collect.md).

![Interroger le journal d’activité](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exporter le journal d’activité
Exportez le journal d’activité vers le stockage Azure pour archivage, ou envoyez-le à un hub d’événements pour ingestion par un service tiers ou une solution d’analytique personnalisée. Consultez [Exporter le journal d’activité Azure](activity-log-export.md). Vous pouvez également analyser les événements du journal d’activité dans Power BI à l’aide du [**pack de contenu Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Alerte dans le journal d’activité
Vous pouvez créer une alerte quand des événements particuliers sont consignés dans le journal d’activité avec une [alerte de journal d’activité](activity-log-alerts.md). Vous pouvez aussi créer une alerte à l’aide d’une [requête de journal](alerts-log-query.md) quand le journal d’activité est connecté à un espace de travail Log Analytics. Sachez toutefois que les alertes de requête de journal entraînent un coût supplémentaire. Les alertes de journal d’activité n’engendrent aucun coût.

## <a name="categories-in-the-activity-log"></a>Catégories dans le journal d’activité
Chaque événement dans le journal d’activité est associé à l’une des catégories décrites dans le tableau suivant. Pour plus d’informations sur le schéma de ces catégories, consultez [Schéma d’événements du journal d’activité](activity-log-schema.md). 

| Category | Description |
|:---|:---|
| Administratif | Contient l’enregistrement de toutes les opérations de création, mise à jour, suppression et action effectuées par le biais de Resource Manager. Les événements _créer une machine virtuelle_ et _supprimer un groupe de sécurité réseau_ sont deux exemples d’événements Administratif.<br><br>Chaque action effectuée par un utilisateur ou une application avec Resource Manager est modélisée comme une opération effectuée sur un type de ressource spécifique. Si le type d’opération est _Écrire_, _Supprimer_ ou _Action_, les enregistrements de début et de réussite ou d’échec de cette opération sont enregistrés dans la catégorie Administratif. Les événements de la catégorie Administratif incluent également les changements apportés au contrôle d’accès en fonction du rôle dans un abonnement. |
| Service Health | Contient l’enregistrement de tous les incidents d’intégrité du service qui se sont produits dans Azure. L’événement _SQL Azure dans la région USA Est rencontre des temps d’arrêt_ est un exemple d’événement d’intégrité du service. <br><br>Les événements Service Health se présentent sous six types : _Action requise_, _Récupération assistée_, _Incident_, _Maintenance_, _Information_ ou _Sécurité_. Ces événements sont créés uniquement si une ressource dans l’abonnement est impactée par l’événement.
| Intégrité des ressources | Contient l’enregistrement de tous les événements d’intégrité de ressource survenus dans vos ressources Azure. L’événement _L’état d’intégrité de la machine virtuelle est passé à Indisponible_ est un exemple d’événement d’intégrité de ressource.<br><br>Les événements d’intégrité de ressource peuvent représenter l’un de ces quatre états d’intégrité : _Disponible_, _Indisponible_, _Détérioré_ et _Inconnu_. En outre, les événements d’intégrité de ressource peuvent être classés dans la catégorie _Lancé par la plateforme_ ou _Lancé par l’utilisateur_. |
| Alerte | Contient l’enregistrement des activations d’alertes Azure. L’événement _Le % processeur sur ma machine virtuelle a dépassé 80 au cours des 5 dernières minutes_.|
| Autoscale | Contient l’enregistrement de tous les événements liés au fonctionnement du moteur de mise à l’échelle automatique selon les paramètres d’échelle automatique définis dans votre abonnement. L’événement _Échec de l’action de scale-up de la mise à l’échelle automatique_ est un exemple d’événement de mise à l’échelle automatique. |
| Recommandation | Contient les événements de recommandation d’Azure Advisor. |
| Sécurité | Contient l’enregistrement de toutes les alertes générées par Azure Security Center. L’événement _Fichier à extension double suspect exécuté_ est un exemple d’événement de sécurité. |
| Stratégie | Contient les enregistrements de toutes les opérations d’action à effet effectuées par Azure Policy. Les événements _Audit_ et _Refuser_ sont des exemples d’événements de stratégie. Chaque action effectuée par Policy est modélisée en tant qu’opération sur une ressource. |


## <a name="next-steps"></a>Étapes suivantes

* [Créer un profil de journal pour exporter le journal d’activité Azure](activity-log-export.md)
* [Stream the Azure Activity Log to Event Hubs (Diffuser en continu le journal d’activités Azure vers Event Hubs)](activity-logs-stream-event-hubs.md)
* [Archiver le journal d’activité Azure dans le stockage](archive-activity-log.md)

