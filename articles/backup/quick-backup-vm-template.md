---
title: Guide de démarrage rapide - Sauvegarder une machine virtuelle avec un modèle Resource Manager
description: Découvrez comment sauvegarder vos machines virtuelles avec un modèle Azure Resource Manager
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc
ms.openlocfilehash: 7218b496a3cd94362d27b1883fa5055b819768c3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171932"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Sauvegarder une machine virtuelle dans Azure avec un modèle Resource Manager

La [sauvegarde Azure](backup-overview.md) sauvegarde les applications et machines locales, ainsi que les machines virtuelles Azure. Cet article explique la procédure de sauvegarde d’une machine virtuelle Azure avec un modèle Resource Manager et Azure PowerShell. Ce guide de démarrage rapide porte essentiellement sur le déploiement d’un modèle Resource Manager en vue de créer un coffre Recover Services. Pour plus d’informations sur le développement de modèles Resource Manager, consultez la [documentation Resource Manager](/azure/azure-resource-manager/) et les [informations de référence sur les modèles](/azure/templates/microsoft.recoveryservices/allversions).

Vous pouvez également sauvegarder une machine virtuelle en utilisant [Azure PowerShell](./quick-backup-vm-powershell.md), l’interface [Azure CLI](quick-backup-vm-cli.md) ou le [portail Azure](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Créer une machine virtuelle et un coffre Recovery Services

Un [coffre Recovery Services](backup-azure-recovery-services-vault-overview.md) est un conteneur logique qui stocke des données de sauvegarde pour des ressources protégées, telles que des machines virtuelles Azure. Quand un travail de sauvegarde s’exécute, il crée un point de récupération à l’intérieur du coffre Recovery Services. Vous pouvez ensuite utiliser un de ces points de récupération pour restaurer des données à un moment donné dans le temps.

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Ce modèle vous permet de déployer une machine virtuelle Windows simple et un coffre Recovery Services configuré avec la stratégie par défaut pour la protection.

Pour déployer le modèle, sélectionnez **Try it** afin d’ouvrir Azure Cloud Shell, puis collez le script PowerShell suivant dans la fenêtre de l’interpréteur de commandes. Pour coller le code, cliquez avec le bouton droit sur la fenêtre de l’interpréteur de commandes, puis sélectionnez **Coller**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Dans ce démarrage rapide, vous utilisez Azure PowerShell pour déployer le modèle Resource Manager. Le [portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md), l’interface [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) et [l’API Rest](../azure-resource-manager/resource-group-template-deploy-rest.md) peuvent également être utilisés pour déployer des modèles.

## <a name="start-a-backup-job"></a>Démarrer un travail de sauvegarde

Le modèle crée une machine virtuelle et permet la sauvegarde sur la machine virtuelle. Après avoir déployé le modèle, vous devez démarrer un travail de sauvegarde. Pour plus d’informations, consultez [Démarrer un travail de sauvegarde](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Surveiller le travail de sauvegarde

Pour surveiller le travail de sauvegarde, consultez [Surveiller le travail de sauvegarde](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Nettoyer le déploiement

Si vous n’avez plus besoin de sauvegarder la machine virtuelle, vous pouvez la nettoyer.

- Si vous voulez essayer de restaurer la machine virtuelle, ignorez cette opération de nettoyage.
- Si vous avez utilisé une machine virtuelle existante, vous pouvez ignorer l’applet de commande finale [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour laisser en place le groupe de ressources et la machine virtuelle.

Désactivez la protection, supprimez les points de restauration et le coffre. Supprimez ensuite le groupe de ressources et les ressources de machine virtuelle associées comme suit :

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre Recovery Services, activé la protection sur une machine virtuelle et créé le point de récupération initial.

- [Découvrez comment](tutorial-backup-vm-at-scale.md) sauvegarder des machines virtuelles dans le portail Azure.
- [Découvrez comment](tutorial-restore-disk.md) rapidement restaurer une machine virtuelle
