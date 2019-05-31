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
ms.openlocfilehash: 34857108cf7f0580c380ffbd4bbcedb5cd5a807a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245020"
---
# <a name="overview-of-azure-activity-log"></a>Vue d’ensemble du journal d’activité Azure

Le **journal d’activité Azure** fournit des informations sur les événements de niveau d’abonnement qui se sont produites dans Azure. Cela inclut une plage de données, à partir de données opérationnelles d’Azure Resource Manager pour les mises à jour des événements de l’état d’intégrité du service. Le journal d’activité était précédemment appelé _journaux d’Audit_ ou _journaux des opérations_, étant donné que la catégorie Administrative indique les événements de plan de contrôle pour vos abonnements. 

Utiliser le journal d’activité, pour déterminer le _que_, _qui_, et _lorsque_ pour toutes les opérations (PUT, POST, DELETE) effectuées sur les ressources dans votre abonnement d’écriture. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. 

Le journal d’activité n’inclut pas les opérations de lecture (GET) ou des opérations pour les ressources qui utilisent le modèle Classic/RDFE.

## <a name="comparison-to-diagnostic-logs"></a>Comparaison avec les journaux de Diagnostic
Il existe un journal d’activité unique pour chaque abonnement Azure. Fournit des données sur les opérations sur une ressource à partir de l’extérieur (le « plan de contrôle »). [Journaux de diagnostic](diagnostic-logs-overview.md) sont émis par une ressource et fournissent des informations sur le fonctionnement de cette ressource (le « plan de données »). Vous devez activer les paramètres de diagnostic pour chaque ressource.

