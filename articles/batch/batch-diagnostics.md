---
title: Métriques, alertes et journaux de diagnostic
description: Enregistrez et analysez les événements du journal de diagnostic pour des ressources de compte Azure Batch telles que des pools et des tâches.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 3bbaf01b71a2256416112f5e365f70d563a9f36d
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109753941"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Métriques, alertes et journaux d’activité Batch pour l’évaluation de diagnostic et la supervision

Azure Monitor collecte des [métriques](../azure-monitor/essentials/data-platform-metrics.md) et des [journaux de diagnostic](../azure-monitor/essentials/platform-logs-overview.md) pour les ressources de votre compte Azure Batch.

Vous pouvez collecter et utiliser ces données de plusieurs façons pour surveiller votre compte Batch et diagnostiquer les problèmes. Vous pouvez également configurer des [alertes de métriques](../azure-monitor/alerts/alerts-overview.md) afin de recevoir des notifications lorsqu’une métrique atteint une valeur spécifiée.

## <a name="batch-metrics"></a>Métriques Batch

Les [métriques](../azure-monitor/essentials/data-platform-metrics.md) sont des données de télémétrie Azure (également appelées compteurs de performance) qui sont émises par vos ressources Azure et consommées par le service Azure Monitor. Parmi les métriques d’un compte Batch figurent par exemple : Événements de création de pool, Nombre de nœuds à priorité basse et Événements de fin de tâche. Ces métriques peuvent aider à identifier des tendances et peuvent être utilisées pour l’analyse de données.

Consultez la [liste des métriques Batch prises en charge](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts).

Ces métriques :

- sont activées par défaut dans chaque compte Batch sans configuration supplémentaire ;
- sont générées toutes les minutes ;
- ne sont pas conservées automatiquement, mais ont un historique propagé de 30 jours. Vous pouvez conserver les métriques d’activité dans le cadre de la journalisation des diagnostics.

## <a name="view-batch-metrics"></a>Affichage des métriques Batch

Dans le Portail Azure, la page **Vue d’ensemble** du compte Batch affiche les métriques de nœuds de clé, de cœurs et de tâches par défaut.

Pour afficher des métriques supplémentaires pour un compte Batch :

1. Dans le Portail Azure, sélectionnez **Tous les services** > **Comptes Batch**, puis le nom de votre compte Batch.
1. Sous **Surveillance**, sélectionnez **Métriques**.
1. Sélectionnez **Ajouter une métrique** puis choisissez une métrique dans la liste déroulante.
1. Sélectionnez une option d’**agrégation** pour la métrique. Pour les métriques basées sur le nombre (comme « Nombre de cœurs dédiés » ou « Nombre de nœuds à basse priorité »), utilisez l’agrégation **Avg**. Pour les métriques basées sur les événements (comme « Événements de fin de redimensionnement de pool »), utilisez l’agrégation **Count**. N’utilisez pas l’agrégation **Sum**, car elle ajoute les valeurs de tous les points de données reçus pendant la période du graphique.
1. Pour ajouter des métriques supplémentaires, répétez les étapes 3 et 4.

Vous pouvez également récupérer les métriques par programmation avec les API d’Azure Monitor. Par obtenir un exemple, consultez [Récupérer les métriques Azure Monitor avec .NET](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/).

> [!NOTE]
> Les métriques émises au cours des 3 dernières minutes peuvent toujours être agrégées, par conséquent, les valeurs peuvent être sous-signalées pendant cette période. La remise de métriques n’est pas garantie et peut être affectée par la remise en désordre, la duplication ou la perte de données.

## <a name="batch-metric-alerts"></a>Alertes de métriques Batch

Vous pouvez configurer des alertes de métriques en temps quasi-réel qui se déclenchent quand la valeur d’une métrique spécifiée dépasse le seuil défini. Ces alertes génèrent une notification lorsqu’elles sont activées (quand le seuil est atteint et que la condition d’alerte est remplie) et lorsqu’elles sont résolues (quand le seuil est de nouveau atteint et que la condition n’est plus remplie).

