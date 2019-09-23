---
title: Créer une stratégie pour des ressources non conformes avec Azure PowerShell
description: Utilisez Azure PowerShell pour créer une affectation de stratégie Azure afin d’identifier les ressources non conformes.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: c18e48d76381bf40e6177feb389a0197aace081d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000850"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Démarrage rapide : Créer une affectation de stratégie pour identifier les ressources non conformes à l’aide d’Azure PowerShell

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources. Dans ce démarrage rapide, vous créerez une affectation de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques managés. Lorsque vous aurez terminé, vous identifierez les machines virtuelles qui ne sont *pas conformes*.

Le module Azure PowerShell est utilisé pour gérer des ressources Azure à partir de la ligne de commande ou dans des scripts.
Ce guide explique comment utiliser un module Az pour créer une attribution de stratégie.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Avant de commencer, assurez-vous que la dernière version d’Azure PowerShell est installée. Pour plus d'informations, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).
- Inscrivez le fournisseur de ressources Azure Policy Insights à l’aide d’Azure PowerShell. L’inscription du fournisseur de ressources permet de s’assurer que votre abonnement fonctionne avec lui. Pour inscrire un fournisseur de ressources, vous devez avoir l’autorisation pour une opération de fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire. Exécutez la commande suivante pour enregistrer le fournisseur de ressources :

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Pour plus d’informations sur l’inscription et l’affichage des fournisseurs de ressources, consultez [Fournisseurs et types de ressources](../../azure-resource-manager/resource-manager-supported-services.md).

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous créez une attribution de stratégie pour la définition *Auditer des machines virtuelles sans disques managés*. Cette définition de stratégie identifie des machines virtuelles n’utilisant pas de disques managés.

Exécutez la commande suivante pour créer une nouvelle attribution de stratégie :

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Les commandes précédentes utilisent les informations suivantes :

- **Name** : nom réel de l’attribution. Pour cet exemple, *audit-vm-manageddisks* a été utilisé.
- **DisplayName** : nom d’affichage pour l’attribution de stratégie. Dans ce cas, nous allons utiliser l’affectation *Audit VMs without managed disks* (Auditer les machines virtuelles sans disques managés).
- **Definition** : définition de la stratégie, que vous utilisez pour créer l’attribution. Dans ce cas, il s’agit de l’ID de la définition de stratégie *Auditer les machines virtuelles qui n’utilisent pas de disques managés*.
- **Scope** : une étendue détermine les ressources ou le regroupement de ressources sur lequel l’attribution de stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources. Assurez-vous de remplacer &lt;scope&gt; par le nom de votre groupe de ressources.

Vous êtes maintenant prêt à identifier les ressources non conformes pour comprendre l’état de conformité de votre environnement.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Utilisez les informations suivantes pour identifier les ressources qui ne sont pas conformes à l’attribution de stratégie que vous avez créée. Exécutez les commandes suivantes :

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Pour plus d’informations sur l’obtention de l’état de la stratégie, voir [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Vos résultats doivent ressembler à l’exemple suivant :

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Les résultats correspondent à ce que vous voyez sous l’onglet **Conformité des ressources** d’une attribution de stratégie dans la vue du portail Azure.

## <a name="clean-up-resources"></a>Supprimer des ressources

Utilisez la commande suivante pour supprimer l’affectation créée :

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie pour identifier les ressources non conformes de votre environnement Azure.

Pour en savoir plus sur l’affectation de stratégies pour vérifier que les nouvelles ressources sont conformes, suivez le didacticiel :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./tutorials/create-and-manage.md)