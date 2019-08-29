---
title: Changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
description: Découvrez comment faire passer la machine virtuelle SQL Server de la licence avec paiement à l’utilisation à la licence BYOL (apportez votre propre licence) à l’aide d’Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2cce369f6a0670790ede7367609f87c18672ddd5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100609"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
Cet article décrit comment changer le modèle de licence d’une machine virtuelle SQL Server dans Azure à l’aide du nouveau fournisseur de ressources de machine virtuelle SQL, **Microsoft.SqlVirtualMachine**.

Il existe deux modèles de licence pour une machine virtuelle qui héberge SQL Server : paiement à l’utilisation et Azure Hybrid Benefit. En utilisant le Portail Azure, Azure CLI ou PowerShell, vous pouvez changer le modèle de licence de votre machine virtuelle SQL Server. 

Le modèle de paiement à l’utilisation signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence SQL Server.
[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) vous permet d’utiliser votre propre licence SQL Server avec une machine virtuelle qui exécute SQL Server. 

Microsoft Azure Hybrid Benefit permet d’utiliser des licences SQL Server avec Software Assurance (« Licence qualifiée ») sur des machines virtuelles Azure. Avec Azure Hybrid Benefit, les clients ne sont pas facturés pour l’utilisation d’une licence SQL Server sur une machine virtuelle. Toutefois, ils doivent toujours payer le coût du calcul cloud sous-jacent (c’est-à-dire, le taux de base), le stockage et les sauvegardes. Ils doivent également payer pour les E/S associées à leur utilisation des services (le cas échéant).

Selon les conditions d’utilisation du produit Microsoft : « Les clients déclarent utiliser Azure SQL Database (Managed Instance, Pool élastique et base de données unique), Azure Data Factory, SQL Server Integration Services ou SQL Server Virtual Machines sous Azure Hybrid Benefit pour SQL Server lors de la configuration des charges de travail sur Azure. »

Pour déclarer l’utilisation d’Azure Hybrid Benefit pour SQL Server sur une machine virtuelle Azure et être conforme, vous disposez de trois options :

- Approvisionnez une machine virtuelle à l’aide d’une image BYOL (apportez votre propre licence) SQL Server à partir de la Place de marché Azure. Cette option est disponible uniquement pour les clients qui disposent d’un Accord Entreprise.
- Approvisionnez une machine virtuelle à l’aide d’une image BYOL (apportez votre propre licence) SQL Server à partir de la Place de marché Azure, puis activez Azure Hybrid Benefit.
- Installez automatiquement SQL Server sur une machine virtuelle Azure, [inscrivez manuellement la machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) et activez Azure Hybrid Benefit.

Le type de licence SQL Server est défini lorsque la machine virtuelle est approvisionnée. Vous pouvez le modifier à tout moment par la suite. Le passage d’un modèle à un autre n’entraîne aucun temps d’arrêt, aucun redémarrage de la machine virtuelle ni aucun coût supplémentaire prend effet immédiatement. En fait, l’activation d'*Azure Hybrid Benefit* réduit les coûts.

## <a name="prerequisites"></a>Prérequis

L’utilisation du fournisseur de ressources de machine virtuelle SQL nécessite l’extension IaaS SQL Server. Vous avez besoin des éléments suivants :
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Une [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) inscrite auprès du [fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md).


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Changer la licence pour les machines virtuelles déjà inscrites auprès du fournisseur de ressources 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Vous pouvez modifier le modèle de licence directement à partir du portail : 

