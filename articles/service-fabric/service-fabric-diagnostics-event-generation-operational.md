---
title: Liste des événements Azure Service Fabric | Microsoft Docs
description: Liste complète des événements fournis par Azure Service Fabric pour faciliter la surveillance des clusters.
services: service-fabric
documentationcenter: .net
author: srrengar
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
ms.openlocfilehash: 03dac03405588ba00a0f8ca5b127956c40853e36
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868511"
---
# <a name="list-of-service-fabric-events"></a>Liste des événements Service Fabric 

Service Fabric expose un ensemble primaire d’événements de cluster pour vous informer de l’état de votre cluster en tant qu’[événements Service Fabric](service-fabric-diagnostics-events.md). Ils sont basés sur les actions effectuées par Service Fabric sur vos nœuds et votre cluster ou les décisions de gestion effectuées par un opérateur/propriétaire de cluster. Ces événements sont accessibles en interrogeant l’[EventStore](service-fabric-diagnostics-eventstore.md) dans votre cluster, ou via le canal opérationnel. Sur les ordinateurs Windows, le canal opérationnel est également relié au journal des événements, afin de pouvoir afficher les événements Service Fabric dans l’observateur d’événements. 

>[!NOTE]
>Pour obtenir la liste des événements Service Fabric pour les clusters des versions < 6.2, reportez-vous à la section suivante. 

Voici une liste de tous les événements disponibles dans la plateforme, triés d’après l’entité à laquelle ils correspondent.

## <a name="cluster-events"></a>Événements de cluster

**Événements de mise à niveau des clusters**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | Une mise à niveau de cluster a commencé | CM | Informations | 1 |
| 29628 | ClusterUpgradeCompleted | Une mise à niveau de cluster est terminée| CM | Informations | 1 |
| 29629 | ClusterUpgradeRollbackStarted | La restauration d’une mise à niveau de cluster a démarré | CM | Informations | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | La restauration d’une mise à niveau de cluster est terminée | CM | Informations | 1 |
| 29631 | ClusterUpgradeDomainCompleted | Une mise à niveau de domaine s’est terminée pendant une mise à niveau de cluster | CM | Informations | 1 |

## <a name="node-events"></a>Événements de nœud

**Événements du cycle de vie des nœuds** 

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | La désactivation d’un nœud est terminée | FM | Informations | 1 |
| 18603 | NodeUp | Le cluster a détecté qu’un nœud avait démarré | FM | Informations | 1 |
| 18604 | NodeDown | Le cluster a détecté qu’un nœud s’était fermé |  FM | Informations | 1 |
| 18605 | NodeAddedToCluster | Un nouveau nœud a été ajouté au cluster | FM | Informations | 1 |
| 18606 | NodeRemovedFromCluster | Un nœud a été supprimé du cluster | FM | Informations | 1 |
| 18607 | NodeDeactivateStarted | La désactivation d’un nœud a commencé | FM | Informations | 1 |
| 25620 | NodeOpening | Un nœud est en cours de démarrage. Première étape du cycle de vie du nœud | FabricNode | Informations | 1 |
| 25621 | NodeOpenSucceeded | Un nœud a démarré correctement | FabricNode | Informations | 1 |
| 25622 | NodeOpenFailed | Un nœud n’a pas pu démarrer | FabricNode | Informations | 1 |
| 25623 | NodeClosing | Un nœud est en cours d’arrêt. Début de la dernière étape du cycle de vie du nœud | FabricNode | Informations | 1 |
| 25624 | NodeClosed | Un nœud s’est fermé avec succès | FabricNode | Informations | 1 |
| 25625 | NodeAborting | Un nœud commence à s’arrêter de manière anormale | FabricNode | Informations | 1 |
| 25626 | NodeAborted | Un nœud s’est arrêté de manière anormale | FabricNode | Informations | 1 |

## <a name="application-events"></a>Événements liés aux applications

**Événements du cycle de vie des applications**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | Une application a été créée | CM | Informations | 1 |
| 29625 | ApplicationDeleted | Une application existante a été supprimée | CM | Informations | 1 |
| 23083 | ApplicationProcessExited | Un processus au sein d’une application s’est terminé | Hébergement | Informations | 1 |

**Événements de mise à niveau des applications**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | Une mise à niveau d’application a commencé | CM | Informations | 1 |
| 29622 | ApplicationUpgradeCompleted | Une mise à niveau d’application s’est terminée | CM | Informations | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | La restauration d’une mise à niveau d’application a démarré |CM | Informations | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | La restauration d’une mise à niveau d’application s’est terminée | CM | Informations | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | Une mise à niveau de domaine s’est terminée pendant une mise à niveau d’application | CM | Informations | 1 |

## <a name="service-events"></a>Événements de service

