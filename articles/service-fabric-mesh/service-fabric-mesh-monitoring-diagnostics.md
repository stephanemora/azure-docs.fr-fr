---
title: Surveillance et diagnostics dans les applications Azure Service Fabric mesh | Microsoft Docs
description: Découvrez l’application de surveillance et de diagnostic dans Service Fabric mesh sur Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939820"
---
# <a name="monitoring-and-diagnostics"></a>Surveillance et diagnostics
Azure Service Fabric mesh est un service entièrement géré qui permet aux développeurs de déployer des applications de microservices sans gestion de machines virtuelles, de stockage ou de mise en réseau. La surveillance et les diagnostics pour Service Fabric mesh portent sur trois types principaux de données de diagnostic :

- Journaux des applications : ils sont définis comme les journaux d’activité de vos applications en conteneur, selon la façon dont vous avez instrumenté votre application (par exemple, journaux d’activité Docker).
- Événements de plateforme : événements de la plateforme Mesh pertinents pour votre opération de conteneur. Ils incluent actuellement l’activation, la désactivation et l’arrêt de conteneur.
- Métriques de conteneur : métriques de l’utilisation et des performances des ressources pour vos conteneurs (statistiques Docker).

Cet article décrit les options de surveillance et de diagnostic pour la dernière préversion disponible.

## <a name="application-logs"></a>Journaux d’activité d’application

Vous pouvez afficher les journaux d’activité Docker de vos conteneurs déployés, séparément pour chaque conteneur. Dans le modèle d’application Service Fabric mesh, chaque conteneur est un package de code dans votre application. Pour afficher les journaux d’activité associés avec un package de code, utilisez la commande suivante :

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Vous pouvez utiliser la commande « az mesh service-replica » pour obtenir le nom du réplica. Noms de réplica sont incrémentation entiers de 0.

Voici à quoi cela ressemble pour l’affichage de journaux d’activité du conteneur VotingWeb.Code de l’application de vote :

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Mesures de conteneur 

L’environnement de maille expose un certain nombre de mesures indiquant le fonctionnement de vos conteneurs. Les métriques suivantes sont disponibles via Azure portal et Azure CLI de surveiller :

| Métrique | Description | Units|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu sous forme de pourcentage | % |
| MemoryUtilization | ActualMem/AllocatedMem sous forme de pourcentage | % |
| AllocatedCpu | Processeur alloué en fonction de modèle Azure Resource Manager | Millicores |
| AllocatedMemory | Mémoire allouée en fonction de modèle Azure Resource Manager | Mo |
| ActualCpu | Utilisation de l’UC | Millicores |
| ActualMemory | Utilisation de la mémoire | Mo |
| ContainerStatus | 0 - non valide : L’état du conteneur est inconnu <br> 1 - en attente : Le conteneur a planifié pour démarrer <br> 2 - début : Le conteneur est en cours de démarrage <br> 3 - en route : Le conteneur a démarré avec succès <br> 4 - arrêt en cours : Le conteneur est en cours d’arrêt <br> 5 - arrêté : Le conteneur s’est arrêté avec succès | N/A |
| ApplicationStatus | 0 - inconnu : L’état n’est pas récupérable <br> 1 - prêt : L’application s’exécute correctement <br> 2 - mise à niveau : Il existe une mise à niveau en cours <br> 3 - Création : L’application est en cours de création. <br> 4 - suppression : L’application est en cours de suppression. <br> 5 - Échec : Échec du déploiement de l’application | N/A |
| ServiceStatus | 0 - non valide : Le service ne dispose pas actuellement d’un état d’intégrité <br> 1 - ok : Le service est sain  <br> 2 - Avertissement : Il peut y avoir un problème nécessitant une investigation <br> 3 - Erreur : Il existe un problème qui nécessite un examen <br> 4 - inconnu : L’état n’est pas récupérable | N/A |
| ServiceReplicaStatus | 0 - non valide : Le réplica n’a pas actuellement un état d’intégrité <br> 1 - ok : Le service est sain  <br> 2 - Avertissement : Il peut y avoir un problème nécessitant une investigation <br> 3 - Erreur : Il existe un problème qui nécessite un examen <br> 4 - inconnu : L’état n’est pas récupérable | N/A | 
| RestartCount | Nombre de redémarrages du conteneur | N/A |

> [!NOTE]
> Les valeurs d’état du service et ServiceReplicaStatus sont les mêmes que les [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) dans Service Fabric. 

Chaque mesure est disponible sur les différentes dimensions afin que vous puissiez voir agrégats à différents niveaux. La liste actuelle des dimensions sont les suivantes :

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> La dimension CodePackageName n’est pas disponible pour les applications Linux. 

Chaque dimension correspond à différents composants de la [modèle d’Application Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Moniteur d’Azure CLI

Une liste complète des commandes est disponible dans le [de documents Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) mais nous avons inclus quelques exemples utiles ci-dessous 

Dans chaque exemple, l’ID de ressource suit ce modèle

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilisation du processeur de conteneurs dans une application

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilisation de la mémoire pour chaque réplica de Service
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Redémarre pour chaque conteneur dans une fenêtre de 1 heure 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilisation d’UC moyenne entre services nommé « VotingWeb » dans une fenêtre de 1 heure
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Explorateur de mesures

Metrics explorer est un panneau dans le portail dans lequel vous pouvez visualiser toutes les mesures pour votre application de maillage. Ce panneau est accessible dans la page d’application dans le portail et le panneau Azure monitor, ce dernier dont vous pouvez utiliser pour afficher les métriques pour toutes vos ressources Azure qui prennent en charge d’Azure Monitor. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur Service Fabric mesh, voir la [vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md).
* Pour en savoir plus sur les commandes de métriques Azure Monitor, consultez le [de documents Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
