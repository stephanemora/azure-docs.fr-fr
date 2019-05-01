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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3f62557d024f56b7014784b6956f15a950f8cca7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926251"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
Cet article décrit comment changer le modèle de licence d’une machine virtuelle SQL Server dans Azure à l’aide du nouveau fournisseur de ressources de machine virtuelle SQL, **Microsoft.SqlVirtualMachine**. Il existe deux modèles pour une machine virtuelle (VM) qui héberge SQL Server - paiement à l’utilisation, de licence et apportez votre propre licence (BYOL). Et, à l’aide du portail Azure, Azure CLI ou PowerShell vous pouvez maintenant modifier le modèle de licence utilise votre machine virtuelle SQL Server. 

Le **paiement à l’utilisation** modèle de (PAYG) signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence de SQL Server.

Le **bring-your-propre licence** modèle (BYOL) est également connu sous le [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), et il vous permet d’utiliser votre propre licence SQL Server avec une machine virtuelle exécutant SQL Server. Pour plus d’informations sur les prix, consultez le [Guide des tarifs des machines virtuelles SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Le passage d’un modèle à l’autre n’entraîne **aucun temps d’arrêt**, aucun redémarrage de la machine virtuelle ni **aucun coût supplémentaire** (en fait, l’activation d’AHB *réduit* même les coûts) et est **à effet immédiat**. 

## <a name="remarks"></a>Remarques


 - Les clients CSP peuvent utiliser l’avantage AHB en commençant par déployer une machine virtuelle avec paiement à l’utilisation, puis en la convertissant en licence BYOL. 
 - Lorsque vous inscrivez une image de machine virtuelle SQL Server personnalisée avec le fournisseur de ressources, spécifiez le type de licence que = 'AHUB'. En laissant la licence, tapez comme vide, ou la définition de « Paiement à l’utilisation » provoque l’inscription de l’échec. 
 - Si vous supprimez votre ressource de machine virtuelle SQL Server, vous revenez au paramètre codé en dur la licence de l’image. 
 - Ajout d’une machine virtuelle SQL Server à un groupe à haute disponibilité nécessite la recréation de la machine virtuelle. En tant que machines virtuelles de ce type, n’importe quel est ajoutés à une disponibilité ensemble revient au type de licence de paiement à l’utilisation par défaut et AHB doit être activée à nouveau. 
 - La possibilité de modifier le modèle de licence est une fonctionnalité du fournisseur de ressources SQL VM. Déploiement d’une image de place de marché via le portail Azure automatiquement inscrit une machine virtuelle SQL Server avec le fournisseur de ressources. Toutefois, les clients qui installent automatiquement SQL Server devront manuellement [inscrire leur machine virtuelle SQL Server](#register-sql-server-vm-with-the-sql-vm-resource-provider). 
 

 
## <a name="limitations"></a>Limites

 - La possibilité de convertir le modèle de licence n’est actuellement disponible que lorsque vous démarrez avec une image de machine virtuelle SQL Server avec paiement à l’utilisation. Si vous démarrez avec une image de licence correspondant au modèle BYOL à partir du portail, vous ne serez pas en mesure de convertir celle-ci en modèle avec paiement à l’utilisation.
 - Modification du modèle de licence est uniquement pris en charge pour les machines virtuelles déployées à l’aide du modèle Resource Manager. Machines virtuelles déployées à l’aide du modèle classic ne sont pas pris en charge. 
 - Modification du modèle de licence est activé uniquement pour les installations de Cloud Public.
 - Modification du modèle de licence est pris en charge uniquement sur les machines virtuelles qui ont une seule carte réseau (interface réseau). Sur les machines virtuelles qui ont plusieurs cartes réseau, vous devez tout d’abord supprimer l’un des cartes réseau (en utilisant le portail Azure) avant de commencer la procédure. Sinon, vous rencontrerez une erreur similaire à ce qui suit : `The virtual machine '\<vmname\>' has more than one NIC associated.` Bien que vous puissiez rajouter la carte réseau à la machine virtuelle après avoir modifié le mode de licence, opérations effectuées via le panneau de configuration SQL, telles que la mise à jour corrective automatique et de sauvegarde, seront plus être considérés comme pris en charge.

## <a name="prerequisites"></a>Conditions préalables

L’utilisation du fournisseur de ressources de machine virtuelle SQL nécessite l’extension IaaS SQL. Ainsi, pour utiliser le fournisseur de ressources de machine virtuelle SQL, vous avez besoin des éléments suivants :
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- [L’assurance logiciel](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Un *paiement à l’utilisation* [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) avec la [extension SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) installé. 

## <a name="with-the-azure-portal"></a>Avec le portail Azure

Vous pouvez modifier le modèle de licence directement à partir du portail. 

1. Accédez à votre machine virtuelle SQL Server dans le [Azure portal](https://portal.azure.com). 
1. Sélectionnez **configuration de SQL Server** dans le **paramètres** volet. 
1. Sélectionnez **modifier** dans le **licence SQL Server** volet Modifier la licence. 

![AHB dans le portail](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Cette option n’est pas disponible pour les images de bring-your-propre licence. 

## <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure

Vous pouvez utiliser Azure CLI pour changer votre modèle de licence.  

L’extrait de code suivant bascule votre modèle de paiement à l’utilisation de licences BYOL (ou à l’aide d’Azure Hybrid Benefit) :

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

L’extrait de code suivant bascule votre modèle bring-your-propre licence vers le paiement à l’utilisation : 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```
## <a name="with-powershell"></a>Avec PowerShell
Vous pouvez utiliser PowerShell pour changer votre modèle de licence.

L’extrait de code suivant bascule votre modèle de paiement à l’utilisation de licences BYOL (ou à l’aide d’Azure Hybrid Benefit) :

```powershell
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

L’extrait de code suivant passe votre modèle BYOL à paiement à l’utilisation :

```powershell
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Inscrire la machine virtuelle SQL Server avec le fournisseur de ressources SQL VM
Dans certaines situations, vous devrez peut-être inscrire manuellement votre machine virtuelle SQL Server avec le fournisseur de ressources SQL VM. Pour ce faire, vous devrez peut-être également inscrire manuellement le fournisseur de ressources avec votre abonnement. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Inscription du fournisseur de ressources SQL VM avec abonnement 

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources SQL, vous devez inscrire celui-ci sur votre abonnement. Vous pouvez le faire avec le portail Azure ou Azure CLI. 

#### <a name="with-the-azure-portal"></a>Avec le portail Azure
Les étapes suivantes inscrira le fournisseur de ressources SQL à votre abonnement Azure à l’aide du portail Azure. 

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Dans le panneau **Abonnements**, accédez à **Fournisseurs de ressources**. 
1. Tapez `sql` dans le filtre pour afficher les fournisseurs de ressources liés à SQL. 
1. Sélectionnez *Inscrire*, *Réinscrire* ou *Désinscrire* pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

   ![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure
L’extrait de code suivant inscrira le fournisseur de ressources SQL VM à votre abonnement Azure. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>Avec PowerShell

L’extrait de code suivant inscrira le fournisseur de ressources SQL à votre abonnement Azure.

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Inscrire une machine virtuelle SQL Server auprès d’un fournisseur de ressources SQL
Une fois que le fournisseur de ressources SQL VM a été inscrit dans votre abonnement, vous pouvez inscrire votre machine virtuelle SQL Server avec le fournisseur de ressources à l’aide d’Azure CLI. 

#### <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure
Inscrivez la machine virtuelle SQL Server à l’aide d’Azure CLI avec l’extrait de code suivant : 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```
#### <a name="with-powershell"></a>Avec PowerShell
Inscrivez la machine virtuelle SQL Server à l’aide de PowerShell avec l’extrait de code suivant :

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```



## <a name="known-errors"></a>Erreurs connues

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>L’extension IaaS SQL n’est pas installée sur la machine virtuelle
L’extension IaaS SQL est un prérequis nécessaire à l’inscription de votre machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL. Si vous essayez d’inscrire votre machine virtuelle SQL Server avant d’installer l’extension IaaS SQL, vous rencontrez l’erreur suivante :

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Pour résoudre ce problème, installez l’extension IaaS SQL avant de tenter d’inscrire votre machine virtuelle SQL Server. 

  > [!NOTE]
  > L’installation de l’extension IaaS SQL redémarre le service SQL Server et doit uniquement être effectuée pendant une fenêtre de maintenance. Pour plus d’informations, consultez [Installation de l’extension IaaS SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>La ressource ' Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<groupe de ressources >' sous le groupe de ressources '\<groupe de ressources >' est introuvable. La propriété 'sqlServerLicenseType' est introuvable sur cet objet. Vérifiez que la propriété existe et qu’il peut être définie.
Cette erreur se produit lorsque vous tentez de modifier le modèle de licence sur une machine virtuelle de SQL Server qui n’a pas été inscrit avec le fournisseur de ressources SQL. Vous devez inscrire le fournisseur de ressources pour votre [abonnement](#register-sql-vm-resource-provider-with-subscription), puis enregistrez votre machine virtuelle SQL Server avec le code SQL [fournisseur de ressources](#register-sql-server-vm-with-sql-resource-provider). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Impossible de valider l’argument sur le paramètre « Sku »
Vous risquez de rencontrer cette erreur quand vous essayez de modifier votre modèle de licence de machine virtuelle SQL Server en utilisant Azure PowerShell > 4.0 : Set-AzResource : Impossible de valider argument sur le paramètre « Sku ». L’argument est null ou vide. Fournir un argument qui n’est pas null ou vide, puis réessayez la commande.
Pour corriger cette erreur, supprimez les marques de commentaire de ces lignes dans l’extrait de code PowerShell mentionné précédemment lorsque vous changez de modèle de licence :

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Utilisez le code suivant pour vérifier votre version d’Azure PowerShell :

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


