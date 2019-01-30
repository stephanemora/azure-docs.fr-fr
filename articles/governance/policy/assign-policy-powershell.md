---
title: Créer une stratégie pour identifier les ressources non conformes avec Azure PowerShell
description: Utilisez Azure PowerShell pour créer une affectation de stratégie Azure afin d’identifier les ressources non conformes.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b5f4306fc1627e679f8f59a92bae4124a48cbd42
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856466"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Créer une affectation de stratégie pour identifier les ressources non conformes à l’aide d’Azure PowerShell

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources. Dans ce démarrage rapide, vous créerez une affectation de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques managés. Lorsque vous aurez terminé, vous identifierez les machines virtuelles qui ne sont *pas conformes* à l’attribution de stratégie.

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans des scripts. Ce guide explique comment utiliser Az pour créer une attribution de stratégie. La stratégie identifie les ressources non conformes dans votre environnement Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

- Si ce n’est pas déjà fait, installez [ARMClient](https://github.com/projectkudu/ARMClient). C’est un outil qui envoie des requêtes HTTP aux API Azure Resource Manager.
- Avant de commencer, assurez-vous que la dernière version d’Azure PowerShell est installée. Consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps) pour plus d’informations.
- Inscrivez le fournisseur de ressources Policy Insights à l’aide d’Azure PowerShell. L’inscription du fournisseur de ressources permet de s’assurer que votre abonnement fonctionne avec lui. Pour inscrire un fournisseur de ressources, vous devez avoir l’autorisation pour une opération de fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire. Exécutez la commande suivante pour enregistrer le fournisseur de ressources :

  ```azurepowershell-interactive
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Pour plus d’informations sur l’inscription et l’affichage des fournisseurs de ressources, consultez [Fournisseurs et types de ressources](../../azure-resource-manager/resource-manager-supported-services.md).

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous allez créer une affectation de stratégie et affecter la définition *Audit VMs without managed disks* (Auditer les machines virtuelles sans disques managés). Cette stratégie identifie les ressources qui ne sont pas conformes aux conditions définies dans sa définition.

Exécutez la commande suivante pour créer une nouvelle attribution de stratégie :

```azurepowershell-interactive
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Les commandes précédentes utilisent les informations suivantes :

- **Name** : nom réel de l’attribution.  Pour cet exemple, *audit-vm-manageddisks* a été utilisé.
- **DisplayName** : nom d’affichage pour l’attribution de stratégie. Dans ce cas, nous allons utiliser l’affectation *Audit VMs without managed disks* (Auditer les machines virtuelles sans disques managés).
- **Definition** : définition de la stratégie, que vous utilisez pour créer l’attribution. Dans ce cas, il s’agit de l’ID de la définition de stratégie *Auditer les machines virtuelles qui n’utilisent pas de disques managés*.
- **Scope** : une étendue détermine les ressources ou le regroupement de ressources sur lequel l’attribution de stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources. Assurez-vous de remplacer &lt;scope&gt; par le nom de votre groupe de ressources.

Vous êtes maintenant prêt à identifier les ressources non conformes pour comprendre l’état de conformité de votre environnement.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Utilisez les informations suivantes pour identifier les ressources qui ne sont pas conformes à l’attribution de stratégie que vous avez créée. Exécutez les commandes suivantes :

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Pour plus d’informations sur les ID d’affectation de stratégie, consultez [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Exécutez ensuite la commande suivante pour obtenir les ID des ressources non conformes, intégrés dans un fichier JSON :

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Vos résultats doivent ressembler à l’exemple suivant :

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Les résultats sont comparables à ce que vous devriez généralement voir sous **Ressources non conformes** dans la vue du portail Azure.

## <a name="clean-up-resources"></a>Supprimer des ressources

Utilisez la commande suivante pour supprimer l’affectation créée :

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie pour identifier les ressources non conformes de votre environnement Azure.

Pour en savoir plus sur l’affectation de stratégies pour vérifier que les nouvelles ressources sont conformes, suivez le didacticiel :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./tutorials/create-and-manage.md)