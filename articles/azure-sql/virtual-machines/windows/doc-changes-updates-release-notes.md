---
title: Modifications apportées à la documentation concernant SQL Server sur des machines virtuelles Azure | Microsoft Docs
description: Découvrir les nouvelles fonctionnalités et améliorations pour SQL Server sur une machine virtuelle Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: f87d72df977e98c60948389d794eb102ac08f8d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032470"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Modifications apportées à la documentation concernant SQL Server sur des machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure permet de déployer une machine virtuelle avec une image de SQL Server intégrée. Cet article résume les modifications apportées à la documentation en lien avec les nouvelles fonctionnalités et les améliorations introduites dans les mises en production récentes de [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Janvier 2020

| Modifications | Détails |
| --- | --- |
| **Prise en charge d’Azure Government** | Il est désormais possible d’inscrire des machines virtuelles SQL Server auprès du fournisseur de ressources de machine virtuelle SQL pour les machines virtuelles hébergées dans le cloud [Azure Government](https://azure.microsoft.com/global-infrastructure/government/). | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Modifications | Détails |
 --- | --- |
| **Réplica de récupération d’urgence gratuit dans Azure** | Vous pouvez héberger une [instance passive gratuite](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) pour la récupération d’urgence dans Azure pour votre instance SQL Server locale si vous disposez de [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Inscription du fournisseur de ressources en bloc** | Vous pouvez désormais [inscrire en bloc](sql-vm-resource-provider-bulk-register.md) des machines virtuelles SQL avec le fournisseur de ressources. | 
|**Configuration du stockage à performances optimisées** | Vous pouvez désormais [personnaliser totalement votre configuration de stockage](storage-configuration.md#new-vms) lors de la création d’une machine virtuelle SQL Server. |
|**Partage de fichiers Premium pour instance de cluster de basculement** | Vous pouvez maintenant créer une instance de cluster de basculement à l’aide d’un [partage de fichiers Premium](failover-cluster-instance-premium-file-share-manually-configure.md), plutôt qu’avec la méthode d’origine d’[Espaces de stockage direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Hôte dédié Azure** | Vous pouvez exécuter votre machine virtuelle SQL Server sur un [hôte dédié Azure](dedicated-host.md). | 
| **Déplacer la machine virtuelle SQL vers une autre région** | Utilisez Azure Site Recovery pour [migrer votre machine virtuelle SQL Server d’une région à une autre](move-sql-vm-different-region.md). |
|  **Nouveaux modes d’installation de SQL IaaS** | Il est désormais possible d’installer l’extension IaaS SQL Server en [mode allégé](sql-server-iaas-agent-extension-automate-management.md) pour éviter de redémarrer le service SQL Server.  |
| **Modification de l’édition de SQL Server** | Vous pouvez désormais changer la [propriété d’édition](change-sql-server-edition.md) de votre machine virtuelle SQL Server. |
| **Change le fournisseur de ressources de machine virtuelle SQL** | Vous pouvez [inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machines virtuelles](sql-vm-resource-provider-register.md) en utilisant les nouveaux modes IaaS SQL. Cette fonctionnalité inclut des images [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes).|
| **Images BYOL (apportez votre propre licence) à l’aide d’Azure Hybrid Benefit** | Les images BYOL (apportez votre propre licence) déployées à partir de la Place de marché Azure peuvent désormais changer leur [type de licence avec paiement à l'utilisation](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Nouvelle gestion de machine virtuelle SQL Server dans le portail Azure** | Il y a désormais une façon de gérer votre machine virtuelle SQL Server dans le portail Azure. Pour plus d’informations, consultez [Gérer des machines virtuelles SQL Server dans le portail Azure](manage-sql-vm-portal.md).  | 
| **Prise en charge étendue pour SQL Server 2008/2008 R2** | [Étendez la prise en charge](sql-server-2008-extend-end-of-support.md) pour SQL Server 2008 et SQL Server 2008 R2 en migrant *tel quel* dans une machine virtuelle Azure. | 
| **Prise en charge de l’image personnalisée** | Vous pouvez maintenant installer l’[extension IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md#installation) pour les images du système d’exploitation et SQL personnalisées, qui offre des fonctionnalités limitées de [licences flexibles](licensing-model-azure-hybrid-benefit-ahb-change.md). Lorsque vous inscrivez votre image personnalisée auprès du fournisseur de ressources SQL, spécifiez le type de licence « AHUB ». Sinon, l’inscription échoue. | 
| **Prise en charge d’instance nommée** | Vous pouvez maintenant utiliser l’[extension IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md#installation) avec une instance nommée si l’instance par défaut a été correctement désinstallée. | 
| **Amélioration du portail** | L’expérience du portail Azure pour le déploiement d’une machine virtuelle SQL Server a été revisitée pour en améliorer son usage. Pour plus d’informations, consultez le court [Démarrage rapide](sql-vm-create-portal-quickstart.md) et un [Guide pratique](create-sql-vm-portal.md) plus détaillé pour déployer d’une machine virtuelle SQL Server.|
| **Amélioration du portail** | Il est désormais possible de changer le modèle de licence d’une machine virtuelle SQL Server de « Paiement à l’utilisation » en « BYOL » (apportez votre propre licence) en utilisant le [portail Azure](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Simplification du déploiement d’un groupe de disponibilité avec l’interface de ligne de commande de machine virtuelle Azure SQL Server** | Il est désormais plus facile que jamais de déployer un groupe de disponibilité sur une machine virtuelle SQL Server dans Azure. Vous pouvez utiliser [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) pour créer le cluster de basculement Windows, l’équilibreur de charge interne et les écouteurs de groupe de disponibilité à partir de la ligne de commande. Pour plus d’informations, consultez [Utiliser l’interface CLI de machine virtuelle Azure SQL Server pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Modifications | Détails |
| --- | --- |
|  **Nouveau fournisseur de ressources pour un cluster SQL Server** | Un nouveau fournisseur de ressources (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) définit les métadonnées du cluster de basculement Windows. Joindre une machine virtuelle SQL Server à *SqlVirtualMachineGroups* amorce le cluster de basculement Windows Server (WSFC) et joint la machine virtuelle au cluster.  |
| **Configuration automatisée du déploiement d’un groupe de disponibilité avec des modèles de démarrage rapide Azure** |Il est désormais possible de créer le cluster de basculement Windows, d’y joindre des machines virtuelles SQL Server, de créer l’écouteur et de configurer l’équilibreur de charge interne avec deux modèles de démarrage rapide Azure. Pour plus d’informations, consultez [Utiliser un modèle de démarrage rapide Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](availability-group-quickstart-template-configure.md). | 
| **Inscription automatique auprès du fournisseur de ressources de machine virtuelle SQL** | Les machines virtuelles SQL Server déployées après la fin du mois sont automatiquement inscrites auprès du nouveau fournisseur de ressources SQL Server. Les machines virtuelles SQL Server déployées dans les mois précédents nécessitent quand même une inscription manuelle. Pour plus d’informations, consultez [Inscrire une machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machines virtuelles SQL](sql-vm-resource-provider-register.md).|
|**Nouveau fournisseur de ressources de machine virtuelle SQL** |  Un nouveau fournisseur de ressources pour les machines virtuelles SQL Server (Microsoft.SqlVirtualMachine) permet de mieux gérer vos machines virtuelles SQL Server. Pour plus d’informations sur l’inscription de vos machines virtuelles, consultez [Inscrire une machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machines virtuelles SQL](sql-vm-resource-provider-register.md). |
|**Changer de modèle de licence** | Vous pouvez maintenant passer du modèle de paiement à l’utilisation au modèle BYOL (apportez votre propre licence) pour votre machine virtuelle SQL Server à l’aide de l’interface de ligne de commande Azure CLI ou de PowerShell. Pour plus d'informations, consultez [Guide pratique pour changer le modèle de licence d'une machine virtuelle SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Ressources supplémentaires

**Machines virtuelles Windows** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Approvisionner une machine virtuelle Windows SQL Server](create-sql-vm-portal.md)
* [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](migrate-to-vm-from-sql-server.md)
* [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](performance-guidelines-best-practices.md)
* [Modèles d'application et stratégies de développement pour SQL Server sur des machines virtuelles Azure](application-patterns-development-strategies.md)

**Machines virtuelles Linux** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Approvisionner une machine virtuelle SQL Server Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Forum Aux Questions (Linux)](../linux/frequently-asked-questions-faq.md)
* [Documentation relative à SQL Server sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
