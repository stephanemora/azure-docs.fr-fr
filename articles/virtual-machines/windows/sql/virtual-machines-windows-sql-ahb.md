---
title: Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
description: Découvrez comment faire passer la machine virtuelle SQL Azure de la licence « paiement à l’utilisation » à la licence BYOL (apportez votre propre licence) à l’aide d’Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
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
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786759"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
Cet article décrit comment changer le modèle de licence d’une machine virtuelle SQL Server dans Azure à l’aide du nouveau fournisseur de ressources de machine virtuelle SQL, **Microsoft.SqlVirtualMachine**.

Il existe deux modèles de licence pour une machine virtuelle qui héberge SQL Server : Paiement à l’utilisation et Azure Hybrid Benefit (AHB). En utilisant le Portail Azure, Azure CLI ou PowerShell, vous pouvez changer le modèle de licence de votre machine virtuelle SQL Server. 

Le modèle **Paiement à l’utilisation** (PAYG) signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence SQL Server.
[Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) vous permet d’utiliser votre propre licence SQL Server avec une machine virtuelle exécutant SQL Server. 

Microsoft Azure Hybrid Benefit pour SQL Server permet d’utiliser des licences SQL Server avec Software Assurance (« Licence qualifiée ») sur des machines virtuelles Azure. Avec Azure Hybrid Benefit pour SQL Server, les clients ne sont pas facturés pour l’utilisation de la licence SQL Server sur la machine virtuelle, mais ils doivent quand même payer le coût du traitement cloud sous-jacent (c’est-à-dire le taux de base), le stockage et les sauvegardes, ainsi que les E-S associées à leur utilisation des services (le cas échéant).

Les conditions d’utilisation des produits Microsoft imposent que les clients déclarent qu’ils utilisent Azure SQL Database (Managed Instance, Pool élastique et base de données unique), Azure Data Factory, SQL Server Integration Services ou SQL Server Virtual Machines sous Azure Hybrid Benefit pour SQL Server lors de la configuration des charges de travail sur Azure.

Pour déclarer l’utilisation d’Azure Hybrid Benefit pour SQL Server sur machine virtuelle Azure et être conforme, vous disposez de trois options :

