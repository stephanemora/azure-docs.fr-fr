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
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305870"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Inscrire une machine virtuelle SQL Server dans Azure avec le fournisseur de ressources de machines virtuelles SQL

Cet article décrit comment inscrire votre machine virtuelle Azure SQL Server avec le fournisseur de ressources de machines virtuelles SQL. 

Le déploiement d’une image de place de marché de machine virtuelle SQL Server via le portail Azure inscrit automatiquement la machine virtuelle SQL Server chez le fournisseur de ressources. Toutefois, si vous choisissez d’installer SQL Server sur une machine virtuelle Azure vous-même, au lieu de choisir une image à partir de la place de marché Azure, vous devez inscrire votre machine virtuelle SQL Server avec le fournisseur de ressources aujourd’hui pour les raisons suivantes :

-  **Conformité** – Conformément aux conditions pour les produits Microsoft, les clients qui utilisent [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) doivent indiquer à Microsoft lorsqu’ils utilisent Azure Hybrid Benefit et, pour ce faire, ils doivent s’enregistrer avec le fournisseur de ressources de machines virtuelles SQL. 

-  **Avantages en matière de fonctionnalités** – L’inscription de votre machine virtuelle SQL Server avec le fournisseur de ressources déverrouille les [correctifs automatiques](virtual-machines-windows-sql-automated-patching.md), la [sauvegarde automatique](virtual-machines-windows-sql-automated-backup-v2.md), les fonctionnalités de surveillance et de gestion, ainsi qu’une flexibilité accrue pour les [licences](virtual-machines-windows-sql-ahb.md) et [l’édition](virtual-machines-windows-sql-change-edition.md). Auparavant, ces fonctionnalités étaient disponibles uniquement pour les images de machine virtuelle SQL Server de la place de marché Azure.

L’installation de SQL Server sur une machine virtuelle Azure ou l’approvisionnement d’une machine virtuelle Azure à partir d’un disque dur virtuel personnalisé avec SQL Server est conforme pour Azure Hybrid Benefit pour SQL Server sous la condition que les clients indiquent cette utilisation à Microsoft en s’inscrivant chez le fournisseur de ressources de machines virtuelles SQL. 

Pour utiliser le fournisseur de ressources de machines virtuelles SQL, vous devez également inscrire le fournisseur de ressources de machines virtuelles SQL à votre abonnement. Cela peut être effectué dans le portail Azure, ou avec Azure CLI ou PowerShell. 

## <a name="prerequisites"></a>Prérequis

