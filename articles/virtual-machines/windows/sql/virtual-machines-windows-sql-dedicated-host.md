---
title: Machine virtuelle SQL Server sur un hôte dédié Azure
description: Découvrez les détails relatifs à l’exécution d’une machine virtuelle SQL Server sur un hôte dédié Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834346"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Machine virtuelle SQL Server sur un hôte dédié Azure 

Cet article détaille les spécificités de l’utilisation d’une machine virtuelle SQL Server avec un [hôte dédié Azure](/azure/virtual-machines/windows/dedicated-hosts). Des informations complémentaires sur l’hôte dédié azure sont disponibles dans le billet de blog [Présentation des hôtes dédiés Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Vue d’ensemble
Un [hôte dédié Azure](/azure/virtual-machines/windows/dedicated-hosts) est un service qui fournit des serveurs physiques (capables d’héberger une ou plusieurs machines virtuelles) dédiés à un abonnement Azure. Les hôtes dédiés sont les mêmes serveurs physiques que ceux utilisés dans les centres de données de Microsoft, fournis en tant que ressources. Vous pouvez approvisionner des hôtes dédiés au sein d’une région, d’une zone de disponibilité et d’un domaine d’erreur. Ensuite, vous pouvez placer des machines virtuelles directement dans vos hôtes approvisionnés, dans la configuration qui répond le mieux à vos besoins.

## <a name="limitations"></a>Limites

- Actuellement, les hôtes dédiés ne prennent pas en charge les groupes de machines virtuelles identiques.
- Les séries de machines virtuelles suivantes sont prises en charge : DSv3 et ESv3. 

## <a name="licensing"></a>Licence

Vous avez le choix entre deux options différentes de gestion des licences quand vous ajoutez votre machine virtuelle SQL Server à un hôte dédié Azure. 

  - **Gestion des licences de machines virtuelles SQL** : Il s’agit de l’option existante de gestion des licences, où vous payez pour chaque licence de machine virtuelle SQL Server. 
  - **Gestion des licences d’hôtes dédiés** : Nouveau modèle de licence disponible pour l’hôte dédié Azure, où les licences SQL Server sont regroupées en bundle et payées au niveau de l’hôte. 


Options au niveau de l’hôte pour l’utilisation des licences SQL Server existantes : 
  - SQL Server Édition Entreprise Azure Hybrid Benefit
    - Accessible aux clients disposant d’un Contrat de service ou d’un abonnement.
    - Affectez une licence à tous les cœurs physiques disponibles, et tirez parti d’une virtualisation illimitée (en fonction du nombre maximal de processeurs virtuels pris en charge par l’hôte).
        - Pour plus d’informations sur l’application d’Azure Hybrid Benefit à un hôte dédié Azure, consultez les [Questions fréquentes (FAQ) sur Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licences SQL Server acquises avant le 1er octobre
      - SQL Server Édition Entreprise dispose à la fois des options de licence au niveau de l’hôte et des options de licence par machine virtuelle. 
      - SQL Server Édition Standard dispose uniquement de l’option de licence par machine virtuelle. 
          - Pour plus d’informations, consultez les [conditions relatives au produit de Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Si aucune option au niveau de l’hôte dédié SQL Server n’est sélectionnée, vous pouvez sélectionner SQL Server AHB au niveau de machines virtuelles individuelles, comme c’est le cas pour des machines virtuelles multilocataires.



## <a name="provisioning"></a>Approvisionnement  
Le provisionnement d’une machine virtuelle SQL Server sur l’hôte dédié n’est pas différent de celui d’une autre machine virtuelle Azure. Pour ce faire, utilisez [Azure PowerShell](../dedicated-hosts-powershell.md), le [portail Azure](../dedicated-hosts-portal.md) et [Azure CLI](../../linux/dedicated-hosts-cli.md).

Le processus d’ajout d’une machine virtuelle SQL Server existante à l’hôte dédié nécessite un temps d’arrêt. Toutefois, cela n’a pas d’impact sur les données et n’entraîne pas de perte de ces dernières. Néanmoins, toutes les bases de données, notamment les bases de données système, doivent être sauvegardées avant le déplacement.

## <a name="virtualization"></a>Virtualisation 

La virtualisation illimitée est l’un des avantages d’un hôte dédié. Par exemple, vous pouvez avoir des licences pour 64 vCores, mais vous pouvez configurer l’hôte pour en avoir 128. Ainsi, vous disposez du double de vCores mais vous ne payez que la moitié des licences SQL Server. 

Dans la mesure où il s’agit de votre hôte, vous pouvez configurer la virtualisation avec un ratio de 1:2. 

## <a name="faq"></a>Questions fréquentes (FAQ)

**Q : Comment Azure Hybrid Benefit fonctionne-t-il avec les licences Windows Server/SQL Server sur l’hôte dédié Azure ?**

A : Les clients peuvent utiliser leurs licences Windows Server et SQL Server existantes avec le programme Software Assurance, ou des licences d’abonnement éligibles, pour bénéficier d’un tarif réduit sur l’hôte dédié Azure via Azure Hybrid Benefit. Les clients Windows Server Datacenter et SQL Server Édition Entreprise bénéficient d’une virtualisation illimitée (ils peuvent déployer autant de machines virtuelles Windows Server que possible sur l’hôte, en fonction de la capacité physique du serveur sous-jacent) quand ils affectent une licence à l’ensemble de l’hôte et qu’il utilisent Azure Hybrid Benefit.  Toutes les charges de travail Windows Server et SQL Server de l’hôte dédié Azure sont également éligibles aux mises à jour de sécurité étendues pour Windows Server et SQL Server 2008/R2, sans frais supplémentaires. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


