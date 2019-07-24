---
title: Inscrire une machine virtuelle SQL Server dans Azure avec le fournisseur de ressources de machines virtuelles SQL | Microsoft Docs
description: Inscrivez votre machine virtuelle SQL Server avec le fournisseur de ressources de machines virtuelles SQL pour améliorer la facilité de gestion.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786752"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Inscrire une machine virtuelle SQL Server dans Azure avec le fournisseur de ressources de machines virtuelles SQL

Cet article décrit comment inscrire votre machine virtuelle Azure SQL Server avec le fournisseur de ressources de machines virtuelles SQL. 

Le déploiement d’une image de place de marché de machine virtuelle SQL Server via le portail Azure inscrit automatiquement la machine virtuelle SQL Server avec le fournisseur de ressources. Toutefois, si vous choisissez d’installer SQL Server sur une machine virtuelle Azure vous-même, au lieu de choisir une image à partir de la place de marché Azure, vous devez inscrire votre machine virtuelle SQL Server avec le fournisseur de ressources aujourd'hui pour les raisons suivantes :

-  **Conformité** – Conformément aux conditions pour les produits Microsoft, les clients qui utilisent [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) doivent indiquer à Microsoft lorsqu’ils utilisent Azure Hybrid Benefit et, pour ce faire, ils doivent s’enregistrer avec le fournisseur de ressources de machines virtuelles SQL. 

-  **Avantages en matière de fonctionnalités** - L’inscription de votre machine virtuelle SQL Server avec le fournisseur de ressources déverrouille les [correctifs automatiques](virtual-machines-windows-sql-automated-patching.md), la [sauvegarde automatique](virtual-machines-windows-sql-automated-backup-v2.md), les fonctionnalités de surveillance et de gestion, ainsi qu’une flexibilité accrue pour les [licences](virtual-machines-windows-sql-ahb.md) et [l’édition](virtual-machines-windows-sql-change-edition.md). Auparavant, ces fonctionnalités étaient disponibles uniquement pour les images de machine virtuelle SQL Server de la place de marché Azure.

L’installation de SQL Server sur une machine virtuelle Azure ou l’approvisionnement d’une machine virtuelle Azure à partir d’un disque dur virtuel personnalisé avec SQL Server est conforme pour Azure Hybrid Benefit pour SQL Server avec la condition que les clients indiquent cette utilisation à Microsoft en s’inscrivant avec le fournisseur de ressources de machines virtuelles SQL. 

Pour utiliser le fournisseur de ressources de machines virtuelles SQL, vous devez également inscrire le fournisseur de ressources de machines virtuelles SQL avec votre abonnement. Cela peut être effectué dans le portail Azure, ou avec Azure CLI ou PowerShell. 

## <a name="prerequisites"></a>Prérequis