Pour inscrire votre machine virtuelle SQL Server avec le fournisseur de ressources, vous aurez besoin des éléments suivants : 

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) et [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>S’inscrire auprès du fournisseur de ressources de machines virtuelles SQL
Si [l’extension IaaS SQL](virtual-machines-windows-sql-server-agent-extension.md) est déjà installée sur la machine virtuelle, l’inscription auprès du fournisseur de ressources de machines virtuelles SQL se fait simplement en créant une ressource de métadonnées de type Microsoft.SqlVirtualMachine/SqlVirtualMachines. Voici l’extrait de code pour s’enregistrer avec le fournisseur de ressources de machines virtuelles SQL si l’extension IaaS SQL est déjà installée sur la machine virtuelle. Vous devez fournir le type de licence SQL Server souhaité lors de l’inscription avec le fournisseur de ressources de machines virtuelles SQL : « PAYG » ou « AHUB ». 

Inscrivez la machine virtuelle SQL Server à l’aide de PowerShell avec l’extrait de code suivant :

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Si l’extension IaaS SQL n’est pas installée sur la machine virtuelle, vous pouvez vous inscrire avec le fournisseur de ressources de machines virtuelles SQL en spécifiant le mode de gestion SQL léger. En mode de gestion SQL léger, le fournisseur de ressources de machines virtuelles SQL installe automatiquement l’extension IaaS SQL en [mode léger](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) et vérifie les métadonnées de l’instance SQL Server ; cela ne pas redémarre pas le service SQL Server. Vous devez fournir le type de licence SQL Server souhaité lors de l’inscription avec le fournisseur de ressources de machines virtuelles SQL : « PAYG » ou « AHUB ». 

Inscrivez la machine virtuelle SQL Server en mode de gestion SQL léger à l’aide de PowerShell avec l’extrait de code suivant :

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
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
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Vérifier l’état de l’inscription
Vous pouvez vérifier si votre serveur SQL Server a déjà été inscrit auprès du fournisseur de ressources de machines virtuelles SQL via le portail Azure, Azure CLI ou PowerShell. 

### <a name="azure-portal"></a>Portail Azure 
Pour vérifier l’état d’inscription à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à vos [machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md).
1. Sélectionnez votre machine virtuelle SQL Server dans la liste. Si votre machine virtuelle SQL Server n’est pas répertoriée ici, il est probable que votre machine virtuelle SQL Server n’ait pas été inscrite avec le fournisseur de ressources de machines virtuelles SQL. 
1. Affichez la valeur sous `Status`. Si `Status = Succeeded`, alors la machine virtuelle SQL Server a été inscrite avec le fournisseur de ressources de machines virtuelles SQL. 

    ![Vérifier l’état avec l’inscription avec le fournisseur de ressources SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Az CLI

Vérifiez l’état d’inscription actuelle d’une machine virtuelle SQL Server avec la commande Az CLI suivante. `ProvisioningState` affichera `Succeeded` si l’inscription a réussi. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Vérifiez l’état d’inscription actuelle d’une machine virtuelle SQL Server avec l’applet de commande PowerShell suivante. `ProvisioningState` affichera `Succeeded` si l’inscription a réussi. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Une erreur indique que la machine virtuelle SQL Server n’a pas été inscrite avec le fournisseur de ressources. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Inscrire un fournisseur de ressources de machines virtuelles SQL avec votre abonnement 

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machines virtuelles SQL, vous devez inscrire celui-ci sur votre abonnement. Vous pouvez le faire avec le portail Azure ou Azure CLI.

### <a name="azure-portal"></a>Portail Azure

Les étapes suivantes permettent d’inscrire le fournisseur de ressources de machines virtuelles SQL avec votre abonnement Azure à l’aide du portail Azure. 

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Sur la page **Abonnements**, accédez à **Fournisseurs de ressources**. 
1. Tapez `sql` dans le filtre pour afficher les fournisseurs de ressources liés à SQL. 
1. Sélectionnez *Inscrire*, *Réinscrire* ou *Désinscrire* pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

   ![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Az CLI
L’extrait de code suivant inscrira le fournisseur de ressources de machines virtuelles SQL avec votre abonnement Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

L’extrait de code PowerShell suivant inscrira le fournisseur de ressources de machines virtuelles SQL avec votre abonnement Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Remarques

 - Le fournisseur de ressources de machines virtuelles SQL prend uniquement en charge les machines virtuelles SQL Server déployées avec Resource Manager. Les machines virtuelles SQL Server déployées à l’aide du modèle classique ne sont pas prises en charge. 
 - Le fournisseur de ressources de machines virtuelles SQL prend uniquement en charge les machines virtuelles SQL Server déployées dans le cloud public. Les déploiements dans un cloud de gouvernement ou privé ne sont pas pris en charge. 
 
## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 

**Dois-je inscrire ma machine virtuelle SQL Server provisionnée à partir d’une image SQL sur la Place de marché Azure ?**

Non. Microsoft inscrit automatiquement les machines virtuelles provisionnées à partir des images SQL sur la Place de marché Azure. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL est requise uniquement si la machine virtuelle n’a PAS été provisionnée à partir des images SQL sur la Place de marché Azure et que SQL Server a été installé automatiquement.

**Le fournisseur de ressources de machine virtuelle SQL est-il disponible pour tous les clients ?** 

Oui. Les clients doivent inscrire leur machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL s’ils n’ont pas utilisé d’image SQL Server à partir de la Place de marché Azure avec SQL Server installé automatiquement, ou qu’ils ont apporté leur disque dur virtuel (VHD) personnalisé. Les machines virtuelles détenues par tous les types d’abonnements (Direct, EA et CSP) peuvent s’inscrire auprès du fournisseur de ressources de machine virtuelle SQL.

**Dois-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL si l’extension IaaS SQL est déjà installée sur ma machine virtuelle SQL Server ?**

Si votre machine virtuelle SQL Server est installée automatiquement et n’est pas provisionnée à partir des images SQL sur la Place de marché Azure, vous devez vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL, même si vous avez installé l’extension IaaS SQL. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL crée une ressource de type Microsoft.SqlVirtualMachines. L’installation de l’extension IaaS SQL ne crée pas cette ressource.

**Quel est le mode de gestion SQL par défaut lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL ?**

Le mode de gestion SQL par défaut lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL est le mode _complet_. Si la propriété de gestion SQL n’est pas définie lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL, le mode de gestion complète est défini. L’installation de l’extension IaaS SQL sur la machine virtuelle est le prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion complète.

**Quels sont les prérequis pour s’inscrire auprès du fournisseur de ressources de machine virtuelle SQL ?**

Il n’existe aucun prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode léger ou sans agent. Le prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode complet est l’installation de l’extension IaaS SQL sur la machine virtuelle.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL si l’extension IaaS SQL n’est pas installée sur la machine virtuelle ?**

Oui, vous pouvez vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL en mode léger de gestion si l’extension IaaS SQL n’est pas installée sur la machine virtuelle. En mode léger, le fournisseur de ressources de machine virtuelle SQL utilise une application console pour vérifier la version et l’édition de l’instance SQL. L’application console s’arrête automatiquement après avoir vérifié qu’au moins une instance SQL est en cours d’exécution sur la machine virtuelle. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode léger ne redémarre pas SQL Server et ne crée pas d’agent sur la machine virtuelle.

**Est-ce que l’inscription auprès du fournisseur de ressources de machine virtuelle SQL permet d’installer un agent sur ma machine virtuelle ?**

Non. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL crée uniquement une ressource de métadonnées et n’installe pas d’agent sur la machine virtuelle. L’extension IaaS SQL est nécessaire uniquement pour activer la gestion complète. Par conséquent, la mise à niveau du mode léger de gestion vers une gestion complète installe l’extension IaaS SQL et redémarre SQL Server.

**Est-ce que l’inscription auprès du fournisseur de ressources de machine virtuelle SQL permet de redémarrer SQL Server sur ma machine virtuelle ?**

Non. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL crée uniquement une ressource de métadonnées et ne redémarre pas SQL Server sur la machine virtuelle. Le redémarrage de SQL Server est nécessaire uniquement pour installer l’extension IaaS SQL et celle-ci permet d’activer la gestion complète. La mise à niveau du mode léger de gestion vers une gestion complète installe l’extension IaaS SQL et redémarre SQL Server.

**Quelle est la différence entre les modes de gestion léger et sans agent lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL ?** 

Le mode de gestion sans agent n’est disponible que pour SQL Server 2008/R2 sur Windows Server 2008. Il s’agit du seul mode de gestion disponible pour ces versions. Pour toutes les autres versions de SQL Server, les deux modes de gestion disponibles sont léger et complet. Le mode sans agent requiert la définition des propriétés de version et d’édition de SQL Server par le client. Le mode léger interroge la machine virtuelle pour trouver la version et l’édition de l’instance SQL.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL en mode léger ou sans agent avec Azure CLI ?**

Non. La propriété du mode de gestion SQL est disponible uniquement lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL avec Azure PowerShell. Azure CLI ne prend pas en charge la définition de la propriété de gestion SQL et est toujours inscrit auprès du fournisseur de ressources de machine virtuelle SQL en mode par défaut (gestion complète).

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL sans spécifier le type de licence SQL ?**

Non. Le type de licence SQL n’est pas une propriété facultative lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL. Vous devez définir PAYG ou AHUB comme type de licence SQL lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL dans tous les modes de gestion (sans agent, léger et complet) avec Azure CLI et PowerShell.

**Puis-je mettre à niveau l’extension IaaS SQL du mode sans agent vers le mode complet ?**

Non. La mise à niveau du mode de gestion SQL vers complet ou léger n’est pas disponible pour le mode sans agent. Il s’agit d’une limitation technique de Windows Server 2008.

**Puis-je mettre à niveau l’extension IaaS SQL du mode léger vers le mode complet ?**

Oui. La mise à niveau du mode de gestion SQL de léger à complet est prise en charge via PowerShell ou le portail Azure, et elle nécessite le redémarrage de SQL Server.

**Puis-je rétrograder l’extension IaaS SQL du mode complet aux modes de gestion sans agent ou léger ?**

Non. La rétrogradation du mode de gestion de l’extension IaaS SQL n’est pas prise en charge. Le mode de gestion SQL ne peut pas être rétrogradé du mode complet au mode léger ou sans agent, ni du mode léger au mode sans agent. Pour changer le mode de gestion à partir de la gestion complète : supprimez l’extension IaaS SQL ; supprimez la ressource Microsoft.SqlVirtualMachine et réinscrivez la machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL à partir du portail Azure ?**

Non. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL n’est pas disponible dans le portail Azure. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion complète est prise en charge avec Azure CLI ou PowerShell ; l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion léger ou sans agent est uniquement prise en charge par les API Azure PowerShell.

**Puis-je inscrire une machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL avant d’installer SQL Server ?**

Non. La machine virtuelle doit disposer d’au moins une instance SQL pour pouvoir être inscrite auprès du fournisseur de ressources de machine virtuelle SQL. S’il n’existe aucune instance SQL sur la machine virtuelle, la nouvelle ressource Microsoft.SqlVirtualMachine sera en état d’échec.

**Puis-je inscrire une machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL s’il existe plusieurs instances SQL ?**

Oui. Le fournisseur de ressources de machine virtuelle SQL n’inscrira qu’une seule instance SQL. Le fournisseur de ressources de machine virtuelle SQL inscrira l’instance SQL par défaut dans le cas de plusieurs instances. En l’absence d’instance par défaut, l’inscription uniquement en mode léger est prise en charge. Pour effectuer une mise à niveau du mode de gestion léger à complet, l’instance SQL par défaut doit exister ou la machine virtuelle ne doit avoir qu’une seule instance SQL nommée.

**Puis-je inscrire une instance de cluster de basculement SQL Server auprès du fournisseur de ressources de machine virtuelle SQL ?**

Oui. Les instances de cluster de basculement SQL sur une machine virtuelle Azure peuvent être inscrites auprès du fournisseur de ressources de machine virtuelle SQL en mode léger. Toutefois, les instances de cluster de basculement SQL ne peuvent pas être mises à niveau vers le mode de gestion complet.

**Puis-je inscrire ma machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL si le groupe de disponibilité Always On est configuré ?**

Oui. Il n’existe aucune restriction quant à l’inscription d’une instance SQL Server sur une machine virtuelle Azure auprès du fournisseur de ressources de machine virtuelle SQL dans le cadre d’une configuration de groupe de disponibilité Always On.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
