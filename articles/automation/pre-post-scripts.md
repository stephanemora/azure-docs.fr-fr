---
title: Configurer des pré-scripts et des post-scripts sur votre déploiement Update Management dans Azure
description: Cet article explique comment configurer et gérer des pré-scripts et des post-scripts pour les déploiements de mises à jour.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 35fba966fcdb6d1c5cd7c531bb22c9c78ae16ff3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417802"
---
# <a name="manage-pre-and-post-scripts"></a>Gérer des pré-scripts et des post-scripts

Les pré-scripts et les post-scripts vous permettent d’exécuter des runbooks PowerShell dans votre compte Azure Automation avant (tâche préalable) et après (tâche postérieure) un déploiement de mises à jour. Tous ces scripts s’exécutent dans le contexte Azure, pas localement. Les pré-scripts s’exécutent au début du déploiement de la mise à jour. Les post-scripts s’exécutent à la fin du déploiement et après chaque redémarrage qui a été configuré.

## <a name="runbook-requirements"></a>Exigences en matière de runbook

Le runbook que vous souhaitez utiliser en tant que pré ou post-script doit être importé dans votre compte Automation et publié. Pour plus d’informations sur ce processus, consultez [Publier un runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Utilisation d’un pré-script ou d’un post-script

Pour utiliser un pré-script ou un post-script dans un déploiement de mises à jour, commencez par créer un déploiement de mises à jour. Sélectionnez **Pre-scripts + Post-scripts**. Cette action ouvre la page **Select Pre-scripts + Post-scripts** (Sélectionner des pré-scripts et post-scripts).

![Sélection des scripts](./media/pre-post-scripts/select-scripts.png)

Sélectionnez le script que vous souhaitez utiliser. Dans cet exemple, nous utilisons le runbook **UpdateManagement-TurnOnVms**. Lorsque vous sélectionnez le runbook, la page **Configurer un script** s’affiche. Sélectionnez **Pré-script**, puis **OK**.

Répétez ce processus pour le script **UpdateManagement-TurnOffVms**. Par contre, lorsque vous choisissez le **Type de script**, sélectionnez **Post-script**.

La section **Éléments sélectionnés** affiche à présent vos deux scripts sélectionnés. L’un est un pré-script, et l’autre un post-script :

![Éléments sélectionnés](./media/pre-post-scripts/selected-items.png)

Achevez la configuration de votre déploiement de mises à jour.

Une fois votre déploiement de mises à jour terminé, vous pouvez accéder à **Déploiements de mises à jour** pour visualiser les résultats. Comme vous pouvez le constater, l’état est fourni pour le pré-script et le post-script :

![Résultats des mises à jour](./media/pre-post-scripts/update-results.png)

Lorsque vous sélectionnez l’exécution du déploiement de mises à jour, vous obtenez des détails supplémentaires sur les pré-scripts et les post-scripts. Un lien d’accès à la source du script au moment de l’exécution est fourni.

![Résultats de l’exécution du déploiement](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Transmission des paramètres

Lorsque vous configurez des pré-scripts et des post-scripts, vous pouvez transmettre des paramètres tout comme vous planifieriez un runbook. Les paramètres sont définis au moment de la création du déploiement de mises à jour. Les pré-scripts et les post-scripts prennent en charge les types suivants :

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

En plus de vos paramètres de runbook standard, un autre paramètre est fourni : **SoftwareUpdateConfigurationRunContext**

Ce paramètre est une chaîne JSON, et si vous le définissez dans votre pré- ou post-script, il est automatiquement transmis par le déploiement de mises à jour. Le paramètre contient des informations sur le déploiement de mises à jour, qui constitue un sous-ensemble des informations retournées par [l’API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). 

Le tableau ci-après présente les propriétés fournies dans la variable.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Propriétés SoftwareUpdateConfigurationRunContext

|Propriété  |Description  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nom de la configuration de mises à jour logicielles.        |
|SoftwareUpdateConfigurationRunId     | ID unique de l’exécution        |
|SoftwareUpdateConfigurationSettings     | Collection de propriétés relatives à la configuration de mises à jour logicielles.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Systèmes d’exploitation ciblés pour le déploiement de mises à jour.         |
|SoftwareUpdateConfigurationSettings.duration     | Durée maximale de l’exécution du déploiement de mises à jour, exprimée sous la forme `PT[n]H[n]M[n]S` conformément à ISO8601 ; également appelée *fenêtre de maintenance*.          |
|SoftwareUpdateConfigurationSettings.Windows     | Collection de propriétés relatives aux ordinateurs Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Liste des bases de connaissances qui sont exclues du déploiement de mises à jour.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classifications des mises à jour sélectionnées pour le déploiement de mises à jour.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Paramètres de redémarrage pour le déploiement de mises à jour.        |
|azureVirtualMachines     | Liste des ID de ressource (resourceIds) des machines virtuelles Azure dans le déploiement de mises à jour.        |
|nonAzureComputerNames|Liste des noms de domaine complets des ordinateurs non-Azure dans le déploiement de mises à jour.|

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

Un exemple complet avec toutes les propriétés est disponible ici : [Obtenir la configuration de mises à jour logicielles par nom](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> L’objet `SoftwareUpdateConfigurationRunContext` peut contenir des entrées en double pour les machines. De ce fait, il se peut que les pré-scripts et les post-scripts s’exécutent plusieurs fois sur la même machine. Pour éviter ce comportement, utilisez le paramètre `Sort-Object -Unique` pour sélectionner uniquement les noms de machine virtuelle uniques dans votre script.


## <a name="stopping-a-deployment"></a>Planification d’un déploiement

Si vous souhaitez arrêter un déploiement basé sur un pré-script, vous devez [lever](automation-runbook-execution.md#throw) une exception. Si vous ne le faites pas, le post-script et le déploiement continueront de s’exécuter. L’extrait de code suivant montre comment lever une exception.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="samples"></a>Exemples

Vous pouvez utiliser les exemples de pré-scripts et de post-scripts disponibles dans la [galerie du Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) et dans [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), ou les importer via le portail Azure. Pour ce faire, dans votre compte Automation, sous **Automatisation des processus**, sélectionnez **Galerie de runbooks**. Utilisez **Update Management** en guise de filtre.

![Liste de la galerie](./media/pre-post-scripts/runbook-gallery.png)

Une autre possibilité consiste à rechercher les scripts par leur nom, comme montré dans la liste suivante :

* Update Management - Turn On VMs
* Update Management - Turn Off VMs
* Update Management - Run Script Locally
* Update Management - Template for Pre/Post Scripts
* Update Management - Run Script with Run Command

> [!IMPORTANT]
> Après avoir importé les runbooks, vous devez les publier avant de pouvoir les utiliser. Pour effectuer cette opération, recherchez le runbook dans votre compte Automation, sélectionnez **Modifier**, puis **Publier**.

Les exemples reposent tous sur le modèle de base qui est défini dans l’exemple suivant. Vous pouvez utiliser ce modèle pour créer votre propre runbook à utiliser avec des pré-scripts et post-scripts. La logique nécessaire à l’authentification auprès d’Azure et à la gestion du paramètre `SoftwareUpdateConfigurationRunContext` est incluse.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
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

## <a name="interacting-with-machines"></a>Interaction avec des machines

Les tâches antérieures et postérieures s’exécutent sous la forme d’un runbook dans votre compte Automation, et non directement sur les machines de votre déploiement. Elles s’exécutent également dans le contexte Azure et n’ont pas accès aux machines non-Azure. Les sections suivantes vous montrent comment interagir directement avec les machines, qu’il s’agisse de machines virtuelles Azure ou de machines non-Azure.

### <a name="interacting-with-azure-machines"></a>Interaction avec des machines Azure

Les tâches antérieures et postérieures s’exécutent sous la forme d’un runbook, et non de manière native, sur les machines Azure de votre déploiement. Pour interagir avec vos machines Azure, vous devez disposer des éléments suivants :

* compte d’identification ;
* runbook à exécuter.

Pour interagir avec des machines Azure, vous devez utiliser la cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Pour savoir comment faire, consultez l’exemple de runbook [Update Management - run script with Run command](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interaction avec les machines non-Azure

Les tâches antérieures et postérieures s’exécutent dans le contexte Azure et n’ont pas accès aux machines non-Azure. Pour interagir avec les machines non-Azure, vous devez disposer des éléments suivants :

* compte d’identification ;
* fonctionnalité Runbook Worker hybride installée sur la machine ;
* runbook à exécuter localement ;
* runbook parent.

Pour interagir avec les machines non-Azure, un runbook parent est exécuté dans le contexte Azure. Ce runbook appelle un runbook enfant avec l’applet de commande [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Vous devez spécifier le paramètre `-RunOn` et indiquer le nom du Runbook Worker hybride sur lequel exécuter le script. Pour plus d’informations, consultez l’exemple de runbook [Update Management - run script locally](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>À propos du déploiement de correctifs

Si votre pré-script retourne une erreur, vous serez peut-être amené à annuler le déploiement. Pour cela, vous devez [lever](/powershell/module/microsoft.powershell.core/about/about_throw) une erreur dans votre script pour toute logique pouvant constituer une défaillance.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Problèmes connus

* Vous ne pouvez pas transmettre d’objet, de valeur booléenne ni de tableau aux paramètres lorsque vous utilisez des pré-scripts et des post-scripts. Dans ce cas, le runbook échoue. Pour obtenir la liste complète des types pris en charge, consultez [Transmission des paramètres](#passing-parameters).

## <a name="next-steps"></a>Étapes suivantes

Poursuivez avec le tutoriel suivant pour apprendre à gérer les mises à jour de vos machines virtuelles Windows :

> [!div class="nextstepaction"]
> [Gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure](automation-tutorial-update-management.md)

