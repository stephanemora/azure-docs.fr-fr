---
title: Automatiser les tâches de gestion avec l’extension d’agent IaaS
description: Cet article indique comment gérer l’extension d’agent IaaS SQL Server, qui automatise certaines tâches d’administration SQL Server. Celles-ci incluent la sauvegarde automatisée, la mise à jour corrective automatisée et l’intégration Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: df3e2ae2737aa8bfedd0e8d7daa9d42771f1937b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570345"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatiser les tâches de gestion sur des machines virtuelles Azure à l’aide de l’extension de l’agent IaaS SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


L’extension Agent IaaS SQL Server (SqlIaasExtension) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cet article fournit une vue d’ensemble des services pris en charge par l’extension. Cet article fournit également des instructions pour l’installation, l’état et la suppression de l’extension.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Pour afficher la version de cet article pour un déploiement Classic, consultez [Extension Agent IaaS SQL Server pour machines virtuelles SQL Server (classique)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Services pris en charge
L’extension Agent IaaS SQL Server prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
| --- | --- |
| **Sauvegarde automatisée de SQL Server** |Automatise la planification des sauvegardes pour toutes les bases de données pour l’instance par défaut ou une instance nommée [correctement installée](frequently-asked-questions-faq.md#administration) de SQL Server sur la machine virtuelle. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (Resource Manager)](automated-backup-sql-2014.md). |
| **Mise à jour corrective automatisée de SQL Server** |Configure une fenêtre de maintenance pendant laquelle les mises à jour de Windows importantes de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (Resource Manager)](automated-patching.md). |
| **Intégration d’Azure Key Vault** |Permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer l’intégration du coffre de clés Azure SQL Server sur des machines virtuelles (Resource Manager)](azure-key-vault-integration-configure.md). |

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

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Télécharger et configurer les dernières commandes Azure PowerShell](/powershell/azure/)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Installation
L’extension IaaS SQL Server est installée lors de l’inscription de votre machine virtuelle SQL Server auprès du [fournisseur de ressources de machine virtuelle SQL Server](sql-vm-resource-provider-register.md). Si nécessaire, vous pouvez installer l’agent IaaS SQL Server manuellement en utilisant la commande PowerShell ci-dessous : 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!WARNING]
> L’installation de l’extension redémarre le service SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installer sur une machine virtuelle avec une seule instance de SQL Server nommée
L’extension IaaS SQL Server fonctionne avec une instance nommée sur SQL Server si l’instance par défaut est désinstallée et si l’extension IaaS est réinstallée.

Pour utiliser une instance nommée de SQL Server, procédez comme suit :
   1. Déployez une machine virtuelle SQL Server à partir de la Place de marché Azure. 
   1. Désinstallez l’extension IaaS depuis le [Portail Azure](https://portal.azure.com).
   1. Désinstallez complètement SQL Server sur la machine virtuelle SQL Server.
   1. Installez SQL Server avec une instance nommée sur la machine virtuelle SQL Server. 
   1. Installez l’extension IaaS depuis le Portail Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Obtenir l’état de l’extension IaaS SQL Server
Pour vérifier que l’extension est installée, un moyen consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **All settings** (Tous les paramètres) dans la fenêtre de la machine virtuelle, puis sélectionnez **Extensions**. L’extension **SslIaaSExtension** doit s’afficher.

![État de l’extension d’agent IaaS SQL Server dans le Portail Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

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

![Désinstallation de l’extension d’agent IaaS SQL Server dans le Portail Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser la cmdlet PowerShell **Remove-AzVMSqlServerExtension** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Étapes suivantes
Commencez par utiliser l’un des services pris en charge par l’extension. Pour plus de détails, voir les articles référencés dans la section [Services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [Qu’est-ce que SQL Server sur les machines virtuelles Azure ?](sql-server-on-azure-vm-iaas-what-is-overview.md).
