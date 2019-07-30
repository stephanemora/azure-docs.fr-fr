---
title: Automatiser les tâches de gestion sur des machines virtuelles Azure avec l’extension de l’agent IaaS SQL Server | Microsoft Docs
description: Cet article indique comment gérer l’extension d’agent SQL Server, qui automatise certaines tâches d’administration SQL Server. Celles-ci incluent Sauvegarde automatisée, Mise à jour corrective automatisée et Azure Key Vault Integration.
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
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798055"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Automatiser les tâches de gestion sur des machines virtuelles Azure avec l’extension de l’agent IaaS SQL Server
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classique](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L’extension Agent IaaS SQL Server (SqlIaasExtension) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cet article présente les services pris en charge par l’extension, ainsi que des instructions d’installation, d’état et de suppression.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Pour afficher la version de cet article pour un déploiement Classic, consultez [Extension Agent SQL Server pour machines virtuelles SQL Server (Classic)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Il existe trois modes de gestion SQL pour l’extension IaaS SQL : **Full**, **Lightweight** et **NoAgent**. 

- Le mode **Full** fournit toutes les fonctionnalités, mais nécessite un redémarrage des autorisations SQL Server et SA. Il s’agit de l’option installée par défaut à utiliser pour gérer une machine virtuelle SQL Server avec une seule instance. 

- Le mode **Lightweight** ne nécessite pas de redémarrage de SQL Server, mais ne prend en charge que la modification du type de licence et de l’édition de SQL Server. Cette option doit être utilisée pour les machines virtuelles SQL Server avec plusieurs instances, ou participant à une instance de cluster de basculement. 

- Le mode **NoAgent** est dédié à SQL Server 2008 et SQL Server 2008 R2 installés sur Windows Server 2008. Pour plus d’informations sur l’utilisation du mode `NoAgent` pour votre image Windows Server 2008, voir [Inscription de Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Services pris en charge
L’extension Agent IaaS SQL Server prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
| --- | --- |
| **Sauvegarde automatisée SQL** |Automatise la planification des sauvegardes pour toutes les bases de données pour l’instance par défaut ou une instance nommée [correctement installée](virtual-machines-windows-sql-server-iaas-faq.md#administration) de SQL Server sur la machine virtuelle. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Mise à jour corrective automatisée SQL** |Configure une fenêtre de maintenance pendant laquelle les mises à jour de Windows importantes de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Intégration du coffre de clés Azure** |Permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer l’intégration du coffre de clés Azure SQL Server sur des machines virtuelles (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Une fois installée et en cours d’exécution, l’extension de l’agent IaaS SQL Server rend ces fonctionnalités d’administration disponibles sur le panneau SQL Server de la machine virtuelle dans le portail Azure et via Azure PowerShell pour les images de place de marché SQL Server, et via Azure PowerShell pour les installations manuelles de l’extension. 

## <a name="prerequisites"></a>Prérequis
Configuration requise pour utiliser l’extension Agent IaaS SQL Server sur votre machine virtuelle :

**Système d’exploitation**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versions de SQL Server**:

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

Vous pouvez afficher le mode actuel de votre agent SQL IaaS à l’aide de PowerShell : 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Pour les machines virtuelles SQL Server sur lesquelles l’extension IaaS *NoAgent* ou *Lightweight* est installée, vous pouvez opérer une mise à niveau le vers mode *Full* via le portail Azure. Comme il n’est pas possible de passer à une version antérieure, pour ce faire, vous devez désinstaller complètement l’extension IaaS SQL, puis la réinstaller. 

Pour mettre à niveau le mode de l’agent vers le *Full*, procédez comme suit : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à votre ressource [machines virtuelles SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource). 
1. Sélectionnez votre machine virtuelle SQL Server, puis choisissez **Vue d’ensemble**. 
1. Pour les machines virtuelles SQL avec les modes IaaS *NoAgent* ou *Lightweight*, sélectionnez le message **Seules les mises à jour de type de licence et d’édition sont disponibles avec l’extension IaaS SQL**.

    ![Lancer le changement du mode à partir du portail](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Acceptez de **redémarrer le service SQL Server** en activant la case à cocher correspondante, puis sélectionnez **Confirmer** pour mettre à niveau votre mode IaaS vers le mode « Full ». 

    ![Activer la gestion complète pour l’extension IaaS](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Installation
L’extension IaaS SQL est installée lors de l’inscription de votre machine virtuelle SQL Server auprès du [fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). Toutefois, si nécessaire, vous pouvez également installer l’agent IaaS SQL manuellement en mode *Full* ou *Lightweight*. 

L’Extension de l’agent IaaS SQL Server *Full* s’installe automatiquement lorsque vous approvisionnez l’une des images de galerie de machine virtuelle SQL Server via le portail Azure. 

### <a name="full-mode-installation"></a>Installation en mode Full
L’extension IaaS SQL *Full* assure la facilité de gestion d’une instance unique sur la machine virtuelle SQL Server. S’il existe une instance par défaut, l’extension fonctionne avec celle-ci et ne prend pas en charge la gestion des autres instances. S’il n’existe pas d’instance par défaut, mais une seule instance nommée, elle gère cette instance nommée. En l’absence d’instance par défaut et si plusieurs instances nommées sont présentes, l’extension échoue à l’installation. 

L’installation du mode *Full* de l’IaaS SQL a pour effet de redémarrer le service SQL Server. Pour éviter le redémarrage du service SQL Server, installez plutôt le mode *Lightweight* offrant une facilité de gestion limitée. 

Installer l’agent IaaS SQL avec le mode *Full* à l’aide de PowerShell :

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Paramètre | Valeurs acceptables                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'` ou `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - Si l’extension n’a pas encore été installée, l’installation de l’extension **Full** a pour effet de redémarrer le service SQL Server. Utilisez le mode **Lightweight** pour éviter le redémarrage du service SQL Server. 
> - La mise à jour de l’extension SQL IaaS n’a pas pour effet de redémarrer le service SQL Server. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installer sur une machine virtuelle avec une seule instance de SQL Server nommée
L’extension IaaS SQL fonctionne avec une instance nommée sur un serveur SQL Server si l’instance par défaut est désinstallée et si l’extension IaaS est réinstallée.

Pour utiliser une instance nommée de SQL Server, procédez comme suit :
   1. Déployez une machine virtuelle SQL Server à partir du marketplace. 
   1. Désinstallez l’extension IaaS depuis le [portail Azure](https://portal.azure.com).
   1. Désinstallez complètement SQL Server sur la machine virtuelle SQL Server.
   1. Installez SQL Server avec une instance nommée sur la machine virtuelle SQL Server. 
   1. Installez l’extension IaaS depuis le portail Azure.  


### <a name="install-in-lightweight-mode"></a>Installer en mode Lightweight
Le mode Lightweight ne redémarre pas votre service SQL Server, mais ses fonctionnalités sont limitées. 

Installer l’agent IaaS SQL avec le mode *Lightweight* à l’aide de PowerShell :


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Paramètre | Valeurs acceptables                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'` ou `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Obtenir l’état de l’extension IaaS SQL
Pour vérifier que l’extension est installée, un moyen consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **All settings** (Tous les paramètres) dans la fenêtre de la machine virtuelle, puis cliquez sur **Extensions**. L’extension **SslIaaSExtension** doit s’afficher.

![Extension de l’agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser la cmdlet Azure PowerShell **Get-AzVMSqlServerExtension**.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

La commande précédente confirme que l’agent est installé et fournit des informations générales sur son état. Vous pouvez également obtenir des informations d’état sur Sauvegarde automatisée et Mise à jour corrective automatisée, grâce aux commandes suivantes.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Suppression
Dans le portail Azure, vous pouvez désinstaller l’extension en cliquant sur le bouton de sélection dans la fenêtre **Extensions** des propriétés de la machine virtuelle. Ensuite, cliquez sur **Supprimer**.

![Désinstaller l’extension d’agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser la cmdlet PowerShell **Remove-AzVMSqlServerExtension**.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Étapes suivantes
Commencez par utiliser l’un des services pris en charge par l’extension. Pour plus de détails, voir les articles référencés dans la section [Services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

