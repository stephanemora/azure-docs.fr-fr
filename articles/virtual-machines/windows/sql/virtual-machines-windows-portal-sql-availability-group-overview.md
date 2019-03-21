---
title: Groupes de disponibilité SQL Server - Machines virtuelles Azure - Vue d’ensemble | Microsoft Docs
description: Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861840"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Présentation des groupes de disponibilité SQL Server AlwaysOn sur des machines virtuelles Azure #

Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure. 

Les groupes de disponibilité AlwaysOn sur les machines virtuelles Azure sont similaires aux groupes de disponibilité AlwaysOn locaux. Pour plus d’informations, voir [Groupes de disponibilité AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Ce schéma illustre les parties d’un groupe de disponibilité SQL Server complet dans les machines virtuelles Azure.

![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La différence principale d’un groupe de disponibilité dans les machines virtuelles Azure est que les machines virtuelles, nécessitent un [équilibrage de charge](../../../load-balancer/load-balancer-overview.md). Cet équilibrage de charge stocke les adresses IP de l’écouteur du groupe de disponibilité. Si vous avez plusieurs groupes de disponibilité, chacun requiert un écouteur. Un équilibrage de charge prend en charge plusieurs écouteurs.

En outre, sur un cluster de basculement invité de machine virtuelle Azure IaaS, nous vous recommandons une seule carte réseau par serveur (nœud de cluster) et un seul sous-réseau. Les réseaux Azure intègrent une redondance physique, ce qui rend inutiles les cartes réseau et les sous-réseaux supplémentaires sur un cluster invité de machine virtuelle IaaS Azure. Même si le rapport de validation de cluster émet un avertissement stipulant que les nœuds sont uniquement accessibles sur un seul réseau, vous pouvez ignorer ce dernier en toute sécurité sur les clusters de basculement invités de machine virtuelle IaaS Azure. 

|  | Version de Windows Server | Version SQL Server | Édition SQL Server | Configuration de Quorum WSFC | Récupération d’urgence avec plusieurs régions | Prise en charge de sous-réseaux multiples | Prise en charge pour une annonce existante | Récupération d’urgence avec multizone même région | Prise en charge dist-groupe de disponibilité sans domaine AD | Prise en charge de dist-AG avec aucun cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [CLI DE MACHINE VIRTUELLE SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Témoin de cloud | Non  | OUI | OUI | Oui | Non  | Non  |
| [Modèles de démarrage rapide](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Témoin de cloud | Non  | OUI | OUI | Oui | Non  | Non  |
| [Modèle du portail](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Partage de fichiers | Non  | Non  | Non  | Non  | Non  | Non  |
| [Manuel](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Tous | Tous | Tous | Tous | Oui | OUI | OUI | OUI | OUI | Oui |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Lorsque vous êtes prêt à générer un groupe de disponibilité SQL Server sur des machines virtuelles Azure, consultez ces didacticiels.

## <a name="manually-with-azure-cli"></a>Manuellement avec Azure CLI
À l’aide d’Azure CLI pour configurer et déployer un groupe de disponibilité d’est l’option recommandée, car il est le meilleur en termes de simplicité et la rapidité de déploiement. Avec Azure CLI, la création du Cluster de basculement Windows, joindre des machines virtuelles SQL Server sur le cluster, ainsi que la création de l’écouteur et un équilibreur de charge interne peuvent toutes être effectuées en moins de 30 minutes. Cette option nécessite la création d’un manuelle du groupe de disponibilité toujours, mais automatise toutes les autres étapes de configuration nécessaires. 

Pour plus d’informations, consultez [utilisation d’une machine virtuelle SQL Azure CLI pour configurer le groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatiquement avec les modèles de démarrage rapide Azure
Les modèles de démarrage rapide Azure utilisent le fournisseur de ressources SQL VM pour déployer le Cluster de basculement Windows, joindre des machines virtuelles SQL Server à elle, créez l’écouteur et configurer l’équilibreur de charge interne. Cette option toujours nécessite la création d’un manuelle du groupe de disponibilité et équilibrage de charge interne (ILB) mais automatise et simplifie tous les autres étapes de configuration nécessaires (y compris la configuration de l’équilibreur de charge interne). 

Pour plus d’informations, consultez [utiliser un modèle Azure Quickstart pour configurer le groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatiquement avec un modèle du portail Azure

[Configurer automatiquement un groupe de disponibilité AlwaysOn dans une machine virtuelle Azure - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuellement dans le portail Azure

Vous pouvez également créer les machines virtuelles vous-même sans le modèle. Tout d’abord, remplissez les conditions requises, puis créez le groupe de disponibilité. Consultez les rubriques suivantes : 

- [Remplir les conditions requises pour les groupes de disponibilité AlwaysOn SQL Server sur les machines virtuelles Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Créer un groupe de disponibilité AlwaysOn pour améliorer la disponibilité et la récupération d’urgence](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Étapes suivantes

[Configurer un groupe de disponibilité SQL Server Always On sur des machines virtuelles Azure dans différentes régions](virtual-machines-windows-portal-sql-availability-group-dr.md)