1. Ouvrez le [portail Azure](https://portal.azure.com), puis la [ressource des machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) pour votre machine virtuelle SQL Server. 
1. Sélectionnez **Configurer** sous **Paramètres**. 
1. Sélectionnez l’option **Azure Hybrid Benefit** et cochez la case pour confirmer que vous disposez d’une licence SQL Server avec Software Assurance. 
1. Sélectionnez **Appliquer** en bas de la page **Configurer**. 

![Azure Hybrid Benefit dans le portail](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Vous pouvez utiliser l’interface de ligne de commande Azure pour changer votre modèle de licence.  

Avec l’extrait de code suivant, votre modèle de licence avec paiement à l’utilisation devient une licence BYOL (apportez votre propre licence) (ou avec Azure Hybrid Benefit) :

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Vous pouvez utiliser PowerShell pour changer votre modèle de licence.

Avec l’extrait de code suivant, votre modèle de licence avec paiement à l’utilisation devient une licence BYOL (apportez votre propre licence) (ou avec Azure Hybrid Benefit) :

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

L’extrait de code suivant bascule le modèle BYOL vers le modèle PAYG :

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
---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Changer la licence pour les machines virtuelles non inscrites auprès du fournisseur de ressources

Si vous avez approvisionné une machine virtuelle SQL Server à partir d’images de paiement à l'utilisation de la Place de marché Azure, le type de licence SQL Server sera avec paiement à l'utilisation. Si vous avez approvisionné une machine virtuelle SQL Server à l’aide d’une image BYOL (apportez votre propre licence) de la Place de marché Azure, le type de licence sera AHUB. Toutes les machines virtuelles SQL Server approvisionnées à partir d’images par défaut (paiement à l'utilisation) ou d’images BYOL (apportez votre propre licence) de la Place de marché Azure seront automatiquement inscrites auprès du fournisseur de ressources de machines virtuelles SQL Server afin qu’il soit possible de changer leur [type de licence](#change-the-license-for-vms-already-registered-with-the-resource-provider).

Vous êtes uniquement autorisé à installer automatiquement SQL Server sur une machine virtuelle Azure via Azure Hybrid Benefit. Vous devez [inscrire ces machines virtuelles auprès du fournisseur de ressources de machines virtuelles SQL](virtual-machines-windows-sql-register-with-resource-provider.md) en sélectionnant Azure Hybrid Benefit comme licence SQL Server pour déclarer que vous utilisez Azure Hybrid Benefit conformément aux conditions d’utilisation du produit Microsoft.

Vous pouvez modifier le type de licence d’une machine virtuelle SQL Server avec paiement à l'utilisation ou Azure Hybrid Benefit uniquement si la machine virtuelle SQL Server est inscrite auprès du fournisseur de ressources de la machine virtuelle SQL. Toutes les machines virtuelles SQL Server doivent être inscrites auprès du fournisseur de ressources pour la conformité des licences.

## <a name="remarks"></a>Remarques

- Les clients Azure Cloud Solution Provider (CSP) peuvent utiliser Azure Hybrid Benefit en commençant par déployer une machine virtuelle avec paiement à l’utilisation, puis en la convertissant en licence BYOL s’ils disposent d’un programme Software Assurance actif.
- Si vous supprimez votre ressource de machine virtuelle SQL Server, vous revenez au paramètre de licence codé en dur de l’image. 
- La possibilité de changer de modèle de licence est une fonctionnalité du fournisseur de ressources de machine virtuelle SQL. Le déploiement d’une image de Place de marché Azure via le portail Azure inscrit automatiquement une machine virtuelle SQL Server auprès du fournisseur de ressources. Mais les clients qui installent automatiquement SQL Server doivent [inscrire manuellement leur machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- L'ajout d'une machine virtuelle SQL Server à un groupe à haute disponibilité implique la recréation de la machine virtuelle. Dès lors, toute machine virtuelle ajoutée à un groupe à haute disponibilité revient au type de licence paiement par défaut à l'utilisation. Azure Hybrid Benefit doit être réactivé. 


## <a name="limitations"></a>Limites

- Un changement de modèle de licence est disponible uniquement pour les clients disposant de Software Assurance.
- Un changement de modèle de licence pris en charge uniquement pour les éditions Standard et Entreprise de SQL Server. Les changements de licence ne sont pas pris en charge pour les versions Express, Web et Développeur. 
- Un changement de modèle de licence est pris en charge uniquement pour les machines virtuelles déployées à l’aide du modèle Azure Resource Manager. Les machines virtuelles déployées via le modèle classique ne sont pas prises en charge. Vous pouvez migrer votre machine virtuelle du modèle classique au modèle Resource Manager et l’inscrire auprès du fournisseur de ressources de machine virtuelle SQL. Une fois que la machine virtuelle est inscrite auprès du fournisseur de ressources de machine virtuelle SQL, les changements du modèle de licence sont disponibles sur la machine virtuelle.
- Un changement de modèle de licence est possible uniquement pour les installations de cloud public.
- Un changement de modèle de licence est pris en charge uniquement sur les machines virtuelles dotée d'une seule carte réseau (interface réseau). Sur les machines virtuelles dotées de plusieurs cartes réseau, vous devez d’abord supprimer une des cartes réseau (via le portail Azure) avant de suivre la procédure. Sinon, un message d’erreur similaire au suivant s’affiche : 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  Bien qu’il vous soit possible d’ajouter à nouveau la carte réseau à la machine virtuelle après avoir changé de modèle de licence, les opérations effectuées via la page de configuration SQL Server dans le Portail Azure (mise à jour corrective automatique et sauvegarde, par exemple) ne seront plus considérées comme prises en charge.

## <a name="known-errors"></a>Erreurs connues

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>La ressource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' sous le groupe de ressources '\<resource-group>' est introuvable.
Cette erreur se produit lorsque vous essayez de changer le modèle de licence sur une machine virtuelle SQL Server qui n’a pas été inscrite auprès du fournisseur de ressources de machines virtuelles SQL Server.

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Vous devez inscrire le fournisseur de ressources avec votre [abonnement](virtual-machines-windows-sql-register-with-resource-provider.md#register-the-sql-vm-resource-provider-with-a-subscription), puis [inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Impossible de valider l’argument sur le paramètre « Sku »
Vous risquez de rencontrer cette erreur quand vous essayez de changer votre modèle de licence de machine virtuelle SQL Server avec des versions d’Azure PowerShell ultérieures à 4.0 :

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Pour corriger cette erreur, supprimez les marques de commentaire de ces lignes dans l’extrait de code PowerShell mentionné précédemment lorsque vous changez de modèle de licence :

  ```powershell-interactive
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
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