Étant donné que la remise des métriques peut être sujette à des incohérences, telles que la remise en désordre, la perte de données ou la duplication, nous vous recommandons d’éviter les alertes qui se déclenchent sur un point de données unique. Utilisez plutôt des seuils pour prendre en compte toutes les incohérences, telles que la remise en désordre, la perte de données et la duplication sur une période donnée.

Par exemple, vous souhaiterez peut-être configurer une alerte de métrique lorsque votre nombre de cœurs en priorité basse descend à un certain niveau, pour vous permettre ainsi d’ajuster la composition de vos pools. Pour obtenir de meilleurs résultats, définissez une période de 10 minutes ou plus, pendant laquelle des alertes se déclenchent si le nombre moyen de cœurs en priorité basse se situe sous la valeur du seuil pendant toute cette période. Cela permet de disposer de temps pour l’agrégation des métriques afin d’obtenir des résultats plus précis.

Pour configurer une alerte de métrique dans le Portail Azure :

1. Sélectionnez **Tous les services** > **Comptes Batch**, puis le nom de votre compte Batch.
1. Sous **Supervision**, sélectionnez **Alertes**, puis **Nouvelle règle d’alerte**.
1. Sélectionnez **Ajouter une condition**, puis choisissez une métrique.
1. Sélectionnez les valeurs souhaitées pour la **Période du graphique**, le **Seuil**, l'**Opérateur** et le **Type d’agrégation**.
1. Entrez une **Valeur de seuil** et sélectionnez l'**Unité** du seuil.  Ensuite, sélectionnez **Terminé**.
1. Ajoutez un [groupe d’actions](../azure-monitor/alerts/action-groups.md) à l’alerte, soit en sélectionnant un groupe d’actions existant, soit en créant un nouveau groupe d’actions.
1. Dans la section **Détails de la règle d’alerte**, entrez un **Nom de règle d’alerte** et une **Description**. Si vous souhaitez que l’alerte soit activée immédiatement, vérifiez que la case **Activer la règle d’alerte lors de la création** est cochée.
1. Sélectionnez **Créer une règle d’alerte**.