1. Approvisionnez une machine virtuelle à l’aide d’une image BYOL (apportez votre propre licence) SQL Server à partir de la Place de marché Azure, disponible uniquement pour les clients ayant souscrit à un Contrat Entreprise.
1. Approvisionnez une machine virtuelle à l’aide d’une image PAYG (Paiement à l'utilisation) SQL Server à partir de la Place de marché Azure, puis activez AHB.
1. Installez automatiquement SQL Server sur une machine virtuelle Azure, [inscrivez manuellement leur machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) et activez AHB.

Défini lorsque la machine virtuelle est approvisionnée, le type de licence SQL Server peut être changé à tout moment par la suite. Le passage d’un modèle à un autre n’entraîne **aucun temps d’arrêt**, aucun redémarrage de la machine virtuelle ni **aucun coût supplémentaire** (en fait, l’activation d’AHB *réduit* même les coûts) et est **à effet immédiat**. 

## <a name="prerequisites"></a>Prérequis

L’utilisation du fournisseur de ressources de machine virtuelle SQL nécessite l’extension IaaS SQL. Ainsi, pour utiliser le fournisseur de ressources de machine virtuelle SQL, vous avez besoin des éléments suivants :
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Une [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) inscrite auprès du [fournisseur de ressources de machines virtuelles SQL](virtual-machines-windows-sql-register-with-resource-provider.md) installé. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Changer de licence pour les machines virtuelles déjà inscrites auprès d’un fournisseur de ressources 

# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Vous pouvez modifier le modèle de licence directement à partir du portail. 

1. Ouvrez le [portail Azure](https://portal.azure.com) et lancez la [ressource des machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) pour votre machine virtuelle SQL Server. 
1. Sélectionnez **Configurer** sous **Paramètres**. 
1. Sélectionnez l’option **Azure Hybrid Benefit** et cochez la case pour confirmer que vous disposez d’une licence SQL Server avec Software Assurance. 
1. Sélectionnez **Appliquer** en bas de la page **Configurer**. 

![Azure Hybrid Benefit dans le portail](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
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
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Changer la licence pour les machines virtuelles non inscrites auprès d’un fournisseur de ressources

Si vous avez approvisionné une machine virtuelle SQL Server à partir d’images PAYG de la Place de marché Azure, le type de licence SQL sera PAYG. Si vous avez approvisionné une machine virtuelle SQL Server à l’aide d’une image BYOL de la Place de marché Azure, le type de licence sera AHUB. Toutes les machines virtuelles SQL Server approvisionnées à partir d’images par défaut (PAYG) ou d’images BYOL de la Place de marché Azure seront automatiquement inscrites auprès du fournisseur de ressources de machines virtuelles SQL Server afin qu’il soit possible de changer leur [type de licence](#change-license-for-vms-already-registered-with-resource-provider).

Vous n’êtes éligible qu'à l’installation automatique de SQL Server sur machine virtuelle Azure via Azure Hybrid Benefit et vous devez [inscrire ces machines virtuelles auprès du fournisseur de ressources de machines virtuelles SQL](virtual-machines-windows-sql-register-with-resource-provider.md) en sélectionnant AHB comme licence SQL Server pour déclarer que vous utilisez AHB conformément aux conditions d’utilisation du produit Microsoft.

Vous ne pouvez changer le type de licence d'une machine virtuelle SQL Server en PAYG ou AHB que si cette machine virtuelle est inscrite auprès du fournisseur de ressources de machines virtuelles SQL Server ; et toutes les machines virtuelles SQL Server doivent être inscrites auprès du fournisseur de ressources de machines virtuelles SQL Server conformément à la licence.

## <a name="remarks"></a>Remarques

 - Les clients Azure Cloud Solution Partner (CSP) peuvent utiliser Azure Hybrid Benefit en commençant par déployer une machine virtuelle avec paiement à l’utilisation, puis en la convertissant en licence BYOL s’il dispose d’une association de sécurité active.
 - Si vous supprimez votre ressource de machine virtuelle SQL Server, vous revenez au paramètre de licence codé en dur de l’image. 
  - La possibilité de changer de modèle de licence est une fonctionnalité du fournisseur de ressources de machine virtuelle SQL. Le déploiement d’une image de place de marché via le portail Azure inscrit automatiquement une machine virtuelle SQL Server auprès du fournisseur de ressources. Toutefois, les clients qui installent automatiquement SQL Server doivent manuellement [inscrire leur machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- L'ajout d'une machine virtuelle SQL Server à un groupe à haute disponibilité implique la recréation de la machine virtuelle. Dès lors, toute machine virtuelle ajoutée à un groupe à haute disponibilité reviendra au type de licence paiement à l'utilisation par défaut et AHB devra être à nouveau activé. 


## <a name="limitations"></a>Limites

 - Un changement de modèle de licence est disponible uniquement pour les clients disposant de la Software Assurance.
 - Un changement de modèle de licence pris en charge uniquement pour les éditions standard et entreprise de SQL Server. Les changements de licence ne sont pas pris en charge pour les versions Express, Web et Développeur. 
 - Un changement de modèle de licence est uniquement possible pour les machines virtuelles déployées à l’aide du modèle Resource Manager. Les machines virtuelles déployées à l’aide du modèle classique ne sont pas prises en charge. 
 - Un changement de modèle de licence est uniquement possible pour les installations de cloud public.
 - Un changement de modèle de licence est uniquement possible sur les machines virtuelles dotée d'une seule carte réseau (interface réseau). Sur les machines virtuelles dotées de plusieurs cartes réseau, vous devez d’abord supprimer une des cartes réseau (via le portail Azure) avant de suivre la procédure. Sinon, vous vous heurterez à une erreur similaire à la suivante : `The virtual machine '\<vmname\>' has more than one NIC associated.` Bien qu’il vous soit possible d’ajouter à nouveau la carte réseau à la machine virtuelle après avoir changé de modèle de licence, les opérations effectuées via la page de configuration SQL dans le Portail Azure (mise à jour corrective automatique et sauvegarde, par exemple) ne seront plus considérées comme prises en charge.


## <a name="known-errors"></a>Erreurs connues

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>La ressource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' sous le groupe de ressources '\<resource-group>' est introuvable. La propriété 'sqlServerLicenseType' est introuvable sur cet objet. Vérifiez que la propriété existe et peut être définie.
Cette erreur se produit lorsque vous tentez de changer le modèle de licence sur une machine virtuelle SQL Server qui n’a pas été inscrite auprès du fournisseur de ressources de machines virtuelles SQL Server. Vous devez inscrire le fournisseur de ressources avec votre [abonnement](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription), puis inscrire votre machine virtuelle SQL Server auprès du [fournisseur de ressources](virtual-machines-windows-sql-register-with-resource-provider.md) SQL. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Impossible de valider l’argument sur le paramètre « Sku »
Vous risquez de rencontrer cette erreur quand vous essayez de changer votre modèle de licence de machine virtuelle SQL Server en utilisant Azure PowerShell > 4.0 : `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

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
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


