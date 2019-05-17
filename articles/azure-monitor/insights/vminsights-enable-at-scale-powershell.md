---
title: Activer Azure Monitor pour les machines virtuelles (version préliminaire) à l’aide du modèle d’Azure PowerShell ou Resource Manager | Microsoft Docs
description: Cet article décrit comment activer des Azure Monitor pour les machines virtuelles pour un ou plusieurs machines virtuelles ou des machines virtuelles identiques à l’aide de modèles Azure PowerShell ou Azure Resource Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: a22bc88fb066d9b845f7fdf1592e2194a03915bc
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524129"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-template"></a>Activer Azure Monitor pour les machines virtuelles (version préliminaire) à l’aide d’Azure PowerShell ou Resource Manager modèle

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cet article explique comment activer Azure Monitor pour les machines virtuelles (version préliminaire) pour les machines virtuelles ou des machines virtuelles identiques à l’aide de modèles Azure PowerShell ou Azure Resource Manager. À la fin de ce processus que vous avez correctement commencée analyse toutes vos machines virtuelles et découvrez si les rencontrez des problèmes de performances et disponibilité. 

## <a name="set-up-a-log-analytics-workspace"></a>Configurer un espace de travail Log Analytics 

Si vous n’avez pas un espace de travail Analytique de journal, créez-le en passant en revue les méthodes qui sont suggérés dans la [conditions préalables](vminsights-enable-overview.md#log-analytics) section avant de continuer avec les étapes pour le configurer afin de terminer le déploiement d’Azure Monitor pour Machines virtuelles à l’aide de la méthode de modèle Azure Resource Manager.

### <a name="enable-performance-counters"></a>Activer les compteurs de performance

Si l’espace de travail Log Analytics référencé par la solution n’est pas déjà configuré pour collecter les compteurs de performance requis par la solution, vous devez activer ces derniers. Vous pouvez le faire de deux manières :
* Manuellement, tel que décrit dans [Sources de données de performance Windows et Linux dans Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* En téléchargeant et en exécutant un script PowerShell disponible dans [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Installer les solutions ServiceMap et InfrastructureInsights
Cette méthode inclut un modèle JSON spécifiant la configuration requise pour activer les composants de la solution dans votre espace de travail Log Analytics.

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
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

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message semblable à celui qui suit s’affiche avec les résultats :

        ```powershell
        provisioningState       : Succeeded
        ```

    * Pour exécuter la commande ci-dessous à l’aide d’Azure CLI :
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-azure-resource-manager-template"></a>Activer avec le modèle Azure Resource Manager
Nous avons créé des exemples de modèles Azure Resource Manager pour l’intégration de vos machines virtuelles et les machines virtuelles identiques. Ces modèles incluent des scénarios pour activer l’analyse sur une ressource existante et pour la création d’une ressource ayant l’analyse est activée.

>[!NOTE]
>Le modèle doit être déployé dans le même groupe de ressources que la ressource à intégrer.

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="download-templates"></a>Télécharger des modèles

Les modèles Azure Resource Manager sont fournis dans un fichier d’archive (.zip) que vous pouvez [télécharger](https://aka.ms/VmInsightsARMTemplates) à partir de notre référentiel GitHub. Contenu du fichier inclure des dossiers représentant chaque scénario de déploiement avec un fichier de modèle et ses paramètres. Avant d’exécuter, modifier le fichier de paramètres et spécifiez les valeurs requises. Ne modifiez pas le fichier de modèle, sauf si vous devez personnaliser pour prendre en charge vos besoins spécifiques. Une fois que vous avez modifié le fichier de paramètres vous pouvez déployer à l’aide des méthodes suivantes, décrites plus loin dans cet article. 

Le fichier de téléchargement contient les modèles suivants pour différents scénarios :

- **ExistingVmOnboarding** modèle permet à Azure Monitor pour les machines virtuelles si les ordinateurs virtuels existe déjà.
- **NewVmOnboarding** modèle crée une machine virtuelle et permet à Azure Monitor pour surveiller les machines virtuelles.
- **ExistingVmssOnboarding** modèle permet à Azure Monitor pour les machines virtuelles si le machines virtuelles identiques déjà existe.
- **NewVmssOnboarding** modèle crée une échelle de machine virtuelle définit et permet à Azure Monitor pour les machines virtuelles pour les analyser.
- **ConfigureWorksapce*** modèle configure votre espace de travail Analytique de journal pour prendre en charge d’Azure Monitor pour les machines virtuelles en activant les solutions et la collection de compteurs de performance de système d’exploitation Linux et Windows.

>[!NOTE]
>Si les machines virtuelles identiques ont été déjà présentes et la stratégie de mise à niveau est définie sur **manuel**, Azure Monitor pour les machines virtuelles ne seront pas activée pour les instances par défaut après l’exécution le **ExistingVmssOnboarding** Modèle de gestionnaire de ressources Azure. Vous devez manuellement mettre à niveau les instances.

### <a name="deploy-using-azure-powershell"></a>Déployer avec Azure PowerShell

L’étape suivante permet la surveillance à l’aide d’Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-using-azure-cli"></a>Déployer à l’aide d’Azure CLI

L’étape suivante permet la surveillance à l’aide d’Azure CLI.   

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

La sortie doit ressembler à ce qui suit :

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Activer avec PowerShell

Si vous souhaitez activer Azure Monitor pour machines virtuelles pour plusieurs machines virtuelles ou des groupes de machines virtuelles identiques, vous pouvez utiliser le script PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), disponible dans Azure PowerShell Gallery. Ce script effectue une itération dans chaque machine virtuelle et groupe de machines virtuelles identiques de votre abonnement, au sein du groupe de ressources à portée spécifié par *ResourceGroup*, ou dans une seule machine virtuelle ou un seul groupe de machines virtuelles identiques spécifié par *Name*. Pour chaque machine virtuelle ou groupe de machines virtuelles identiques, le script vérifie si l’extension de machine virtuelle est déjà installée. Si l’extension de machine virtuelle n’est pas installée, le script tente de la réinstaller. Si l’extension de machine virtuelle est installée, le script installe les extensions de machine virtuelle Log Analytics Agent et Dependency Agent.

Ce script nécessite le module Azure PowerShell Az version 1.0.0 ou une version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

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
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

L’exemple suivant illustre l’utilisation des commandes PowerShell dans le dossier pour activer Azure Monitor pour machines virtuelles et comprendre la sortie attendue :

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

Already Onboarded: (0)

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

Maintenant que l’analyse est activée pour vos machines virtuelles, ces informations sont disponibles pour l’analyse avec Azure Monitor pour les machines virtuelles. Pour savoir comment utiliser la fonctionnalité de contrôle de l’intégrité, consultez [Comprendre l’intégrité de vos machines virtuelles Azure grâce à Azure Monitor pour machines virtuelles](vminsights-health.md). Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md). Pour identifier les goulots d’étranglement et les performances d’utilisation globale avec vos machines virtuelles, consultez l’article indiquant comment [afficher les performances avec Azure Monitor pour les machines virtuelles](vminsights-performance.md). Pour visualiser les dépendances d’application découvertes, consultez l’article expliquant comment [afficher la fonctionnalité Map d’Azure Monitor pour les machines virtuelles](vminsights-maps.md).