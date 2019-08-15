---
title: Notes de publication de SQL Server sur les machines virtuelles Azure | Microsoft Docs
description: Découvrir les nouvelles fonctionnalités et améliorations de SQL Server sur une machine virtuelle Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/01/2019
ms.openlocfilehash: e656cd4d901ad9f3180963047a85cd980e56df8b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774216"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Notes de publication de SQL Server sur les machines virtuelles Azure

Azure permet de déployer une machine virtuelle avec une image de SQL Server intégrée. Cet article résume les nouvelles fonctionnalités et les améliorations présentes dans les versions récentes de [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Il répertorie également les mises à jour de contenu importantes qui ne sont pas directement associées à la version, mais qui ont été publiées en même temps. Pour connaître les améliorations apportées aux autres services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates)

## <a name="july-2019"></a>Juillet 2019

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Documentation | Détails |
| --- | --- |
| **Déplacer la machine virtuelle SQL vers une autre région** | Utilisez Azure Site Recovery pour [migrer votre machine virtuelle SQL Server d’une région à une autre](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Juin 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| **Nouveaux modes d’installation de SQL IaaS** | Il est désormais possible d’installer l’extension IaaS SQL en [mode allégé](virtual-machines-windows-sql-server-agent-extension.md) pour éviter de redémarrer le service SQL Server.  |
| **Modification de l’édition de SQL Server** | Vous pouvez désormais changer la [propriété d’édition](virtual-machines-windows-sql-change-edition.md) de votre machine virtuelle SQL Server. |
| **Modifications du fournisseur de ressources de la machine virtuelle SQL** | Vous pouvez [inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de la machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider), même les [images Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms), en utilisant les nouveaux modes IaaS SQL. |
| **Images BYOL avec AHUB** | Les images BYOL déployées à partir de la Place de marché peuvent désormais changer leur [type de licence en « PAYG »](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Mai 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| **Nouvelle gestion de machine virtuelle SQL dans le portail Azure** | Il y a désormais une nouvelle façon de gérer votre machine virtuelle SQL Server dans le portail Azure. Pour plus d’informations, consultez [Gérer une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-sql-manage-portal.md).  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Documentation | Détails |
| --- | --- |
| **Nouvelle gestion du portail de machine virtuelle SQL** | Environ une dizaine d’articles a été mise à jour vers une nouvelle expérience du portail de gestion de machine virtuelle SQL. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Avril 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| **Prise en charge étendue de SQL Server 2008/2008 R2** | [Étendez la prise en charge](virtual-machines-windows-sql-server-2008-eos-extend-support.md) pour SQL Server 2008 et SQL Server 2008 R2 en migrant *tel quel* dans une machine virtuelle Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Mars 2019

| Améliorations du service | Détails |
| --- | --- |
| **Prise en charge de l’image personnalisée** | Vous pouvez maintenant installer l’[extension SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) pour les images du système d’exploitation et SQL personnalisées, qui offre des fonctionnalités limitées de [licences flexibles](virtual-machines-windows-sql-ahb.md). Lors de l’inscription de votre image personnalisée avec le fournisseur de ressources SQL, spécifiez le type de licence « AHUB », ou l’inscription échoue. | 
| **Prise en charge d’instance nommée** | Vous pouvez maintenant utiliser l’[extension SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) avec une instance nommée, si l’instance par défaut a été correctement désinstallée. | 
| **Amélioration du portail** | L’expérience du portail Azure pour le déploiement d’une machine virtuelle SQL Server a été revisitée pour en améliorer son usage. Pour plus d’informations, consultez le court [Démarrage rapide](quickstart-sql-vm-create-portal.md) et un [Guide pratique](virtual-machines-windows-portal-sql-server-provision.md) plus détaillé pour le déploiement d’une machine virtuelle SQL Server.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Février 2019

| Améliorations du service | Détails |
| --- | --- |
| **Amélioration du portail** | Il est désormais possible de changer le modèle de licence d’une machine virtuelle SQL Server de « Paiement à l’utilisation » en « BYOL » (apportez votre propre licence) en utilisant le [portail Azure](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider).|
|**Simplification du déploiement de groupe de disponibilité avec l’interface CLI de machine virtuelle Azure SQL** | Il est désormais plus facile que jamais de déployer un groupe de disponibilité sur une machine virtuelle SQL Server dans Azure. L’[interface CLI de machine virtuelle Azure SQL](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) vous permet de créer l’écouteur WSFC, ILB et AG depuis la ligne de commande, et en un temps record ! Pour plus d’informations, consultez [Utiliser l’interface CLI de machine virtuelle SQL Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Décembre 2018

| Améliorations du service | Détails |
| --- | --- |
| **Nouveau fournisseur de ressources de groupe de clusters SQL** | Nouveau fournisseur de ressources (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) qui définit les métadonnées liées au cluster de basculement Windows. Joindre une machine virtuelle SQL Server à *SqlVirtualMachineGroups* amorce le service de cluster de basculement Windows et joint la machine virtuelle au cluster.  |
|**Automatiser la configuration d’un déploiement de groupe de disponibilité avec les modèles de démarrage rapide Azure** |Il est désormais possible de créer le cluster de basculement Windows, d’y joindre des machines virtuelles SQL Server, de créer l’écouteur et de configurer l’équilibreur de charge interne avec deux modèles de démarrage rapide Azure. Pour plus d’informations, consultez [Utiliser un modèle de démarrage rapide Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Inscription automatique du fournisseur de ressources de machine virtuelle SQL** | Les machines virtuelles SQL Server déployées après la fin du mois sont automatiquement inscrites auprès du nouveau fournisseur de ressources SQL Server. Les machines virtuelles SQL Server déployées avant ce mois nécessitent quand même une inscription manuelle. Pour plus d’informations, consultez [Inscrire une machine virtuelle SQL existante auprès du nouveau fournisseur de ressources de la machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Novembre 2018

| Améliorations du service | Détails |
| --- | --- |
| **Nouveau fournisseur de ressources de machine virtuelle SQL** |  Nouveau fournisseur de ressources pour les machines virtuelles SQL Server (Microsoft.SqlVirtualMachine) qui permet de mieux gérer votre machine virtuelle SQL Server. Pour plus d’informations sur l’inscription de votre machine virtuelle, consultez [Inscrire une machine virtuelle SQL existante avec le nouveau fournisseur de ressources](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Changer de modèle de licence** | Vous pouvez maintenant passer du modèle de paiement à l’utilisation au modèle BYOL (apportez votre propre licence) pour votre machine virtuelle SQL à l’aide d’Azure CLI ou de PowerShell. Pour plus d’informations, consultez [Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Ressources supplémentaires

**Machines virtuelles Windows** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Approvisionner une machine virtuelle Windows SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrating a Database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md)
* [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modèles d'application et stratégies de développement pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Machines virtuelles Linux** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Configurer une machine virtuelle Linux SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Forum Aux Questions (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentation relative à SQL Server sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
