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
ms.date: 2/5/2019
ms.openlocfilehash: b59ac4798260381c11ab22adb7358ff63e5e1d77
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245420"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Notes de publication de SQL Server sur les machines virtuelles Azure

Azure permet de déployer une machine virtuelle avec une image de SQL Server intégrée. Cet article résume les nouvelles fonctionnalités et les améliorations présentes dans les versions récentes de [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Il répertorie également les mises à jour de contenu importantes qui ne sont pas directement associées à la version, mais qui ont été publiées en même temps. Pour connaître les améliorations apportées aux autres services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates)

## <a name="december-2018"></a>Décembre 2018

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| **Nouveau fournisseur de ressources de groupe de clusters SQL** | Nouveau fournisseur de ressources (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) qui définit les métadonnées liées au cluster de basculement Windows. Joindre une machine virtuelle SQL Server à *SqlVirtualMachineGroups* amorce le service de cluster de basculement Windows et joint la machine virtuelle au cluster.  |
|**Automatiser la configuration d’un déploiement de groupe de disponibilité avec les modèles de démarrage rapide Azure** |Il est désormais possible de créer le cluster de basculement Windows, d’y joindre des machines virtuelles SQL Server, de créer l’écouteur et de configurer l’équilibreur de charge interne avec deux modèles de démarrage rapide Azure. Pour plus d’informations, consultez [Créer un cluster WSFC, un écouteur, puis configurer un équilibreur de charge interne pour un groupe de disponibilité Always On sur une machine virtuelle SQL Server à l’aide d’un modèle de démarrage rapide Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Inscription automatique du fournisseur de ressources de machine virtuelle SQL** | Les machines virtuelles SQL Server déployées après la fin du mois sont automatiquement inscrites auprès du nouveau fournisseur de ressources SQL Server. Les machines virtuelles SQL Server déployées avant ce mois nécessitent quand même une inscription manuelle. Pour plus d’informations, consultez [Inscrire une machine virtuelle SQL existante auprès du nouveau fournisseur de ressources](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
|Aucun | |
| | |

## <a name="november-2018"></a>Novembre 2018

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| **Nouveau fournisseur de ressources de machine virtuelle SQL** |  Nouveau fournisseur de ressources pour les machines virtuelles SQL Server (Microsoft.SqlVirtualMachine) qui permet de mieux gérer votre machine virtuelle SQL Server. Pour plus d’informations sur l’inscription de votre machine virtuelle, consultez [Inscrire une machine virtuelle SQL existante avec le nouveau fournisseur de ressources](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider). |
|**Changer de modèle de licence** |Vous pouvez maintenant passer du modèle de paiement à l’utilisation au modèle BYOL (apportez votre propre licence) pour votre machine virtuelle SQL à l’aide d’Azure CLI ou de PowerShell. Pour plus d’informations, consultez [Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
|Aucun | |
| | |

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