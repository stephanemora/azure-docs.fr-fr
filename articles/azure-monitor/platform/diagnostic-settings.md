---
title: Créer un paramètre de diagnostic pour collecter des journaux et métriques dans Azure | Microsoft Docs
description: Créez des paramètres de diagnostic pour transférer les journaux de la plateforme Azure vers les journaux Azure Monitor, le Stockage Azure ou Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: b90e5ccf38e95d33c4b5b6f3b8da0e91a4facb5a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023743"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure
Les [journaux de plateforme](resource-logs-overview.md) dans Azure fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Cet article fournit des détails sur la création et la configuration de paramètres de diagnostic pour collecter les journaux de plateforme vers différentes destinations.

Chaque ressource Azure requiert son propre paramètre de diagnostic. Le paramètre de diagnostic définit ce qui suit pour cette ressource :

- Catégories de journaux et données de métriques envoyées aux destinations définies dans le paramètre. Les catégories disponibles varient en fonction des types de ressources.
- Une ou plusieurs destinations auxquelles envoyer les journaux. Les destinations actuelles sont l’espace de travail Log Analytics, Event Hubs et le Stockage Azure.
- Stratégie de rétention pour les données stockées dans le Stockage Azure.
 
Un seul paramètre de diagnostic peut définir l’une des destinations. Si vous souhaitez envoyer des données à plus d’un type de destination (par exemple, deux espaces de travail Log Analytics), créez plusieurs paramètres. Chaque ressource peut avoir jusqu’à 5 paramètres de diagnostic.

> [!NOTE]
> Il est possible de transférer le journal d’activité aux mêmes destinations que les autres journaux de la plateforme, mais il n’est pas configuré avec les paramètres de diagnostic. Pour plus d’informations, voir [Vue d’ensemble des journaux de plateforme dans Azure](platform-logs-overview.md#destinations).

> [!NOTE]
> Les [métriques de plateforme](metrics-supported.md) sont collectées automatiquement dans les [métriques Azure Monitor](data-platform-metrics.md). Les paramètres de diagnostic permettent de collecter des métriques pour certains services Azure dans les journaux Azure Monitor afin de les analyser avec d’autres données de surveillance à l’aide de [requêtes de journal](../log-query/log-query-overview.md).

## <a name="destinations"></a>Destinations 
Il est possible d’envoyer des journaux de plateforme aux destinations indiquées dans le tableau suivant. Pour configurer chaque destination, suivez le processus de création des paramètres de diagnostic décrit dans cet article. Pour plus d’informations sur l’envoi de données à une destination, suivez le lien correspondant dans le tableau suivant.

| Destination | Description |
|:---|:---|
| [Espace de travail Log Analytics](resource-logs-collect-workspace.md) | La collecte de journaux dans un espace de travail Log Analytics vous permet d’analyser ceux-ci avec d’autres données de surveillance collectées par Azure Monitor à l’aide de requêtes de journal puissantes, ainsi que d’exploiter d’autres fonctionnalités d’Azure Monitor, telles que les alertes et les visualisations. |
| [Event Hubs](resource-logs-stream-event-hubs.md) | L’envoi de journaux à Event Hubs vous permet de transmettre en continu des données à des systèmes externes tels que des solutions SIEM tierces et d’autres solutions d’analytique des journaux d’activité. |
| [Compte Azure Storage](resource-logs-collect-storage.md) | L’archivage des journaux dans un compte de stockage Azure est utile à des fins d’audit, d’analyse statique ou de sauvegarde. |


> [!IMPORTANT]
> Les comptes ADLS Gen2 ne sont actuellement pas pris en charge en tant que destination pour les paramètres de diagnostic, même s’ils peuvent être répertoriés comme une option valide dans le Portail Azure.

## <a name="create-diagnostic-settings-in-azure-portal"></a>Créer des paramètres de diagnostic sur le portail Azure
Vous pouvez configurer des paramètres de diagnostic sur le portail Azure à partir du menu Azure Monitor ou du menu de la ressource.

1. Dans le menu Azure Monitor sur le portail Azure, sous **Paramètres**, cliquez sur **Paramètres de diagnostic**, puis sur la ressource.

    ![Paramètres de diagnostic](media/diagnostic-settings/menu-monitor.png)

    Ou bien, dans le menu de la ressource sur le portail Azure, sous **Analyse**, cliquez sur **Paramètres de diagnostic**.

    ![Paramètres de diagnostic](media/diagnostic-settings/menu-resource.png)

2. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur **Activer les diagnostics**.

   ![Ajouter le paramètre de diagnostic - aucun paramètre existant](media/diagnostic-settings/add-setting.png)

   S’il des paramètres sont définis pour la ressource, la liste des paramètres configurés s’affiche. Cliquez sur **Ajouter un paramètre de diagnostic** pour ajouter un paramètre ou sur **Modifier le paramètre** pour modifier un paramètre existant. Chaque paramètre ne peut pas avoir plus d’un type de destination.

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-settings/edit-setting.png)

