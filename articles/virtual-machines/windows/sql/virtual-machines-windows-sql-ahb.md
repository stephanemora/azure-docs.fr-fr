---
title: Changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
description: Découvrez comment faire passer la machine virtuelle SQL Server de la licence avec paiement à l’utilisation à la licence BYOL (apportez votre propre licence) à l’aide d’Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201818"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Changer le modèle de licence d’une machine virtuelle SQL Server dans Azure
Cet article décrit comment changer le modèle de licence d’une machine virtuelle SQL Server dans Azure à l’aide du nouveau fournisseur de ressources de machine virtuelle SQL, **Microsoft.SqlVirtualMachine**.

Il existe trois modèles de licence pour une machine virtuelle qui héberge SQL Server : paiement à l’utilisation Azure Hybrid Benefit et récupération d’urgence (DR). En utilisant le Portail Azure, Azure CLI ou PowerShell, vous pouvez changer le modèle de licence de votre machine virtuelle SQL Server. 

- Le modèle de **paiement à l’utilisation** signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence SQL Server.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) vous permet d’utiliser votre propre licence SQL Server avec une machine virtuelle qui exécute SQL Server. 
- Le type de licence de **récupération d’urgence** est utilisé pour le [réplica de récupération d’urgence gratuit](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) dans Azure. 

Microsoft Azure Hybrid Benefit permet d’utiliser des licences SQL Server avec Software Assurance (« Licence qualifiée ») sur des machines virtuelles Azure. Avec Azure Hybrid Benefit, les clients ne sont pas facturés pour l’utilisation d’une licence SQL Server sur une machine virtuelle. Toutefois, ils doivent toujours payer le coût du calcul cloud sous-jacent (c’est-à-dire, le taux de base), le stockage et les sauvegardes. Ils doivent également payer pour les E/S associées à leur utilisation des services (le cas échéant).

Selon les conditions d’utilisation du produit Microsoft : « Les clients déclarent utiliser Azure SQL Database (Managed Instance, Pool élastique et base de données unique), Azure Data Factory, SQL Server Integration Services ou SQL Server Virtual Machines sous Azure Hybrid Benefit pour SQL Server lors de la configuration des charges de travail sur Azure. »

Pour déclarer l’utilisation d’Azure Hybrid Benefit pour SQL Server sur une machine virtuelle Azure et être conforme, vous disposez de trois options :

- Approvisionnez une machine virtuelle à l’aide d’une image BYOL (apportez votre propre licence) SQL Server à partir de la Place de marché Azure. Cette option est disponible uniquement pour les clients qui disposent d’un Accord Entreprise.
- Provisionnez une machine virtuelle à l’aide d’une image BYOL (apportez votre propre licence) SQL Server à partir de la Place de marché Azure, puis activez Azure Hybrid Benefit.
- Installez automatiquement SQL Server sur une machine virtuelle Azure, [effectuez l’inscription auprès du fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md) et activez Azure Hybrid Benefit.

Le type de licence SQL Server est défini lorsque la machine virtuelle est approvisionnée. Vous pouvez le modifier à tout moment par la suite. Le passage d’un modèle à un autre n’entraîne aucun temps d’arrêt, aucun redémarrage de la machine virtuelle ou du service SQL Server, aucun coût supplémentaire et prend effet immédiatement. En fait, l’activation d'*Azure Hybrid Benefit* réduit les coûts.

## <a name="prerequisites"></a>Prérequis

