---
title: Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL Server dans Azure | Microsoft Docs
description: Apprenez à changer la licence d’une machine virtuelle SQL dans Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ff1281a249abf456176cffe2b02ef3c63b718d5a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767994"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
Cet article décrit comment changer le modèle de licence d’une machine virtuelle SQL Server dans Azure à l’aide du nouveau fournisseur de ressources de machine virtuelle SQL, **Microsoft.SqlVirtualMachine**. Il existe deux modèles de licence pour une machine virtuelle qui héberge SQL Server : le paiement à l’utilisation et BYOL (apportez votre propre licence). En utilisant PowerShell ou Azure CLI, vous pouvez changer le modèle de licence que votre machine virtuelle SQL Server utilise. 

Le modèle **Paiement à l’utilisation** (PAYG) signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence SQL Server.

Le modèle **BYOL (Apportez votre propre licence)** est également connu sous le nom d’[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) et vous permet d’utiliser votre propre licence SQL Server avec une machine virtuelle exécutant SQL Server. Pour plus d’informations sur les prix, consultez le [Guide des tarifs des machines virtuelles SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Le passage d’un modèle à l’autre n’entraîne **aucun temps d’arrêt**, aucun redémarrage de la machine virtuelle ni **aucun coût supplémentaire** (en fait, l’activation d’AHB *réduit* même les coûts) et est **à effet immédiat**. 

  >[!NOTE]
  > - La possibilité de convertir le modèle de licence n’est actuellement disponible que lorsque vous démarrez avec une image de machine virtuelle SQL Server avec paiement à l’utilisation. Si vous démarrez avec une image de licence correspondant au modèle BYOL à partir du portail, vous ne serez pas en mesure de convertir celle-ci en modèle avec paiement à l’utilisation. 
  > - Les clients CSP peuvent utiliser l’avantage AHB en commençant par déployer une machine virtuelle avec paiement à l’utilisation, puis en la convertissant en licence BYOL. 

## <a name="prerequisites"></a>Prérequis
L’utilisation du fournisseur de ressources de machine virtuelle SQL nécessite l’extension IaaS SQL. Ainsi, pour utiliser le fournisseur de ressources de machine virtuelle SQL, vous avez besoin des éléments suivants :
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) avec l’[extension IaaS SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) installée. 


## <a name="register-existing-sql-server-vm-with-sql-resource-provider"></a>Inscrire une machine virtuelle SQL Server existante auprès d’un fournisseur de ressources SQL
La possibilité de passer d’un modèle de licence à l’autre est une fonctionnalité offerte par le nouveau fournisseur de ressources de machine virtuelle SQL (Microsoft.SqlVirtualMachine). Les machines virtuelles SQL Server déployées après décembre 2018 sont automatiquement inscrites auprès du nouveau fournisseur de ressources. En revanche, les machines virtuelles existantes qui ont été déployées avant cette date doivent être inscrites manuellement auprès du fournisseur de ressources pour pouvoir changer de modèle de licence. 

  > [!NOTE] 
  > Si vous supprimez votre ressource de machine virtuelle SQL, vous revenez au paramètre de licence codé en dur de l’image. 

### <a name="register-sql-resource-provider-with-your-subscription"></a>Inscrire un fournisseur de ressources SQL avec votre abonnement 

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources SQL, vous devez inscrire celui-ci sur votre abonnement. Vous pouvez le faire avec PowerShell ou avec le portail Azure. 

#### <a name="using-powershell"></a>Utiliser PowerShell
L’extrait de code suivant inscrira le fournisseur de ressources SQL avec votre abonnement Azure. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

