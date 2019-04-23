---
title: Créer des alertes de performances à l’aide d’Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article décrit comment utiliser Azure Monitor pour les conteneurs pour créer des alertes personnalisées basées sur des requêtes de journal pour la mémoire et l’utilisation du processeur.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791539"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Comment configurer des alertes pour les problèmes de performances dans Azure Monitor pour les conteneurs
Azure Monitor pour les conteneurs surveille les performances des charges de travail de conteneur qui sont déployés sur Azure Container Instances ou sur managed Kubernetes clusters qui sont hébergés sur Azure Kubernetes Service (AKS).

Cet article décrit comment activer les alertes pour les situations suivantes :

* Lorsque l’utilisation du processeur ou mémoire sur les nœuds de cluster dépasse un seuil défini.
* Lorsque l’utilisation du processeur ou mémoire sur n’importe quel conteneur au sein d’un contrôleur dépasse un seuil défini par rapport à une limite qui est définie sur la ressource correspondante
* *NotReady* nœud État du compte
*  *Échec de*, *en attente*, *inconnu*, *en cours d’exécution*, ou *Succeeded* compte de la phase de pod

Pour alerter en cas de processeur élevé ou utilisation de la mémoire sur les nœuds de cluster, utilisez les requêtes qui sont fournis pour créer une alerte de mesure ou une alerte de mesure métrique. Alertes de métrique ont une latence plus faible que les alertes de journal. Mais fournissent des alertes de journal d’interrogation avancées et complexité. Requêtes comparent une valeur datetime jusqu'à présent à l’aide des alertes de journal le *maintenant* opérateur et en accédant à une heure de sauvegarde. (Azure Monitor pour les conteneurs stocke toutes les dates au format de temps universel coordonné (UTC).)

