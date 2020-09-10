---
title: S’inscrire auprès du fournisseur de ressources de machine virtuelle SQL
description: Inscrivez votre machine virtuelle Azure SQL Server auprès du fournisseur de ressources de machine virtuelle SQL pour activer les fonctionnalités des machines virtuelles SQL Server déployées en dehors de Place de marché Azure, ainsi que la conformité et la gestion améliorée.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 11e8a2fd709b40c68b90e5ed139f18997e4cb29e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396962"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>Inscrire une machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machine virtuelle SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article explique comment inscrire votre machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machine virtuelle SQL. L’inscription auprès du fournisseur de ressources crée la _ressource_ de **machine virtuelle SQL** dans votre abonnement, qui est une ressource distincte de la ressource de machine virtuelle. Le fait de désinscrire votre machine virtuelle SQL Server du fournisseur de ressources va supprimer la _ressource_ de **machine virtuelle SQL**, mais pas la machine virtuelle elle-même. 

Pendant le déploiement d’une image de machine virtuelle SQL Server de la Place de marché Azure via le portail Azure, la machine virtuelle SQL Server est inscrite automatiquement auprès du fournisseur de ressources. Toutefois, si vous choisissez d’installer SQL Server sur une machine virtuelle Azure vous-même ou de provisionner une machine virtuelle Azure à partir d’un disque dur virtuel personnalisé, vous devez inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources pour les raisons suivantes :

- **Avantages en termes de fonctionnalités** : l’inscription de la machine virtuelle SQL Server auprès du fournisseur de ressources a pour effet de déverrouiller les fonctionnalités de [mise à jour corrective automatisée](automated-patching.md), de [sauvegarde automatisée](automated-backup.md) ainsi que de supervision et de gestion. De même, cela facilite la gestion des [licences](licensing-model-azure-hybrid-benefit-ahb-change.md) et des [éditions](change-sql-server-edition.md). Auparavant, ces fonctionnalités étaient uniquement accessibles aux images de machine virtuelle SQL Server déployées à partir de la Place de marché Azure. 

- **Conformité** : l’inscription auprès du fournisseur de ressources de machine virtuelle SQL offre une méthode simplifiée pour notifier à Microsoft qu’Azure Hybrid Benefit a été activé comme spécifié dans les termes du produit. Ce processus élimine la nécessité de gérer les formulaires d’inscription de licence pour chaque ressource.  

- **Administration gratuite** : l’inscription auprès du fournisseur de ressources de machine virtuelle SQL dans les trois modes de gestion est entièrement gratuite. Aucun coût supplémentaire n’est associé au fournisseur de ressources ou au changement de modes de gestion. 