**Événements du cycle de vie du service**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Un service a été créé | FM | Informations | 1 |
| 18658 | ServiceDeleted | Un service existant a été supprimé | FM | Informations | 1 |

## <a name="partition-events"></a>Événements de partition

**Événements de déplacement de partition**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | La reconfiguration d’une partition s’est terminée | RA | Informations | 1 |

## <a name="container-events"></a>Événements de conteneur

**Événements du cycle de vie des conteneurs** 

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Un conteneur a démarré | Hébergement | Informations | 1 |
| 23075 | ContainerDeactivated | Un conteneur s’est arrêté | Hébergement | Informations | 1 |
| 23082 | ContainerExited | Un conteneur s’est arrêté : vérifiez l’indicateur UnexpectedTermination | Hébergement | Informations | 1 |

## <a name="health-reports"></a>Rapports d'intégrité

**Événements du rapport d’intégrité des clusters**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Un nouveau rapport d’intégrité de cluster est disponible | HM | Informations | 1 |
| 54437 | ClusterHealthReportExpired | Un rapport d’intégrité de cluster existant a expiré | HM | Informations | 1 |

**Événements du rapport d’intégrité des nœuds**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Un nouveau rapport d’intégrité de nœud est disponible | HM | Informations | 1 |
| 54432 | NodeHealthReportExpired | Un rapport d’intégrité de nœud existant a expiré | HM | Informations | 1 |

**Événements du rapport d’intégrité des applications**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Un rapport d’intégrité d’application a été créé. Il concerne les applications non déployées. | HM | Informations | 1 |
| 54426 | DeployedApplicationNewHealthReport | Un rapport d’intégrité d’application déployée a été créé | HM | Informations | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Un rapport d’intégrité de service déployée a été créé | HM | Informations | 1 |
| 54434 | ApplicationHealthReportExpired | Un rapport d’intégrité d’application existant a expiré | HM | Informations | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Un rapport d’intégrité d’application déployée existant a expiré | HM | Informations | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Un rapport d’intégrité de service déployé existant a expiré | HM | Informations | 1 |

**Événements du rapport d’intégrité du service**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Un rapport d’intégrité de service a été créé | HM | Informations | 1 |
| 54433 | ServiceHealthReportExpired | Un rapport d’intégrité de service existant a expiré | HM | Informations | 1 |

**Événements du rapport d’intégrité des partitions**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Un rapport d’intégrité de partition a été créé | HM | Informations | 1 |
| 54431 | PartitionHealthReportExpired | Un rapport d’intégrité de partition existant a expiré | HM | Informations | 1 |

**Événements du rapport d’intégrité de réplica**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Un rapport d’intégrité de réplica avec état a été créé | HM | Informations | 1 |
| 54430 | StatelessInstanceNewHealthReport | Un rapport d’intégrité d’instance sans état a été créé | HM | Informations | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Un rapport d’intégrité de réplica avec état existant a expiré | HM | Informations | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Un rapport d’intégrité d’instance sans état existant a expiré | HM | Informations | 1 |

## <a name="chaos-testing-events"></a>Événements de test par le chaos 

**Événements de session de test par le chaos**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Une session de test par le chaos a démarré | Testabilité | Informations | 1 |
| 50023 | ChaosStopped | Une session de test par le chaos s’est arrêtée | Testabilité | Informations | 1 |

**Événements du nœud de chaos**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Un nœud est planifié pour redémarrer dans le cadre d’une session de test par le chaos | Testabilité | Informations | 1 |
| 50087 | ChaosNodeRestartCompleted | Un nœud a fini de redémarrer dans le cadre d’une session de test par le chaos | Testabilité | Informations | 1 |

**Événements liés aux applications de chaos**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Un redémarrage de package de code a été planifié pendant une session de test par le chaos | Testabilité | Informations | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Un redémarrage de package de code s’est terminé pendant une session de test par le chaos | Testabilité | Informations | 1 |

**Événements de partition de chaos**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Une partition principale est planifiée pour redémarrer dans le cadre d’une session de test par le chaos | Testabilité | Informations | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Une partition secondaire est planifiée pour redémarrer dans le cadre d’une session de test par le chaos | Testabilité | Informations | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Une analyse plus approfondie du déplacement de la partition principale est disponible | Testabilité | Informations | 1 |

**Événements de réplica de chaos**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Un redémarrage de réplica a été planifié pendant une session de test par le chaos | Testabilité | Informations | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Une suppression de réplica a été planifiée pendant une session de test par le chaos | Testabilité | Informations | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Une suppression de réplica s’est terminée pendant une session de test par le chaos | Testabilité | Informations | 1 |

## <a name="other-events"></a>Autres événements

**Événements de corrélation**

| EventId | NOM | Description |Source (tâche) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Une corrélation a été détectée | Testabilité | Informations | 1 |

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
