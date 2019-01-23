---
title: Groupes de disponibilité SQL Server - Machines virtuelles Azure - Vue d’ensemble | Microsoft Docs
description: Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
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
ms.openlocfilehash: 5f8ae6d9138a7413b0cca4cca7bcc47c13212674
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358049"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Présentation des groupes de disponibilité SQL Server AlwaysOn sur des machines virtuelles Azure #

Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure. 

Les groupes de disponibilité AlwaysOn sur les machines virtuelles Azure sont similaires aux groupes de disponibilité AlwaysOn locaux. Pour plus d’informations, voir [Groupes de disponibilité AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Ce schéma illustre les parties d’un groupe de disponibilité SQL Server complet dans les machines virtuelles Azure.

![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La différence principale d’un groupe de disponibilité dans les machines virtuelles Azure est que les machines virtuelles, nécessitent un [équilibrage de charge](../../../load-balancer/load-balancer-overview.md). Cet équilibrage de charge stocke les adresses IP de l’écouteur du groupe de disponibilité. Si vous avez plusieurs groupes de disponibilité, chacun requiert un écouteur. Un équilibrage de charge prend en charge plusieurs écouteurs.

En outre, sur un cluster de basculement invité de machine virtuelle IaaS Azure, nous vous recommandons d’utiliser une seule carte réseau par serveur (nœud de cluster) et un seul sous-réseau. La mise en réseau Azure intègre une redondance physique, ce qui rend inutiles les cartes réseau et les sous-réseaux supplémentaires sur un cluster invité de machine virtuelle IaaS Azure. Même si le rapport de validation de cluster émet un avertissement stipulant que les nœuds sont uniquement accessibles sur un seul réseau, vous pouvez ignorer ce dernier en toute sécurité sur les clusters de basculement invités de machine virtuelle IaaS Azure. 

Lorsque vous êtes prêt à générer un groupe de disponibilité SQL Server sur des machines virtuelles Azure, consultez ces didacticiels.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Créer automatiquement un groupe de disponibilité à l’aide d’un modèle

[Configurer automatiquement un groupe de disponibilité AlwaysOn dans une machine virtuelle Azure - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Créer manuellement un groupe de disponibilité dans le portail Azure

Vous pouvez également créer les machines virtuelles vous-même sans le modèle. Tout d’abord, remplissez les conditions requises, puis créez le groupe de disponibilité. Consultez les rubriques suivantes : 

- [Remplir les conditions requises pour les groupes de disponibilité AlwaysOn SQL Server sur les machines virtuelles Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Créer un groupe de disponibilité AlwaysOn pour améliorer la disponibilité et la récupération d’urgence](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Étapes suivantes

[Configurer un groupe de disponibilité AlwaysOn SQL Server sur des machines virtuelles dans différentes régions](virtual-machines-windows-portal-sql-availability-group-dr.md).
