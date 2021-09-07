---
title: Créer des analyses d’alerte de métrique dans Azure CLI
description: Découvrez comment créer des alertes de métrique dans Azure Monitor avec des commandes Azure CLI. Ces exemples créent des alertes pour une machine virtuelle et un plan App Service.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 60d2b7f706c985eaa13c634e0323927eb39446ad
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117533"
---
# <a name="create-metric-alert-monitors-in-azure-cli"></a>Créer des analyses d’alerte de métrique dans Azure CLI

Ces exemples créent des analyses d’alerte de métrique dans Azure Monitor avec des commandes Azure CLI. Le premier exemple crée une alerte pour une machine virtuelle. La deuxième commande crée une alerte qui comprend une dimension pour un plan App Service.  

[!INCLUDE [Prepare your Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-alert"></a>Créer une alerte

Cette alerte supervise une machine virtuelle existante nommée `VM07` dans le groupe de ressources nommé `ContosoVMRG`.

Vous pouvez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Pour plus d’informations sur la création de machines virtuelles, consultez [Créer une machine virtuelle Windows avec Azure CLI](../virtual-machines/windows/quick-create-cli.md), [Créer une machine virtuelle Linux avec Azure CLI](../virtual-machines/linux/quick-create-cli.md) et la commande [az vm create](/cli/azure/vm#az-vm-create).

```azurecli
# resource group name: ContosoVMRG
# virtual machine name: VM07

# Create scope
scope=$(az vm show --resource-group ContosoVMRG --name VM07 --output tsv --query id)

# Create action
action=$(az monitor action-group create --name ContosoWebhookAction \
  --resource-group ContosoVMRG --output tsv --query id \
  --action webhook https://alerts.contoso.com usecommonalertschema)

# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
  --metric "Percentage CPU" --op GreaterThan --type static --threshold 90 --output tsv)

# Create metrics alert
az monitor metrics alert create --name alert-01 --resource-group ContosoVMRG \
  --scopes $scope --action $action --condition $condition --description "Test High CPU"
```

Cet exemple utilise le type de sortie `tsv`, qui n’inclut pas de symboles indésirables tels que les guillemets. Pour plus d’informations, consultez [Utiliser Azure CLI efficacement](/cli/azure/use-cli-effectively).

## <a name="create-an-alert-with-a-dimension"></a>Créer une alerte avec une dimension

Cet exemple crée un plan App Service, puis crée une alerte de métriques pour celui-ci. L’exemple utilise une dimension pour spécifier que toutes les instances du plan App Service relèvent de cette métrique. L’exemple crée un groupe de ressources et un plan de service d’application.

```azurecli
# Create resource group
az group create --name ContosoRG --location eastus2
 
# Create application service plan
az appservice plan create --resource-group ContosoRG --name ContosoAppServicePlan \
   --is-linux --number-of-workers 4 --sku S1 
 
# Create scope
scope=$(az appservice plan show --resource-group ContosoRG --name ContosoAppServicePlan \
   --output tsv --query id) 
 
# Create dimension
dim01=$(az monitor metrics alert dimension create --name Instance --value * --op Include --output tsv)
 
# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
   --metric CpuPercentage --op GreaterThan --type static --threshold 90 \
   --dimension $dim01 --output tsv)
```

Pour voir la liste des métriques possibles, exécutez la commande [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions). Le paramètre `--output` affiche les valeurs dans un format lisible.


```azurecli
az monitor metrics list-definitions --resource $scope --output table 
 
# Create metrics alert
az monitor metrics alert create --name alert-02 --resource-group ContosoRG \
   --scopes $scope --condition $condition --description "Service Plan High CPU"
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Si vous avez créé des groupes de ressources pour tester ces commandes, vous pouvez supprimer un groupe de ressources et tout son contenu en utilisant la commande [az group delete](/cli/azure/group#az-group-delete) :

```azurecli
az group delete --name ContosoVMRG

az group delete --name ContosoRG
```

Si vous avez utilisé des ressources existantes que vous souhaitez conserver, utilisez la commande [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete) pour supprimer vos alertes :

```azurecli
az monitor metrics alert delete --name alert-01

az monitor metrics alert delete --name alert-02
```

## <a name="azure-cli-commands-used-in-this-article"></a>Commandes Azure CLI utilisées dans cet article

Cet article utilise les commandes Azure CLI suivantes :

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az appservice plan show](/cli/azure/appservice/plan#az_appservice_plan_show)
- [az group create](/cli/azure/group#az-group-create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor action-group create](/cli/azure/monitor/action-group#az_monitor_action_group_create)
- [az monitor metrics alert condition create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-condition-create)
- [az monitor metrics alert create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-create)
- [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete)
- [az monitor metrics alert dimension create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-dimension-create)
- [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>Étapes suivantes

- [Exemples d’interface CLI Azure Monitor](cli-samples.md)
- [Comprendre le fonctionnement des alertes de métrique dans Azure Monitor](alerts/alerts-metric-overview.md)