Si vous n’êtes pas familiarisé avec les alertes Azure Monitor, consultez [vue d’ensemble des alertes dans Microsoft Azure](../platform/alerts-overview.md) avant de commencer. Pour en savoir plus sur les alertes qui utilisent des requêtes de journal, consultez [alertes de journal dans Azure Monitor](../platform/alerts-unified-log.md). Pour plus d’informations sur les alertes de métriques, consultez [alertes de métrique dans Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Requêtes de recherche de journal l’utilisation des ressources
Les requêtes dans cette section prend en charge chaque scénario d’alerte. Ils sont utilisés à l’étape 7 de la [créer alerte](#create-an-alert-rule) section de cet article.

La requête suivante calcule l’utilisation moyenne du processeur en moyenne d’utilisation du processeur de nœuds de membres toutes les minutes.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

La requête suivante calcule le taux d’utilisation moyenne de la mémoire comme une moyenne d’utilisation de la mémoire des nœuds membres toutes les minutes.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Les requêtes suivantes utilisent les valeurs d’espace réservé \<your-cluster-name > et \<your-contrôleur-name > pour représenter votre cluster et le contrôleur. Remplacez-les par des valeurs spécifiques à votre environnement lorsque vous définissez des alertes.

La requête suivante calcule l’utilisation moyenne du processeur de tous les conteneurs dans un contrôleur comme une moyenne de l’utilisation du processeur de chaque instance de conteneur dans un contrôleur de toutes les minutes. La mesure est un pourcentage de la limite configurée pour un conteneur.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

La requête suivante calcule l’utilisation moyenne de la mémoire de tous les conteneurs dans un contrôleur en moyenne d’utilisation de la mémoire de chaque instance de conteneur dans un contrôleur de toutes les minutes. La mesure est un pourcentage de la limite configurée pour un conteneur.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

La requête suivante retourne tous les nœuds et les nombres dont l’état de *prêt* et *NotReady*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
La requête suivante retourne le nombre de phase de pod basée sur toutes les phases : *Échec de*, *en attente*, *inconnu*, *en cours d’exécution*, ou *a réussi*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Alerter sur certaines phases pod, telles que *en attente*, *échec*, ou *inconnu*, modifiez la dernière ligne de la requête. Par exemple, pour générer des alertes sur *FailedCount* utiliser : <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>Création d'une règle d'alerte
Suivez ces étapes pour créer une alerte de journal dans Azure Monitor en utilisant l’une des règles de recherche de journal qui a été fourni précédemment.  

>[!NOTE]
>La procédure suivante pour créer une règle d’alerte pour l’utilisation des ressources de conteneur, vous devez basculer vers un nouveau journal alertes API comme décrit dans [préférence de basculer les API pour les alertes de journal](../platform/alerts-log-api-switch.md).
>

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **moniteur** dans le volet situé à gauche. Sous **Insights**, sélectionnez **conteneurs**.
3. Sur le **Clusters analysés** , sélectionnez un cluster à partir de la liste.
4. Dans le volet situé à gauche sous **surveillance**, sélectionnez **journaux** pour ouvrir la page de journaux Azure Monitor. Cette page vous permet d’écrire et exécuter des requêtes d’Analytique de journal Azure.
5. Sur le **journaux** page, sélectionnez **+ nouvelle règle d’alerte**.
6. Dans le **Condition** section, sélectionnez le **chaque fois que le journal recherche personnalisée est \<logique non définie >** prédéfinis de condition de journal personnalisé. Le **recherche de journal personnalisée** type de signal est sélectionné automatiquement, car nous créons une règle d’alerte directement à partir de la page des journaux Azure Monitor.  
7. Collez un de le [requêtes](#resource-utilization-log-search-queries) fourni précédemment dans le **requête de recherche** champ.
8. Configurez l’alerte comme suit :

    1. Dans la liste déroulante **Basé sur**, sélectionnez **Mesure des métriques**. Une mesure métrique crée une alerte pour chaque objet dans la requête qui a une valeur supérieure à notre seuil spécifié.
    1. Pour **Condition**, sélectionnez **supérieur**, puis entrez **75** comme une base de référence initiale **seuil**. Ou entrez une valeur différente qui répond à vos critères.
    1. Dans le **déclencheur alerte basés sur** section, sélectionnez **violations consécutives**. Dans la liste déroulante, sélectionnez **supérieur**, puis entrez **2**.
    1. Pour configurer une alerte pour l’UC du conteneur ou l’utilisation de la mémoire, sous **agréger sur**, sélectionnez **ContainerName**. 
    1. Dans le **évalué selon** section, définissez le **période** valeur **60 minutes**. La règle exécute toutes les 5 minutes et retourner les enregistrements qui ont été créés dans la dernière heure à partir de l’heure actuelle. Définition de la période de temps pour des comptes d’une fenêtre large potentiels latence des données. Elle garantit également que la requête retourne des données afin d’éviter un faux négatif dans lequel l’alerte se déclenche jamais.

9. Sélectionnez **fait** pour terminer la règle d’alerte.
10. Entrez un nom dans la **nom de la règle d’alerte** champ. Spécifiez un **Description** qui fournit des détails sur l’alerte. Et sélectionnez un niveau de gravité approprié parmi les options fournies.
11. Pour activer immédiatement la règle d’alerte, acceptez la valeur par défaut **activer la règle lors de la création**.
12. Sélectionnez un **groupe d’actions** ou créez un nouveau groupe. Cette étape garantit que la même action chaque fois qu’une alerte est déclenchée. Configurer selon la façon dont votre service informatique ou de l’équipe des opérations DevOps gère les incidents.
13. Sélectionnez **créer une règle d’alerte** pour terminer la règle d’alerte. Son exécution démarre immédiatement.

## <a name="next-steps"></a>Étapes suivantes

* Vue [connecter des exemples de requêtes](container-insights-analyze.md#search-logs-to-analyze-data) pour en savoir plus sur les requêtes prédéfinies et des exemples pour évaluer ou personnaliser pour d’autres scénarios d’alerte.
* Pour en savoir plus sur Azure Monitor et comment surveiller d’autres aspects de votre cluster AKS, consultez [vue Azure Kubernetes Service health](container-insights-analyze.md).
