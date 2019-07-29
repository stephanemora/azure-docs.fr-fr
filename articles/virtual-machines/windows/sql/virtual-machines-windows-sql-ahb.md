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
ms.openlocfilehash: 667a696e96234aca33981946a5b063ab5bfb080b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075878"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
Cet article décrit comment changer le modèle de licence d’une machine virtuelle SQL Server dans Azure à l’aide du nouveau fournisseur de ressources de machine virtuelle SQL, **Microsoft.SqlVirtualMachine**. Il existe deux modèles de licence pour une machine virtuelle qui héberge SQL Server : Paiement à l’utilisation et BYOL (apportez votre propre licence). En utilisant le portail Azure, Azure CLI ou PowerShell, vous pouvez changer le modèle de licence que votre machine virtuelle SQL Server utilise. 

Le modèle **Paiement à l’utilisation** (PAYG) signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence SQL Server.

Le modèle **BYOL (apportez votre propre licence)** est également connu sous le nom d’[Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) et vous permet d’utiliser votre propre licence SQL Server avec une machine virtuelle exécutant SQL Server. Pour plus d’informations sur les prix, consultez le [Guide des tarifs des machines virtuelles SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Le passage d’un modèle à l’autre n’entraîne **aucun temps d’arrêt**, aucun redémarrage de la machine virtuelle ni **aucun coût supplémentaire** (en fait, l’activation d’AHB *réduit* même les coûts) et est **à effet immédiat**. 

## <a name="remarks"></a>Remarques


 - Les clients Azure Cloud Solution Partner (CSP) peuvent utiliser Azure Hybrid Benefit en commençant par déployer une machine virtuelle avec paiement à l’utilisation, puis en la convertissant en licence BYOL. 
 - Lorsque vous inscrivez une image de machine virtuelle SQL Server personnalisée auprès du fournisseur de ressources, spécifiez le type de licence = 'AHUB'. Si vous laissez le type de licence vide ou spécifiez PAYG, l'inscription échoue. 
 - Si vous supprimez votre ressource de machine virtuelle SQL Server, vous revenez au paramètre de licence codé en dur de l’image. 
 - L'ajout d'une machine virtuelle SQL Server à un groupe à haute disponibilité implique la recréation de la machine virtuelle. Dès lors, toute machine virtuelle ajoutée à un groupe à haute disponibilité reviendra au type de licence paiement à l'utilisation par défaut et AHB devra être à nouveau activé. 
 - La possibilité de changer de modèle de licence est une fonctionnalité du fournisseur de ressources de machine virtuelle SQL. Le déploiement d’une image de place de marché via le portail Azure inscrit automatiquement une machine virtuelle SQL Server auprès du fournisseur de ressources. Toutefois, les clients qui installent automatiquement SQL Server doivent manuellement [inscrire leur machine virtuelle SQL Server](#register-sql-server-vm-with-the-sql-vm-resource-provider). 
 

 
## <a name="limitations"></a>Limites

 - La possibilité de convertir le modèle de licence n’est actuellement disponible que lorsque vous démarrez avec une image de machine virtuelle SQL Server avec paiement à l’utilisation. Si vous démarrez avec une image de licence correspondant au modèle BYOL à partir du portail, vous ne serez pas en mesure de convertir celle-ci en modèle avec paiement à l’utilisation.
 - Un changement de modèle de licence est uniquement possible pour les machines virtuelles déployées à l’aide du modèle Resource Manager. Les machines virtuelles déployées à l’aide du modèle classique ne sont pas prises en charge. 
 - Un changement de modèle de licence est uniquement possible pour les installations de cloud public.
 - Un changement de modèle de licence est uniquement possible sur les machines virtuelles dotée d'une seule carte réseau (interface réseau). Sur les machines virtuelles dotées de plusieurs cartes réseau, vous devez d’abord supprimer une des cartes réseau (via le portail Azure) avant de suivre la procédure. Sinon, vous vous heurterez à une erreur similaire à la suivante : `The virtual machine '\<vmname\>' has more than one NIC associated.` Bien qu'il vous soit possible d'ajouter à nouveau la carte réseau à la machine virtuelle après avoir changé de modèle de licence, les opérations effectuées via le panneau de configuration SQL (mise à jour corrective automatique et sauvegarde, par exemple) ne seront plus considérées comme prises en charge.

## <a name="prerequisites"></a>Prérequis

L’utilisation du fournisseur de ressources de machine virtuelle SQL nécessite l’extension IaaS SQL. Ainsi, pour utiliser le fournisseur de ressources de machine virtuelle SQL, vous avez besoin des éléments suivants :
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Une *machine virtuelle SQL Server* [paiement à l'utilisation](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) avec l'[extension IaaS SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) installée. 

## <a name="with-the-azure-portal"></a>Avec le portail Azure

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Vous pouvez modifier le modèle de licence directement à partir du portail. 

1. Ouvrez le [portail Azure](https://portal.azure.com) et lancez la [ressource des machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) pour votre machine virtuelle SQL Server. 
1. Sélectionnez **Configurer** sous **Paramètres**. 
1. Sélectionnez l'option **Azure Hybrid Benefit** et confirmez que vous disposez d'une licence SQL Server avec Software Assurance. 
1. Sélectionnez **Appliquer** en bas de la page **Configurer**. 

![Azure Hybrid Benefit dans le portail](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Cette option n’est pas disponible pour les images BYOL (apportez votre propre licence). 

## <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure

Vous pouvez utiliser Azure CLI pour changer votre modèle de licence.  

Avec l’extrait de code suivant, votre modèle de licence Paiement à l’utilisation se change en modèle BYOL (ou Azure Hybrid Benefit) :

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

L’extrait de code suivant bascule le modèle BYOL vers le modèle PAYG : 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

## <a name="with-powershell"></a>Avec PowerShell
Vous pouvez utiliser PowerShell pour changer votre modèle de licence.

Avec l’extrait de code suivant, votre modèle de licence Paiement à l’utilisation se change en modèle BYOL (ou Azure Hybrid Benefit) :

```powershell-interactive
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

Avec l’extrait de code suivant, votre modèle BYOL se change en Paiement à l’utilisation :

```powershell-interactive
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


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Inscrire une machine virtuelle SQL Server auprès d’un fournisseur de ressources de machine virtuelle SQL
Dans certaines situations, vous pouvez être amené à inscrire manuellement votre machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL. Pour ce faire, vous devrez peut-être également inscrire manuellement le fournisseur de ressources avec votre abonnement. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Inscrire un fournisseur de ressources de machine virtuelle SQL avec votre abonnement 

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources SQL, vous devez inscrire celui-ci sur votre abonnement. Vous pouvez le faire via le portail Azure ou Azure CLI. 

#### <a name="with-the-azure-portal"></a>Avec le portail Azure
Les étapes suivantes permettent d’inscrire le fournisseur de ressources SQL avec votre abonnement Azure à l’aide du portail Azure. 

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Dans le panneau **Abonnements**, accédez à **Fournisseurs de ressources**. 
1. Tapez `sql` dans le filtre pour afficher les fournisseurs de ressources liés à SQL. 
1. Sélectionnez *Inscrire*, *Réinscrire* ou *Désinscrire* pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

   ![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure
L’extrait de code suivant inscrira le fournisseur de ressources de machine virtuelle SQL avec votre abonnement Azure. 

```azurecli-interactive
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>Avec PowerShell

L’extrait de code suivant inscrira le fournisseur de ressources de machine virtuelle SQL avec votre abonnement Azure.

```powershell-interactive
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Inscrire une machine virtuelle SQL Server auprès d’un fournisseur de ressources SQL
Une fois le fournisseur de ressources de machine virtuelle SQL inscrit avec votre abonnement, vous pouvez utiliser PowerShell pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources SQL à l'aide d'Azure CLI. 

#### <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure

Inscrivez la machine virtuelle SQL Server à l’aide d’Azure CLI avec l'extrait de code suivant : 

```azurecli-interactive
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation> --license-type <AHUB or PAYG>
```

#### <a name="with-powershell"></a>Avec PowerShell
Inscrivez la machine virtuelle SQL Server à l’aide de PowerShell avec l’extrait de code suivant :

```powershell-interactive
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


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>La ressource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' sous le groupe de ressources '\<resource-group>' est introuvable. La propriété 'sqlServerLicenseType' est introuvable sur cet objet. Vérifiez que la propriété existe et peut être définie.
Cette erreur se produit lorsque vous tentez de modifier le modèle de licence sur une machine virtuelle SQL Server qui n’a pas été inscrite auprès du fournisseur de ressources SQL. Vous devez inscrire le fournisseur de ressources avec votre [abonnement](#register-sql-vm-resource-provider-with-subscription), puis inscrire votre machine virtuelle SQL Server auprès du [fournisseur de ressources](#register-sql-server-vm-with-sql-resource-provider) SQL. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Impossible de valider l’argument sur le paramètre « Sku »
Vous risquez de rencontrer cette erreur quand vous essayez de modifier votre modèle de licence de machine virtuelle SQL Server en utilisant Azure PowerShell > 4.0 : Set-AzResource : Impossible de valider l’argument sur le paramètre « Sku ». L’argument est Null ou vide. Fournissez un argument qui n’est pas non Null ou vide, puis réessayez la commande.
Pour corriger cette erreur, supprimez les marques de commentaire de ces lignes dans l’extrait de code PowerShell mentionné précédemment lorsque vous changez de modèle de licence :

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Utilisez le code suivant pour vérifier votre version d’Azure PowerShell :

```powershell-interactive
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