![Journaux d’activité par rapport aux journaux de Diagnostic](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Le journal d’activité Azure est principalement utilisé pour les activités qui se produisent dans Azure Resource Manager. Il ne suit pas ugresources à l’aide du modèle Classic/RDFE. Certains types de ressources Classic ont un fournisseur de ressources proxy dans Azure Resource Manager (par exemple, Microsoft.ClassicCompute). Si vous interagissez avec un type de ressource Classic par le biais d’Azure Resource Manager à l’aide de ces fournisseurs de ressources proxy, les opérations s’affichent dans le journal d’activité. Si vous interagissez avec un type de ressource Classic en dehors de proxys Azure Resource Manager, vos actions sont uniquement enregistrées dans le journal des opérations. Le journal des opérations est accessible dans une section distincte du portail.

## <a name="activity-log-retention"></a>Rétention de journal d’activité
Événements de journal d’activité sont stockées pendant 90 jours. Pour stocker ces données pour des périodes plus longues, [collecter dans Azure Monitor](activity-log-collect.md) ou [exportez-le vers le stockage ou Event Hubs](activity-log-export.md).

## <a name="view-the-activity-log"></a>Afficher le journal d’activité
Afficher le journal d’activité pour toutes les ressources à partir de la **moniteur** menu dans le portail Azure. Afficher le journal d’activité pour une ressource particulière à partir de la **journal d’activité** option dans le menu d’une ressource. Vous pouvez également récupérer les enregistrements de journal d’activité avec PowerShell, CLI ou l’API REST.  Consultez [vue et récupérer Azure activité journaliser les événements](activity-log-view.md).

![Afficher le journal d’activité](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Collecter le journal d’activité dans Azure Monitor
Collecter le journal d’activité dans un espace de travail Analytique de journal dans Azure Monitor pour analyser avec d’autres données d’analyse et de rétention des données pendant plus de 90 jours. Consultez [collecter et analyser les journaux d’activité Azure dans l’espace de travail Analytique de journal dans Azure Monitor](activity-log-collect.md).

![Journal d’activité de requête](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exporter le journal d’activité
Exporter le journal d’activité dans le stockage Azure pour l’archivage ou un flux à un concentrateur d’événements pour l’ingestion par un service tiers ou d’une solution d’analytique personnalisée. Consultez [exporter le journal d’activité Azure](activity-log-export.md). Vous pouvez également analyser les événements du journal d’activité à l’aide de Power BI le [ **pack de contenu Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Alerte de journal d’activité
Vous pouvez créer une alerte lorsque des événements particuliers sont créées dans le journal d’activité avec un [alerte de journal d’activité](activity-log-alerts.md). Vous pouvez également créer une alerte à l’aide un [requête de journal](alerts-log-query.md) lorsque votre journal d’activité est connecté à un espace de travail Analytique de journal, mais il existe un coût s’interroger les alertes. Il n’existe aucun coût pour les alertes de journal d’activité.

## <a name="categories-in-the-activity-log"></a>Catégories dans le journal d’activité
Chaque événement dans le journal d’activité a une catégorie particulière qui sont décrites dans le tableau suivant. Pour plus d’informations sur le schéma de ces catégories, consultez [Schéma d’événements du journal d’activité](activity-log-schema.md). 

| Catégorie | Description |
|:---|:---|
| Administratif | Contient l’enregistrement de tous les créer, les opérations de mise à jour, suppression et action effectuées via le Gestionnaire de ressources. Exemples d’événements d’administration _créer la machine virtuelle_ et _supprimer le groupe de sécurité réseau_.<br><br>Chaque action effectuée par un utilisateur ou d’une application à l’aide du Gestionnaire de ressources est modélisée comme une opération sur un type de ressource particulier. Si le type d’opération est _écrire_, _supprimer_, ou _Action_, les enregistrements de début et de réussite ou Échec de cette opération est enregistrée dans la catégorie Administrative. Les événements d’administration incluent également les modifications apportées à un contrôle d’accès en fonction du rôle dans un abonnement. |
| Service Health | Contient l’enregistrement de tout incident de contrôle d’intégrité de service qui se sont produites dans Azure. Un exemple d’un événement de l’intégrité du Service _SQL Azure dans l’est des États-Unis est confrontée à des indisponibilités_. <br><br>Événements d’intégrité du service se présentent sous cinq variétés : _Action requise_, _récupération assistée_, _Incident_, _Maintenance_, _informations_, ou  _Sécurité_. Ces événements sont créés uniquement si vous avez une ressource dans l’abonnement est affectée par l’événement.
| Intégrité des ressources | Contient l’enregistrement des ressources des événements d’état qui se sont produites à vos ressources Azure. Est un exemple d’un événement d’intégrité de ressource _état d’intégrité de Machine virtuelle modifié à indisponible_.<br><br>Événements d’intégrité de ressource peuvent représenter un des quatre états d’intégrité : _Disponible_, _indisponible_, _détérioré_, et _inconnu_. En outre, les événements d’intégrité de ressource peuvent être classés comme étant _la plateforme a lancé_ ou _initiée par l’utilisateur_. |
| Alerte | Contient l’enregistrement d’activations pour les alertes Azure. Est un exemple d’un événement d’alerte _% du processeur sur myVM a été supérieur à 80 pour les 5 dernières minutes_.|
| Autoscale | Contient l’enregistrement de tous les événements liés au fonctionnement du moteur de mise à l’échelle selon les paramètres de mise à l’échelle que vous avez défini dans votre abonnement. Est un exemple d’un événement de mise à l’échelle _Échec de l’action de l’échelle_. |
| Recommandation | Contient des événements de recommandation d’Azure Advisor. |
| Sécurité | Contient l’enregistrement de toutes les alertes générées par Azure Security Center. Est un exemple d’un événement de sécurité _fichier double extension suspecte exécutées_. |
| Stratégie | Contient les enregistrements de toutes les opérations d’action d’effet effectuées par Azure Policy. Exemples d’événements de stratégie _Audit_ et _Deny_. Chaque action effectuée par Policy est modélisée en tant qu’opération sur une ressource. |


## <a name="next-steps"></a>Étapes suivantes

* [Créer un profil de journal pour exporter le journal d’activité Azure](activity-log-export.md)
* [Stream the Azure Activity Log to Event Hubs (Diffuser en continu le journal d’activités Azure vers Event Hubs)](activity-logs-stream-event-hubs.md)
* [Archiver le journal d’activité Azure vers le stockage](archive-activity-log.md)

