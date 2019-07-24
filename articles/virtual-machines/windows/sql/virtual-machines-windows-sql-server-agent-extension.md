---
title: Automatiser les tâches de gestion sur des machines virtuelles SQL (Resource Manager) | Microsoft Docs
description: Cet article indique comment gérer l’extension d’agent SQL Server, qui automatise certaines tâches d’administration SQL Server. Celles-ci incluent Sauvegarde automatisée, Mise à jour corrective automatisée et Azure Key Vault Integration.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d3e9b3b2db4a4adc01ad3b1f348b66496658f0f5
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190928"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatiser les tâches de gestion sur des machines virtuelles Azure avec l’extension SQL Server Agent (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classique](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L’extension Agent IaaS SQL Server (SqlIaasExtension) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cet article présente les services pris en charge par l’extension, ainsi que des instructions d’installation, d’état et de suppression.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Pour afficher la version de cet article pour un déploiement Classic, consultez [Extension Agent SQL Server pour machines virtuelles SQL Server (Classic)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

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

> [!IMPORTANT]
> À ce stade, l’[extension Agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) n’est pas prise en charge pour l’ICF SQL Server sur Azure. Nous vous recommandons de désinstaller l’extension des machines virtuelles qui participent à une FCI. Les fonctionnalités prises en charge par l’extension ne sont pas disponibles sur les machines virtuelles SQL une fois l’agent désinstallé.

## <a name="installation"></a>Installation
L’Extension Agent IaaS SQL Server s’installe automatiquement lorsque vous approvisionnez une des images de galerie de machine virtuelle SQL Server. L’extension IaaS SQL assure la facilité de gestion d’une instance unique sur la machine virtuelle SQL Server. S’il existe une instance par défaut, l’extension fonctionne avec celle-ci et ne prend pas en charge la gestion des autres instances. S’il n’existe pas d’instance par défaut, mais une seule instance nommée, elle gère cette instance nommée. En l’absence d’instance par défaut et si plusieurs instances nommées sont présentes, l’extension échoue à l’installation. 



Si vous avez besoin de réinstaller l’extension manuellement sur l’une de ces machines virtuelles SQL Server, utilisez la commande PowerShell suivante :

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Si elle n’a pas encore été réalisée, l’installation de l’extension redémarre le service SQL Server. Cela étant, la mise à jour de l’extension SQL IaaS ne redémarre pas le service SQL Server. 

> [!NOTE]
> Bien qu’il soit possible d’installer SQL Server IaaS Agent pour personnaliser les images SQL Server, la fonctionnalité se limite actuellement à [la modification du type de licence](virtual-machines-windows-sql-ahb.md). Les autres fonctionnalités fournies par l’extension IaaS SQL fonctionnent uniquement sur des [images de galerie de machine virtuelle SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (paiement à l’utilisation ou BYOL).

### <a name="use-a-single-named-instance"></a>Utiliser une seule instance nommée
L’extension IaaS SQL fonctionne avec une instance nommée sur une image SQL Server si l’instance par défaut est correctement désinstallée et si l’extension IaaS est réinstallée.

Pour utiliser une instance nommée de SQL Server, procédez comme suit :
   1. Déployez une machine virtuelle SQL Server à partir du marketplace. 
   1. Désinstallez l’extension IaaS depuis le [portail Azure](https://portal.azure.com).
   1. Désinstallez complètement SQL Server sur la machine virtuelle SQL Server.
   1. Installez SQL Server avec une instance nommée sur la machine virtuelle SQL Server. 
   1. Installez l’extension IaaS depuis le portail Azure.  

## <a name="status"></a>Statut
Pour vérifier que l’extension est installée, un moyen consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **All settings** (Tous les paramètres) dans la fenêtre de la machine virtuelle, puis cliquez sur **Extensions**. L’extension **SslIaaSExtension** doit s’afficher.

![Extension de l’agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser la cmdlet Azure PowerShell **Get-AzVMSqlServerExtension**.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

La commande précédente confirme que l’agent est installé et fournit des informations générales sur son état. Vous pouvez également obtenir des informations d’état sur Sauvegarde automatisée et Mise à jour corrective automatisée, grâce aux commandes suivantes.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Suppression
Dans le portail Azure, vous pouvez désinstaller l’extension en cliquant sur le bouton de sélection dans la fenêtre **Extensions** des propriétés de la machine virtuelle. Ensuite, cliquez sur **Supprimer**.

![Désinstaller l’extension d’agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser la cmdlet PowerShell **Remove-AzVMSqlServerExtension**.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Étapes suivantes
Commencez par utiliser l’un des services pris en charge par l’extension. Pour plus d’informations, consultez les articles référencés dans la section [Services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

