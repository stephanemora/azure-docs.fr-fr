---
title: Exemple - Effectuer un audit pour vérifier si les applications n’ont pas été installées sur les machines virtuelles Linux
description: Dans cet exemple, l’initiative de configuration de stratégie d’invité et ses définitions vérifient si les applications spécifiées sont installées sur les machines virtuelles Linux.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 05/02/2019
ms.author: dacoulte
ms.openlocfilehash: ef2ab4bebf2247b08cdc80ed74bbe17a67c5baae
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977036"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Exemple - Effectuer un audit pour vérifier si les applications spécifiées n’ont pas été installées sur les machines virtuelles Linux

L’initiative de configuration de stratégie d’invité crée un événement d’audit si les applications spécifiées ne sont pas installées sur les machines virtuelles Linux. L’ID de cette initiative intégrée est `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Toutes les initiatives de configuration d’invité sont composées des définitions de stratégie **audit** et **deployIfNotExists**. Si vous n’affectez qu’une seule de ces définitions de stratégie, la configuration d’invité ne fonctionnera pas correctement.

Vous pouvez affecter cet exemple avec :

- [Portail Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Composants d’une initiative

L’initiative [Configuration d’invité](../concepts/guest-configuration.md) est constituée des stratégies suivantes :

- [audit](#audit-definition) : effectuer un audit pour vérifier si les applications n’ont pas été installées sur les machines virtuelles Linux
  - ID : `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) : déployer l’extension de la machine virtuelle pour vérifier si les applications n’ont pas été installées sur les machines virtuelles Linux
  - ID : `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Définition d’initiative

L’initiative est créée en joignant les [paramètres de l’initiative](#initiative-parameters) aux définitions **audit** et **deployIfNotExists**. Il s’agit du code JSON de la définition.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Paramètres d’initiative

|Nom |type |Description |
|---|---|---|
|applicationName |Chaîne |Noms des applications. Exemple : 'python', 'powershell' ou une liste séparée par des virgules, par exemple, 'python,powershell'. Utilisez \* pour effectuer une recherche par caractère générique, comme 'power\*'. |

Lors de la création d’une affectation via PowerShell ou l’interface de ligne de commande Azure (Azure CLI), les valeurs de paramètres peuvent être passées au format JSON dans une chaîne ou via un fichier à l’aide de `-PolicyParameter` (PowerShell) ou de `--params` (Azure CLI).
PowerShell prend également en charge `-PolicyParameterObject` qui nécessite de passer une table de hachage Nom/Valeur à l’applet de commande, où **Nom** est le nom du paramètre et **Valeur** est la valeur unique ou le tableau des valeurs transmises pendant l’affectation.

Dans cet exemple de paramètre, l’installation des applications _python_ et _powershell_ est auditée.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Seule la définition de stratégie **deployIfNotExists** utilise les paramètres de l’initiative.

### <a name="audit-definition"></a>Définition d’audit

Code JSON qui définit les règles de la définition de stratégie **audit**.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>Définition deployIfNotExists

Code JSON qui définit les règles de la définition de stratégie **deployIfNotExists**.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

La définition de stratégie **deployIfNotExists** définit les images Azure à l’aide desquelles la stratégie a été validée :

|Publisher |Offre |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Toutes sauf 6\* |
|Red Hat |RHEL |Toutes sauf 6\* |
|Red Hat |osa | Tous |
|credativ |Debian | Toutes sauf 7\* |
|SUSE |SLES\* |Toutes sauf 11\* |
|Canonical| UbuntuServer |Toutes sauf 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Tous |
|microsoft-dsvm |azureml |Tous |
|cloudera |cloudera-centos-os |Toutes sauf 6\* |
|cloudera |cloudera-altus-centos-os |Tous |
|microsoft-ads |linux\* |Tous |
|microsoft-aks |Tous |Tous |
|AzureDatabricks |Tous |Tous |
|qubole-inc |Tous |Tous |
|datastax |Tous |Tous |
|couchbase |Tous |Tous |
|scalegrid |Tous |Tous |
|checkpoint |Tous |Tous |
|paloaltonetworks |Tous |Tous |

La partie **deployment** de la règle passe le paramètre _installedApplication_ à l’agent Guest Configuration sur la machine virtuelle. Cette configuration permet à l’agent d’effectuer les validations et de confirmer la conformité via la définition de stratégie **audit**.

## <a name="azure-portal"></a>Portail Azure

Après la création des définitions **audit** et **deployIfNotExists** dans le portail, il est recommandé de les regrouper dans une [initiative](../concepts/definition-structure.md#initiatives) en vue de les attribuer.

### <a name="create-copy-of-audit-definition"></a>Créer une copie d’une définition d’audit

[![Déployer l’exemple de stratégie dans Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Déployer l’exemple de stratégie dans Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Lorsque vous utilisez ces boutons pour effectuer le déploiement via le portail, cela crée une copie de la définition de stratégie **audit**.
Sans la définition de stratégie **deployIfNotExists** associée, la configuration d’invité ne fonctionnera pas correctement.

### <a name="create-copy-of-deployifnotexists-definition"></a>Créer une copie de la définition deployIfNotExists

[![Déployer l’exemple de stratégie dans Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Déployer l’exemple de stratégie dans Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Lorsque vous utilisez ces boutons pour effectuer le déploiement via le portail, cela crée une copie de la définition de stratégie **deployIfNotExists**. Sans la définition de stratégie **audit** associée, la configuration d’invité ne fonctionnera pas correctement.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Déployer avec Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Copier et attribuer l’initiative

Les étapes suivantes créent une copie de l’initiative comprenant les stratégies intégrées de **audit** et de **deployIfNotExists**, puis attribuent l’initiative à un groupe de ressources.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Exécutez les commandes suivantes pour supprimer l’affectation et la définition précédentes :

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Copier et attribuer la définition audit

Les étapes suivantes créent une copie de la définition **audit**, puis l’attribuent à un groupe de ressources. Cette définition ne fonctionnera pas correctement si vous n’attribuez pas également la définition **deployIfNotExists** associée.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Exécutez les commandes suivantes pour supprimer l’affectation et la définition précédentes :

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Copier et attribuer la définition deployIfNotExists

Les étapes suivantes créent une copie de la définition **deployIfNotExists**, puis l’attribuent à un groupe de ressources.
Cette définition ne fonctionnera pas correctement si vous n’attribuez pas également la définition **audit** associée.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Exécutez les commandes suivantes pour supprimer l’affectation et la définition précédentes :

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Explication Azure PowerShell

Les scripts de déploiement et de suppression utilisent les commandes suivantes. Chaque commande du tableau suivant renvoie à une documentation spécifique :

| Commande | Notes |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Crée une initiative Azure Policy. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Crée une définition Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Obtient un groupe de ressources unique. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Crée une nouvelle attribution Azure Policy pour une initiative ou une définition. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Affecte une attribution de rôle existante à un principal. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Supprime une affectation Azure Policy existante. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Supprime une initiative. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Supprime une définition. |

## <a name="next-steps"></a>Étapes suivantes

- Consulter d’autres [exemples Azure Policy](index.md)
- En savoir plus sur la [Configuration d’invité Azure Policy](../concepts/guest-configuration.md).
- Consulter [Structure de définition Azure Policy](../concepts/definition-structure.md)