#### <a name="using-azure-portal"></a>En passant par le portail Azure
Les étapes suivantes permettent d’inscrire le fournisseur de ressources SQL avec votre abonnement Azure à l’aide du portail Azure. 

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Dans le panneau **Abonnements**, accédez à **Fournisseurs de ressources**. 
1. Tapez `sql` dans le filtre pour afficher les fournisseurs de ressources liés à SQL. 
1. Sélectionnez *Inscrire*, *Réinscrire* ou *Désinscrire* pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

  ![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Inscrire une machine virtuelle SQL Server auprès d’un fournisseur de ressources SQL
Une fois le fournisseur de ressources SQL inscrit avec votre abonnement, vous pouvez utiliser PowerShell pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources SQL. 


```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="use-powershell"></a>Utiliser PowerShell 
Vous pouvez utiliser PowerShell pour changer votre modèle de licence.  Vérifiez que votre machine virtuelle SQL Server est déjà inscrite auprès du nouveau fournisseur de ressources SQL avant de changer de modèle de licence. 

Avec l’extrait de code suivant, votre modèle de licence Paiement à l’utilisation se change en modèle BYOL (ou Azure Hybrid Benefit) : 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
``` 

Avec l’extrait de code suivant, votre modèle BYOL se change en Paiement à l’utilisation :
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Pour changer de licences, vous devez utiliser le nouveau fournisseur de ressources de machine virtuelle SQL. Si vous essayez d’exécuter ces commandes avant d’inscrire votre machine virtuelle SQL Server auprès du nouveau fournisseur, vous risquez de rencontrer cette erreur : `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Si vous voyez cette erreur, [inscrivez votre machine virtuelle SQL Server auprès du nouveau fournisseur de ressources](#register-existing-sql-server-vm-with-sql-resource-provider). 
 

## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Vous pouvez utiliser Azure CLI pour changer votre modèle de licence.  Vérifiez que votre machine virtuelle SQL Server est déjà inscrite auprès du nouveau fournisseur de ressources SQL avant de changer de modèle de licence. 

Avec l’extrait de code suivant, votre modèle de licence Paiement à l’utilisation se change en modèle BYOL (ou Azure Hybrid Benefit) :
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Avec l’extrait de code suivant, votre modèle BYOL se change en Paiement à l’utilisation : 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Pour changer de licences, vous devez utiliser le nouveau fournisseur de ressources de machine virtuelle SQL. Si vous essayez d’exécuter ces commandes avant d’inscrire votre machine virtuelle SQL Server auprès du nouveau fournisseur, vous risquez de rencontrer cette erreur : `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Si vous voyez cette erreur, [inscrivez votre machine virtuelle SQL Server auprès du nouveau fournisseur de ressources](#register-existing-sql-server-vm-with-sql-resource-provider). 

## <a name="view-current-licensing"></a>Voir la licence actuelle 

L’extrait de code suivant vous permet de voir le modèle de licence actuel de votre machine virtuelle SQL Server. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>Erreurs connues

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>L’extension IaaS SQL n’est pas installée sur la machine virtuelle
L’extension IaaS SQL est un prérequis nécessaire à l’inscription de votre machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL. Si vous essayez d’inscrire votre machine virtuelle SQL Server avant d’installer l’extension IaaS SQL, vous rencontrez l’erreur suivante :

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Pour résoudre ce problème, installez l’extension IaaS SQL avant de tenter d’inscrire votre machine virtuelle SQL Server. 

  > [!NOTE]
  > L’installation de l’extension IaaS SQL redémarre le service SQL Server et doit uniquement être effectuée pendant une fenêtre de maintenance. Pour plus d’informations, consultez [Installation de l’extension IaaS SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Impossible de valider l’argument sur le paramètre « Sku »
Vous risquez de rencontrer cette erreur quand vous essayez de modifier votre modèle de licence de machine virtuelle SQL Server en utilisant Azure PowerShell > 4.0 :

`Set-AzureRmResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Pour corriger cette erreur, supprimez les marques de commentaire de ces lignes dans l’extrait de code PowerShell mentionné précédemment lorsque vous changez de modèle de licence : 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Utilisez le code suivant pour vérifier votre version d’Azure PowerShell :

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


