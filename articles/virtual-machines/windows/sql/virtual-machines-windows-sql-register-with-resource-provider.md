---
title: Inscrire une machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machine virtuelle SQL | Microsoft Docs
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
ms.openlocfilehash: a0e5076f6ecb102b239a94b986830235eb720125
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512363"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Inscrire une machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machine virtuelle SQL

Cet article explique comment inscrire votre machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machine virtuelle SQL. 

Pendant le déploiement d’une image de machine virtuelle SQL Server de la Place de marché Azure via le portail Azure, la machine virtuelle SQL Server est inscrite automatiquement auprès du fournisseur de ressources. Si vous choisissez d’installer automatiquement SQL Server sur une machine virtuelle Azure au lieu de choisir une image à sur la Place de marché Azure, ou si vous provisionnez une machine virtuelle Azure à partir d’un disque dur virtuel personnalisé avec SQL Server, vous devez inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources pour les raisons suivantes :

- **Simplifier la gestion des licences** : conformément aux conditions d’utilisation du produit Microsoft, les clients doivent informer Microsoft qu’ils utilisent [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). L’inscription auprès du fournisseur de ressources de machine virtuelle SQL simplifie la gestion des licences SQL Server et vous permet d’identifier rapidement les machines virtuelles SQL Server en utilisant d’Azure Hybrid Benefit dans le [portail](virtual-machines-windows-sql-manage-portal.md) ou l’interface de ligne de commande Azure : 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **Avantages en termes de fonctionnalités** : l’inscription de la machine virtuelle SQL Server auprès du fournisseur de ressources a pour effet de déverrouiller les fonctionnalités de [mise à jour corrective automatisée](virtual-machines-windows-sql-automated-patching.md), de [sauvegarde automatisée](virtual-machines-windows-sql-automated-backup-v2.md), de supervision et de gestion. De même, cela facilite la gestion des [licences](virtual-machines-windows-sql-ahb.md) et des [éditions](virtual-machines-windows-sql-change-edition.md). Auparavant, ces fonctionnalités étaient uniquement accessibles aux images de machine virtuelle SQL Server issues de la Place de marché Azure.

- **Administration gratuite** :  L’inscription auprès du fournisseur de ressources de machine virtuelle SQL et de tous les modes de gestion est entièrement gratuite. Aucun coût supplémentaire n’est associé au fournisseur de ressources ou au changement de modes de gestion. 

Pour utiliser le fournisseur de ressources de machines virtuelles SQL, vous devez également inscrire le fournisseur de ressources de machines virtuelles SQL à votre abonnement. Vous pouvez effectuer cette opération à partir du portail Azure, d’Azure CLI ou de PowerShell. 

  > [!NOTE]
  > Aucune exigence de licence supplémentaire n’est associée à l’inscription auprès du fournisseur de ressources. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL offre une méthode simplifiée permettant de notifier à Microsoft qu’Azure Hybrid Benefit a été activé à la place de la gestion des formulaires d’inscription de licence pour chaque ressource. 

Pour plus d’informations sur les avantages du fournisseur de ressources de machine virtuelle SQL, consultez la vidéo de [Channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) suivante : 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Avantages du fournisseur de ressources de machine virtuelle SQL lors de l’installation automatique de SQL Server sur Azure - Vidéo sur Microsoft Channel 9"></iframe>


