---
title: Valider la configuration de compte Azure Automation
description: Cet article décrit comment vérifier que votre compte Automation est correctement configuré.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 32b73cf4570393ed24ee6d1121aef75aaf193134
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713752"
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Test d’authentification du compte d’identification Azure Automation

Une fois qu’un compte Automation est correctement créé, vous pouvez effectuer un test simple pour confirmer que vous êtes en mesure de vous authentifier dans Azure Resource Manager ou un déploiement classique Azure à l’aide de votre compte d’identification Automation nouvellement créé ou mis à jour.

## <a name="automation-run-as-authentication"></a>Authentification d’identification Automation

Utilisez l’exemple de code ci-dessous pour [créer un runbook PowerShell](automation-creating-importing-runbook.md) afin de vérifier l’authentification à l’aide d’un compte d’identification. Utilisez-le aussi sur votre runbook personnalisé pour vous authentifier et gérer les ressources du Gestionnaire des ressources avec un compte Automation.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

    $logonAttempt = 0
    $logonResult = $False

    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
    # Logging in to Azure...
    $connectionResult = Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

     Start-Sleep -Seconds 30
    }
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Get all ARM resources from all resource groups
$ResourceGroups = Get-AzureRmResourceGroup

foreach ($ResourceGroup in $ResourceGroups)
{
    Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
    $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
    ForEach ($Resource in $Resources)
    {
        Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
    }
    Write-Output ("")
}
```

Notez que l’applet de commande utilisée pour l’authentification (**Connect-AzureRmAccount**) dans le Runbook utilise le jeu de paramètres *ServicePrincipalCertificate*.  Elle effectue l’authentification à l’aide du certificat du principal du service et non des informations d’identification.  

> [!IMPORTANT]
> **Add-AzureRmAccount** est désormais un alias de **Connect-AzureRMAccount**. Quand vous effectuez une recherche dans vos éléments de bibliothèque, si vous ne voyez pas **Connect-AzureRMAccount**, vous pouvez utiliser **Add-AzureRmAccount** ou mettre à jour vos modules dans votre compte Automation.

Quand vous [exécutez un runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) pour valider un compte d’identification, un [travail du runbook](automation-runbook-execution.md) est créé. La page du travail s’affiche alors et l’état du travail est indiqué dans la vignette **Résumé du travail**. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.

Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie** .  La page **Sortie** doit indiquer qu’il a authentifié et retourné la liste de toutes les ressources de l’ensemble des groupes de ressources de votre abonnement.  

Pensez à supprimer le bloc de code en commençant par le commentaire `#Get all ARM resources from all resource groups` lorsque vous réutiliser le code de vos runbooks.

## <a name="classic-run-as-authentication"></a>Authentification d’identification Classic

Utilisez l’exemple de code ci-dessous pour [créer un runbook PowerShell](automation-creating-importing-runbook.md) afin de vérifier l’authentification à l’aide d’un compte d’identification Classic. Utilisez-le aussi sur votre runbook personnalisé pour vous authentifier et gérer les ressources dans le modèle de déploiement Classic.  

```powershell
$ConnectionAssetName = "AzureClassicRunAsConnection"
# Get the connection
$connection = Get-AutomationConnection -Name $connectionAssetName

# Authenticate to Azure with certificate
Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
$Conn = Get-AutomationConnection -Name $ConnectionAssetName
if ($Conn -eq $null)
{
    throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
}

$CertificateAssetName = $Conn.CertificateAssetName
Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
$AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
if ($AzureCert -eq $null)
{
    throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
}

Write-Verbose "Authenticating to Azure with certificate." -Verbose
Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

#Get all VMs in the subscription and return list with name of each
Get-AzureVM | ft Name
```

Lorsque vous [exécutez un runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) pour valider un compte d’identification, un [travail du runbook](automation-runbook-execution.md) est créé. La page Travail s’affiche alors et l’état du travail est indiqué dans la mosaïque **Résumé du travail**. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.

Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie** .  La page **Sortie** doit indiquer qu’il a authentifié et retourné la liste de toutes les machines virtuelles Azure de VMName qui sont déployées dans votre abonnement.  

N’oubliez pas de supprimer le cmdlet **Get-AzureVM** lorsque vous réutilisez le code de vos runbooks.

## <a name="next-steps"></a>Étapes suivantes

* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