Pour plus d’informations sur la création d’alertes de métriques, consultez [Comprendre le fonctionnement des alertes de métrique dans Azure Monitor](../azure-monitor/alerts/alerts-metric-overview.md) et [Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

Vous pouvez également configurer une alerte en temps quasi-réel à l’aide de [l’API REST Azure Monitor](/rest/api/monitor/). Pour plus d’informations, consultez [Vue d’ensemble des alertes dans Microsoft Azure](../azure-monitor/alerts/alerts-overview.md). Pour inclure des informations relatives aux travaux, aux tâches ou aux pools dans vos alertes, consultez [Alertes de journal Azure Monitor](../azure-monitor/alerts/alerts-log.md).

## <a name="batch-diagnostics"></a>Diagnostics Batch

[Les journaux de diagnostic](../azure-monitor/essentials/platform-logs-overview.md) contiennent des informations émises par les ressources Azure qui décrivent le fonctionnement de chaque ressource. Pour Batch, vous pouvez collecter les journaux d’activité suivants :

- **ServiceLog** : [Les événements émis par le service Batch](#service-log-events) pendant la durée de vie d’une ressource individuelle telle qu’un pool ou une tâche.
- **AllMetrics** : Les métriques au niveau du compte Batch.

Vous devez activer explicitement les paramètres de diagnostic pour chaque compte Batch à surveiller.

### <a name="log-destination-options"></a>Options de la destination du journal

Un scénario courant consiste à sélectionner un compte de stockage Azure en tant que destination du journal. Pour stocker les journaux d’activité dans le stockage Azure, créez le compte avant d’activer la collecte de journaux d’activité. Si vous avez associé un compte de stockage à votre compte Batch, vous pouvez choisir ce compte comme destination du journal.

Vous pouvez également :

- Diffusez les événements du journal de diagnostic Batch vers un service [Azure Event Hub](../event-hubs/event-hubs-about.md). Le service Event Hubs peut traiter à chaque seconde des millions d’événements que vous pouvez transformer et stocker à l’aide de tout fournisseur d’analyses en temps réel.
- Envoyez les journaux de diagnostic aux [journaux Azure Monitor](../azure-monitor/logs/log-query-overview.md), où vous pouvez les analyser ou les importer pour analyse dans Power BI ou Excel.

> [!NOTE]
> Vous risquez de payer des frais supplémentaires pour stocker ou traiter les données de journal de diagnostics avec les services Azure.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Activer la collecte des journaux de diagnostic Batch

Pour créer un nouveau paramètre de diagnostic dans le Portail Azure, suivez les étapes ci-dessous.

1. Dans le Portail Azure, sélectionnez **Tous les services** > **Comptes Batch**, puis le nom de votre compte Batch.
2. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.
3. Dans **Paramètres de diagnostic**, sélectionnez **Ajouter un paramètre de diagnostic**.
4. Attribuez un nom au paramètre.
5. Sélectionnez une destination : **Envoyer à Log Analytics**, **Archiver dans un compte de stockage** ou **Diffuser vers Event Hub**. Si vous sélectionnez un compte de stockage, vous pouvez éventuellement sélectionner le nombre de jours pendant lesquels les données sont conservées pour chaque journal. Si vous ne spécifiez pas de nombre de jours de rétention, les données sont conservées pendant la durée de vie du compte de stockage.
6. Sélectionnez **ServiceLog**, **AllMetrics** ou les deux.
7. Sélectionnez **Enregistrer** pour créer le paramètre de diagnostic.

Vous pouvez également activer la collection de journaux en [créant des paramètres de diagnostic dans le Portail Azure](../azure-monitor/essentials/diagnostic-settings.md), à l’aide d’[un modèle Resource Manager](../azure-monitor/essentials/resource-manager-diagnostic-settings.md) ou avec Azure PowerShell ou Azure CLI. Pour plus d’informations, consultez [Vue d’ensemble des journaux de plateforme Azure](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Accéder aux journaux de diagnostics dans le stockage

Si vous [archivez les journaux de diagnostic Batch dans un compte de stockage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage), un conteneur de stockage est créé dans le compte de stockage dès qu’un événement lié se produit. Les objets blob sont créés selon le modèle d’affectation de noms suivant :

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Par exemple :

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Chaque fichier blob `PT1H.json` contient des événements au format JSON qui se sont produits pendant l’heure spécifiée dans l’URL de l’objet blob (par exemple, `h=12`). Pendant l’heure en cours, les événements sont ajoutés au fichier `PT1H.json` à mesure qu’ils se produisent. La valeur de minute (`m=00`) est toujours `00`, étant donné que les événements du journal de diagnostic sont répartis en différents objets blob par heure. (Toutes les heures sont exprimées en heure UTC.)

Voici un exemple d’entrée `PoolResizeCompleteEvent` dans un fichier journal `PT1H.json`. Il comprend des informations sur le nombre actuel et cible de nœuds dédiés et à faible priorité, ainsi que sur l’heure de début et de fin de l’opération :

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Pour accéder par programme aux journaux d’activité de votre compte de stockage, utilisez les API de stockage.

### <a name="service-log-events"></a>Événements du journal de service

Les journaux de service Azure Batch contiennent des événements émis par le service Batch pendant la durée de vie d’une ressource Batch individuelle telle qu’un pool ou une tâche. Chaque événement émis par Batch est enregistré au format JSON. Par exemple, ceci est le corps d’un exemple d’**événement de création de pool** :

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Les événements de journal de service émis par le service Batch incluent les éléments suivants :

- [Création de pool](batch-pool-create-event.md)
- [Début de suppression de pool](batch-pool-delete-start-event.md)
- [Fin de suppression de pool](batch-pool-delete-complete-event.md)
- [Début de redimensionnement de pool](batch-pool-resize-start-event.md)
- [Fin de redimensionnement de pool](batch-pool-resize-complete-event.md)
- [Mise à l’échelle automatique de pool](batch-pool-autoscale-event.md)
- [Début de tâche](batch-task-start-event.md)
- [Fin de tâche](batch-task-complete-event.md)
- [Échec de tâche](batch-task-fail-event.md)
- [Échec de planification de tâche](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [outils et API Batch](batch-apis-tools.md) disponibles pour créer des solutions Batch.
- Consultez d’autres informations sur les [solutions de surveillance Batch](monitoring-overview.md).