Le changement de modèle de licence de votre machine virtuelle SQL Server présente les exigences suivantes : 

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) inscrite auprès du [fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Vous devez disposer de [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) pour utiliser [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Machines virtuelles déjà inscrites auprès du fournisseur de ressources 

# <a name="portal"></a>[Portail](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Vous pouvez modifier le modèle de licence directement à partir du portail : 

1. Ouvrez le [portail Azure](https://portal.azure.com), puis la [ressource des machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) pour votre machine virtuelle SQL Server. 
1. Sélectionnez **Configurer** sous **Paramètres**. 
1. Sélectionnez l’option **Azure Hybrid Benefit** et cochez la case pour confirmer que vous disposez d’une licence SQL Server avec Software Assurance. 
1. Sélectionnez **Appliquer** en bas de la page **Configurer**. 

![Azure Hybrid Benefit dans le portail](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez utiliser l’interface de ligne de commande Azure pour changer votre modèle de licence.  


**Azure Hybrid Benefit**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Paiement à l’utilisation** 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Récupération d’urgence (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vous pouvez utiliser PowerShell pour changer votre modèle de licence.

**Azure Hybrid Benefit**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Paiement à l’utilisation**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Récupération d’urgence** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Machines virtuelles non inscrites auprès du fournisseur de ressources

Si vous avez approvisionné une machine virtuelle SQL Server à partir d’images de paiement à l'utilisation de la Place de marché Azure, le type de licence SQL Server sera avec paiement à l'utilisation. Si vous avez approvisionné une machine virtuelle SQL Server à l’aide d’une image BYOL (apportez votre propre licence) de la Place de marché Azure, le type de licence sera AHUB. Toutes les machines virtuelles SQL Server approvisionnées à partir d’images par défaut (paiement à l'utilisation) ou d’images BYOL (apportez votre propre licence) de la Place de marché Azure seront automatiquement inscrites auprès du fournisseur de ressources de machines virtuelles SQL Server afin qu’il soit possible de changer leur [type de licence](#vms-already-registered-with-the-resource-provider).

Vous êtes uniquement autorisé à installer automatiquement SQL Server sur une machine virtuelle Azure via Azure Hybrid Benefit. Vous devez [inscrire ces machines virtuelles auprès du fournisseur de ressources de machines virtuelles SQL](virtual-machines-windows-sql-register-with-resource-provider.md) en sélectionnant Azure Hybrid Benefit comme licence SQL Server pour déclarer que vous utilisez Azure Hybrid Benefit conformément aux conditions d’utilisation du produit Microsoft.

Vous pouvez modifier le type de licence d’une machine virtuelle SQL Server avec paiement à l'utilisation ou Azure Hybrid Benefit uniquement si la machine virtuelle SQL Server est inscrite auprès du fournisseur de ressources de la machine virtuelle SQL.

## <a name="remarks"></a>Notes

- Les clients Azure Cloud Solution Provider (CSP) peuvent utiliser Azure Hybrid Benefit en commençant par déployer une machine virtuelle avec paiement à l’utilisation, puis en la convertissant en licence BYOL s’ils disposent d’un programme Software Assurance actif.
- Si vous supprimez votre ressource de machine virtuelle SQL Server, vous revenez au paramètre de licence codé en dur de l’image. 
- La possibilité de changer de modèle de licence est une fonctionnalité du fournisseur de ressources de machine virtuelle SQL. Pendant le déploiement d’une image de la Place de marché Azure via le portail Azure, une machine virtuelle SQL Server est inscrite automatiquement auprès du fournisseur de ressources. Mais les clients qui installent automatiquement SQL Server doivent [inscrire manuellement leur machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- L’ajout d’une machine virtuelle SQL Server à un groupe à haute disponibilité implique la recréation de la machine virtuelle. Dès lors, toute machine virtuelle ajoutée à un groupe à haute disponibilité revient au type de licence paiement par défaut à l'utilisation. Azure Hybrid Benefit doit être réactivé. 


## <a name="limitations"></a>Limites

Le changement de modèle de licence est :
   - Disponible uniquement pour les clients disposant de [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Pris en charge uniquement pour les éditions Standard et Entreprise de SQL Server. Les changements de licence ne sont pas pris en charge pour les versions Express, Web et Développeur. 
   - Pris en charge uniquement pour les machines virtuelles déployées à l’aide du modèle Azure Resource Manager. Les machines virtuelles déployées via le modèle classique ne sont pas prises en charge. 
   - Disponible uniquement pour les clouds publics ou Azure Government. 
   - Pris en charge uniquement sur les machines virtuelles dotées d’une seule interface réseau (NIC). 


## <a name="known-errors"></a>Erreurs connues

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>La ressource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' sous le groupe de ressources '\<resource-group>' est introuvable.

Cette erreur se produit lorsque vous essayez de changer le modèle de licence sur une machine virtuelle SQL Server qui n’a pas été inscrite auprès du fournisseur de ressources de machines virtuelles SQL Server.

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Vous devez inscrire votre abonnement auprès du fournisseur de ressources, puis [inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources](virtual-machines-windows-sql-register-with-resource-provider.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>La machine virtuelle « \<vmname\> » a plus d’une carte réseau associée

Cette erreur se produit sur les machines virtuelles dotées de plusieurs cartes d’interface réseau. Supprimez l’une des cartes d’interface réseau avant de changer de modèle de licence. Bien que vous puissiez rajouter la carte d’interface réseau à la machine virtuelle après avoir changé de modèle de licence, les opérations dans le portail Azure telles que la sauvegarde et la mise à jour corrective automatiques ne sont plus prises en charge. 


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


