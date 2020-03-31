---
title: Vue d’ensemble des groupes de disponibilité
description: Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037494"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Présentation des groupes de disponibilité SQL Server sur des machines virtuelles Azure

Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure. 

Les groupes de disponibilité AlwaysOn sur les machines virtuelles Azure sont similaires aux groupes de disponibilité AlwaysOn locaux. Pour plus d’informations, voir [Groupes de disponibilité AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Ce schéma illustre les parties d’un groupe de disponibilité SQL Server complet dans les machines virtuelles Azure.

![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La différence principale d’un groupe de disponibilité dans les machines virtuelles Azure est que les machines virtuelles, nécessitent un [équilibrage de charge](../../../load-balancer/load-balancer-overview.md). Cet équilibrage de charge stocke les adresses IP de l’écouteur du groupe de disponibilité. Si vous avez plusieurs groupes de disponibilité, chacun requiert un écouteur. Un équilibrage de charge prend en charge plusieurs écouteurs.

En outre, sur un cluster de basculement invité de machine virtuelle IaaS Azure, nous vous recommandons d'utiliser une seule carte réseau par serveur (nœud de cluster) et un seul sous-réseau. Les réseaux Azure intègrent une redondance physique, ce qui rend inutiles les cartes réseau et les sous-réseaux supplémentaires sur un cluster invité de machine virtuelle IaaS Azure. Même si le rapport de validation de cluster émet un avertissement stipulant que les nœuds sont uniquement accessibles sur un seul réseau, vous pouvez ignorer ce dernier en toute sécurité sur les clusters de basculement invités de machine virtuelle IaaS Azure. 

Pour accroître la redondance et la haute disponibilité, les machines virtuelles SQL Server doivent se trouver dans le même [groupe à haute disponibilité](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets) ou dans des [zones de disponibilité](/azure/availability-zones/az-overview) différentes. 

|  | Version de Windows Server | Version de SQL Server | Édition SQL Server | Configuration de Quorum WSFC | Récupération d'urgence avec plusieurs régions | Prise en charge de plusieurs sous-réseaux | Prise en charge d'un domaine d'application existant | Récupération d'urgence dans plusieurs zones de la même région | Prise en charge Dist-AG sans domaine AD | Prise en charge Dist-AG sans cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [Interface CLI des machines virtuelles SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Témoin de cloud | Non | Oui | Oui | Oui | Non | Non |
| [Modèles de démarrage rapide](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Témoin de cloud | Non | Oui | Oui | Oui | Non | Non |
| [Modèle du portail](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Partage de fichiers | Non | Non | Non | Non | Non | Non |
| [Manuel](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Tous | Tous | Tous | Tous | Oui | Oui | Oui | Oui | Oui | Oui |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Lorsque vous êtes prêt à générer un groupe de disponibilité SQL Server sur des machines virtuelles Azure, consultez ces didacticiels.

## <a name="manually-with-azure-cli"></a>Manuellement avec Azure CLI
L'utilisation d'Azure CLI pour configurer et déployer un groupe de disponibilité est l'option recommandée, car il s'agit de la meilleure solution en termes de simplicité et de rapidité de déploiement. Avec Azure CLI, les opérations de création du cluster de basculement Windows, de jonction des machines virtuelles SQL Server au cluster, et de création de l'écouteur et de l'équilibreur de charge interne peuvent toutes être réalisées en moins de 30 minutes. Cette option nécessite toujours une création manuelle du groupe de disponibilité, mais automatise toutes les autres étapes de configuration nécessaires. 

Pour plus d’informations, consultez [Utiliser l’interface CLI de machine virtuelle SQL Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatiquement avec les modèles de démarrage rapide Azure
Les modèles de démarrage rapide Azure utilisent le fournisseur de ressources de machines virtuelles SQL pour déployer le cluster de basculement Windows, y joindre des machines virtuelles SQL Server, créer l'écouteur et configurer l'équilibreur de charge interne. Cette option nécessite toujours une création manuelle du groupe de disponibilité et de l'équilibreur de charge interne (ILB) mais automatise et simplifie toutes les autres étapes de configuration nécessaires (y compris la configuration de l'ILB). 

Pour plus d’informations, consultez [Utiliser un modèle de démarrage rapide Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatiquement avec un modèle du portail Azure

[Configurer automatiquement un groupe de disponibilité AlwaysOn dans une machine virtuelle Azure - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuellement sur le portail Azure

Vous pouvez également créer les machines virtuelles vous-même sans le modèle. Tout d’abord, remplissez les conditions requises, puis créez le groupe de disponibilité. Consultez les rubriques suivantes : 

- [Remplir les conditions requises pour les groupes de disponibilité AlwaysOn SQL Server sur les machines virtuelles Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Créer un groupe de disponibilité AlwaysOn pour améliorer la disponibilité et la récupération d’urgence](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Étapes suivantes

[Configurer un groupe de disponibilité SQL Server Always On sur des machines virtuelles Azure dans différentes régions](virtual-machines-windows-portal-sql-availability-group-dr.md)
