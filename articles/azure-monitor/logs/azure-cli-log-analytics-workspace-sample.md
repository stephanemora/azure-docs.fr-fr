---
title: Gestion des journaux Azure Monitor dans Azure CLI
description: Découvrez comment utiliser les commandes Azure CLI pour gérer un espace de travail dans les journaux Azure Monitor, notamment la façon dont les espaces de travail interagissent avec d’autres services Azure.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 530602fdb471b86ba84aea0093d9e6c4d91d3c75
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820606"
---
# <a name="managing-azure-monitor-logs-in-azure-cli"></a>Gestion des journaux Azure Monitor dans Azure CLI

Utilisez les commandes Azure CLI décrites ici pour gérer votre espace de travail Log Analytics dans Azure Monitor.

> [!NOTE]
> Le 31 août 2024, Microsoft mettra hors service l’agent Log Analytics. Vous pouvez utiliser l’agent Azure Monitor après cette date. Pour plus d’informations, consultez [Présentation des agents Azure Monitor](../agents/agents-overview.md).

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-workspace-for-monitor-logs"></a>Créer un espace de travail pour les journaux Monitor

Exécutez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources ou utiliser un groupe de ressources existant. Pour créer un espace de travail, utilisez la commande [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create).

```azurecli
az group create --name ContosoRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Pour plus d’informations sur les espaces de travail, consultez [Présentation des journaux Azure Monitor](./data-platform-logs.md).

## <a name="list-tables-in-your-workspace"></a>Répertorier les tables dans votre espace de travail

Chaque espace de travail contient des tables avec des colonnes contenant plusieurs lignes de données. Chaque table est définie par un ensemble unique de colonnes de données fournies par la source de données.

Pour voir les tables de votre espace de travail, utilisez la commande [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list) :

```azurecli
az monitor log-analytics workspace table list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

La valeur de sortie `table` présente les résultats dans un format plus lisible. Pour plus d’informations, consultez [Format de sortie](/cli/azure/use-cli-effectively#output-formatting).

Pour modifier le temps de rétention pour une table, exécutez la commande [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update) :

```azurecli
az monitor log-analytics workspace table update --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name Syslog --retention-time 45
```

La durée de rétention est comprise entre 30 et 730 jours.

Pour plus d’informations sur les tables, consultez [Structure de données](./data-platform-logs.md#data-structure).

## <a name="export-data-from-selected-tables"></a>Exporter des données à partir de tables sélectionnées

Vous pouvez exporter en continu des données à partir de tables sélectionnées vers un compte de stockage Azure ou Azure Event Hubs. Utilisez la commande [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create) :

```azurecli
az monitor log-analytics workspace data-export create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --table Syslog \
   --destination /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/exportaccount \
   --enable
```

Pour voir vos exportations de données, exécutez la commande [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list).

```azurecli
az monitor log-analytics workspace data-export list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

Pour supprimer une exportation de données, exécutez la commande [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete). Le paramètre `--yes` passe la confirmation.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --yes
```

Pour plus d’informations sur l’exportation des données, consultez [Exportation des données de l’espace de travail Log Analytics dans Azure Monitor](./logs-data-export.md).

## <a name="manage-a-linked-service"></a>Gérer un service lié

Les services liés définissent une relation entre l’espace de travail et une autre ressource Azure. Les journaux Azure Monitor et les ressources Azure utilisent cette connexion dans leurs opérations. Exemples d’utilisation des services liés, y compris une association d’un compte Automation et d’un espace de travail à des clés gérées par le client.

Pour créer un service lié, exécutez la commande [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create) :

```azurecli
az monitor log-analytics workspace linked-service create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation \
   --resource-id /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Web/sites/ContosoWebApp09

az monitor log-analytics workspace linked-service list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Pour supprimer une relation de service liée, exécutez la commande [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete) :

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation
```

Pour plus d’informations, consultez [az monitor log-analytics workspace linked-service](/cli/azure/monitor/log-analytics/workspace/linked-service).

## <a name="manage-linked-storage"></a>Gérer le stockage lié

Si vous fournissez et gérez votre propre compte de stockage pour Log Analytics, vous pouvez le gérer à l’aide de ces commandes Azure CLI.

Pour lier votre espace de travail à un compte de stockage, exécutez la commande [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create) :

```azurecli
az monitor log-analytics workspace linked-storage create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace \
   --storage-accounts /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/contosostorage \
   --type Alerts

az monitor log-analytics workspace linked-storage list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

Pour supprimer la liaison vers un compte de stockage, exécutez la commande [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete) :

```azurecli
az monitor log-analytics workspace linked-storage delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --type Alerts
```

Pour plus d’informations, consultez [Utilisation de comptes de stockage gérés par le client dans Azure Monitor Log Analytics](./private-storage.md).

## <a name="manage-intelligence-packs"></a>Gérer les packs d’intelligence décisionnelle

Pour voir les packs d’intelligence disponibles, exécutez la commande [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list). La commande vous indique également si le pack est activé.

```azurecli
az monitor log-analytics workspace pack list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Utilisez la commande [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable) ou [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable) :

```azurecli
az monitor log-analytics workspace pack enable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow

az monitor log-analytics workspace pack disable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow
```

## <a name="manage-saved-searches"></a>Gérer les recherches enregistrées

Pour créer une recherche enregistrée, exécutez la commande [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create) :

```azurecli
az monitor log-analytics workspace saved-search create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 \
   --category "Log Management" --display-name SavedSearch01 \
   --saved-query "AzureActivity | summarize count() by bin(TimeGenerated, 1h)" --fa Function01 --fp "a:string = value"
```

Affichez votre recherche enregistrée à l’aide de la commande [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show). Consultez toutes les recherches enregistrées en utilisant [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list).

```azurecli
az monitor log-analytics workspace saved-search show --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01
az monitor log-analytics workspace saved-search list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Pour supprimer une recherche enregistrée, exécutez la commande [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete) :

```azurecli
az monitor log-analytics workspace saved-search delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 --yes
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Si vous avez créé un groupe de ressources pour tester ces commandes, vous pouvez supprimer le groupe de ressources et tout son contenu en utilisant la commande [az group delete](/cli/azure/group#az-group-delete) :

```azurecli
az group delete --name ContosoRG
```

Si vous souhaitez supprimer un nouvel espace de travail d’un groupe de ressources existant, exécutez la commande [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete) :

```azurecli
az monitor log-analytics workspace delete --resource-group ContosoRG 
   --workspace-name ContosoWorkspace --yes
```

Les espaces de travail Log Analytics ont une option de suppression réversible. Vous pouvez récupérer un espace de travail supprimé pendant deux semaines après la suppression. Exécutez la commande [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover) :

```azurecli
az monitor log-analytics workspace recover --resource-group ContosoRG 
   --workspace-name ContosoWorkspace
```

Dans la commande delete, ajoutez le paramètre `--force` pour supprimer immédiatement l’espace de travail.

## <a name="azure-cli-commands-used-in-this-article"></a>Commandes Azure CLI utilisées dans cet article

- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create)
- [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete)
- [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list)
- [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete)
- [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create)
- [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete)
- [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create)
- [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete)
- [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable)
- [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable)
- [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list)
- [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover)
- [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete)
- [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list)
- [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show)
- [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create)
- [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list)
- [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update)

## <a name="next-steps"></a>Étapes suivantes

[Présentation de Log Analytics dans Azure Monitor](log-analytics-overview.md)