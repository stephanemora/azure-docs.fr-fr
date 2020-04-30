---
title: Activer Azure Monitor pour machines virtuelles avec PowerShell ou des modèles
description: Cet article décrit l’activation d’Azure Monitor pour machines virtuelles sur une ou plusieurs machines virtuelles (ou sur un ou plusieurs groupes de machines virtuelles identiques) à l’aide des modèles Azure PowerShell ou Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 77fe4b4ffbf7c189a5bf64e662f395fc78e53944
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82581504"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Activer Azure Monitor pour machines virtuelles à l’aide de modèles Azure PowerShell ou Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cet article explique comment activer Azure Monitor pour machines virtuelles sur des machines virtuelles (ou des groupes de machines virtuelles identiques) Azure à l’aide de modèles Azure PowerShell ou Azure Resource Manager. À la fin de ce processus, vous aurez commencé à analyser toutes vos machines virtuelles et saurez reconnaître les problèmes de performances ou de disponibilité.

## <a name="set-up-a-log-analytics-workspace"></a>Configurer un espace de travail Log Analytics

Si vous ne disposez pas d’un espace de travail Log Analytics, vous devez en créer un. Consultez les méthodes proposées dans la section [Conditions préalables](vminsights-enable-overview.md#log-analytics) avant de passer à la configuration de l’espace de travail. Vous pouvez ensuite terminer le déploiement d’Azure Monitor pour machines virtuelles à l’aide de la méthode du modèle Azure Resource Manager.

### <a name="install-the-vminsights-solution"></a>Installer la solution VMInsights

Cette méthode inclut un modèle JSON spécifiant la configuration requise pour activer les composants de la solution dans votre espace de travail Log Analytics.

Si vous ne savez pas comment déployer des ressources à l’aide d’un modèle, consultez les sections suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/templates/deploy-cli.md)

Pour utiliser Azure CLI, vous devez d’abord installer et utiliser l’interface CLI localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Enregistrez ce fichier sous *installsolutionsforvminsights.json* dans un dossier local.

1. Capturez les valeurs pour *WorkspaceName*, *ResourceGroupName* et *WorkspaceLocation*. La valeur de *WorkspaceName* est le nom de votre espace de travail Log Analytics. La valeur de *WorkspaceLocation* correspond à la région dans laquelle l’espace de travail est défini.

1. Vous êtes prêt à déployer ce modèle.

    * Utilisez les commandes PowerShell suivantes dans le dossier qui contient le modèle :

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci et contenant les résultats s’affiche :

        ```output
        provisioningState       : Succeeded
        ```

    * Pour exécuter la commande ci-dessous à l’aide d’Azure CLI :

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci et contenant les résultats s’affiche :

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Activer avec des modèles Azure Resource Manager

Nous avons créé des exemples de modèles Azure Resource Manager pour l’intégration de vos machines virtuelles et groupes de machines virtuelles identiques. Ces modèles incluent des scénarios que vous pouvez utiliser pour activer la surveillance sur une ressource existante et pour créer une ressource où la surveillance est activée.

>[!NOTE]
>Le modèle doit être déployé dans le même groupe de ressources que la ressource à intégrer.

Si vous ne savez pas comment déployer des ressources à l’aide d’un modèle, consultez les sections suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/templates/deploy-cli.md)

Pour utiliser Azure CLI, vous devez d’abord installer et utiliser l’interface CLI localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Télécharger des modèles

Les modèles Azure Resource Manager sont fournis dans un fichier d’archive (.zip) que vous pouvez [télécharger](https://aka.ms/VmInsightsARMTemplates) à partir de notre référentiel GitHub. Le fichier contient des dossiers représentant chaque scénario de déploiement, avec un fichier de modèle et un fichier de paramètres. Avant de les exécuter, modifiez le fichier des paramètres et spécifiez les valeurs requises. Ne modifiez pas le fichier du modèle, sauf si vous devez le personnaliser pour prendre en charge des exigences spécifiques. Une fois que vous avez modifié le fichier des paramètres, vous pouvez le déployer à l’aide des méthodes suivantes, décrites plus loin dans cet article.

Le fichier de téléchargement contient les modèles suivants pour différents scénarios :

- Le modèle **ExistingVmOnboarding** active Azure Monitor pour machines virtuelles si la machine virtuelle existe déjà.
- Le modèle **NewVmOnboarding** crée une machine virtuelle et active Azure Monitor pour machines virtuelles à des fins de surveillance.
- Le modèle **ExistingVmssOnboarding** active Azure Monitor pour machines virtuelles si le groupe de machines virtuelles identiques existe déjà.
- Le modèle **NewVmssOnboarding** crée un groupe de machines virtuelles identiques et active Azure Monitor pour machines virtuelles afin de les surveiller.
- Le modèle **ConfigureWorkspace** configure votre espace de travail Log Analytics pour prendre en charge Azure Monitor pour machines virtuelles en activant les solutions et la collection de compteurs de performances pour les systèmes d’exploitation Linux et Windows.

>[!NOTE]
>Si les groupes de machines virtuelles identiques étaient déjà présents et que la stratégie de mise à niveau est **manuelle**, Azure Monitor pour machines virtuelles n’est pas activé pour les instances par défaut après l’exécution du modèle Resource Manager **ExistingVmssOnboarding**. Vous devez mettre à niveau les instances manuellement.

### <a name="deploy-by-using-azure-powershell"></a>Déployer en utilisant Azure PowerShell

L’étape suivante active la surveillance à l’aide d’Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci et contenant les résultats s’affiche :

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Déployer à l’aide d’Azure CLI

L’étape suivante active la surveillance à l’aide d’Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

La sortie se présente comme suit :

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Activer avec PowerShell

Si vous souhaitez activer Azure Monitor pour machines virtuelles sur plusieurs machines virtuelles ou groupes de machines virtuelles identiques, utilisez le script PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights). Il est disponible sur Azure PowerShell Gallery. Ce script effectue une itération au sein des groupes suivants :

- Chaque machine virtuelle et groupe de machines virtuelles identiques dans votre abonnement.
- Le groupe de ressources à portée spécifié par *ResourceGroup*.
- Une machine virtuelle ou un groupe de machines virtuelles identiques unique spécifié par *Name*.

Pour chaque machine virtuelle ou groupe de machines virtuelles identiques, le script vérifie si l’extension de machine virtuelle est déjà installée. Si l’extension de machine virtuelle est installée, le script tente de la réinstaller. Si l’extension de machine virtuelle n’est pas installée, le script installe les extensions de machine virtuelle Log Analytics Agent et Dependency Agent.

Vérifiez que vous utilisez le module Azure PowerShell Az version 1.0.0 ou ultérieure avec les alias de compatibilité `Enable-AzureRM` activés. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

Pour obtenir une liste des détails de l’argument du script et un exemple d’utilisation, exécutez `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

L’exemple suivant illustre l’utilisation des commandes PowerShell dans le dossier pour activer Azure Monitor pour machines virtuelles et comprendre la sortie attendue :

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Étapes suivantes

Une fois la supervision activée pour vos machines virtuelles, ces informations peuvent être analysées par Azure Monitor pour machines virtuelles.

- Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md).

- Pour identifier les goulots d’étranglement et l’utilisation globale avec les performances de votre machine virtuelle, consultez [Afficher les performances des machines virtuelles Azure](vminsights-performance.md).
