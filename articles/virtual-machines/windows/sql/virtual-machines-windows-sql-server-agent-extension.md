---
title: Automatiser les tâches de gestion sur des machines virtuelles Azure à l’aide de l’extension de l’agent IaaS SQL Server | Microsoft Docs
description: Cet article indique comment gérer l’extension d’agent IaaS SQL Server, qui automatise certaines tâches d’administration SQL Server. Celles-ci incluent la sauvegarde automatisée, la mise à jour corrective automatisée et l’intégration Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59b5138950e0fb94ea0051fa9cfe9aa75cd7d770
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877803"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatiser les tâches de gestion sur des machines virtuelles Azure à l’aide de l’extension de l’agent IaaS SQL Server
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classique](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L’extension Agent IaaS SQL Server (SqlIaasExtension) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cet article fournit une vue d’ensemble des services pris en charge par l’extension. Cet article fournit également des instructions pour l’installation, l’état et la suppression de l’extension.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Pour afficher la version de cet article pour un déploiement Classic, consultez [Extension Agent IaaS SQL Server pour machines virtuelles SQL Server (classique)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Il existe trois modes de gestion SQL pour l’extension IaaS SQL Server : 

- Le mode **complet** fournit toutes les fonctionnalités, mais nécessite un redémarrage des autorisations SQL Server et administrateur système. Il s’agit de l’option qui est installée par défaut. Utilisez-la pour gérer une machine virtuelle SQL Server avec une seule instance. 

- Le mode **léger** ne nécessite pas de redémarrage de SQL Server, mais ne prend en charge que la modification du type de licence et de l’édition de SQL Server. Utilisez cette option pour les machines virtuelles SQL Server avec plusieurs instances, ou participant à une instance de cluster de basculement. 

- Le mode **NoAgent** est dédié à SQL Server 2008 et SQL Server 2008 R2 installés sur Windows Server 2008. Pour plus d’informations sur l’utilisation de ce mode pour votre image Windows Server 2008, voir [Inscription de Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Services pris en charge
L’extension Agent IaaS SQL Server prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
| --- | --- |
| **Sauvegarde automatisée de SQL Server** |Automatise la planification des sauvegardes pour toutes les bases de données pour l’instance par défaut ou une instance nommée [correctement installée](virtual-machines-windows-sql-server-iaas-faq.md#administration) de SQL Server sur la machine virtuelle. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Mise à jour corrective automatisée de SQL Server** |Configure une fenêtre de maintenance pendant laquelle les mises à jour de Windows importantes de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Intégration d’Azure Key Vault** |Permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer l’intégration du coffre de clés Azure SQL Server sur des machines virtuelles (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Une fois l’extension de l’agent IaaS SQL Server installée et active, les fonctionnalités d’administration sont disponibles :

* Dans le panneau SQL Server de la machine virtuelle dans le Portail Azure et par le biais d’images Azure PowerShell pour SQL Server sur la Place de marché Azure.
* Via Azure PowerShell pour les installations manuelles de l’extension. 

## <a name="prerequisites"></a>Prérequis
Voici la configuration requise pour utiliser l’extension Agent IaaS SQL Server sur votre machine virtuelle :

**Système d’exploitation** :

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Version de SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Télécharger et configurer les dernières commandes Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Gérer les modes de gestion

Vous pouvez afficher le mode actuel de votre agent SQL Server IaaS à l’aide de PowerShell : 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Machines virtuelles SQL Server sur lesquelles l’extension IaaS en mode *léger* est installée, vous pouvez opérer une mise à niveau vers le mode _complet_ via le Portail Azure. Les machines virtuelles SQL Server en mode _No-Agent_ peuvent passer en mode _complet_ une fois que le système d’exploitation est mis à niveau vers Windows 2008 R2 et versions ultérieures. Comme il n’est pas possible de passer à une version antérieure, pour ce faire, vous devez désinstaller complètement l’extension IaaS SQL, puis la réinstaller. 

Pour mettre à niveau l’agent en mode complet : 


# <a name="azure-portaltabazure-portal"></a>[Portail Azure](#tab/azure-portal)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à votre ressource [machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). 
1. Sélectionnez votre machine virtuelle SQL Server, puis choisissez **Vue d’ensemble**. 
1. Pour les machines virtuelles SQL Server avec les modes IaaS NoAgent ou léger, sélectionnez le message **Seules les mises à jour de type de licence et d’édition sont disponibles avec l’extension IaaS SQL**.

   ![Sélections de changement de mode à partir du portail](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Activez la case à cocher **J’accepte de redémarrer le service SQL Server sur la machine virtuelle**, puis sélectionnez **Confirmer** pour mettre à niveau votre mode IaaS vers le mode complet. 

    ![Case à cocher J’accepte de redémarrer le service SQL Server sur la machine virtuelle](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

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


##  <a name="installation"></a>Installation
L’extension IaaS SQL Server est installée lors de l’inscription de votre machine virtuelle SQL Server auprès du [fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md). Si nécessaire, vous pouvez installer l’agent IaaS SQL Server manuellement en utilisant le mode complet ou léger. 

L’Extension de l’agent IaaS SQL Server en mode complet s’installe automatiquement lorsque vous approvisionnez l’une des images Place de marché Azure de machine virtuelle SQL Server via le Portail Azure. 

### <a name="install-in-full-mode"></a>Installation en mode complet
Pour l’extension IaaS SQL Server, le mode complet assure la facilité de gestion d’une instance unique sur la machine virtuelle SQL Server. S’il existe une instance par défaut, l’extension fonctionne avec celle-ci et ne prend pas en charge la gestion des autres instances. S’il n’existe pas d’instance par défaut, mais une seule instance nommée, elle gère cette instance nommée. En l’absence d’instance par défaut et si plusieurs instances nommées sont présentes, l’installation de l’extension échoue. 

Installez l’agent IaaS SQL Server avec le mode complet à l’aide de PowerShell :

  ```powershell-interactive
     #Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with 'Full' SQL Server IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Paramètre | Valeurs acceptables                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` ou `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Si l’extension n’a pas encore été installée, l’installation de l’extension complète a pour effet de redémarrer le service SQL Server. Pour éviter le redémarrage du service SQL Server, installez plutôt le mode léger offrant une facilité de gestion limitée.
> 
> La mise à jour de l’extension SQL Server IaaS n’a pas pour effet de redémarrer le service SQL Server. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installer sur une machine virtuelle avec une seule instance de SQL Server nommée
L’extension IaaS SQL Server fonctionne avec une instance nommée sur SQL Server si l’instance par défaut est désinstallée et si l’extension IaaS est réinstallée.

Pour utiliser une instance nommée de SQL Server :
   1. Déployez une machine virtuelle SQL Server à partir de la Place de marché Azure. 
   1. Désinstallez l’extension IaaS depuis le [Portail Azure](https://portal.azure.com).
   1. Désinstallez complètement SQL Server sur la machine virtuelle SQL Server.
   1. Installez SQL Server avec une instance nommée sur la machine virtuelle SQL Server. 
   1. Installez l’extension IaaS depuis le Portail Azure.  


### <a name="install-in-lightweight-mode"></a>Installer en mode léger
Le mode Lightweight ne redémarre pas votre service SQL Server, mais ses fonctionnalités sont limitées. 

Installez l’agent IaaS SQL Server avec le mode léger à l’aide de PowerShell :


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Paramètre | Valeurs acceptables                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` ou `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Obtenir l’état de l’extension IaaS SQL Server
Pour vérifier que l’extension est installée, un moyen consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **All settings** (Tous les paramètres) dans la fenêtre de la machine virtuelle, puis sélectionnez **Extensions**. L’extension **SslIaaSExtension** doit s’afficher.

![État de l’extension d’agent IaaS SQL Server dans le Portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser la cmdlet Azure PowerShell **Get-AzVMSqlServerExtension** :

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

La commande précédente confirme que l’agent est installé et fournit des informations générales sur son état. Vous pouvez obtenir des informations d’état sur la sauvegarde automatisée et la mise à jour corrective automatisée à l’aide des commandes suivantes :

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Suppression
Dans le Portail Azure, vous pouvez désinstaller l’extension en sélectionnant le bouton de sélection dans la fenêtre **Extensions** des propriétés de la machine virtuelle. Puis sélectionnez **Supprimer**.

![Désinstallation de l’extension d’agent IaaS SQL Server dans le Portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser la cmdlet PowerShell **Remove-AzVMSqlServerExtension** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Étapes suivantes
Commencez par utiliser l’un des services pris en charge par l’extension. Pour plus de détails, voir les articles référencés dans la section [Services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [Qu’est-ce que SQL Server sur les machines virtuelles Azure ?](virtual-machines-windows-sql-server-iaas-overview.md).

