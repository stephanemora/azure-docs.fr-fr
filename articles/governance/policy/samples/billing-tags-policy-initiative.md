---
title: 'Exemple : Initiative de la stratégie de facturation en fonction de balises'
description: Cet exemple de stratégie exige des valeurs d’étiquette spécifiées pour le nom de produit et le centre de coûts.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.openlocfilehash: 1929cb7287460557cf815b62bd5d31ffe38ab161
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317776"
---
# <a name="billing-tags-policy-initiative"></a>Initiative de la stratégie de facturation en fonction d’étiquettes

Cette stratégie exige des valeurs d’étiquette spécifiées pour le nom de produit et le centre de coûts. Utilise des stratégies intégrées pour appliquer les étiquettes nécessaires. Spécifiez les valeurs nécessaires pour les balises.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exemple de modèle

[!code-json[main](../../../../policy-templates/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.json "Billing Tags Policy Initiative")]

Vous pouvez déployer ce modèle en utilisant le [portail Azure](#deploy-with-the-portal) ou avec [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Déployer avec le portail

[![Déployer sur Azure](http://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.parameters.json"

$policyset= New-AzureRmPolicySetDefinition -Name "multiple-billing-tags" -DisplayName "Billing Tags Policy Initiative" -Description "Specify cost Center tag and product name tag" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzureRmPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -costCenterValue <required value for Cost Center tag> -productNameValue <required value for product Name tag>
```

### <a name="clean-up-powershell-deployment"></a>Nettoyer un déploiement PowerShell

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="apply-tags-to-existing-resources"></a>Appliquer des balises aux ressources existantes

Après avoir attribué les stratégies, vous pouvez déclencher une mise à jour sur toutes les ressources existantes pour appliquer les stratégies de balise que vous avez ajoutées. Le script suivant conserve toutes les autres balises existant sur les ressources :

```azurepowershell-interactive
$resources = Get-AzureRmResource -ResourceGroupName 'ExampleGroup'

foreach ($r in $resources) {
    try {
        Set-AzureRmResource -Tags ($a = if ($r.Tags -eq $NULL) { @{} } else {$r.Tags}) -ResourceId $r.ResourceId -Force -UsePatchSemantics
    }
    catch {
        Write-Host $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Consulter d’autres exemples dans [Exemples Azure Policy](index.md)