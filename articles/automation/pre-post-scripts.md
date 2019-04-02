---
title: Configurer des pré/post-scripts sur votre déploiement Update Management dans Azure (préversion)
description: Cet article explique comment configurer et gérer des pré/post-scripts pour les déploiements de mises à jour.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dc0c516ce9dc3a13474cefc61b6634dbeea0fce0
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793634"
---
# <a name="manage-pre-and-post-scripts-preview"></a>Gérer les pré-scripts et les post-scripts (préversion)

Les pré-scripts et post-scripts vous permettent d’exécuter des runbooks PowerShell dans votre compte Automation avant (tâche préalable) et après (tâche postérieure) un déploiement de mises à jour. Les pré/post-scripts s’exécutent dans le contexte Azure, et non localement. Les scripts s’exécutent au début du déploiement de mise à jour. Les scripts postérieurs s’exécutent à la fin du déploiement et après chaque redémarrage qui a été configuré.

## <a name="runbook-requirements"></a>Exigences en matière de runbook

Le runbook que vous souhaitez utiliser en tant que pré/post-script doit être importé dans votre compte Automation et publié. Pour plus d’informations sur ce processus, consultez la section [Publication d’un runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Utilisation d’un pré/post-script

Pour utiliser un pré-script ou un post-script dans un déploiement de mises à jour, commencez par créer un déploiement de mises à jour. Sélectionnez **Pre-scripts + Post Scripts (Preview)** (Pré-scripts + post-scripts [préversion]). Cette action ouvre la page **Select Pre-scripts + Post-scripts** (Sélectionner des pré-scripts et post-scripts).  

![Sélection des scripts](./media/pre-post-scripts/select-scripts.png)

Sélectionnez le script que vous souhaitez utiliser ; dans cet exemple, vous avez utilisé le runbook **UpdateManagement-TurnOnVms**. Lorsque vous sélectionnez le runbook, la page **Configurer un script** s’affiche. Fournissez les valeurs des paramètres, puis choisissez **Pré-script**. Une fois l’opération terminée, cliquez sur **OK**.

![Configurer un script](./media/pre-post-scripts/configure-script.png)

Répétez ce processus pour le script **UpdateManagement-TurnOffVms**. Toutefois, pour le **Type de script**, choisissez **Post-script** cette fois-ci.

La section **Éléments sélectionnés** présente désormais les scripts que vous avez sélectionnés, l’un en tant que pré-script et l’autre en tant que post-script.

![Éléments sélectionnés](./media/pre-post-scripts/selected-items.png)

Achevez la configuration de votre déploiement de mises à jour.

Une fois votre déploiement de mises à jour terminé, vous pouvez accéder à **Déploiements de mises à jour** pour en visualiser les résultats. Comme vous pouvez le constater, l’état du pré-script et du post-script est fourni.

![Résultats des mises à jour](./media/pre-post-scripts/update-results.png)

Lorsque vous cliquez sur l’exécution du déploiement de mises à jour, vous obtenez des détails supplémentaires sur les pré/post-scripts. Un lien d’accès à la source du script au moment de l’exécution est fourni.

![Résultats de l’exécution du déploiement](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Transmission des paramètres

Lorsque vous configurez des pré/post-scripts, vous pouvez transmettre des paramètres tout comme vous planifieriez un runbook. Les paramètres sont définis au moment de la création du déploiement de mises à jour. Les pré et post-scripts prennent en charge les types suivants :

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Si vous avez besoin d’un autre type d’objet, vous pouvez le caster en un autre type avec votre propre logique dans le runbook.

Outre vos paramètres de runbook standard, un paramètre supplémentaire est fourni. Il s’agit du paramètre **SoftwareUpdateConfigurationRunContext**. Ce paramètre est une chaîne JSON, et si vous le définissez dans votre pré/post-script, il est automatiquement transmis par le déploiement de mises à jour. Le paramètre contient des informations sur le déploiement de mises à jour, qui constituent un sous-ensemble des informations renvoyées par [l’API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). Le tableau ci-après vous présente les propriétés qui sont fournies dans la variable :

### <a name="softwareupdateconfigurationruncontext-properties"></a>Propriétés SoftwareUpdateConfigurationRunContext

|Propriété  |Description  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nom de la configuration de mises à jour logicielles        |
|SoftwareUpdateConfigurationRunId     | ID unique de l’exécution        |
|SoftwareUpdateConfigurationSettings     | Collection de propriétés relatives à la configuration de mises à jour logicielles         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Systèmes d’exploitation ciblés pour le déploiement de mises à jour         |
|SoftwareUpdateConfigurationSettings.duration     | Durée maximale de l’exécution du déploiement de mises à jour, exprimée sous la forme `PT[n]H[n]M[n]S` conformément à ISO8601, et également appelée « fenêtre de maintenance »          |
|SoftwareUpdateConfigurationSettings.Windows     | Collection de propriétés relatives aux ordinateurs Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Liste des bases de connaissances qui sont exclues du déploiement de mises à jour        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classifications des mises à jour sélectionnées pour le déploiement de mises à jour        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Paramètres de redémarrage pour le déploiement de mises à jour        |
|azureVirtualMachines     | Liste des ID de ressource (resourceIds) des machines virtuelles Azure dans le déploiement de mises à jour        |
|nonAzureComputerNames|Liste des noms de domaine complets des ordinateurs non-Azure dans le déploiement de mises à jour|

Voici un exemple de chaîne JSON transmise dans le paramètre **SoftwareUpdateConfigurationRunContext** :

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ], 
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Un exemple complet avec toutes les propriétés est disponible ici : [Configurations de mise à jour logicielle - Obtenir par nom](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> Le `SoftwareUpdateConfigurationRunContext` objet peut contenir des entrées en double pour les machines. Cela peut entraîner des pré / post-scripts exécuter plusieurs fois sur le même ordinateur. Pour contourner ce comportement, utilisez `Sort-Object -Unique` pour sélectionner uniquement les noms de machine virtuelle uniques dans votre script.

## <a name="samples"></a>Exemples

Des exemples de pré-scripts et de post-scripts sont disponibles dans le [référentiel du Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) ou sont importés par le biais du Portail Azure. Pour les importer par l’intermédiaire du portail, dans votre compte Automation, sous **Automatisation des processus**, sélectionnez **Galerie de runbooks**. Utilisez **Update Management** en guise de filtre.

![Liste de la galerie](./media/pre-post-scripts/runbook-gallery.png)

Une autre possibilité consiste à rechercher les scripts par leur nom, comme indiqué dans la liste suivante :

* Update Management - Turn On VMs
* Update Management - Turn Off VMs
* Update Management - Run Script Locally
* Update Management - Template for Pre/Post Scripts
* Update Management - Run Script with Run Command

> [!IMPORTANT]
> Après avoir importé les runbooks, vous devez les **publier** avant de pouvoir les utiliser. Pour effectuer cette opération, recherchez le runbook dans votre compte Automation, sélectionnez **Modifier**, puis cliquez sur **Publier**.

Les exemples reposent tous sur le modèle de base qui est défini dans l’exemple suivant. Vous pouvez utiliser ce modèle pour créer votre propre runbook à utiliser avec des pré/post-scripts. La logique nécessaire à l’authentification auprès d’Azure et à la gestion du paramètre `SoftwareUpdateConfigurationRunContext` est incluse.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
  It requires a RunAs account. 
 
.PARAMETER SoftwareUpdateConfigurationRunContext 
  This is a system variable which is automatically passed in by Update Management during a deployment. 
#> 
 
param( 
    [string]$SoftwareUpdateConfigurationRunContext 
) 
#region BoilerplateAuthentication 
#This requires a RunAs account 
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection' 
 
Add-AzureRmAccount ` 
    -ServicePrincipal ` 
    -TenantId $ServicePrincipalConnection.TenantId ` 
    -ApplicationId $ServicePrincipalConnection.ApplicationId ` 
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint 
 
$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID 
#endregion BoilerplateAuthentication 
 
#If you wish to use the run context, it must be converted from JSON 
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext 
#Access the properties of the SoftwareUpdateConfigurationRunContext 
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId 
 
Write-Output $context 
 
#Example: How to create and write to a variable using the pre-script: 
<# 
#Create variable named after this run so it can be retrieved 
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false 
#Set value of variable  
Set-AutomationVariable –Name $runId -Value $vmIds 
#> 
 
#Example: How to retrieve information from a variable set during the pre-script 
<# 
$variable = Get-AutomationVariable -Name $runId 
#>      
```

## <a name="interacting-with-non-azure-machines"></a>Interaction avec les machines non-Azure

Les tâches préalables et postérieures s’exécutent dans le contexte Azure et n’ont pas accès aux machines non-Azure. Pour interagir avec les machines non-Azure, vous devez disposer des éléments suivants :

* compte d’identification ;
* fonctionnalité Runbook Worker hybride installée sur la machine ;
* runbook à exécuter localement ;
* runbook parent.

Pour interagir avec les machines non-Azure, un runbook parent est exécuté dans le contexte Azure. Ce runbook appelle un runbook enfant avec l’applet de commande [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Vous devez spécifier le paramètre `-RunOn` et indiquer le nom du Runbook Worker hybride sur lequel exécuter le script.

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="known-issues"></a>Problèmes connus

* Vous ne pouvez pas transmettre d’objets ni de tableaux aux paramètres lorsque vous utilisez des pré-scripts et des post-scripts. Si vous le faites, le runbook échoue.

## <a name="next-steps"></a>Étapes suivantes

Poursuivez avec le didacticiel pour apprendre à gérer les mises à jour de vos machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure](automation-tutorial-update-management.md)

