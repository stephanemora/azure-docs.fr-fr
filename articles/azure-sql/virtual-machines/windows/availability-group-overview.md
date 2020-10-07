---
title: Vue d’ensemble des groupes de disponibilité Always On SQL Server
description: Cet article présente les groupes de disponibilité Always On de SQL Server sur les machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 34d76d7c85a478b5e31a692e653752aa1653884c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91293660"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Présentation des groupes de disponibilité SQL Server Always On sur des machines virtuelles Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure. 

Les groupes de disponibilité AlwaysOn sur les machines virtuelles Azure sont similaires aux groupes de disponibilité AlwaysOn locaux. Pour plus d’informations, voir [Groupes de disponibilité AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Le schéma suivant illustre les parties d’un groupe de disponibilité SQL Server complet dans les machines virtuelles Azure.

![Groupe de disponibilité](./media/availability-group-overview/00-EndstateSampleNoELB.png)

La différence principale d’un groupe de disponibilité sur les machines virtuelles Azure est que ces machines virtuelles nécessitent un [équilibreur de charge](../../../load-balancer/load-balancer-overview.md). Cet équilibrage de charge stocke les adresses IP de l’écouteur du groupe de disponibilité. Si vous avez plusieurs groupes de disponibilité, chacun nécessite un écouteur. Un équilibrage de charge prend en charge plusieurs écouteurs.

En outre, sur un cluster de basculement invité de machine virtuelle IaaS Azure, nous vous recommandons d'utiliser une seule carte réseau par serveur (nœud de cluster) et un seul sous-réseau. Les réseaux Azure intègrent une redondance physique, ce qui rend inutiles les cartes réseau et les sous-réseaux supplémentaires sur un cluster invité de machine virtuelle IaaS Azure. Même si le rapport de validation de cluster émet un avertissement stipulant que les nœuds sont uniquement accessibles sur un seul réseau, vous pouvez ignorer ce dernier en toute sécurité sur les clusters de basculement invités de machine virtuelle IaaS Azure. 

Pour accroître la redondance et la haute disponibilité, les machines virtuelles SQL Server doivent se trouver dans le même [groupe à haute disponibilité](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets) ou dans des [zones de disponibilité](/azure/availability-zones/az-overview) différentes. 

|  | Version de Windows Server | Version de SQL Server | Édition SQL Server | Configuration de Quorum WSFC | Récupération d'urgence avec plusieurs régions | Prise en charge de plusieurs sous-réseaux | Prise en charge d'un domaine d'application existant | Récupération d'urgence dans plusieurs zones de la même région | Prise en charge Dist-AG sans domaine AD | Prise en charge Dist-AG sans cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[Portail Azure](availability-group-azure-portal-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Ent | Témoin de cloud | Non | Oui | Oui | Oui | Non | Non |
| **[Azure CLI/PowerShell](availability-group-az-cli-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Ent | Témoin de cloud | Non | Oui | Oui | Oui | Non | Non |
| **[Modèles de démarrage rapide](availability-group-quickstart-template-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016  | Ent | Témoin de cloud | Non | Oui | Oui | Oui | Non | Non |
| **[Manuelle](availability-group-manually-configure-prerequisites-tutorial.md)** | Tous | Tous | Tous | Tous | Oui | Oui | Oui | Oui | Oui | Oui |

Le modèle **Cluster AlwaysOn SQL Server (préversion)** a été supprimé de la Place de marché Azure et n’est plus disponible. 

Lorsque vous êtes prêt à générer un groupe de disponibilité SQL Server sur des machines virtuelles Azure, consultez ces didacticiels.

## <a name="manually-with-azure-cli"></a>Manuellement avec Azure CLI

Nous vous recommandons d’utiliser Azure CLI pour configurer et déployer un groupe de disponibilité, car c’est la façon la plus simple et la plus rapide d’effectuer le déploiement. Avec Azure CLI, les opérations de création du cluster de basculement Windows, de jonction des machines virtuelles SQL Server au cluster, et de création de l'écouteur et de l'équilibreur de charge interne peuvent toutes être réalisées en moins de 30 minutes. Cette option nécessite toujours une création manuelle du groupe de disponibilité, mais automatise toutes les autres étapes de configuration nécessaires. 

Pour plus d’informations, consultez [Utiliser l’interface CLI de machine virtuelle SQL Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatiquement avec les modèles de démarrage rapide Azure

Les modèles de démarrage rapide Azure utilisent le fournisseur de ressources de machines virtuelles SQL pour déployer le cluster de basculement Windows, y joindre des machines virtuelles SQL Server, créer l'écouteur et configurer l'équilibreur de charge interne. Cette option nécessite toujours une création manuelle du groupe de disponibilité et de l’équilibreur de charge interne. Toutefois, elle automatise et simplifie toutes les autres étapes de configuration nécessaires, y compris la configuration de l’équilibreur de charge interne. 

Pour plus d’informations, consultez [Utiliser un modèle de démarrage rapide Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatiquement avec un modèle du portail Azure

[Configurer automatiquement un groupe de disponibilité AlwaysOn dans une machine virtuelle Azure - Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>Manuellement dans le portail Azure

Vous pouvez également créer les machines virtuelles vous-même sans le modèle. Tout d’abord, remplissez les conditions requises, puis créez le groupe de disponibilité. Consultez les rubriques suivantes : 

- [Remplir les conditions requises pour les groupes de disponibilité AlwaysOn SQL Server sur les machines virtuelles Azure](availability-group-manually-configure-prerequisites-tutorial.md)

- [Créer un groupe de disponibilité AlwaysOn pour améliorer la disponibilité et la récupération d’urgence](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Étapes suivantes

[Configurer un groupe de disponibilité SQL Server Always On sur des machines virtuelles Azure dans différentes régions](availability-group-manually-configure-multiple-regions.md)