- **Gestion des licences simplifiée**  : l’inscription auprès du fournisseur de ressources de machines virtuelles SQL simplifie la gestion des licences SQL Server et vous permet d’identifier rapidement les machines virtuelles SQL Server avec Azure Hybrid Benefit activé à l’aide du [Portail Azure](manage-sql-vm-portal.md), de l’interface de ligne de commande Azure ou de PowerShell : 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Pour utiliser le fournisseur de ressources de machine virtuelle SQL, vous devez d’abord [inscrire votre abonnement auprès du fournisseur de ressources](#register-subscription-with-rp), ce qui donne à ce dernier la possibilité de créer des ressources dans cet abonnement spécifique.

## <a name="prerequisites"></a>Prérequis

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources, voici ce dont vous avez besoin : 

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle SQL Server](create-sql-vm-portal.md) sur le modèle Azure Resource Manager déployée sur le cloud public ou le cloud Azure Government. 
- La version la plus récente d’[Azure CLI](/cli/azure/install-azure-cli) ou de [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Modes de gestion

Si l’[extension SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) n’a pas déjà été installée, l’inscription auprès du fournisseur de ressources de machine virtuelle SQL installe automatiquement l’extension SQL Server IaaS dans l’un des trois modes de gestion, spécifié pendant le processus d’inscription. Si vous ne spécifiez pas le mode de gestion, l’extension SQL IaaS est installée en mode de gestion complète.  

Si l’extension SQL IaaS a déjà été installée manuellement, elle est déjà en mode de gestion complète et l’inscription auprès du fournisseur de ressources en mode complet ne redémarre pas le service SQL Server.

Les trois modes de gestion sont les suivants :

- Le mode **léger** ne nécessite pas de redémarrage de SQL Server, mais ne prend en charge que la modification du type de licence et de l’édition de SQL Server. Utilisez cette option pour les machines virtuelles SQL Server avec plusieurs instances, ou participant à une instance de cluster de basculement. Le mode léger n’a aucun impact sur la mémoire ou le processeur et il n’y a aucun coût associé. Nous vous recommandons d’abord d’inscrire votre machine virtuelle SQL Server en mode léger, puis de procéder à une mise à niveau vers le mode complet pendant une fenêtre de maintenance planifiée.  

- Le mode **complet** fournit toutes les fonctionnalités, mais nécessite un redémarrage des autorisations SQL Server et administrateur système. Il s’agit de l’option qui est installée par défaut lors de l’installation manuelle de l’extension SQL IaaS. Utilisez-la pour gérer une machine virtuelle SQL Server avec une seule instance. Le mode Full installe deux services Windows qui ont un impact minimal sur la mémoire et le processeur. L’activité de ces services est visible dans le gestionnaire des tâches. L’utilisation du mode de gestion Full est gratuite. 

- Le mode **sans agent** est dédié à SQL Server 2008 et à SQL Server 2008 R2 sur Windows Server 2008. Le mode NoAgent n’a aucun impact sur la mémoire ou l’UC. L’utilisation du mode de gestion NoAgent est gratuite. 

Vous pouvez afficher le mode actuel de votre agent SQL Server IaaS à l’aide de PowerShell : 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Inscrire un abonnement auprès d’un fournisseur de ressources

Pour pouvoir inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL, vous devez inscrire votre abonnement auprès du fournisseur. Ainsi, le fournisseur de ressources de machine virtuelle SQL peut créer des ressources dans votre abonnement.  Pour ce faire, vous pouvez utiliser le portail Azure, Azure CLI ou PowerShell.

### <a name="azure-portal"></a>Portail Azure

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Dans la page **Abonnements**, accédez à **Fournisseurs de ressources**. 
1. Entrez **sql** dans le filtre pour afficher les fournisseurs de ressources liées à SQL. 
1. Sélectionnez **Inscrire**, **Réinscrire** ou **Désinscrire** pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

   ![Modifier le fournisseur](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Ligne de commande

Inscrivez votre fournisseur de ressources de machines virtuelles SQL dans votre abonnement Azure en utilisant Azure CLI ou PowerShell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Inscrire auprès du RP

### <a name="lightweight-management-mode"></a>Mode de gestion léger

Si l’[extension SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) n’a pas été installée sur la machine virtuelle, nous vous recommandons d’effectuer l’inscription auprès du fournisseur de ressources de machines virtuelles SQL en mode allégé. Cette opération installe l’extension SQL IaaS en [mode léger](#management-modes) et empêche le redémarrage du service SQL Server. Vous pouvez ensuite effectuer une mise à niveau vers le mode complet à tout moment ; cependant, comme cela entraîne le redémarrage du service SQL Server, nous vous recommandons d’attendre une fenêtre de maintenance planifiée. 

Indiquez une licence SQL Server de type paiement à l’utilisation (`PAYG`) pour payer en fonction de l’utilisation, Azure Hybrid Benefit (`AHUB`) pour utiliser votre propre licence ou récupération d’urgence (`DR`) pour activer la [licence de réplica de récupération d’urgence gratuite](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Les instances de cluster de basculement et les déploiements multi-instances ne peuvent être inscrits auprès du fournisseur de ressources de machine virtuelle SQL qu’en mode allégé. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Inscrire une machine virtuelle SQL Server en mode allégé avec l’interface de ligne de commande Azure : 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Inscrire une machine virtuelle SQL Server en mode léger avec PowerShell :  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Mode de gestion complet


Si l’extension SQL IaaS a déjà été installée sur la machine virtuelle manuellement, vous pouvez inscrire la machine virtuelle SQL Server en mode complet sans redémarrer le service SQL Server. **Toutefois, si l’extension SQL IaaS n’a pas été installée, l’inscription en mode complet installe l’extension SQL IaaS en mode complet et redémarre le service SQL Server. Agissez avec précaution.**


Pour inscrire directement votre machine virtuelle SQL Server en mode complet (et éventuellement redémarrer votre service SQL Server), utilisez la commande PowerShell suivante : 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Mode de gestion sans agent 

Les instances de SQL Server 2008 et 2008 R2 installées sur Windows Server 2008 (_non R2_) peuvent être inscrites auprès du fournisseur de ressources de machines virtuelles SQL en [mode sans agent](#management-modes). Cette option garantit la conformité et permet de surveiller la machine virtuelle SQL Server dans le portail Azure avec des fonctionnalités limitées.

Spécifiez `AHUB`, `PAYG` ou `DR` pour **sqlLicenseType** et `SQL2008-WS2008` ou `SQL2008R2-WS2008` pour **sqlImageOffer**. 

Pour inscrire votre SQL Server 2008 ou 2008 R2 sur une instance Windows Server 2008, utilisez l’extrait de code PowerShell ou Azure CLI suivant : 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Inscrire votre machine virtuelle SQL Server 2008 en mode sans agent avec l’interface de ligne de commande Azure : 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Inscrire votre machine virtuelle SQL Server 2008 R2 en mode sans agent avec l’interface de ligne de commande Azure : 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Inscrire votre machine virtuelle SQL Server 2008 en mode sans agent avec PowerShell : 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Inscrire votre machine virtuelle SQL Server 2008 R2 en mode sans agent avec PowerShell : 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>Mettre à niveau vers le mode complet  

Les machines virtuelles SQL Server sur lesquelles l’extension IaaS en mode *léger* est installée peuvent passer en mode _complet_ par le biais du Portail Azure, d’Azure CLI et de PowerShell. Les machines virtuelles SQL Server en mode _sans agent_ peuvent passer en mode _complet_ une fois que le système d’exploitation est mis à niveau vers Windows 2008 R2 et versions ultérieures. Il est impossible de passer à une version antérieure. Pour ce faire, vous devez [annuler l’inscription](#unregister-from-rp) de la machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL. Cette opération supprime la _ressource_ de **machine virtuelle SQL**, mais ne supprime pas la machine virtuelle elle-même. 

Vous pouvez afficher le mode actuel de votre agent SQL Server IaaS à l’aide de PowerShell : 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Pour mettre à niveau l’agent en mode complet : 


### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à votre ressource [machines virtuelles SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Sélectionnez votre machine virtuelle SQL Server, puis choisissez **Vue d’ensemble**. 
1. Pour les machines virtuelles SQL Server avec les modes IaaS NoAgent ou léger, sélectionnez le message **Seules les mises à jour de type de licence et d’édition sont disponibles avec l’extension IaaS SQL**.

   ![Sélections de changement de mode à partir du portail](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Activez la case à cocher **J’accepte de redémarrer le service SQL Server sur la machine virtuelle**, puis sélectionnez **Confirmer** pour mettre à niveau votre mode IaaS vers le mode complet. 

    ![Case à cocher J’accepte de redémarrer le service SQL Server sur la machine virtuelle](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Ligne de commande

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Exécutez l’extrait de code Azure CLI suivant :

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Exécutez l’extrait de code PowerShell suivant :

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Vérifier l’état de l’inscription
Vous pouvez vérifier si votre machine virtuelle SQL Server a déjà été inscrite auprès du fournisseur de ressources de machine virtuelle SQL via le portail Azure, Azure CLI ou PowerShell. 

### <a name="azure-portal"></a>Portail Azure 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à vos [machines virtuelles SQL Server](manage-sql-vm-portal.md).
1. Sélectionnez votre machine virtuelle SQL Server dans la liste. Si votre machine virtuelle SQL Server n’est pas listée ici, il est probable qu’elle n’a pas été inscrite auprès du fournisseur de ressources de machine virtuelle SQL. 
1. Examinez la valeur sous **État**. Si l’**État** indique **Réussi**, la machine virtuelle SQL Server a bien été inscrite auprès du fournisseur de ressources de machine virtuelle SQL. 

   ![Vérifier l’état avec l’inscription avec le fournisseur de ressources SQL](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Ligne de commande

Vérifiez l’état d’inscription actuel d’une machine virtuelle SQL Server à l’aide d’Azure CLI ou de PowerShell. `ProvisioningState` affichera `Succeeded` si l’inscription a réussi. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Une erreur indique que la machine virtuelle SQL Server n’a pas été inscrite avec le fournisseur de ressources. 


## <a name="unregister-from-rp"></a>Désinscrire du fournisseur de ressources

Pour désinscrire votre machine virtuelle SQL Server du fournisseur de ressources de machine virtuelle SQL, supprimez la *ressource* de machine virtuelle SQL à l’aide du portail Azure ou de l’interface de ligne de commande Azure. La suppression de la *ressource* de machine virtuelle SQL n’entraîne pas la suppression de la machine virtuelle SQL Server proprement dite. Toutefois, suivez attentivement les étapes, car il est possible de supprimer par inadvertance la machine virtuelle lors de la tentative de suppression de la *ressource*. 

La désinscription de la machine virtuelle SQL du fournisseur de ressources de machine virtuelle SQL est nécessaire pour passer du mode d’administration complet à une version inférieure. 

### <a name="azure-portal"></a>Portail Azure

Pour annuler l’inscription de votre machine virtuelle SQL Server auprès du fournisseur de ressources à l’aide du Portail Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à la ressource de machine virtuelle SQL. 
  
   ![Ressource Machines virtuelles SQL](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Sélectionnez **Supprimer**. 

   ![Supprimer le fournisseur de ressources de machine virtuelle SQL](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Saisissez le nom de la machine virtuelle SQL et **désactivez la case à cocher à côte de celle-ci**.

   ![Supprimer le fournisseur de ressources de machine virtuelle SQL](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Si vous ne désactivez pas la case à cocher en regard du nom de la machine virtuelle, vous *supprimez entièrement* celle-ci. Désactivez la case à cocher pour annuler l’inscription de la machine virtuelle SQL Server du fournisseur de ressources *sans supprimer la machine virtuelle proprement dite*. 

1. Sélectionnez **Supprimer** pour confirmer la suppression de la *ressource* de machine virtuelle SQL et non pas la machine virtuelle SQL Server. 

### <a name="command-line"></a>Ligne de commande

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour désinscrire votre machine virtuelle SQL Server du fournisseur de ressources à l’aide d’Azure CLI, utilisez la commande [az sql vm delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete). Cela supprimera la *ressource* de machine virtuelle SQL Server, mais ne supprimera pas la machine virtuelle. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour désinscrire votre machine virtuelle SQL Server du fournisseur de ressources à l’aide de PowerShell, utilisez la commande [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Cela supprimera la *ressource* de machine virtuelle SQL Server, mais ne supprimera pas la machine virtuelle. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limites

Le fournisseur de ressources de machine virtuelle SQL prend uniquement en charge :
- Les machines virtuelles SQL Server déployées par le biais d’Azure Resource Manager. Les machines virtuelles SQL Server déployées via le modèle classique ne sont pas prises en charge. 
- Les machines virtuelles SQL Server déployées sur le cloud public ou le cloud Azure Government. Les déploiements sur d’autres clouds privés ou du secteur public ne sont pas pris en charge. 


## <a name="frequently-asked-questions"></a>Forum aux questions 

**Dois-je inscrire ma machine virtuelle SQL Server provisionnée à partir d’une image SQL Server de la Place de marché Azure ?**

Non. Microsoft inscrit automatiquement les machines virtuelles provisionnées à partir des images SQL Server de la Place de marché Azure. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL n’est nécessaire que si la machine virtuelle n’a *pas* été provisionnée à partir des images SQL Server de la Place de marché Azure et que SQL Server a été installé automatiquement.

**Le fournisseur de ressources de machine virtuelle SQL est-il disponible pour tous les clients ?** 

Oui. Les clients doivent inscrire leurs machines virtuelles SQL Server auprès du fournisseur de ressources de machine virtuelle SQL s’ils n’ont pas utilisé d’image SQL Server de la Place de marché Azure et qu’à la place, ils ont installé SQL Server automatiquement ou apporté leur disque dur virtuel personnalisé. Les machines virtuelles relevant de tous les types d’abonnement (Direct, Contrat Entreprise et Fournisseur de solutions Cloud) peuvent s’inscrire auprès du fournisseur de ressources de machine virtuelle SQL.

**Dois-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL si l’extension IaaS SQL Server est déjà installée sur ma machine virtuelle SQL Server ?**

Si votre machine virtuelle SQL Server est installée automatiquement et n’est pas provisionnée à partir des images SQL Server de la Place de marché Azure, vous devez vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL, même si vous avez installé l’extension IaaS SQL Server. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL crée une ressource de type Microsoft.SqlVirtualMachine. L’installation de l’extension IaaS SQL Server ne crée pas cette ressource.

**Quel est le mode de gestion par défaut au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL ?**

Le mode de gestion par défaut au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL est *complet*. Si la propriété de gestion SQL Server n’est pas définie au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL, le mode de gestion complet est défini et votre service SQL Server redémarre. Nous vous recommandons d’abord d’effectuer l’inscription auprès du fournisseur de ressources de machine virtuelle en mode léger, puis de procéder à une mise à niveau vers le mode complet pendant une fenêtre de maintenance. 

**Quels sont les prérequis pour s’inscrire auprès du fournisseur de ressources de machine virtuelle SQL ?**

Il n’existe aucun prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode léger ou sans agent. Le prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode complet est l’installation de l’extension SQL Server IaaS sur la machine virtuelle, sinon le service SQL Server redémarre. 

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL si l’extension IaaS SQL Server n’est pas installée sur la machine virtuelle ?**

Oui, vous pouvez vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion léger si l’extension IaaS SQL Server n’est pas installée sur la machine virtuelle. En mode léger, le fournisseur de ressources de machine virtuelle SQL utilise une application console pour vérifier la version et l’édition de l’instance SQL Server. 

Le mode de gestion SQL par défaut au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL est _Complet_. Si la propriété de gestion SQL n’est pas définie lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL, le mode de gestion complète est défini. Nous vous recommandons d’abord d’effectuer l’inscription auprès du fournisseur de ressources de machine virtuelle en mode léger, puis de procéder à une mise à niveau vers le mode complet pendant une fenêtre de maintenance. 

**L’inscription auprès du fournisseur de ressources de machine virtuelle SQL a-t-elle pour effet d’installer un agent sur ma machine virtuelle ?**

Oui, l’inscription auprès du fournisseur de ressources de machine virtuelle SQL installera un agent sur la machine virtuelle.

L’extension IaaS SQL Server s’appuie sur l’agent pour interroger les métadonnées de SQL Server. La seule fois où un agent n’est pas installé est lorsque le fournisseur de ressources de machine virtuelle SQL est inscrit en mode NoAgent.

**Est-ce que l’inscription auprès du fournisseur de ressources de machine virtuelle SQL permet de redémarrer SQL Server sur ma machine virtuelle ?**

Cela dépend du mode spécifié lors de l’inscription. Si le mode léger ou sans agent est spécifié, le service SQL Server ne redémarre pas. Toutefois, si vous spécifiez le mode de gestion complet ou laissez le mode de gestion vide, l’extension SQL IaaS est installée en mode de gestion complet, ce qui entraîne le redémarrage du service SQL Server. 

**Quelle est la différence entre les modes de gestion léger et sans agent au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL ?** 

Le mode de gestion sans agent n’est disponible que pour les instances SQL Server 2008 et SQL Server 2008 R2 sur Windows Server 2008. Il s’agit du seul mode de gestion disponible pour ces versions. Pour toutes les autres versions de SQL Server, les deux modes de gestion disponibles sont léger et complet. 

Pour le mode sans agent, les propriétés de version et d’édition de SQL Server doivent être définies par le client. Le mode léger interroge la machine virtuelle pour déterminer la version et l’édition de l’instance SQL Server.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL sans spécifier le type de licence SQL Server ?**

Non. Le type de licence SQL Server n’est pas une propriété facultative au moment de vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL. Vous devez définir le type de licence « paiement à l’utilisation » ou Azure Hybrid Benefit au moment de vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL dans tous les modes de gestion (sans agent, léger et complet).

**Puis-je mettre à niveau l’extension IaaS SQL Server du mode sans agent vers le mode complet ?**

Non. La mise à niveau vers le mode de gestion complet ou léger n’est pas disponible pour le mode sans agent. Il s’agit d’une limitation technique de Windows Server 2008. Vous devez d’abord mettre à niveau le système d’exploitation vers Windows Server 2008 R2 ou une version ultérieure, puis effectuer une mise à niveau vers le mode de gestion complet. 

**Puis-je mettre à niveau l’extension IaaS SQL Server du mode léger vers le mode complet ?**

Oui. La mise à niveau du mode de gestion de léger à complet est prise en charge via PowerShell ou le portail Azure. Elle nécessite le redémarrage du service SQL Server.

**Puis-je rétrograder l’extension IaaS SQL Server du mode complet vers le mode de gestion sans agent ou léger ?**

Non. La rétrogradation du mode de gestion de l’extension IaaS SQL Server n’est pas prise en charge. Le mode de gestion SQL ne peut pas être rétrogradé du mode complet vers le mode léger ou sans agent, ni du mode léger vers le mode sans agent. 

Pour passer d’un mode d’administration complet à un autre mode d’administration, [désinscrivez](#unregister-from-rp) la machine virtuelle SQL Server du fournisseur de ressources de machine virtuelle SQL en supprimant la *ressource* SQL Server et en réinscrivant la machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL avec un mode d’administration différent.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL à partir du portail Azure ?**

Non. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL n’est pas disponible sur le portail Azure. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL est uniquement prise en charge avec Azure CLI ou PowerShell. 

**Puis-je inscrire une machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL avant d’installer SQL Server ?**

Non. Une machine virtuelle doit disposer d'au moins une instance SQL Server (moteur de base de données) pour pouvoir être inscrite auprès du fournisseur de machines virtuelles SQL. S’il n’existe aucune instance SQL Server sur la machine virtuelle, la nouvelle ressource Microsoft.SqlVirtualMachine sera en état d’échec.

**Puis-je inscrire une machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL s’il existe plusieurs instances SQL Server ?**

Oui. Le fournisseur de machines virtuelles SQL n'inscrira qu'une seule instance de SQL Server (moteur de base de données). Le fournisseur de ressources de machine virtuelle SQL inscrira l’instance SQL Server par défaut en présence de plusieurs instances. En l’absence d’instance par défaut, l’inscription uniquement en mode léger est prise en charge. Pour effectuer une mise à niveau du mode de gestion léger à complet, l’instance SQL Server par défaut doit exister ou la machine virtuelle ne doit avoir qu’une seule instance SQL Server nommée.

**Puis-je inscrire une instance de cluster de basculement SQL Server auprès du fournisseur de ressources de machine virtuelle SQL ?**

Oui. Les instances de cluster de basculement SQL Server sur une machine virtuelle Azure peuvent être inscrites auprès du fournisseur de ressources de machine virtuelle SQL en mode léger. Cependant, les instances de cluster de basculement SQL Server ne peuvent pas être mises à niveau vers le mode de gestion complet.

**Puis-je inscrire ma machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL si le groupe de disponibilité Always On est configuré ?**

Oui. Il n’existe aucune restriction quant à l’inscription d’une instance SQL Server sur une machine virtuelle Azure auprès du fournisseur de ressources de machine virtuelle SQL si vous faites partie d’une configuration de groupe de disponibilité Always On.

**Quel est le coût de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL ou de la mise à niveau vers le mode de gestion complet ?**
Aucun. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL ou l’utilisation de l’un des trois modes de gestion n’est pas facturée. La gestion de votre machine virtuelle SQL Server avec le fournisseur de ressources est entièrement gratuite. 

**Quel est l’impact de l’utilisation des différents modes de gestion sur les performances ?**
Les modes de gestion *NoAgent* et *Lightweight* n’ont pas d’impact sur la mémoire et l’UC. L’utilisation du mode de gestion *Full* à partir de deux services installés sur le système d’exploitation a un impact minime. Ces derniers peuvent être supervisés par le biais du gestionnaire des tâches et observés dans la console des services Windows intégrée. 

Les deux noms de service sont les suivants :
- `SqlIaaSExtensionQuery` (nom d’affichage : `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (nom d’affichage : `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](frequently-asked-questions-faq.md)  
* [Guide des tarifs pour SQL Server sur une machine virtuelle Windows](pricing-guidance.md)
* [Notes de publication pour SQL Server sur une machine virtuelle Windows](../../database/doc-changes-updates-release-notes.md)
