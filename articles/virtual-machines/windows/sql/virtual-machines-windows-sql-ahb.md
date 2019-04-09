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
ms.openlocfilehash: f3ebbfb1b9894b2bf1ca41ac46970e138d107f7b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265081"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
Cet article décrit comment changer le modèle de licence d’une machine virtuelle SQL Server dans Azure à l’aide du nouveau fournisseur de ressources de machine virtuelle SQL, **Microsoft.SqlVirtualMachine**. Il existe deux modèles pour une machine virtuelle (VM) qui héberge SQL Server - paiement à l’utilisation, de licence et apportez votre propre licence (BYOL). En utilisant PowerShell ou Azure CLI, vous pouvez changer le modèle de licence que votre machine virtuelle SQL Server utilise. 

Le **paiement à l’utilisation** modèle de (PAYG) signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence de SQL Server.

Le **bring-your-propre licence** modèle (BYOL) est également connu sous le [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), et il vous permet d’utiliser votre propre licence SQL Server avec une machine virtuelle exécutant SQL Server. Pour plus d’informations sur les prix, consultez le [Guide des tarifs des machines virtuelles SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Le passage d’un modèle à l’autre n’entraîne **aucun temps d’arrêt**, aucun redémarrage de la machine virtuelle ni **aucun coût supplémentaire** (en fait, l’activation d’AHB *réduit* même les coûts) et est **à effet immédiat**. 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="remarks"></a>Remarques

 - Les clients CSP peuvent utiliser l’avantage AHB en commençant par déployer une machine virtuelle avec paiement à l’utilisation, puis en la convertissant en licence BYOL. 
 - Lorsque vous inscrivez une image de machine virtuelle SQL Server personnalisée avec le fournisseur de ressources, spécifiez le type de licence que = 'AHUB'. En laissant la licence, tapez comme vide, ou la définition de « Paiement à l’utilisation » provoque l’inscription de l’échec. 
 
## <a name="limitations"></a>Limites

 - La possibilité de convertir le modèle de licence n’est actuellement disponible que lorsque vous démarrez avec une image de machine virtuelle SQL Server avec paiement à l’utilisation. Si vous démarrez avec une image de licence correspondant au modèle BYOL à partir du portail, vous ne serez pas en mesure de convertir celle-ci en modèle avec paiement à l’utilisation.
  - Actuellement, modification du modèle de licence est uniquement pris en charge pour les machines virtuelles déployées à l’aide du modèle Resource Manager. Machines virtuelles déployées à l’aide du modèle classic ne sont pas pris en charge. 
   - Actuellement, la modification du modèle de licence est activé uniquement pour les installations de Cloud Public.
   - Actuellement, cette procédure est prise en charge uniquement sur les machines virtuelles qui ont une seule carte réseau (interface réseau). Sur les machines virtuelles qui ont plusieurs cartes réseau, vous devez tout d’abord supprimer l’un des cartes réseau (en utilisant le portail Azure) avant de commencer la procédure. Sinon, vous rencontrerez une erreur similaire à ce qui suit : « La machine virtuelle '\<vmname\>' a plusieurs cartes réseau associée. » Bien que vous puissiez rajouter la carte réseau à la machine virtuelle après avoir modifié le mode de licence, opérations effectuées via le panneau de configuration SQL, telles que la mise à jour corrective automatique et de sauvegarde, seront plus être considérés comme pris en charge.

## <a name="prerequisites"></a>Conditions préalables

L’utilisation du fournisseur de ressources de machine virtuelle SQL nécessite l’extension IaaS SQL. Ainsi, pour utiliser le fournisseur de ressources de machine virtuelle SQL, vous avez besoin des éléments suivants :
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- [L’assurance logiciel](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Un *paiement à l’utilisation* [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) avec la [extension SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) installé. 


## <a name="register-sql-resource-provider-to-your-subscription"></a>Inscrire le fournisseur de ressources SQL à votre abonnement 

La possibilité de passer d’un modèle de licence à l’autre est une fonctionnalité offerte par le nouveau fournisseur de ressources de machine virtuelle SQL (Microsoft.SqlVirtualMachine). Les machines virtuelles SQL Server déployées après décembre 2018 sont automatiquement inscrites auprès du nouveau fournisseur de ressources. En revanche, les machines virtuelles existantes qui ont été déployées avant cette date doivent être inscrites manuellement auprès du fournisseur de ressources pour pouvoir changer de modèle de licence. 

  > [!NOTE] 
  > Si vous supprimez votre ressource de machine virtuelle SQL Server, vous revenez au paramètre de licence codé en dur de l’image. 

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources SQL, vous devez inscrire celui-ci sur votre abonnement. Vous pouvez le faire avec le portail Azure, Azure CLI ou PowerShell. 

### <a name="with-the-azure-portal"></a>Avec le portail Azure
Les étapes suivantes inscrira le fournisseur de ressources SQL à votre abonnement Azure à l’aide du portail Azure. 

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Dans le panneau **Abonnements**, accédez à **Fournisseurs de ressources**. 
1. Tapez `sql` dans le filtre pour afficher les fournisseurs de ressources liés à SQL. 
1. Sélectionnez *Inscrire*, *Réinscrire* ou *Désinscrire* pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

   ![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure
L’extrait de code suivant inscrira le fournisseur de ressources SQL à votre abonnement Azure. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="with-powershell"></a>Avec PowerShell
L’extrait de code suivant inscrira le fournisseur de ressources SQL à votre abonnement Azure. 

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```


## <a name="register-sql-server-vm-with-sql-resource-provider"></a>Inscrire une machine virtuelle SQL Server auprès d’un fournisseur de ressources SQL
Une fois que le fournisseur de ressources SQL a été inscrit dans votre abonnement, vous pouvez inscrire votre machine virtuelle SQL Server avec le fournisseur de ressources. Vous pouvez le faire avec Azure CLI et PowerShell. 

### <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure

Inscrivez la machine virtuelle SQL Server à l’aide d’Azure CLI avec l’extrait de code suivant : 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```

### <a name="with-powershell"></a>Avec PowerShell

Inscrivez la machine virtuelle SQL Server à l’aide de PowerShell avec l’extrait de code suivant : 

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

## <a name="change-licensing-model"></a>Modifier le modèle de licence

Une fois que votre machine virtuelle SQL Server a été inscrite avec le fournisseur de ressources, vous pouvez modifier le modèle de licence à l’aide du portail Azure, Azure CLI ou PowerShell. 

### <a name="with-the-azure-portal"></a>Avec le portail Azure

Vous pouvez modifier le modèle de licence directement à partir du portail. 

1. Accédez à votre machine virtuelle SQL Server dans le [Azure portal](https://portal.azure.com). 
1. Sélectionnez **configuration de SQL Server** dans le **paramètres** volet. 
1. Sélectionnez **modifier** dans le **licence SQL Server** volet Modifier la licence. 

![AHB dans le portail](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Cette option n’est pas disponible pour les images de bring-your-propre licence. 

### <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure

Vous pouvez utiliser Azure CLI pour changer votre modèle de licence.  

L’extrait de code suivant bascule votre modèle de paiement à l’utilisation de licences BYOL (ou à l’aide d’Azure Hybrid Benefit) :

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

L’extrait de code suivant passe votre modèle BYOL à paiement à l’utilisation : 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

### <a name="with-powershell"></a>Avec PowerShell 

Vous pouvez utiliser PowerShell pour changer votre modèle de licence. 

L’extrait de code suivant bascule votre modèle de paiement à l’utilisation de licences BYOL (ou à l’aide d’Azure Hybrid Benefit) : 

```PowerShell
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

```PowerShell
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


## <a name="view-current-licensing"></a>Voir la licence actuelle 

L’extrait de code suivant vous permet de voir le modèle de licence actuel de votre machine virtuelle SQL Server. 

```powershell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
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

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

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

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>La ressource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < resource-group > sous le groupe de ressources '< resource-group >' est introuvable. La propriété 'sqlServerLicenseType' est introuvable sur cet objet. Vérifiez que la propriété existe et qu’il peut être définie.
Cette erreur se produit lorsque vous tentez de modifier le modèle de licence sur une machine virtuelle de SQL Server qui n’a pas été inscrit avec le fournisseur de ressources SQL. Vous devez inscrire le fournisseur de ressources pour votre [abonnement](#register-sql-resource-provider-to-your-subscription), puis enregistrez votre machine virtuelle SQL Server avec le code SQL [fournisseur de ressources](#register-sql-server-vm-with-sql-resource-provider). 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sur un forum aux questions de la machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server sur une machine virtuelle de Windows tarification](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL notes Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