## <a name="prerequisites"></a>Prérequis

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources, voici ce dont vous avez besoin : 

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Une [machine virtuelle SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- La version la plus récente d’[Azure CLI](/cli/azure/install-azure-cli) ou de [PowerShell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>S’inscrire auprès du fournisseur de ressources de machines virtuelles SQL
Si l’[extension SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md) n’est pas installée sur la machine virtuelle, vous pouvez vous inscrire auprès du fournisseur de ressources de machines virtuelles SQL en spécifiant le mode de gestion SQL léger. 

Lorsque le mode léger est spécifié pendant le processus d’inscription, le fournisseur de ressources de machines virtuelles SQL installe automatiquement l’extension IaaS SQL en [mode léger](#change-management-modes) et vérifie les métadonnées de l’instance SQL Server. Cela ne redémarre pas le service SQL Server. Vous devez fournir le type de licence SQL Server souhaité lors de l’inscription avec le fournisseur de ressources de machines virtuelles SQL : « PAYG » ou « AHUB ».

L’inscription auprès du fournisseur de ressources de machines virtuelles SQL en mode léger garantira la conformité et activera les licences flexibles, ainsi que les mises à jour de l’édition SQL Server utilisée. Les instances de cluster de basculement et les déploiements à plusieurs instances peuvent être inscrits avec le fournisseur de ressources de machines virtuelles SQL uniquement en mode léger. Vous pouvez à tout moment [effectuer une mise à niveau](#change-management-modes) vers le mode Gestion complète. Toutefois, cela entraînera le redémarrage du service SQL Server. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Utilisez l’extrait de code suivant pour une inscription auprès du fournisseur de ressources de machine virtuelle SQL si l’extension IaaS SQL Server est déjà installée sur la machine virtuelle. Vous devez indiquer le type de licence SQL Server que vous voulez pendant l’inscription auprès du fournisseur de ressources de machine virtuelle SQL : paiement à l’utilisation (`PAYG`) ou Azure Hybrid Benefit (`AHUB`). 

Inscrivez la machine virtuelle SQL Server en utilisant l’extrait de code PowerShell suivant :

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Pour les éditions payantes (Enterprise ou Standard) :

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

Pour les éditions gratuites (Developer, Web ou Express) :

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Si l’extension IaaS SQL est déjà installée sur la machine virtuelle, vous pouvez vous inscrire auprès du fournisseur de ressources de machines virtuelles SQL en mode Gestion complète en créant une ressource de métadonnées de type Microsoft.SqlVirtualMachine/SqlVirtualMachines. Voici l’extrait de code pour s’enregistrer avec le fournisseur de ressources de machines virtuelles SQL si l’extension IaaS SQL est déjà installée sur la machine virtuelle. Vous devez fournir le type de licence SQL Server souhaité (« PAYG » ou « AHUB »). Pour vous inscrire en mode Gestion complète, utilisez la commande PowerShell suivante :

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Inscrire SQL Server 2008 ou 2008 R2 sur des machines virtuelles Windows Server 2008

Les instances SQL Server 2008 et 2008 R2 installées sur Windows Server 2008 peuvent être inscrites auprès du fournisseur de ressources de machine virtuelle SQL en mode [sans agent](#change-management-modes). Cette option garantit la conformité et permet de surveiller la machine virtuelle SQL Server dans le portail Azure avec des fonctionnalités limitées.

Le tableau suivant répertorie les valeurs autorisées pour les paramètres fournis lors de l’inscription :

| Paramètre | Valeurs acceptables                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` ou `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` ou `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Pour inscrire votre instance SQL Server 2008 ou 2008 R2 sur une instance Windows Server 2008, utilisez l’extrait de code PowerShell ou Az CLI suivant :  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Vérifier l’état de l’inscription
Vous pouvez vérifier si votre machine virtuelle SQL Server a déjà été inscrite auprès du fournisseur de ressources de machine virtuelle SQL via le portail Azure, Azure CLI ou PowerShell. 

### <a name="azure-portal"></a>Portail Azure 

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
1. Accédez à vos [machines virtuelles SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Sélectionnez votre machine virtuelle SQL Server dans la liste. Si votre machine virtuelle SQL Server n’est pas listée ici, il est probable qu’elle n’a pas été inscrite auprès du fournisseur de ressources de machine virtuelle SQL. 
1. Examinez la valeur sous **État**. Si l’**État** indique **Réussi**, la machine virtuelle SQL Server a bien été inscrite auprès du fournisseur de ressources de machine virtuelle SQL. 

![Vérifier l’état avec l’inscription avec le fournisseur de ressources SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Ligne de commande

Vérifiez l’état d’inscription actuel d’une machine virtuelle SQL Server à l’aide d’Az CLI ou de PowerShell. `ProvisioningState` affichera `Succeeded` si l’inscription a réussi. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Une erreur indique que la machine virtuelle SQL Server n’a pas été inscrite avec le fournisseur de ressources. 

## <a name="change-management-modes"></a>Gérer les modes de gestion

Il existe trois modes de gestion gratuits pour l’extension IaaS SQL Server : 

- Le mode **complet** fournit toutes les fonctionnalités, mais nécessite un redémarrage des autorisations SQL Server et administrateur système. Il s’agit de l’option qui est installée par défaut. Utilisez-la pour gérer une machine virtuelle SQL Server avec une seule instance. Le mode Full installe deux services Windows qui ont un impact minimal sur la mémoire et le processeur. L’activité de ces services est visible dans le gestionnaire des tâches. L’utilisation du mode de gestion Full est gratuite. 

- Le mode **léger** ne nécessite pas de redémarrage de SQL Server, mais ne prend en charge que la modification du type de licence et de l’édition de SQL Server. Utilisez cette option pour les machines virtuelles SQL Server avec plusieurs instances, ou participant à une instance de cluster de basculement. Le mode Lightweight n’a aucun impact sur la mémoire ou l’UC. L’utilisation du mode de gestion Lightweight est gratuite. 

- Le mode **sans agent** est dédié à SQL Server 2008 et à SQL Server 2008 R2 sur Windows Server 2008. Le mode NoAgent n’a aucun impact sur la mémoire ou l’UC. L’utilisation du mode de gestion NoAgent est gratuite. 

Vous pouvez afficher le mode actuel de votre agent SQL Server IaaS à l’aide de PowerShell : 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Machines virtuelles SQL Server sur lesquelles l’extension IaaS en mode *léger* est installée, vous pouvez opérer une mise à niveau vers le mode _complet_ via le Portail Azure. Les machines virtuelles SQL Server en mode _No-Agent_ peuvent passer en mode _complet_ une fois que le système d’exploitation est mis à niveau vers Windows 2008 R2 et versions ultérieures. Pour passer à une version antérieure, vous devez obligatoirement supprimer la ressource du fournisseur de ressources de machine virtuelle SQL à l’aide du portail Azure, puis vous inscrire à nouveau auprès du fournisseur de ressources de machine virtuelle SQL. 

Pour mettre à niveau l’agent en mode complet : 


### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à votre ressource [machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). 
1. Sélectionnez votre machine virtuelle SQL Server, puis choisissez **Vue d’ensemble**. 
1. Pour les machines virtuelles SQL Server avec les modes IaaS NoAgent ou léger, sélectionnez le message **Seules les mises à jour de type de licence et d’édition sont disponibles avec l’extension IaaS SQL**.

   ![Sélections de changement de mode à partir du portail](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Activez la case à cocher **J’accepte de redémarrer le service SQL Server sur la machine virtuelle**, puis sélectionnez **Confirmer** pour mettre à niveau votre mode IaaS vers le mode complet. 

    ![Case à cocher J’accepte de redémarrer le service SQL Server sur la machine virtuelle](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Ligne de commande

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Exécutez l’extrait de code Az CLI suivant :

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Exécutez l’extrait de code PowerShell suivant :

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```
---

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Inscrire le fournisseur de ressources de machine virtuelle SQL dans un abonnement 

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL, vous devez inscrire celui-ci dans votre abonnement. Pour ce faire, vous pouvez utiliser le portail Azure, Azure CLI ou PowerShell.

### <a name="azure-portal"></a>Portail Azure

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Dans la page **Abonnements**, accédez à **Fournisseurs de ressources**. 
1. Entrez **sql** dans le filtre pour afficher les fournisseurs de ressources liées à SQL. 
1. Sélectionnez **Inscrire**, **Réinscrire** ou **Désinscrire** pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Ligne de commande

Inscrivez votre fournisseur de ressources de machine virtuelle SQL dans votre abonnement Azure en utilisant Az CLI ou PowerShell. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
L’extrait de code suivant inscrira le fournisseur de ressources de machines virtuelles SQL avec votre abonnement Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Remarques

- Le fournisseur de ressources de machine virtuelle SQL prend uniquement en charge les machines virtuelles SQL Server déployées via Azure Resource Manager. Les machines virtuelles SQL Server déployées via le modèle classique ne sont pas prises en charge. 
- Le fournisseur de ressources de machine virtuelle SQL prend uniquement en charge les machines virtuelles SQL Server déployées dans le cloud public. Les déploiements dans un cloud privé ou du secteur public ne sont pas pris en charge. 
 

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 

**Dois-je inscrire ma machine virtuelle SQL Server provisionnée à partir d’une image SQL Server de la Place de marché Azure ?**

Non. Microsoft inscrit automatiquement les machines virtuelles provisionnées à partir des images SQL Server de la Place de marché Azure. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL n’est nécessaire que si la machine virtuelle n’a *pas* été provisionnée à partir des images SQL Server de la Place de marché Azure et que SQL Server a été installé automatiquement.

**Le fournisseur de ressources de machine virtuelle SQL est-il disponible pour tous les clients ?** 

Oui. Les clients doivent inscrire leurs machines virtuelles SQL Server auprès du fournisseur de ressources de machine virtuelle SQL s’ils n’ont pas utilisé d’image SQL Server de la Place de marché Azure et qu’à la place, ils ont installé SQL Server automatiquement ou apporté leur disque dur virtuel personnalisé. Les machines virtuelles relevant de tous les types d’abonnement (Direct, Contrat Entreprise et Fournisseur de solutions Cloud) peuvent s’inscrire auprès du fournisseur de ressources de machine virtuelle SQL.

**Dois-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL si l’extension IaaS SQL Server est déjà installée sur ma machine virtuelle SQL Server ?**

Si votre machine virtuelle SQL Server est installée automatiquement et n’est pas provisionnée à partir des images SQL Server de la Place de marché Azure, vous devez vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL, même si vous avez installé l’extension IaaS SQL Server. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL crée une ressource de type Microsoft.SqlVirtualMachines. L’installation de l’extension IaaS SQL Server ne crée pas cette ressource.

**Quel est le mode de gestion par défaut au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL ?**

Le mode de gestion par défaut au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL est *complet*. Si la propriété de gestion SQL Server n’est pas définie au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL, le mode de gestion complet est défini. L’installation de l’extension IaaS SQL Server sur la machine virtuelle est le prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion complet.

**Quels sont les prérequis pour s’inscrire auprès du fournisseur de ressources de machine virtuelle SQL ?**

Il n’existe aucun prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode léger ou sans agent. Le prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode complet est l’installation de l’extension IaaS SQL Server sur la machine virtuelle.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL si l’extension IaaS SQL Server n’est pas installée sur la machine virtuelle ?**

Oui, vous pouvez vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion léger si l’extension IaaS SQL Server n’est pas installée sur la machine virtuelle. En mode léger, le fournisseur de ressources de machine virtuelle SQL utilise une application console pour vérifier la version et l’édition de l’instance SQL Server. 

Le mode de gestion SQL par défaut au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL est _Complet_. Si la propriété de gestion SQL n’est pas définie lors de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL, le mode de gestion complète est défini. L’installation de l’extension IaaS SQL sur la machine virtuelle est le prérequis à l’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion complète.

**L’inscription auprès du fournisseur de ressources de machine virtuelle SQL a-t-elle pour effet d’installer un agent sur ma machine virtuelle ?**

Non. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL crée uniquement une ressource de métadonnées. Elle n’a pas pour effet d’installer un agent sur la machine virtuelle.

L’extension IaaS SQL Server n’est nécessaire que pour activer la gestion complète. La mise à niveau du mode de gestion léger à complet a pour effet d’installer l’extension IaaS SQL Server et de redémarrer SQL Server.

**L’inscription auprès du fournisseur de ressources de machine virtuelle SQL a-t-elle pour effet de redémarrer SQL Server sur ma machine virtuelle ?**

Non. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL crée uniquement une ressource de métadonnées. Elle n’a pas pour effet de redémarrer SQL Server sur la machine virtuelle. 

Le redémarrage de SQL Server n’est nécessaire que pour installer l’extension IaaS SQL Server. Et l’extension IaaS SQL Server n’est nécessaire que pour activer la gestion complète. La mise à niveau du mode de gestion léger à complet a pour effet d’installer l’extension IaaS SQL Server et de redémarrer SQL Server.

**Quelle est la différence entre les modes de gestion léger et sans agent au moment de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL ?** 

Le mode de gestion sans agent n’est disponible que pour les instances SQL Server 2008 et SQL Server 2008 R2 sur Windows Server 2008. Il s’agit du seul mode de gestion disponible pour ces versions. Pour toutes les autres versions de SQL Server, les deux modes de gestion disponibles sont léger et complet. 

Pour le mode sans agent, les propriétés de version et d’édition de SQL Server doivent être définies par le client. Le mode léger interroge la machine virtuelle pour déterminer la version et l’édition de l’instance SQL Server.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL en mode léger ou sans agent à l’aide d’Azure CLI ?**

Non. La propriété du mode de gestion est disponible uniquement quand vous vous inscrivez auprès du fournisseur de ressources de machine virtuelle SQL à l’aide d’Azure PowerShell. Azure CLI ne permet pas de définir la propriété de gestion SQL Server. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL s’effectue toujours dans le mode par défaut, c’est-à-dire en gestion complète.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL sans spécifier le type de licence SQL Server ?**

Non. Le type de licence SQL Server n’est pas une propriété facultative au moment de vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL. Vous devez définir le type de licence « paiement à l’utilisation » ou Azure Hybrid Benefit au moment de vous inscrire auprès du fournisseur de ressources de machine virtuelle SQL dans tous les modes de gestion (sans agent, léger et complet), que ce soit à l’aide d’Azure CLI ou de PowerShell.

**Puis-je mettre à niveau l’extension IaaS SQL Server du mode sans agent vers le mode complet ?**

Non. La mise à niveau vers le mode de gestion complet ou léger n’est pas disponible pour le mode sans agent. Il s’agit d’une limitation technique de Windows Server 2008.

**Puis-je mettre à niveau l’extension IaaS SQL Server du mode léger vers le mode complet ?**

Oui. La mise à niveau du mode de gestion de léger à complet est prise en charge via PowerShell ou le portail Azure. Elle nécessite le redémarrage de SQL Server.

**Puis-je rétrograder l’extension IaaS SQL Server du mode complet vers le mode de gestion sans agent ou léger ?**

Non. La rétrogradation du mode de gestion de l’extension IaaS SQL Server n’est pas prise en charge. Le mode de gestion SQL ne peut pas être rétrogradé du mode complet vers le mode léger ou sans agent, ni du mode léger vers le mode sans agent. 

Pour changer le mode de gestion à partir de la gestion complète : supprimez la ressource Microsoft.SqlVirtualMachine, puis réinscrivez la machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL.

**Puis-je m’inscrire auprès du fournisseur de ressources de machine virtuelle SQL à partir du portail Azure ?**

Non. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL n’est pas disponible sur le portail Azure. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion complet est prise en charge avec Azure CLI ou PowerShell. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion léger ou sans agent est prise en charge uniquement par les API Azure PowerShell.

**Puis-je inscrire une machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL avant d’installer SQL Server ?**

Non. Une machine virtuelle doit disposer d’au moins une instance SQL Server pour pouvoir être inscrite auprès du fournisseur de ressources de machine virtuelle SQL. S’il n’existe aucune instance SQL Server sur la machine virtuelle, la nouvelle ressource Microsoft.SqlVirtualMachine sera en état d’échec.

**Puis-je inscrire une machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL s’il existe plusieurs instances SQL Server ?**

Oui. Le fournisseur de ressources de machine virtuelle SQL n’inscrira qu’une seule instance SQL Server. Le fournisseur de ressources de machine virtuelle SQL inscrira l’instance SQL Server par défaut en présence de plusieurs instances. En l’absence d’instance par défaut, l’inscription uniquement en mode léger est prise en charge. Pour effectuer une mise à niveau du mode de gestion léger à complet, l’instance SQL Server par défaut doit exister ou la machine virtuelle ne doit avoir qu’une seule instance SQL Server nommée.

**Puis-je inscrire une instance de cluster de basculement SQL Server auprès du fournisseur de ressources de machine virtuelle SQL ?**

Oui. Les instances de cluster de basculement SQL Server sur une machine virtuelle Azure peuvent être inscrites auprès du fournisseur de ressources de machine virtuelle SQL en mode léger. Cependant, les instances de cluster de basculement SQL Server ne peuvent pas être mises à niveau vers le mode de gestion complet.

**Puis-je inscrire ma machine virtuelle auprès du fournisseur de ressources de machine virtuelle SQL si le groupe de disponibilité Always On est configuré ?**

Oui. Il n’existe aucune restriction quant à l’inscription d’une instance SQL Server sur une machine virtuelle Azure auprès du fournisseur de ressources de machine virtuelle SQL si vous faites partie d’une configuration de groupe de disponibilité Always On.

**Quel est le coût de l’inscription auprès du fournisseur de ressources de machine virtuelle SQL ou de la mise à niveau vers le mode de gestion complet ?**
Aucune. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL ou l’utilisation de l’un des trois modes de gestion n’est pas facturée. La gestion de votre machine virtuelle SQL Server avec le fournisseur de ressources est entièrement gratuite. 

**Quel est l’impact de l’utilisation des différents modes de gestion sur les performances ?**
Les modes de gestion *NoAgent* et *Lightweight* n’ont pas d’impact sur la mémoire et l’UC. L’utilisation du mode de gestion *Full* à partir de deux services installés sur le système d’exploitation a un impact minime. L’activité de ces services peut être surveillée à l’aide du gestionnaire des tâches. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
