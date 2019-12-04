---
title: Historique de déploiement
description: Décrit comment afficher les opérations de déploiement d’Azure Resource Manager avec le portail, PowerShell, la CLI Azure et l’API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 895704e5c4cb8acc60067809bdd7e7baa6f05142
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538895"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Afficher l’historique des déploiements avec Azure Resource Manager

Azure Resource Manager vous permet d’afficher l’historique des déploiements et d’examiner des opérations spécifiques liées aux derniers déploiements. Vous pouvez voir les ressources qui ont été déployées et obtenir des informations relatives aux erreurs.

Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Récupérer des déploiements et l’ID de corrélation

Vous pouvez afficher les détails d’un déploiement via le Portail Azure, PowerShell, Azure CLI ou l’API REST. Chaque déploiement a un ID de corrélation, qui est utilisé pour suivre les événements connexes. Cela peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème de déploiement.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Sélectionnez le groupe de ressources que vous souhaitez examiner.

1. Sélectionnez le lien sous **Déploiements**.

   ![Sélectionner l’historique des déploiements](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Sélectionnez un des déploiements dans l’historique des déploiements.

   ![Sélectionner un déploiement](./media/resource-manager-deployment-operations/select-details.png)

1. Un résumé du déploiement s’affiche, y compris l’ID de corrélation. 

    ![Résumé du déploiement](./media/resource-manager-deployment-operations/show-correlation-id.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pour répertorier tous les déploiements d’un groupe de ressources, utilisez la commande [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment).

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Pour obtenir un déploiement spécifique à partir d’un groupe de ressources, ajoutez le paramètre **DeploymentName**.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Pour obtenir l’ID de corrélation, utilisez :

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Pour répertorier le déploiement d’un groupe de ressources, utilisez la commande [az group deployment list](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-list).

```azurecli-interactive
az group deployment list --resource-group ExampleGroup
```

Pour obtenir un déploiement spécifique, utilisez la commande [az group deployment show](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-show).

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment
```
  
Pour obtenir l’ID de corrélation, utilisez :

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Pour répertorier les déploiements d’un groupe de ressources, utilisez l’opération suivante. Pour obtenir le numéro de version le plus récent de l’API à utiliser dans la requête, consultez [Déploiements – Liste par groupe de ressources](/rest/api/resources/deployments/listbyresourcegroup). 

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Pour obtenir un déploiement spécifique, utilisez l’opération suivante. Pour obtenir le numéro de version le plus récent de l’API à utiliser dans la requête, consultez [Déploiements – Récupérer](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

La réponse inclut l’ID de corrélation.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Récupérer des opérations de déploiement et un message d’erreur

Chaque déploiement peut comprendre plusieurs opérations. Pour plus d’informations sur un déploiement, consultez les opérations de déploiement. En cas d’échec d’un déploiement, les opérations de déploiement incluent un message d’erreur.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Dans le résumé d’un déploiement, sélectionnez **Détails des opérations**.

    ![Sélectionner les opérations de déploiement](./media/resource-manager-deployment-operations/get-operation-details.png)

1. Vous voyez les détails de cette étape du déploiement. Lorsqu’une erreur se produit, les détails incluent le message d’erreur.

    ![Afficher les détails de l’opération](./media/resource-manager-deployment-operations/see-operation-details.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pour afficher les opérations de déploiement relatives au déploiement vers un groupe de ressources, utilisez la commande [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation).

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Pour afficher les opérations ayant échoué, filtrez les opérations à l’aide de l’état **Échec**.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Pour obtenir le message d’état d’opérations ayant échoué, utilisez la commande suivante :

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Pour afficher les opérations de déploiement relatives au déploiement vers un groupe de ressources, utilisez la commande [az group deployment operation list](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-group-deployment-operation-list).

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeployment
```

Pour afficher les opérations ayant échoué, filtrez les opérations à l’aide de l’état **Échec**.

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Pour obtenir le message d’état d’opérations ayant échoué, utilisez la commande suivante :

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Pour récupérer des opérations de déploiement, utilisez l’opération suivante. Pour obtenir le numéro de version le plus récent de l’API à utiliser dans la requête, consultez [Opérations de déploiement – Liste](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

La réponse comprend un message d’erreur.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour en savoir plus sur l’utilisation des journaux d’activité pour surveiller d’autres types d’actions, consultez [Afficher les journaux d’activité pour gérer les ressources Azure](resource-group-audit.md).
* Pour valider votre déploiement avant son exécution, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

