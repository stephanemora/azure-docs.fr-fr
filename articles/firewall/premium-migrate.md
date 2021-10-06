---
title: Migrer vers le Pare-feu Azure Premium
description: Découvrez comment migrer du Pare-feu Azure Standard vers le Pare-feu Azure Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 09/13/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 580dcb11ae04aaae78d2c15f24c2c08d1df6158d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061843"
---
# <a name="migrate-to-azure-firewall-premium"></a>Migrer vers le Pare-feu Azure Premium

Vous pouvez migrer du Pare-feu Azure Standard vers le Pare-feu Azure Premium pour tirer parti des nouvelles fonctionnalités Premium. Pour plus d’informations sur les fonctionnalités du Pare-feu Azure Premium, consultez [Fonctionnalités du Pare-feu Azure Premium](premium-features.md).

Les exemples suivants montrent comment :
- Migrer une stratégie standard existante à l’aide d’Azure PowerShell
- Migrez un pare-feu standard existant (avec les règles classiques) vers le Pare-feu Azure Premium avec une stratégie Premium.

Si vous utilisez Terraform pour déployer le Pare-feu Azure, vous pouvez utiliser Terraform pour migrer vers le Pare-feu Azure Premium. Pour plus d'informations, consultez [Migrer le Pare-feu Azure Standard vers la version Premium à l'aide de Terraform](/azure/developer/terraform/firewall-upgrade-premium?toc=/azure/firewall/toc.json&bc=/azure/firewall/breadcrumb/toc.json).

## <a name="performance-considerations"></a>Considérations relatives aux performances

Les performances sont un facteur à prendre en compte lors de la migration à partir de la référence SKU standard. L’inspection IDPS et TLS sont des opérations gourmandes en ressources de calcul. La référence SKU Premium utilise une référence SKU de machine virtuelle plus puissante qui s’adapte à un débit maximal de 30 Gbits/s comparables à la référence SKU standard. Le débit de 30 Gbits/s est pris en charge lorsqu’il est configuré avec IDPS en mode alerte. L’utilisation d’IDPS en mode de refus et de l’inspection TLS augmente la consommation du processeur. Une dégradation du débit maximal peut se produire. 

Le débit du pare-feu peut être inférieur à 30 Gbits/s quand une ou plusieurs signatures sont définies sur **Alerter et refuser** ou l’**inspection TLS** est activée pour des règles d’application. Microsoft recommande aux clients d’effectuer des tests à grande échelle lors de leur déploiement Azure pour s’assurer que les performances du service de pare-feu répondent aux attentes.

## <a name="downtime"></a>Temps d’arrêt

Effectuez la migration de votre pare-feu pendant une période de maintenance planifiée, car il y aura un certain temps d’arrêt pendant la migration.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrer une stratégie existante à l’aide d’Azure PowerShell

`Transform-Policy.ps1` est un script Azure PowerShell qui crée une stratégie Premium à partir d’une stratégie standard existante.

En fonction d’un ID de stratégie de pare-feu standard, le script le transforme en stratégie de Pare-feu Azure Premium. Le script se connecte d’abord à votre compte Azure, extrait la stratégie, transforme/ajoute différents paramètres, puis charge une nouvelle stratégie Premium. La nouvelle stratégie Premium est appelée `<previous_policy_name>_premium`. Dans le cas d'une transformation de stratégie enfant, le lien vers la stratégie parente est conservé.

Exemple d’utilisation :

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Le script ne migre pas les paramètres Threat Intelligence. Vous devez noter ces paramètres avant de continuer et les migrer manuellement.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy.Id
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5.0)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy

```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Migrer un pare-feu standard existant à l’aide du portail Azure

Cet exemple montre comment utiliser le portail Azure pour migrer un pare-feu standard (règles classiques) vers le Pare-feu Azure Premium avec une stratégie Premium.

1. Dans le portail Azure, sélectionnez votre pare-feu standard. Sur la page **Vue d’ensemble**, sélectionnez **Migrer vers une stratégie de pare-feu**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrer vers une stratégie de pare-feu":::

1. Sur la page **Migrer vers une stratégie de pare-feu**, sélectionnez **Vérifier + créer**.
1. Sélectionnez **Create** (Créer).

   Le déploiement prend quelques minutes.
1. Utilisez le `Transform-Policy.ps1`script [Azure PowerShell ](#migrate-an-existing-policy-using-azure-powershell) pour transformer cette nouvelle stratégie standard en stratégie Premium.
1. Dans le portail, sélectionnez votre ressource de pare-feu standard. 
1. Sous **Automation**, sélectionnez **Exporter le modèle**. Laissez cet onglet de navigateur ouvert. Vous y reviendrez plus tard.
   > [!TIP]
   > Pour vous assurer de ne pas perdre le modèle, téléchargez-le et enregistrez-le au cas où votre onglet de navigateur venait à être fermé ou actualisé.
1. Ouvrez un nouvel onglet de navigateur, accédez au portail Azure et ouvrez le groupe de ressources contenant votre pare-feu.
1. Supprimez l’instance de pare-feu standard existante.

   Ceci prend quelques minutes.

1. Revenez à l’onglet du navigateur avec le modèle exporté.
1. Sélectionnez **Déployer**, puis dans la page **Déploiement personnalisé**, sélectionnez **Modifier un modèle**.
1. Modifier le texte du modèle :
   
   1. Sous la ressource `Microsoft.Network/azureFirewalls`, sous `Properties`, `sku`, basculez `tier` de « Standard » vers « Premium ».
   1. Sous le modèle `Parameters` , modifiez `defaultValue` pour `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` à partir de :
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      to:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Sélectionnez **Enregistrer**.
1. Sélectionnez **Vérifier + créer**.
1. Sélectionnez **Create** (Créer).

Une fois le déploiement terminé, vous pouvez configurer toutes les nouvelles fonctionnalités du Pare-feu Azure Premium.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les fonctionnalités du Pare-feu Azure Premium](premium-features.md)
