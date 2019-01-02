---
title: Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL dans Azure | Microsoft Docs
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
ms.openlocfilehash: cd784163047f4fe15fde719ce56aba64eed60dd2
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336983"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
Cet article décrit comment changer le modèle de licence d’une machine virtuelle SQL Server dans Azure à l’aide du nouveau fournisseur de ressources SQL, **Microsoft.SqlVirtualMachine**. Il existe deux modèles de licence pour une machine virtuelle qui héberge SQL Server : le paiement à l’utilisation et BYOL (apportez votre propre licence). En utilisant PowerShell ou Azure CLI, vous pouvez changer le modèle de licence que votre machine virtuelle SQL utilise. 

Le modèle **Paiement à l’utilisation** signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence SQL Server.

Le modèle **BYOL (Apportez votre propre licence)** est également connu sous le nom d’[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) et vous permet d’utiliser votre propre licence SQL Server avec une machine virtuelle exécutant SQL Server. Pour plus d’informations sur les prix, consultez le [Guide des tarifs des machines virtuelles SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Le passage d’un modèle à l’autre n’entraîne **aucun temps d’arrêt**, aucun redémarrage de la machine virtuelle ni **aucun coût supplémentaire** (en fait, l’activation d’AHB réduit même les coûts), et est à **effet immédiat**. 


## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Inscrire une machine virtuelle SQL existante auprès du nouveau fournisseur de ressources
La possibilité de passer d’un modèle de licence à l’autre est une fonctionnalité offerte par le nouveau fournisseur de ressources de machine virtuelle SQL (Microsoft.SqlVirtualMachine). À l’heure actuelle, pour pouvoir changer votre modèle de licence, vous devez d’abord inscrire le nouveau fournisseur à votre abonnement, puis inscrire votre machine virtuelle existante auprès du nouveau fournisseur de ressources de machine virtuelle SQL. Pour utiliser le fournisseur de ressources de machine virtuelle SQL, vous devez également installer l’extension IaaS SQL. Cela vous permettra d’inscrire une machine virtuelle déployée avec un disque dur virtuel. Pour plus d’informations, voir [Extension IaaS SQL](virtual-machines-windows-sql-server-agent-extension.md). 

  >[!IMPORTANT]
  > Si vous supprimez votre ressource de machine virtuelle SQL, vous revenez au paramètre de licence codé en dur de l’image. 

### <a name="powershell"></a>PowerShell


L’extrait de code suivant vous permet de vous connecter à Azure et de vérifier l’ID d’abonnement que vous utilisez. 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

L’extrait de code suivant inscrit tout d’abord le nouveau fournisseur de ressources SQL à votre abonnement, puis inscrit votre machine virtuelle SQL existante auprès du nouveau fournisseur de ressources. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your existing SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portail
Vous pouvez également inscrire le nouveau fournisseur de ressources de machine virtuelle SQL en utilisant le portail. Pour cela, effectuez ces étapes :
1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Dans le panneau **Abonnements**, accédez à **Fournisseur de ressources**. 
1. Tapez `sql` dans le filtre pour afficher les fournisseurs de ressources liés à SQL. 
1. Sélectionnez *Inscrire*, *Réinscrire* ou *Désinscrire* pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

  ![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Utiliser PowerShell 
Vous pouvez utiliser PowerShell pour changer votre modèle de licence.  Vérifiez que votre machine virtuelle SQL est déjà inscrite auprès du nouveau fournisseur de ressources SQL avant de changer de modèle de licence. 

Avec l’extrait de code suivant, votre modèle de licence Paiement à l’utilisation se change en modèle BYOL (ou Azure Hybrid Benefit) : 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

Avec l’extrait de code suivant, votre modèle BYOL se change en Paiement à l’utilisation :
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Pour changer de licences, vous devez utiliser le nouveau fournisseur de ressources de machine virtuelle SQL. Si vous essayez d’exécuter ces commandes avant d’inscrire votre machine virtuelle SQL auprès du nouveau fournisseur, vous pourriez rencontrer cette erreur : `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Si vous voyez cette erreur, [inscrivez votre machine virtuelle SQL auprès du nouveau fournisseur de ressources](#register-existing-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Vous pouvez utiliser Azure CLI pour changer votre modèle de licence.  Vérifiez que votre machine virtuelle SQL est déjà inscrite auprès du nouveau fournisseur de ressources SQL avant de changer de modèle de licence. 

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
  >Pour changer de licences, vous devez utiliser le nouveau fournisseur de ressources de machine virtuelle SQL. Si vous essayez d’exécuter ces commandes avant d’inscrire votre machine virtuelle SQL auprès du nouveau fournisseur, vous pourriez rencontrer cette erreur : `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Si vous voyez cette erreur, [inscrivez votre machine virtuelle SQL auprès du nouveau fournisseur de ressources](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Voir la licence actuelle 

L’extrait de code suivant vous permet de voir le modèle de licence actuel de votre machine virtuelle SQL. 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


