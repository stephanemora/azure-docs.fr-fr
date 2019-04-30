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
ms.date: 2/13/2019
ms.openlocfilehash: 23e072369aa8ac6ca6ada5ec185df1a8d7e03c5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477176"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Notes de publication de SQL Server sur les machines virtuelles Azure

Azure permet de déployer une machine virtuelle avec une image de SQL Server intégrée. Cet article résume les nouvelles fonctionnalités et les améliorations présentes dans les versions récentes de [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Il répertorie également les mises à jour de contenu importantes qui ne sont pas directement associées à la version, mais qui ont été publiées en même temps. Pour connaître les améliorations apportées aux autres services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates)

## <a name="march-2019"></a>Mars 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| **Prise en charge de l’image personnalisée** | Vous pouvez maintenant installer le [extension SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) pour les images du système d’exploitation et SQL personnalisées, qui offre des fonctionnalités limitées de [licences flexibles](virtual-machines-windows-sql-ahb.md). Lorsque l’inscription de votre image personnalisée avec le fournisseur de ressources SQL, spécifiez le type de licence en tant que « AHUB » comme sinon l’inscription échoue.  | 
| **Prise en charge de l’instance nommée** | Vous pouvez maintenant utiliser le [extension SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) avec une instance nommée, si l’instance par défaut a été correctement désinstallé. | 
| **Amélioration de portail** | L’utilisation du portail Azure pour le déploiement d’une machine virtuelle SQL Server a été repensée pour améliorer la facilité d’utilisation. Pour plus d’informations, consultez la brève [Quickstart](quickstart-sql-vm-create-portal.md) et plus poussée [procédures](virtual-machines-windows-portal-sql-server-provision.md) guide de déploiement d’une machine virtuelle SQL Server. |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

Aucun


## <a name="february-2019"></a>Février 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| **Amélioration de portail** | Il est désormais possible de modifier le modèle de licence pour une machine virtuelle SQL Server à partir de paiement à l’utilisation à bring-your-propre licence à l’aide du [Azure portal](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1).|
|**Simplification de déploiement de groupe de disponibilité avec Azure CLI de machine virtuelle SQL** | Il est désormais plus facile que jamais, pour déployer un groupe de disponibilité sur une machine virtuelle SQL Server dans Azure. [Azure CLI de machine virtuelle SQL](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) vous permet de créer l’écouteur WSFC, équilibrage de charge interne et groupe de disponibilité à partir de la ligne de commande et en un temps record ! Pour plus d’informations, consultez [utilisation d’une machine virtuelle SQL Azure CLI pour configurer le groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Décembre 2018

| Améliorations du service | Détails |
| --- | --- |
| **Nouveau fournisseur de ressources de groupe de clusters SQL** | Nouveau fournisseur de ressources (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) qui définit les métadonnées liées au cluster de basculement Windows. Joindre une machine virtuelle SQL Server à *SqlVirtualMachineGroups* amorce le service de cluster de basculement Windows et joint la machine virtuelle au cluster.  |
|**Automatiser la configuration d’un déploiement de groupe de disponibilité avec les modèles de démarrage rapide Azure** |Il est désormais possible de créer le cluster de basculement Windows, d’y joindre des machines virtuelles SQL Server, de créer l’écouteur et de configurer l’équilibreur de charge interne avec deux modèles de démarrage rapide Azure. Pour plus d’informations, consultez [utiliser un modèle Azure Quickstart pour configurer le groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Inscription automatique du fournisseur de ressources de machine virtuelle SQL** | Les machines virtuelles SQL Server déployées après la fin du mois sont automatiquement inscrites auprès du nouveau fournisseur de ressources SQL Server. Les machines virtuelles SQL Server déployées avant ce mois nécessitent quand même une inscription manuelle. Pour plus d’informations, consultez [inscrire les VM SQL existante avec le fournisseur de ressources SQL VM](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Novembre 2018

| Améliorations du service | Détails |
| --- | --- |
| **Nouveau fournisseur de ressources de machine virtuelle SQL** |  Nouveau fournisseur de ressources pour les machines virtuelles SQL Server (Microsoft.SqlVirtualMachine) qui permet de mieux gérer votre machine virtuelle SQL Server. Pour plus d’informations sur l’inscription de votre machine virtuelle, consultez [Inscrire une machine virtuelle SQL existante avec le nouveau fournisseur de ressources](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
|**Changer de modèle de licence** |Vous pouvez maintenant passer du modèle de paiement à l’utilisation au modèle BYOL (apportez votre propre licence) pour votre machine virtuelle SQL à l’aide d’Azure CLI ou de PowerShell. Pour plus d’informations, consultez [Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL](virtual-machines-windows-sql-ahb.md). | 
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