3. Si votre paramètre n’a pas de nom, nommez-le.
4. Cochez la case correspondant à chaque destination à laquelle envoyer les journaux. Cliquez sur **Configurer** pour spécifier les paramètres comme décrit dans le tableau suivant.

    | Paramètre | Description |
    |:---|:---|
    | Espace de travail Log Analytics | Nom de l’espace de travail. |
    | Compte de stockage | Nom du compte de stockage. |
    | Espace de noms du hub d’événements | Espace de noms dans lequel le hub d’événements est créé (si c’est la première fois que vous diffusez en continu des journaux) ou vers lequel il est diffusé (si des ressources diffusent déjà cette catégorie de journal d’activité vers cet espace de noms).
    | Nom du hub d’événements | Vous pouvez éventuellement spécifier dans le paramètre un nom de hub d’événements auquel envoyer toutes les données. Si vous ne spécifiez pas de nom, un hub d’événements est créé pour chaque catégorie de journal. Si vous envoyez plusieurs catégories, vous pouvez spécifier un nom pour limiter le nombre de hubs d’événements créés. Pour plus de détails, voir [Quotas et limites d’Azure Event Hubs](../../event-hubs/event-hubs-quotas.md). |
    | Nom de la stratégie du hub d’événements | Définit les autorisations dont dispose le mécanisme de diffusion en continu. |

    ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-settings/setting-details.png)

5. Cochez la case correspondant à chacune des catégories de données à envoyer aux destinations spécifiées. Si vous avez sélectionné l’option **Archiver dans un compte de stockage**, vous devrez également spécifier la [période de rétention](resource-logs-collect-storage.md#data-retention).



> [!NOTE]
> L’envoi de métriques multidimensionnelles via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.

4. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource, et les journaux sont envoyés aux destinations spécifiées au fur et à mesure de la génération de nouvelles données d’événement. Notez qu’un délai de quinze minutes peut s’écouler entre le déclenchement d’un événement et son [affichage dans l’espace de travail Log Analytics](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Créer des paramètres de diagnostic à l’aide de PowerShell
Pour créer un paramètre de diagnostic avec [Azure PowerShell](powershell-quickstart-samples.md), utilisez la cmdlet [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting). Pour obtenir une description des paramètres de celle-ci, consultez sa documentation.

Voici un exemple de cmdlet PowerShell permettant de créer un paramètre de diagnostic utilisant les trois destinations.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Créer des paramètres de diagnostic à l’aide d’Azure CLI
Pour créer un paramètre de diagnostic avec [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), utilisez la commande [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create). Pour une description des paramètres de cette commande, consultez sa documentation.

Voici un exemple de commande Azure CLI pour créer un paramètre de diagnostic utilisant les trois destinations.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Configurer des paramètres de diagnostic à l’aide de l’API REST
Pour créer ou mettre à jour des paramètres de diagnostic à l’aide de l’[API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/), voir [Paramètres de diagnostic](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Configurer des paramètres de diagnostic à l’aide du modèle Resource Manager
Pour créer ou mettre à jour des paramètres de diagnostic à l’aide d’un modèle Resource Manager, voir [Activer automatiquement les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager](diagnostic-settings-template.md).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de la plateforme Azure](resource-logs-overview.md)