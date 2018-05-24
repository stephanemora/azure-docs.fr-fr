---
title: Liste des événements Azure Service Fabric | Microsoft Docs
description: Liste complète des événements fournis par Azure Service Fabric pour faciliter la surveillance des clusters.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
---
# <a name="list-of-service-fabric-events"></a>Liste des événements Service Fabric 

Service Fabric expose un ensemble primaire d’événements de cluster pour vous informer de l’état de votre cluster en tant qu’[événements Service Fabric](service-fabric-diagnostics-events.md). Ils sont basés sur les actions effectuées par Service Fabric sur vos nœuds et votre cluster ou les décisions de gestion effectuées par un opérateur/propriétaire de cluster. Ces événements sont accessibles en interrogeant l’[EventStore](service-fabric-diagnostics-eventstore.md) dans votre cluster, ou via le canal opérationnel. Sur les ordinateurs Windows, le canal opérationnel est également relié au journal des événements, afin de pouvoir afficher les événements Service Fabric dans l’observateur d’événements. 

>[!NOTE]
>Pour obtenir la liste des événements Service Fabric pour les clusters des versions < 6.2, reportez-vous à la section suivante. 

Voici une liste de tous les événements disponibles dans la plateforme, triés d’après l’entité à laquelle ils correspondent.

## <a name="cluster-events"></a>Événements de cluster

**Événements de mise à niveau des clusters**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | Informations | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | Informations | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | Informations | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | Informations | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | Informations | 1 |

**Événements du rapport d’intégrité des clusters**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Informations | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Informations | 1 |

**Événements de service de chaos**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Testabilité | Informations | 1 |
| 50023 | ChaosStoppedEvent | Testabilité | Informations | 1 |

## <a name="node-events"></a>Événements de nœud

**Événements du cycle de vie des nœuds** 

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Informations | 1 |
| 18603 | NodeUpOperational | FM | Informations | 1 |
| 18604 | NodeDownOperational | FM | Informations | 1 |
| 18605 | NodeAddedOperational | FM | Informations | 1 |
| 18606 | NodeRemovedOperational | FM | Informations | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Informations | 1 |
| 25620 | NodeOpening | FabricNode | Informations | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Informations | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Informations | 1 |
| 25623 | NodeClosing | FabricNode | Informations | 1 |
| 25624 | NodeClosed | FabricNode | Informations | 1 |
| 25625 | NodeAborting | FabricNode | Informations | 1 |
| 25626 | NodeAborted | FabricNode | Informations | 1 |

**Événements du rapport d’intégrité des nœuds**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Informations | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Informations | 1 |

**Événements du nœud de chaos**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Testabilité | Informations | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Testabilité | Informations | 1 |

## <a name="application-events"></a>Événements liés aux applications

**Événements du cycle de vie des applications**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | Informations | 1 |
| 29625 | ApplicationDeletedOperational | CM | Informations | 1 |
| 23083 | ProcessExitedOperational | Hébergement | Informations | 1 |

**Événements de mise à niveau des applications**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | Informations | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | Informations | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | Informations | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | Informations | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | Informations | 1 |

**Événements du rapport d’intégrité des applications**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Informations | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Informations | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Informations | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Informations | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Informations | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Informations | 1 |

**Événements liés aux applications de chaos**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Testabilité | Informations | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Testabilité | Informations | 1 |

## <a name="service-events"></a>Événements de service

**Événements du cycle de vie du service**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Informations | 1 |
| 18658 | ServiceDeletedOperational | FM | Informations | 1 |

**Événements du rapport d’intégrité du service**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Informations | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Informations | 1 |

## <a name="partition-events"></a>Événements de partition

**Événements de déplacement de partition**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | Informations | 1 |

**Événements du rapport d’intégrité des partitions**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Informations | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Informations | 1 |

**Événements de partition de chaos**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Testabilité | Informations | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Testabilité | Informations | 1 |

**Événements d’analyse de partition**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Testabilité | Informations | 1 |

## <a name="replica-events"></a>Événements de réplica

**Événements du rapport d’intégrité de réplica**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Informations | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Informations | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Informations | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Informations | 1 |

**Événements de réplica de chaos**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Testabilité | Informations | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Testabilité | Informations | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Testabilité | Informations | 1 |

## <a name="container-events"></a>Événements de conteneur

**Événements du cycle de vie des conteneurs** 

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hébergement | Informations | 1 |
| 23075 | ContainerDeactivatedOperational | Hébergement | Informations | 1 |
| 23082 | ContainerExitedOperational | Hébergement | Informations | 1 |

## <a name="other-events"></a>Autres événements

**Événements de corrélation**

| EventId | NOM | Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Testabilité | Informations | 1 |

## <a name="events-prior-to-version-62"></a>Événements antérieurs à la version 6.2

Voici la liste complète des événements fournis par Service Fabric avant la version 6.2.

| EventId | NOM | Source (tâche) | Niveau |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informations |
| 25621 | NodeOpenedSuccess | FabricNode | Informations |
| 25622 | NodeOpenedFailed | FabricNode | Informations |
| 25623 | NodeClosing | FabricNode | Informations |
| 25624 | NodeClosed | FabricNode | Informations |
| 25625 | NodeAborting | FabricNode | Informations |
| 25626 | NodeAborted | FabricNode | Informations |
| 29627 | ClusterUpgradeStart | CM | Informations |
| 29628 | ClusterUpgradeComplete | CM | Informations |
| 29629 | ClusterUpgradeRollback | CM | Informations |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informations |
| 29631 | ClusterUpgradeDomainComplete | CM | Informations |
| 23074 | ContainerActivated | Hébergement | Informations |
| 23075 | ContainerDeactivated | Hébergement | Informations |
| 29620 | ApplicationCreated | CM | Informations |
| 29621 | ApplicationUpgradeStart | CM | Informations |
| 29622 | ApplicationUpgradeComplete | CM | Informations |
| 29623 | ApplicationUpgradeRollback | CM | Informations |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informations |
| 29625 | ApplicationDeleted | CM | Informations |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informations |
| 18566 | ServiceCreated | FM | Informations |
| 18567 | ServiceDeleted | FM | Informations |

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [génération d’événements au niveau de la plateforme](service-fabric-diagnostics-event-generation-infra.md) dans Service Fabric
* Modification de votre configuration [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) pour collecter davantage de journaux
* [Configuration d’Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) pour voir les journaux de votre canal opérationnel
