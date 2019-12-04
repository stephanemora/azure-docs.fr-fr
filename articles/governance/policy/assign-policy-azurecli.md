---
title: 'Démarrage rapide : Nouvelle affectation de stratégie avec Azure CLI'
description: Dans ce guide de démarrage rapide, vous allez utiliser Azure CLI pour créer une attribution Azure Policy afin d’identifier les ressources non conformes.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 80dbccdb728da94d9f9fdd0aeb506ade40fd7394
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482635"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Démarrage rapide : Créer une affectation de stratégie pour identifier les ressources non conformes avec Azure CLI

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources.
Ce démarrage rapide vous guide pas à pas dans le processus de création d’une attribution de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques managés.

À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques managés. Elles sont _non conformes_ à l’attribution de stratégie.

Azure CLI permet de créer et de gérer des ressources Azure à partir de la ligne de commande ou dans des scripts. Ce guide utilise l’interface de ligne de commande Azure pour créer une attribution de stratégie et identifier les ressources non conformes dans votre environnement Azure.

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

- Ce guide de démarrage rapide nécessite que vous exécutiez la version 2.0.76 (ou ultérieure) d’Azure CLI pour installer et utiliser l’interface de ligne de commande localement. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

- Inscrivez le fournisseur de ressources Azure Policy Insights à l'aide d'Azure CLI. L’inscription du fournisseur de ressources permet de s’assurer que votre abonnement fonctionne avec lui. Pour inscrire un fournisseur de ressources, vous devez avoir l’autorisation pour une opération de fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire. Exécutez la commande suivante pour enregistrer le fournisseur de ressources :

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Pour plus d’informations sur l’inscription et l’affichage des fournisseurs de ressources, consultez [Fournisseurs et types de ressources](../../azure-resource-manager/resource-manager-supported-services.md).

- Si ce n’est pas déjà fait, installez [ARMClient](https://github.com/projectkudu/ARMClient). C’est un outil qui envoie des requêtes HTTP aux API Azure Resource Manager.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous créez une attribution de stratégie et affectez la définition **Auditer les machines virtuelles qui n’utilisent pas de disques managés**. La définition de cette stratégie identifie les ressources qui ne sont pas conformes aux conditions définies dans la définition de stratégie.

Exécutez la commande suivante pour créer une attribution de stratégie :

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

La commande précédente utilise les informations suivantes :

- **Name** : nom réel de l’attribution. Pour cet exemple, _audit-vm-manageddisks_ a été utilisé.
- **DisplayName** : nom d’affichage pour l’attribution de stratégie. Dans ce cas, nous allons utiliser l’affectation _Audit VMs without managed disks_ (Auditer les machines virtuelles sans disques managés).
- **Policy** : ID de définition de la stratégie, que vous utilisez pour créer l’attribution. Dans ce cas, il s’agit de l’ID de la définition de stratégie _Auditer les machines virtuelles qui n’utilisent pas de disques managés_. Pour obtenir l’ID de définition de stratégie, exécutez cette commande : `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Scope** : une étendue détermine les ressources ou le regroupement de ressources sur lequel l’attribution de stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources. Assurez-vous de remplacer &lt;scope&gt; par le nom de votre groupe de ressources.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Pour afficher les ressources qui ne sont pas conformes à cette nouvelle attribution, obtenez l’ID d’attribution de stratégie en exécutant les commandes suivantes :

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Pour plus d’informations sur les ID d’affectation de stratégie, consultez [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Exécutez ensuite la commande suivante pour obtenir les ID des ressources non conformes, intégrés dans un fichier JSON :

```console
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

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie pour identifier les ressources non conformes de votre environnement Azure.

Pour en savoir plus sur l’affectation de stratégies pour vérifier que les nouvelles ressources sont conformes, suivez le didacticiel :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./tutorials/create-and-manage.md)