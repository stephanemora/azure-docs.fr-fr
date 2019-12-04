---
title: Exemple - Garantir le trafic HTTPS pour les comptes de stockage
description: Cet exemple de définition de stratégie exige que tous les comptes de stockage Azure autorisent uniquement le trafic HTTPS sécurisé.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 4d6b6f7e494d2f8c5278c546ead1f9697f245f38
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463593"
---
# <a name="sample---ensure-https-traffic-only-for-storage-account"></a>Exemple - Garantir le trafic HTTPS pour le compte de stockage uniquement

Cette stratégie exige que les comptes de stockage utilisent le trafic HTTPS.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exemple de modèle

[!code-json[main](../../../../policy-templates/samples/Storage/https-traffic-only/azurepolicy.json "Ensure https traffic only for storage account")]

Vous pouvez déployer ce modèle en utilisant le [portail Azure](#deploy-with-the-portal), avec [PowerShell](#deploy-with-powershell) ou avec [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Déployer avec le portail

[![Déployer l’exemple de stratégie dans Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FStorage%2Fhttps-traffic-only%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "https-traffic-only" -DisplayName "Ensure https traffic only for storage account" -description "Ensure https traffic only for storage account" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
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
az policy definition create --name 'https-traffic-only' --display-name 'Ensure https traffic only for storage account' --description 'Ensure https traffic only for storage account' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "https-traffic-only"
```

### <a name="clean-up-azure-cli-deployment"></a>Nettoyer un déploiement Azure CLI

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

- Consulter d’autres exemples dans [Exemples Azure Policy](index.md)