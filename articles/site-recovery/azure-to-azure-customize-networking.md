---
title: Personnaliser les configurations de mise en réseau pour une machine virtuelle de basculement | Microsoft Docs
description: Fournit une vue d’ensemble de la personnalisation des configurations de mise en réseau pour une machine virtuelle de basculement dans le cadre de la réplication des machines virtuelles Azure avec Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 907a698a675a039dfdc852210adecb94c7bfab25
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886540"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personnaliser les configurations réseau de la machine virtuelle Azure cible

Cet article fournit des conseils sur la façon de personnaliser les configurations de mise en réseau sur la machine virtuelle Azure quand vous répliquez et récupérez des machines virtuelles Azure d’une région vers une autre avec [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Avant de commencer

Découvrez comment Site Recovery permet la récupération d’urgence pour [ce scénario](azure-to-azure-architecture.md).

## <a name="support-networking-resources"></a>Ressources de support de mise en réseau

La configuration des ressources clés suivantes peut être fournie pour la machine virtuelle de basculement lors de la réplication des machines virtuelles Azure.

- [Équilibreur de charge interne](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Adresse IP publique](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) pour le sous-réseau et pour la carte réseau

## <a name="pre-requisites"></a>Conditions préalables

- Veillez à planifier à l’avance vos configurations côté récupération.
- Vous devez créer à l’avance les ressources de mise en réseau. Fournissez-les en entrée afin que le service Azure Site Recovery puisse respecter ces paramètres et s’assurer que la machine virtuelle de basculement adhérera à ces paramètres.

## <a name="steps-to-customize-failover-networking-configurations"></a>Étapes de personnalisation des configurations de mise en réseau du basculement

1. Accédez à **Éléments répliqués**. 
2. Cliquez sur la machine virtuelle Azure de votre choix.
3. Cliquez sur **Calcul et réseau**, puis sur **Modifier**. Vous remarquerez que les paramètres de configuration de la carte réseau incluent les ressources correspondantes à la source. 

     ![Personnalisation](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Cliquez sur **Modifier** à côté de la carte réseau que vous souhaitez configurer. Dans le panneau suivant qui s’affiche, sélectionnez les ressources précréées correspondantes dans la cible.

    ![Exploration de la carte réseau](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Cliquez sur **OK**.

Site Recovery respecte à présent ces paramètres et s’assure que, lors du basculement, la machine virtuelle est connectée à la ressource sélectionnée via la carte réseau correspondante.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="unable-to-view-or-select-a-resource"></a>Impossible d’afficher ou de sélectionner une ressource

Si vous ne parvenez pas à sélectionner ou à afficher une ressource de mise en réseau, effectuez les vérifications suivantes et passez en revue les conditions suivantes :

- Le champ cible d’une ressource réseau est activé uniquement si la machine virtuelle source avait une entrée correspondante. Cela est basé sur le principe selon lequel, pour un scénario de récupération d’urgence, vous souhaiterez la version exacte ou une version réduite de votre source.
- Pour chacune des ressources réseau en question, certains filtres sont appliqués dans la liste déroulante pour garantir que la machine virtuelle de basculement pourra s’attacher à la ressource sélectionnée et que la fiabilité du basculement sera maintenue. Ces filtres sont basés sur les conditions de mise en réseau qui auraient été vérifiées lors de la configuration de la machine virtuelle source.

Validations de l’équilibreur de charge interne :

1. L’abonnement et la région de l’équilibreur de charge et de la machine virtuelle cible doivent être identiques.
2. Le réseau virtuel associé à l’équilibreur de charge interne et celui de la machine virtuelle cible doivent être identiques.
3. La référence SKU d’adresse IP publique de la machine virtuelle cible et la référence SKU de l’équilibreur de charge interne doivent être identiques.
4. Si la machine virtuelle cible est configurée de manière à être placée dans une zone de disponibilité, vérifiez si l’équilibreur de charge est redondant interzone ou fait partie d’une zone de disponibilité quelconque. (Les équilibreurs de charge de référence SKU de base ne prennent pas en charge les zones et n’apparaîtront pas dans la liste déroulante dans ce cas.)
5. Assurez-vous que l’équilibreur de charge interne possède un pool principal et une configuration frontale créés au préalable.


Adresse IP publique :
    
1. L’abonnement et la région de l’adresse IP publique et de la machine virtuelle cible doivent être identiques.
2. La référence SKU d’adresse IP publique de la machine virtuelle cible et la référence SKU de l’équilibreur de charge interne doivent être identiques.

Groupe de sécurité réseau :
1. L’abonnement et la région du groupe de sécurité réseau et de la machine virtuelle cible doivent être identiques.


> [!WARNING]
> Si la machine virtuelle cible est associée à un groupe à haute disponibilité, vous devez associer l’adresse IP publique/l’équilibreur de charge interne de la même référence SKU comme étant ceux de l’adresse IP publique/l’équilibreur de charge interne d’une autre machine virtuelle dans le groupe à haute disponibilité. Si vous ne le faites pas, cela peut entraîner un échec du basculement.
