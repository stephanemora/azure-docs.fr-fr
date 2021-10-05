---
title: Gérer les composants Application Insights dans Azure CLI
description: Utilisez cet exemple de code pour gérer des composants dans Application Insights. Cette fonctionnalité fait partie d’Azure Monitor.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/10/2012
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8c7b81847a1ac2484a1eaa0df56bde35eec6385
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709092"
---
# <a name="manage-application-insights-components-by-using-azure-cli"></a>Gérer les composants Application Insights en utilisant Azure CLI

Dans Azure Monitor, les composants sont des fragments de votre application distribuée/de microservices que vous pouvez déployer de manière indépendante. Utilisez ces commandes Azure CLI pour gérer les composants dans Application Insights.

Les exemples figurant dans cet article effectuent les tâches de gestion suivantes :

- Créer un composant.
- Connecter un composant à une application web.
- Lier un composant à un compte de stockage avec un composant.
- Créer une configuration d’exportation continue pour un composant.

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-component"></a>Créer un composant

Si vous n’avez pas déjà un groupe de ressources et un espace de travail, créez-les à l’aide des commandes [az group create](/cli/azure/group#az_group_create) et [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create) :

```azurecli
az group create --name ContosoAppInsightRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoAppInsightRG \
   --workspace-name AppInWorkspace
```

Pour créer un composant, exécutez la commande [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create). La commande [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show) affiche le composant.

```azurecli
az monitor app-insights component create --resource-group ContosoAppInsightRG \
   --app ContosoApp --location eastus2 --kind web --application-type web \
   --retention-time 120
az monitor app-insights component show --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="connect-a-webapp"></a>Connecter une application web

Cet exemple connecte votre composant à une application web. Vous pouvez créer une application web à l’aide des commandes [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) et [az webapp create](/cli/azure/webapp#az_webapp_create) :

```azurecli
az appservice plan create --resource-group ContosoAppInsightRG --name ContosoAppService
az webapp create --resource-group ContosoAppInsightRG --name ContosoApp \
   --plan ContosoAppService --name ContosoApp8765
```

Exécutez la commande [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp) pour connecter votre composant à l’application web :

```azurecli
az monitor app-insights component connect-webapp --resource-group ContosoAppInsightRG \
   --app ContosoApp --web-app ContosoApp8765 --enable-debugger false --enable-profiler false
```

Vous pouvez aussi vous connecter à une fonction Azure à l’aide de la commande [az monitor app-insights component connect-function](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_function).

## <a name="link-a-component-to-storage"></a>Lier un composant au stockage

Vous pouvez lier un composant à un compte de stockage. Pour créer un compte de stockage, utilisez la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) :

```azurecli
az storage account create --resource-group ContosoAppInsightRG \
   --name contosolinkedstorage --location eastus2 --sku Standard_LRS
```

Pour lier votre composant au compte de stockage, exécutez la commande [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link). Vous pouvez voir les liens existants à l’aide de la commande [az monitor app-insights component linked-storage show](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_show) :


```azurecli
az monitor app-insights component linked-storage link  --resource-group ContosoAppInsightRG \
   --app ContosoApp --storage-account contosolinkedstorage
az monitor app-insights component linked-storage show --resource-group ContosoAppInsightRG \
   --app ContosoApp
```

Pour supprimer le lien au stockage, exécutez la commande [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink) :

```AzureCLI
az monitor app-insights component linked-storage unlink  \
   --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="set-up-continuous-export"></a>Configurer une exportation continue

Une exportation continue permet d’enregistrer les événements à partir du portail Application Insights dans un conteneur de stockage au format JSON.

> [!NOTE]
> L’exportation continue est uniquement prise en charge pour les ressources Application Insights classiques. [Les ressources Application Insights basées sur un espace de travail](../app/create-workspace-resource.md) doivent utiliser des [paramètres de diagnostic](../app/create-workspace-resource.md#export-telemetry).
>

Pour créer un conteneur de stockage, exécutez la commande [az storage container create](/cli/azure/storage/container#az_storage_container_create). 

```azurecli
az storage container create --name contosostoragecontainer --account-name contosolinkedstorage \
   --public-access blob 
```

Vous avez besoin d’un accès en écriture seule au conteneur. Exécutez l’applet de commande [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create) :

```azurecli
az storage container policy create --container-name contosostoragecontainer \
   --account-name contosolinkedstorage --name WAccessPolicy --permissions w
```

Créez une clé SAS à l’aide de la commande [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas). Veillez à utiliser la valeur de paramètre `--output tsv` pour enregistrer la clé sans mise en forme indésirable, comme des guillemets. Pour plus d’informations, consultez [Utiliser Azure CLI efficacement](/cli/azure/use-cli-effectively).

```azurecli
containersas=$(az storage container generate-sas --name contosostoragecontainer \
   --account-name contosolinkedstorage --permissions w --output tsv)
```

Pour créer une exportation continue, exécutez la commande [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create) :

```azurecli
az monitor app-insights component continues-export create --resource-group ContosoAppInsightRG \
   --app ContosoApp --record-types Event --dest-account contosolinkedstorage \
   --dest-container contosostoragecontainer --dest-sub-id 00000000-0000-0000-0000-000000000000 \
   --dest-sas $containersas
```

Vous pouvez supprimer une exportation continue configurée à l’aide de la commande [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete) : 

```azurecli
az monitor app-insights component continues-export list \
   --resource-group ContosoAppInsightRG --app ContosoApp
az monitor app-insights component continues-export delete \
   --resource-group ContosoAppInsightRG --app ContosoApp --id abcdefghijklmnopqrstuvwxyz=
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Si vous avez créé un groupe de ressources pour tester ces commandes, vous pouvez supprimer le groupe de ressources et tout son contenu en utilisant la commande [az group delete](/cli/azure/group#az_group_delete) :

```azurecli
az group delete --name ContosoAppInsightRG 
```

## <a name="azure-cli-commands-used-in-this-article"></a>Commandes Azure CLI utilisées dans cet article

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp)
- [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create)
- [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete)
- [az monitor app-insights component continues-export list](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_list)
- [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)
- [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link)
- [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink)
- [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az storage account create](/cli/azure/storage/account#az_storage_account_create)
- [az storage container create](/cli/azure/storage/container#az_storage_container_create)
- [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas)
- [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create)
- [az webapp create](/cli/azure/webapp#az_webapp_create)

## <a name="next-steps"></a>Étapes suivantes

[Exemples d’interface CLI Azure Monitor](../cli-samples.md)

[Rechercher et diagnostiquer des problèmes de performances](../app/tutorial-performance.md)

[Surveiller et alerter sur l’intégrité des applications](../app/tutorial-alert.md)
