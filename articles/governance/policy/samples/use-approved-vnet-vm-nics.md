---
title: Exemple - Utiliser un réseau virtuel répertorié pour les interfaces réseau de machine virtuelle
description: Cet exemple de définition de stratégie exige que les interfaces réseau utilisent un réseau virtuel approuvé défini dans un paramètre.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 367edbe2f127c363a19e345f7d55ba125006c999
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463090"
---
# <a name="sample---use-approved-vnet-for-vm-network-interfaces"></a>Exemple - Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle

Cette stratégie exige que les interfaces réseau utilisent un réseau virtuel approuvé. Spécifiez l’identifiant du réseau virtuel approuvé.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exemple de modèle

[!code-json[main](../../../../policy-templates/samples/Network/vm-creation-in-approved-vnet/azurepolicy.json "Use approved vNet for VM network interfaces")]

Vous pouvez déployer ce modèle en utilisant le [portail Azure](#deploy-with-the-portal), avec [PowerShell](#deploy-with-powershell) ou avec [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Déployer avec le portail

[![Déployer l’exemple de stratégie dans Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fvm-creation-in-approved-vnet%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "vm-creation-in-approved-vnet" -DisplayName "Use approved vNet for VM network interfaces" -description "This policy enforces VM network interfaces to use vNet." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/vm-creation-in-approved-vnet/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/vm-creation-in-approved-vnet/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -vNetId <vNet Id> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Nettoyer un déploiement PowerShell

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'vm-creation-in-approved-vnet' --display-name 'Use approved vNet for VM network interfaces' --description 'This policy enforces VM network interfaces to use vNet.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/vm-creation-in-approved-vnet/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/vm-creation-in-approved-vnet/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "vm-creation-in-approved-vnet"
```

### <a name="clean-up-azure-cli-deployment"></a>Nettoyer un déploiement Azure CLI

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

- Consulter d’autres exemples dans [Exemples Azure Policy](index.md)