---
title: L’historique de déploiement avec Azure Resource Manager | Microsoft Docs
description: Décrit comment afficher les opérations de déploiement d’Azure Resource Manager avec le portail, PowerShell, la CLI Azure et l’API REST.
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606054"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Afficher l’historique de déploiement avec Azure Resource Manager

Azure Resource Manager vous permet de visualiser votre historique de déploiement et d’examiner des opérations spécifiques dans les derniers déploiements. Vous pouvez voir les ressources qui ont été déployées et obtenir des informations relatives aux erreurs.

Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portail

Pour obtenir des informations sur un déploiement à partir de l’historique de déploiement.

1. Sélectionnez le groupe de ressources que vous souhaitez examiner.

1. Sélectionnez le lien situé sous **déploiements**.

   ![Sélectionnez l’historique de déploiement](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Sélectionnez un des déploiements à partir de l’historique de déploiement.

   ![Sélectionnez le déploiement](./media/resource-manager-deployment-operations/select-details.png)

1. Un résumé du déploiement s’affiche, notamment une liste de ressources qui ont été déployées.

    ![Résumé du déploiement](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Pour afficher le modèle utilisé pour le déploiement, sélectionnez **modèle**. Vous pouvez télécharger le modèle pour la réutiliser.

    ![Afficher le modèle](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Si votre déploiement a échoué, vous voyez le message d’erreur. Sélectionnez le message d’erreur pour plus d’informations.

    ![afficher l’échec du déploiement](./media/resource-manager-deployment-operations/show-error.png)

1. Le message d’erreur détaillé s’affiche.

    ![Afficher les détails de l'erreur](./media/resource-manager-deployment-operations/show-details.png)

1. L’ID de corrélation est utilisé pour effectuer le suivi des événements connexes et peut être utile lorsque vous travaillez avec le support technique pour résoudre les problèmes d’un déploiement.

    ![Obtenir l’ID de corrélation](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Pour en savoir plus sur l’étape qui a échoué, sélectionnez **détails de l’opération**.

    ![Sélectionnez les opérations de déploiement](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Vous consultez les détails pour cette étape du déploiement.

    ![Afficher les détails de l’opération](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour obtenir l’état global d’un déploiement, utilisez la commande **Get-AzResourceGroupDeployment** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Sinon, vous pouvez filtrer les résultats en les restreignant aux déploiements qui ont échoué.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

L’ID de corrélation est utilisé pour effectuer le suivi des événements connexes et peut être utile lorsque vous travaillez avec le support technique pour résoudre les problèmes d’un déploiement. Pour obtenir l’ID de corrélation, utilisez :

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Chaque déploiement comprend plusieurs opérations. Chaque opération représente une étape dans le processus de déploiement. Pour découvrir la cause du problème rencontré lors d’un déploiement, vous devez généralement afficher les détails concernant les opérations de déploiement. Vous pouvez afficher l’état des opérations avec **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Cette requête retourne plusieurs opérations, chacune au format suivant :

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Pour obtenir plus d’informations sur les opérations ayant échoué, récupérez les propriétés des opérations dont l’état est **Failed** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Cette requête retourne toutes les opérations ayant échoué, chacune au format suivant :

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Notez le serviceRequestId et le trackingId de l’opération. L’élément serviceRequestId peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème de déploiement. Vous utiliserez le trackingId à l’étape suivante pour vous concentrer sur une opération particulière.

Pour obtenir le message d’état d’une opération ayant échoué, utilisez la commande suivante :

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Résultat :

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Chaque opération de déploiement dans Azure inclut le contenu de la demande et de la réponse. Au cours du déploiement, vous pouvez utiliser **DeploymentDebugLogLevel** paramètre pour spécifier que la demande et/ou la réponse est enregistré.

Vous obtenez ces informations à partir du journal et les enregistrez localement en utilisant les commandes PowerShell suivantes :

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

Pour obtenir l’état global d’un déploiement, utilisez le **show de déploiement de groupe azure** commande.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
L’ID de corrélation est utilisé pour effectuer le suivi des événements connexes et peut être utile lorsque vous travaillez avec le support technique pour résoudre les problèmes d’un déploiement.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Pour voir les opérations d’un déploiement, utilisez :

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Pour obtenir des informations sur le déploiement, utilisez le [obtenir des informations sur le déploiement d’un modèle](https://docs.microsoft.com/rest/api/resources/deployments) opération.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Dans la réponse, repérez en particulier les éléments **provisioningState**, **correlationId** et **error**. L’élément **correlationId** est utilisé pour effectuer le suivi des événements connexes et peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème de déploiement.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Pour obtenir des informations sur les déploiements, utilisez [répertorier toutes les opérations de déploiement de modèle](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
La réponse inclut des informations sur la demande et/ou la réponse selon ce que vous avez spécifié dans la propriété **debugSetting** pendant le déploiement.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour en savoir plus sur l’utilisation des journaux d’activité pour surveiller d’autres types d’actions, consultez [Afficher les journaux d’activité pour gérer les ressources Azure](resource-group-audit.md).
* Pour valider votre déploiement avant son exécution, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

