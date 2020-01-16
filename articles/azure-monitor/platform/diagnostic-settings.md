---
title: Créer un paramètre de diagnostic pour collecter les journaux et métriques dans Azure
description: Créez des paramètres de diagnostic pour transférer les journaux de la plateforme Azure vers les journaux Azure Monitor, le Stockage Azure ou Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 22932121b97c1b0fe91c46b5eea0222a022a4e61
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751084"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure
[Les journaux de plateforme](platform-logs-overview.md) dans Azure, y compris le journal d’activité Azure et les journaux de ressources, fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Cet article fournit des détails sur la création et la configuration de paramètres de diagnostic pour envoyer les journaux de plateforme vers différentes destinations.

> [!IMPORTANT]
> Avant de créer un paramètre de diagnostic pour collecter le journal d’activité, vous devez désactiver toute configuration héritée. Pour plus d’informations, voir [Collecter le journal d’activité Azure avec des paramètres hérités](diagnostic-settings-legacy.md).

Chaque ressource Azure requiert son propre paramètre de diagnostic qui définit les éléments suivants :

- Catégories de journaux et données de métriques envoyées aux destinations définies dans le paramètre. Les catégories disponibles varient en fonction des types de ressources.
- Une ou plusieurs destinations auxquelles envoyer les journaux. Les destinations actuelles sont l’espace de travail Log Analytics, Event Hubs et le Stockage Azure.
 
Un seul paramètre de diagnostic ne peut pas définir plus d’une destination. Si vous souhaitez envoyer des données à plus d’un type de destination (par exemple, deux espaces de travail Log Analytics), créez plusieurs paramètres. Chaque ressource peut avoir jusqu’à 5 paramètres de diagnostic.


> [!NOTE]
> Les [métriques de plateforme](metrics-supported.md) sont collectées automatiquement dans les [métriques Azure Monitor](data-platform-metrics.md). Les paramètres de diagnostic permettent de collecter des métriques pour certains services Azure dans les journaux Azure Monitor afin de les analyser avec d’autres données de surveillance à l’aide de [requêtes de journal](../log-query/log-query-overview.md).

## <a name="destinations"></a>Destinations 
Il est possible d’envoyer des journaux de plateforme aux destinations indiquées dans le tableau suivant. Pour configurer chaque destination, suivez le processus de création des paramètres de diagnostic décrit dans cet article. Pour plus d’informations sur l’envoi de données à une destination, suivez le lien correspondant dans le tableau suivant.

| Destination | Description |
|:---|:---|
| [Espace de travail Log Analytics](resource-logs-collect-workspace.md) | La collecte de journaux dans un espace de travail Log Analytics vous permet d’analyser ceux-ci avec d’autres données de surveillance collectées par Azure Monitor à l’aide de requêtes de journal puissantes, ainsi que d’exploiter d’autres fonctionnalités d’Azure Monitor, telles que les alertes et les visualisations. |
| [Event Hubs](resource-logs-stream-event-hubs.md) | L’envoi de journaux à Event Hubs vous permet de transmettre en continu des données à des systèmes externes tels que des solutions SIEM tierces et d’autres solutions d’analytique des journaux d’activité. |
| [Compte Azure Storage](resource-logs-collect-storage.md) | L’archivage des journaux dans un compte de stockage Azure est utile à des fins d’audit, d’analyse statique ou de sauvegarde. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Créer des paramètres de diagnostic sur le portail Azure
Vous pouvez configurer des paramètres de diagnostic sur le portail Azure à partir du menu Azure Monitor ou du menu de la ressource.

1. L’endroit où vous configurez les paramètres de diagnostic dans le portail Azure dépend de la ressource.

   - Pour une ressource unique, cliquez sur **Paramètres de diagnostic** sous **Analyse** dans le menu de la ressource.

        ![Paramètres de diagnostic](media/diagnostic-settings/menu-resource.png)

    - Pour une ou plusieurs ressources, dans le menu Azure Monitor, sous **Paramètres**, cliquez sur **Paramètres de diagnostic**, puis sur la ressource.
    
        ![Paramètres de diagnostic](media/diagnostic-settings/menu-monitor.png)

    - Pour le journal d’activité, dans le menu **Azure Monitor**, cliquez sur **Journal d’activité**, puis sur **Paramètres de diagnostic**. Veillez à désactiver toute configuration héritée pour le journal d’activité. Pour plus d’informations, voir [Désactiver des paramètres existants](diagnostic-settings-legacy.md#disable-existing-settings).

        ![Paramètres de diagnostic](media/diagnostic-settings/menu-activity-log.png)

2. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur **Ajouter le paramètre de diagnostic**.

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

5. Cochez la case correspondant à chacune des catégories de données à envoyer aux destinations spécifiées. La liste des catégories pour chaque service Azure.

   > [!NOTE]
   > L’envoi de métriques multidimensionnelles via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
   >
   > *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.

6. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource, et les journaux sont envoyés aux destinations spécifiées au fur et à mesure de la génération de nouvelles données d’événement. Notez qu’un délai de quinze minutes peut s’écouler entre le déclenchement d’un événement et son [affichage dans l’espace de travail Log Analytics](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Créer des paramètres de diagnostic à l’aide de PowerShell
Pour créer un paramètre de diagnostic avec [Azure PowerShell](powershell-quickstart-samples.md), utilisez la cmdlet [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting). Pour obtenir une description des paramètres de celle-ci, consultez sa documentation.

> [!IMPORTANT]
> Vous ne pouvez pas utiliser cette méthode pour le journal d’activité Azure. Utilisez plutôt la méthode [Créer un paramètre de diagnostic dans Azure Monitor à l’aide d’un modèle Resource Manager](diagnostic-settings-template.md) pour créer un modèle Resource Manager et le déployer avec PowerShell.

Voici un exemple de cmdlet PowerShell permettant de créer un paramètre de diagnostic utilisant les trois destinations.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Créer des paramètres de diagnostic à l’aide d’Azure CLI
Pour créer un paramètre de diagnostic avec [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), utilisez la commande [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create). Pour une description des paramètres de cette commande, consultez sa documentation.

> [!IMPORTANT]
> Vous ne pouvez pas utiliser cette méthode pour le journal d’activité Azure. Utilisez plutôt la méthode [Créer un paramètre de diagnostic dans Azure Monitor à l’aide d’un modèle Resource Manager](diagnostic-settings-template.md) pour créer un modèle Resource Manager et le déployer avec CLI.

Voici un exemple de commande Azure CLI pour créer un paramètre de diagnostic utilisant les trois destinations.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Configurer des paramètres de diagnostic à l’aide de l’API REST
Pour créer ou mettre à jour des paramètres de diagnostic à l’aide de l’[API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/), voir [Paramètres de diagnostic](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Configurer des paramètres de diagnostic à l’aide du modèle Resource Manager
Pour créer ou mettre à jour des paramètres de diagnostic à l’aide d’un modèle Resource Manager, voir [Créer un paramètre de diagnostic dans Azure Monitor à l’aide d’un modèle Resource Manager](diagnostic-settings-template.md).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de la plateforme Azure](platform-logs-overview.md)