Pour inscrire votre machine virtuelle SQL Server avec le fournisseur de ressources, vous aurez besoin des éléments suivants : 

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli) et [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>S’inscrire auprès du fournisseur de ressources de machines virtuelles SQL
Si [l’extension IaaS SQL](virtual-machines-windows-sql-server-agent-extension.md) est déjà installée sur la machine virtuelle, l’inscription auprès du fournisseur de ressources de machines virtuelles SQL se fait simplement en créant une ressource de métadonnées de type Microsoft.SqlVirtualMachine/SqlVirtualMachines. Voici l’extrait de code pour s’enregistrer avec le fournisseur de ressources de machines virtuelles SQL si l’extension IaaS SQL est déjà installée sur la machine virtuelle. Vous devez fournir le type de licence SQL Server souhaité lors de l’inscription avec le fournisseur de ressources de machines virtuelles SQL : « PAYG » ou « AHUB ». 

Inscrivez la machine virtuelle SQL Server à l’aide de PowerShell avec l’extrait de code suivant :

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

Si l’extension IaaS SQL n’est pas installée sur la machine virtuelle, vous pouvez vous inscrire avec le fournisseur de ressources de machines virtuelles SQL en spécifiant le mode de gestion SQL léger. En mode de gestion SQL léger, le fournisseur de ressources de machines virtuelles SQL installe automatiquement l’extension IaaS SQL en [mode léger](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) et vérifie les métadonnées de l’instance SQL Server ; cela ne pas redémarre pas le service SQL Server. Vous devez fournir le type de licence SQL Server souhaité lors de l’inscription avec le fournisseur de ressources de machines virtuelles SQL : « PAYG » ou « AHUB ». 

Inscrivez la machine virtuelle SQL Server en mode de gestion SQL léger à l’aide de PowerShell avec l’extrait de code suivant :

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
L’inscription auprès du fournisseur de ressources de machines virtuelles SQL en [mode léger](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) garantira la conformité et activera les licences flexibles ainsi que les mises à jour de l’édition de SQL Server en place. Les instances de cluster de basculement et les déploiements à plusieurs instances peuvent être inscrits avec le fournisseur de ressources de machines virtuelles SQL uniquement en mode léger. Vous pouvez suivre les instructions figurant sur le portail Azure pour mettre à niveau vers le [mode complet](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) et activer le jeu de fonctionnalités de gestion complet avec un redémarrage de SQL Server à tout moment. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Inscrire SQL Server 2008/R2 sur des machines virtuelles Windows Server 2008

SQL Server 2008 et 2008 R2 installés sur Windows Server 2008 peuvent être enregistrés avec le fournisseur de ressources de machines virtuelles SQL en mode [NoAgent](virtual-machines-windows-sql-server-agent-extension.md). Cette option garantit la conformité et permet de surveiller la machine virtuelle SQL Server dans le portail Azure avec des fonctionnalités limitées.

Le tableau suivant répertorie les valeurs autorisées pour les paramètres fournis lors de l’inscription :

| Paramètre | Valeurs acceptables                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'` ou `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` ou `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Pour inscrire votre serveur SQL Server 2008 ou 2008 R2 sur une instance Windows Server 2008, utilisez l’extrait de code PowerShell suivant :  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>Vérifier l’état de l’inscription
Vous pouvez vérifier si votre serveur SQL Server a déjà été inscrit auprès du fournisseur de ressources de machines virtuelles SQL via le portail Azure, Azure CLI ou PowerShell. 

# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)
Pour vérifier l’état d’inscription à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à vos [machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md).
1. Sélectionnez votre machine virtuelle SQL Server dans la liste. Si votre machine virtuelle SQL Server n’est pas répertoriée ici, il est probable que votre machine virtuelle SQL Server n’ait pas été inscrite avec le fournisseur de ressources de machines virtuelles SQL. 
1. Affichez la valeur sous `Status`. Si `Status = Succeeded`, alors la machine virtuelle SQL Server a été inscrite avec le fournisseur de ressources de machines virtuelles SQL. 

    ![Vérifier l’état avec l’inscription avec le fournisseur de ressources SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Vérifiez l’état d’inscription actuelle d’une machine virtuelle SQL Server avec la commande AZ CLI suivante. `ProvisioningState` affichera `Succeeded` si l’inscription a réussi. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Vérifiez l’état d’inscription actuelle d’une machine virtuelle SQL Server avec l’applet de commande PowerShell suivante. `ProvisioningState` affichera `Succeeded` si l’inscription a réussi. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Une erreur indique que la machine virtuelle SQL Server n’a pas été inscrite avec le fournisseur de ressources. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Inscrire un fournisseur de ressources de machines virtuelles SQL avec votre abonnement 

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machines virtuelles SQL, vous devez inscrire celui-ci sur votre abonnement. Vous pouvez le faire avec le portail Azure ou Azure CLI.

# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)


Les étapes suivantes permettent d’inscrire le fournisseur de ressources de machines virtuelles SQL avec votre abonnement Azure à l’aide du portail Azure. 

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Sur la page **Abonnements**, accédez à **Fournisseurs de ressources**. 
1. Tapez `sql` dans le filtre pour afficher les fournisseurs de ressources liés à SQL. 
1. Sélectionnez *Inscrire*, *Réinscrire* ou *Désinscrire* pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

   ![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

L’extrait de code suivant inscrira le fournisseur de ressources de machines virtuelles SQL avec votre abonnement Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

L’extrait de code PowerShell suivant inscrira le fournisseur de ressources de machines virtuelles SQL avec votre abonnement Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Remarques

 - Le fournisseur de ressources de machines virtuelles SQL prend uniquement en charge les machines virtuelles SQL Server déployées avec Resource Manager. Les machines virtuelles SQL Server déployées à l’aide du modèle classique ne sont pas prises en charge. 
 - Le fournisseur de ressources de machines virtuelles SQL prend uniquement en charge les machines virtuelles SQL Server déployées dans le cloud public. Les déploiements dans un cloud de gouvernement ou privé ne sont pas pris en charge. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


