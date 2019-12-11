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
ms.date: 08/01/2019
ms.openlocfilehash: c122baa21c5d94b57f29cb0530f0a2655faa87d0
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790381"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Modifications apportées à la documentation concernant SQL Server sur des machines virtuelles Azure

Azure permet de déployer une machine virtuelle avec une image de SQL Server intégrée. Cet article résume les modifications apportées à la documentation en lien avec les nouvelles fonctionnalités et les améliorations introduites dans les mises en production récentes de [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="october-2019"></a>Octobre 2019

| Changements | Détails |
| --- | --- |
| **Inscription du fournisseur de ressources en bloc** | Vous pouvez désormais [inscrire en bloc](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) des machines virtuelles SQL avec le fournisseur de ressources. | 
| **Configuration du stockage à performances optimisées** | Vous pouvez désormais [personnaliser totalement votre configuration de stockage](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) lors de la création d’une machine virtuelle SQL Server. |
| **Partage de fichiers Premium pour instance de cluster de basculement** | Vous pouvez maintenant créer une instance de cluster de basculement à l’aide d’un [partage de fichiers Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md), plutôt qu’avec la méthode d’origine d’[Espaces de stockage direct](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| &nbsp; | &nbsp; |

## <a name="august-2019"></a>Août 2019

| Changements | Détails |
| --- | --- |
| **Hôte dédié Azure** | Vous pouvez exécuter votre machine virtuelle SQL Server sur un [hôte dédié Azure](virtual-machines-windows-sql-dedicated-host.md). |
| &nbsp; | &nbsp; |


## <a name="july-2019"></a>Juillet 2019


| Changements | Détails |
| --- | --- |
| **Déplacer la machine virtuelle SQL vers une autre région** | Utilisez Azure Site Recovery pour [migrer votre machine virtuelle SQL Server d’une région à une autre](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Juin 2019


| Changements | Détails |
| --- | --- |
| **Nouveaux modes d’installation de SQL IaaS** | Il est désormais possible d’installer l’extension IaaS SQL Server en [mode allégé](virtual-machines-windows-sql-server-agent-extension.md) pour éviter de redémarrer le service SQL Server.  |
| **Modification de l’édition de SQL Server** | Vous pouvez désormais changer la [propriété d’édition](virtual-machines-windows-sql-change-edition.md) de votre machine virtuelle SQL Server. |
| **Change le fournisseur de ressources de machine virtuelle SQL** | Vous pouvez [inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machines virtuelles](virtual-machines-windows-sql-register-with-resource-provider.md) en utilisant les nouveaux modes IaaS SQL. Cette fonctionnalité inclut des images [Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes).|
| **Images BYOL (apportez votre propre licence) à l’aide d’Azure Hybrid Benefit** | Les images BYOL (apportez votre propre licence) déployées à partir de la Place de marché Azure peuvent désormais changer leur [type de licence avec paiement à l'utilisation](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Mai 2019

| Changements | Détails |
| --- | --- |
| **Nouvelle gestion de machine virtuelle SQL Server dans le portail Azure** | Il y a désormais une façon de gérer votre machine virtuelle SQL Server dans le portail Azure. Pour plus d’informations, consultez [Gérer des machines virtuelles SQL Server dans le portail Azure](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |



## <a name="april-2019"></a>Avril 2019

| Changements | Détails |
| --- | --- |
| **Prise en charge étendue pour SQL Server 2008/2008 R2** | [Étendez la prise en charge](virtual-machines-windows-sql-server-2008-eos-extend-support.md) pour SQL Server 2008 et SQL Server 2008 R2 en migrant *tel quel* dans une machine virtuelle Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Mars 2019

| Changements | Détails |
| --- | --- |
| **Prise en charge de l’image personnalisée** | Vous pouvez maintenant installer l’[extension IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) pour les images du système d’exploitation et SQL personnalisées, qui offre des fonctionnalités limitées de [licences flexibles](virtual-machines-windows-sql-ahb.md). Lorsque vous inscrivez votre image personnalisée auprès du fournisseur de ressources SQL, spécifiez le type de licence « AHUB ». Sinon, l’inscription échoue. | 
| **Prise en charge d’instance nommée** | Vous pouvez maintenant utiliser l’[extension IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) avec une instance nommée si l’instance par défaut a été correctement désinstallée. | 
| **Amélioration du portail** | L’expérience du portail Azure pour le déploiement d’une machine virtuelle SQL Server a été revisitée pour en améliorer son usage. Pour plus d’informations, consultez le court [Démarrage rapide](quickstart-sql-vm-create-portal.md) et un [Guide pratique](virtual-machines-windows-portal-sql-server-provision.md) plus détaillé pour déployer d’une machine virtuelle SQL Server.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Février 2019

| Changements | Détails |
| --- | --- |
| **Amélioration du portail** | Il est désormais possible de changer le modèle de licence d’une machine virtuelle SQL Server de « Paiement à l’utilisation » en « BYOL » (apportez votre propre licence) en utilisant le [portail Azure](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider).|
|**Simplification du déploiement d’un groupe de disponibilité avec l’interface de ligne de commande de machine virtuelle Azure SQL Server** | Il est désormais plus facile que jamais de déployer un groupe de disponibilité sur une machine virtuelle SQL Server dans Azure. Vous pouvez utiliser [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) pour créer le cluster de basculement Windows, l’équilibreur de charge interne et les écouteurs de groupe de disponibilité à partir de la ligne de commande. Pour plus d’informations, consultez [Utiliser l’interface CLI de machine virtuelle Azure SQL Server pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018


### <a name="december-2018"></a>Décembre 2018

| Changements | Détails |
| --- | --- |
| **Nouveau fournisseur de ressources pour un cluster SQL Server** | Un nouveau fournisseur de ressources (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) définit les métadonnées du cluster de basculement Windows. Joindre une machine virtuelle SQL Server à *SqlVirtualMachineGroups* amorce le cluster de basculement Windows Server (WSFC) et joint la machine virtuelle au cluster.  |
|**Configuration automatisée du déploiement d’un groupe de disponibilité avec des modèles de démarrage rapide Azure** |Il est désormais possible de créer le cluster de basculement Windows, d’y joindre des machines virtuelles SQL Server, de créer l’écouteur et de configurer l’équilibreur de charge interne avec deux modèles de démarrage rapide Azure. Pour plus d’informations, consultez [Utiliser un modèle de démarrage rapide Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Inscription automatique auprès du fournisseur de ressources de machine virtuelle SQL** | Les machines virtuelles SQL Server déployées après la fin du mois sont automatiquement inscrites auprès du nouveau fournisseur de ressources SQL Server. Les machines virtuelles SQL Server déployées dans les mois précédents nécessitent quand même une inscription manuelle. Pour plus d’informations, consultez [Inscrire une machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machines virtuelles SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


### <a name="november-2018"></a>Novembre 2018

| Changements | Détails |
| --- | --- |
| **Nouveau fournisseur de ressources de machine virtuelle SQL** |  Un nouveau fournisseur de ressources pour les machines virtuelles SQL Server (Microsoft.SqlVirtualMachine) permet de mieux gérer vos machines virtuelles SQL Server. Pour plus d’informations sur l’inscription de vos machines virtuelles, consultez [Inscrire une machine virtuelle SQL Server dans Azure auprès du fournisseur de ressources de machines virtuelles SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Changer de modèle de licence** | Vous pouvez maintenant passer du modèle de paiement à l’utilisation au modèle BYOL (apportez votre propre licence) pour votre machine virtuelle SQL Server à l’aide de l’interface de ligne de commande Azure CLI ou de PowerShell. Pour plus d'informations, consultez [Guide pratique pour changer le modèle de licence d'une machine virtuelle SQL Server](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Ressources supplémentaires

**Machines virtuelles Windows** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Approvisionner une machine virtuelle Windows SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md)
* [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-performance.md)
* [Modèles d'application et stratégies de développement pour SQL Server sur des machines virtuelles Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Machines virtuelles Linux** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Approvisionner une machine virtuelle SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Forum Aux Questions (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentation relative à SQL Server sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
