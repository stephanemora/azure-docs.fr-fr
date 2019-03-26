---
title: Utiliser des outils de ligne de commande pour démarrer et arrêter des machines virtuelles Azure DevTest Labs | Microsoft Docs
description: Découvrez comment utiliser les outils de ligne de commande pour démarrer et arrêter des machines virtuelles dans Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 9b7df83b710bac0b37ac28c432f63a47ddda21d1
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439923"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Utiliser des outils de ligne de commande pour démarrer et arrêter des machines virtuelles Azure DevTest Labs
Cet article vous montre comment utiliser Azure PowerShell ou Azure CLI pour démarrer ou arrêter des machines virtuelles dans un laboratoire dans Azure DevTest Labs. Vous pouvez créer des scripts PowerShell/CLI pour automatiser ces opérations. 

## <a name="overview"></a>Présentation
Azure DevTest Labs est un moyen de créer des environnements de développement/test rapides, simples et légers. Il vous permet de gérer les coûts rapidement approvisionner des machines virtuelles et réduire les déchets.  Il existe des fonctionnalités intégrées dans le portail Azure qui vous permettent de configurer des machines virtuelles dans un laboratoire pour automatiquement démarrer et arrêter à des moments donnés. 

Toutefois, dans certains scénarios, vous souhaiterez automatiser le démarrage et arrêt des machines virtuelles à partir de scripts PowerShell/CLI. Il vous donne une certaine flexibilité avec démarrage et arrêt des ordinateurs individuels à tout moment à la place d’à des moments donnés. Voici quelques cas dans les en cours d’exécution, ces tâches à l’aide de scripts peut s’avérer utiles.

- Lorsque vous utilisez une application de couche 3 en tant que partie d’un environnement de test, les niveaux doivent être démarrés dans une séquence. 
- Désactivez l’option d’une machine virtuelle lorsque des critères personnalisés est remplie pour réaliser des économies. 
- Utilisez-le en tant que tâche dans un flux de travail CI/CD pour commencer au début du flux, utilisez des machines virtuelles comme ordinateurs de build, machines, ou l’infrastructure de test, puis arrêter les machines virtuelles lorsque le processus est terminé. Un exemple de ce serait la fabrique d’image personnalisée avec Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell
Le script PowerShell suivant démarre une machine virtuelle dans un laboratoire. [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction?view=azurermps-6.13.0) est le principal objectif de ce script. Le **ResourceId** paramètre est l’ID de ressource qualifiée complète pour la machine virtuelle dans le laboratoire. Le **Action** paramètre est l’endroit où le **Démarrer** ou **arrêter** options sont définies en fonction de ce qui est nécessaire.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzureRMSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzureRmResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
Le [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) est un autre moyen d’automatiser le démarrage et l’arrêt des machines virtuelles de DevTest Labs. Azure CLI peut être [installé](/cli/azure/install-azure-cli?view=azure-cli-latest) sur différents systèmes d’exploitation. Le script suivant vous donne des commandes pour démarrer et arrêter une machine virtuelle dans un laboratoire. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant pour l’utilisation du portail Azure pour effectuer ces opérations : [Redémarrer une machine virtuelle](devtest-lab-restart-vm.md).